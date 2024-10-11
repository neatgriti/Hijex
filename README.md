# Hijex
 Wi-Fi-based one-way voice transmitter, where audio is captured on a server (transmitter), sent over Wi-Fi, and played on a client device (receiver). This allows real-time voice streaming over a local network. The system uses Python and libraries like PyAudio and Sockets to handle audio capture, transmission, and playback between device
import socket
import pyaudio

# Server configuration
HOST = '0.0.0.0'  # Listen on all network interfaces
PORT = 5000  # Port to listen on

# Audio configuration
CHUNK = 1024  # Number of frames per buffer
FORMAT = pyaudio.paInt16  # 16-bit resolution
CHANNELS = 1  # Mono audio
RATE = 44100  # 44.1kHz sampling rate

# Initialize PyAudio
audio = pyaudio.PyAudio()
stream = audio.open(format=FORMAT, channels=CHANNELS,
                    rate=RATE, input=True, frames_per_buffer=CHUNK)

# Initialize Socket
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_socket.bind((HOST, PORT))
server_socket.listen(1)
print(f"Server is listening on {HOST}:{PORT}")

conn, addr = server_socket.accept()
print(f"Connection from {addr}")

try:
    while True:
        # Read audio from microphone
        data = stream.read(CHUNK)
        # Send audio data to the client
        conn.sendall(data)
except KeyboardInterrupt:
    print("Server shutting down...")
finally:
    conn.close()
    stream.stop_stream()
    stream.close()
    audio.terminate()
    server_socket.close()

