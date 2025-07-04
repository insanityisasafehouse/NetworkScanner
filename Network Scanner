import socket
import sys
import threading
from datetime import datetime

# --- Configuration ---
# Define a timeout for connection attempts in seconds.
# This prevents the scanner from hanging indefinitely on unresponsive ports.
CONNECTION_TIMEOUT = 1.0

# --- Helper Functions ---

def scan_port(target_ip: str, port: int, open_ports: list):
    """
    Attempts to connect to a specific port on a target IP address.
    If the connection is successful, the port is considered open.

    Args:
        target_ip (str): The IP address of the target.
        port (int): The port number to scan.
        open_ports (list): A shared list to store open ports found.
    """
    try:
        # Create a new socket object.
        # AF_INET specifies the address family (IPv4).
        # SOCK_STREAM specifies the socket type (TCP).
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        # Set a timeout for the connection attempt.
        s.settimeout(CONNECTION_TIMEOUT)
        # Attempt to connect to the target IP and port.
        result = s.connect_ex((target_ip, port)) # connect_ex returns an error indicator instead of raising an exception

        if result == 0:
            # If result is 0, the connection was successful, meaning the port is open.
            print(f"Port {port} is OPEN")
            open_ports.append(port)
        s.close() # Close the socket regardless of success or failure
    except socket.gaierror:
        # Handles cases where the hostname cannot be resolved.
        print("Hostname could not be resolved.")
        sys.exit()
    except socket.error:
        # Handles general socket errors (e.g., network unreachable).
        print("Couldn't connect to server.")
        sys.exit()
    except Exception as e:
        # Catch any other unexpected errors.
        print(f"An unexpected error occurred while scanning port {port}: {e}")

def main():
    """
    Main function to run the network scanner.
    Prompts the user for a target and a range of ports.
    """
    print("-" * 50)
    print("Simple Network Scanner")
    print("-" * 50)

    # Get target IP/hostname from user input.
    target = input("Enter the target IP address or hostname: ")

    # Resolve hostname to IP address.
    try:
        target_ip = socket.gethostbyname(target)
    except socket.gaierror:
        print("Error: Hostname could not be resolved. Exiting.")
        sys.exit()

    # Get port range from user input.
    try:
        start_port = int(input("Enter the starting port number: "))
        end_port = int(input("Enter the ending port number: "))
        if not (0 <= start_port <= 65535 and 0 <= end_port <= 65535 and start_port <= end_port):
            print("Error: Invalid port range. Ports must be between 0 and 65535, and start port must be less than or equal to end port. Exiting.")
            sys.exit()
    except ValueError:
        print("Error: Invalid port number. Please enter an integer. Exiting.")
        sys.exit()

    print(f"\nScanning target: {target_ip}")
    print(f"Scanning ports from {start_port} to {end_port}...\n")

    # Record the start time for performance measurement.
    t1 = datetime.now()

    # List to store all open ports found.
    open_ports = []
    threads = []

    # Iterate through the specified port range and start a thread for each port.
    for port in range(start_port, end_port + 1):
        thread = threading.Thread(target=scan_port, args=(target_ip, port, open_ports))
        threads.append(thread)
        thread.start()

    # Wait for all threads to complete.
    for thread in threads:
        thread.join()

    # Record the end time.
    t2 = datetime.now()
    # Calculate the total time taken for the scan.
    total_time = t2 - t1

    print("\n" + "-" * 50)
    print("Scan complete.")
    if open_ports:
        print(f"Open ports found: {sorted(open_ports)}")
    else:
        print("No open ports found in the specified range.")
    print(f"Scanning took: {total_time}")
    print("-" * 50)

if __name__ == "__main__":
    main()
