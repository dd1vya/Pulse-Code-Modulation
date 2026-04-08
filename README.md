# Pulse-Code-Modulation
# Aim
Write a simple Python program for the modulation and demodulation of PCM, and DM.
# Tools required
Google Colab
# Theory
### Pulse Code Modulation
A signal is pulse code modulated to convert its analog information into a binary sequence, i.e., 1s and 0s. The output of a PCM will resemble a binary sequence. The following figure shows an example of PCM output with respect to instantaneous values of a given sine wave.
Instead of a pulse train, PCM produces a series of numbers or digits, and hence this process is called as digital. Each one of these digits, though in binary code, represent the approximate amplitude of the signal sample at that instant.
In Pulse Code Modulation, the message signal is represented by a sequence of coded pulses. This message signal is achieved by representing the signal in discrete form in both time and amplitude.
### Delta Modulation 
Delta Modulation (DM) is a simple method of analog-to-digital conversion used in digital communication. Instead of sending the actual signal value, it transmits only the difference between the current sample and the previous one. Each sample is encoded as 1 bit—a ‘1’ if the signal increases and a ‘0’ if it decreases.
This makes delta modulation easy to implement and bandwidth-efficient, but it has limitations like slope overload distortion (when the signal changes too fast) and granular noise (when the step size is too large). Despite this, DM is useful in low-complexity and low-data-rate communication systems.
# Program
### Pulse Code Modulation (PCM)
```
import numpy as np
import matplotlib.pyplot as plt

# Parameters
fs = 5000          # Sampling frequency
fm = 50            # Message frequency
levels = 16        # Quantization levels (4-bit PCM)
duration = 0.1

t = np.linspace(0, duration, int(fs*duration), endpoint=False)

# 1. Message signal
m = np.sin(2*np.pi*fm*t)

# 2. Sampling 
sampled = m.copy()

# 3. Quantization
step = (m.max() - m.min()) / levels
qidx = np.floor((sampled - m.min()) / step)
qidx = np.clip(qidx, 0, levels-1)

q = qidx * step + m.min() + step/2   # mid-rise quantizer

# 4. Encoding (PCM bitstream)
nbits = int(np.log2(levels))
bitstream = [format(int(i), f'0{nbits}b') for i in qidx]

# 5. Decoding 
didx = np.array([int(b, 2) for b in bitstream])
decoded = didx * step + m.min() + step/2

# 6. Reconstruction
kernel = 10
reconstructed = np.convolve(decoded, np.ones(kernel)/kernel, mode='same')

clock = np.sign(np.sin(2*np.pi*200*t))

plt.figure(figsize=(12,10))

plt.subplot(4,1,1)
plt.plot(t, m)
plt.title("Message Signal (Analog)")
plt.grid()

plt.subplot(4,1,2)
plt.plot(t, clock)
plt.title("Clock Signal (Sampling Reference)")
plt.grid()

plt.subplot(4,1,3)
plt.step(t, q)
plt.title("PCM Signal (Sampled + Quantized)")
plt.grid()

plt.subplot(4,1,4)
plt.plot(t, reconstructed, '--')
plt.title("Reconstructed Signal (After Decoding)")
plt.grid()

plt.tight_layout()
plt.show()
```
### Delta Modulation
```
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, filtfilt

fs, f, d = 10000, 10, 0.1

t = np.arange(0, 1, 1/fs)
m = np.sin(2*np.pi*f*t)

dm, bits = [0], []

for x in m:
    s = d if x > dm[-1] else -d
    bits.append(s > 0)
    dm.append(dm[-1] + s)

dem = [0]
for b in bits:
    dem.append(dem[-1] + (d if b else -d))

dm, dem = np.array(dm), np.array(dem)

b, a = butter(4, 20/(fs/2))
y = filtfilt(b, a, dem)

plt.figure(figsize=(12, 6))

plt.subplot(3,1,1)
plt.plot(t, m, linewidth=1)
plt.title("Original Signal")
plt.grid()

plt.subplot(3,1,2)
plt.step(t, dm[:-1], where='mid')
plt.title("Delta Modulated Signal")
plt.grid()

plt.subplot(3,1,3)
plt.plot(t, y[:-1], 'r:', linewidth=1)
plt.title("Demodulated & Filtered Signal")
plt.grid()

plt.tight_layout()
plt.show()
```
# Output Waveform
### Pulse Code Modulation 

<img width="1190" height="989" alt="image" src="https://github.com/user-attachments/assets/4a895df8-9f03-4e17-911d-748b94f7b620" />



### Delta Modulation 

<img width="1189" height="590" alt="image" src="https://github.com/user-attachments/assets/e6d20a2c-6531-49d3-8f04-763e67d7a36b" />



# Results
Thus, the programs for pulse code modulation and delta modulation have been executed and the waveforms have been verified successfully.
