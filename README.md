import json
import os
from tkinter import *
from tkinter import messagebox
from cryptography.fernet import Fernet

# Load or generate encryption key
if not os.path.exists("secret.key"):
    key = Fernet.generate_key()
    with open("secret.key", "wb") as key_file:
        key_file.write(key)
else:
    with open("secret.key", "rb") as key_file:
        key = key_file.read()

fernet = Fernet(key)

# Encrypt function
def encrypt_data(data):
    return fernet.encrypt(data.encode()).decode()

# Decrypt function
def decrypt_data(data):
    return fernet.decrypt(data.encode()).decode()

# Save Password Function
def save_password():
    website = website_entry.get()
    username = username_entry.get()
    password = password_entry.get()

    if not website or not username or not password:
        messagebox.showwarning("Oops", "Please don't leave any fields empty!")
        return

    encrypted_password = encrypt_data(password)
    new_data = {
        website: {
            "username": username,
            "password": encrypted_password
        }
    }

    try:
        with open("data.json", "r") as data_file:
            data = json.load(data_file)
    except FileNotFoundError:
        data = {}

    data.update(new_data)

    with open("data.json", "w") as data_file:
        json.dump(data, data_file, indent=4)

    website_entry.delete(0, END)
    username_entry.delete(0, END)
    password_entry.delete(0, END)
    messagebox.showinfo("Saved", "Password saved successfully!")

# GUI Setup
root = Tk()
root.title("CyberVault - Password Manager")
root.config(padx=40, pady=40)

# Labels
Label(root, text="Website:").grid(row=0, column=0)
Label(root, text="Username:").grid(row=1, column=0)
Label(root, text="Password:").grid(row=2, column=0)

# Entry fields
website_entry = Entry(root, width=35)
username_entry = Entry(root, width=35)
password_entry = Entry(root, width=35)

website_entry.grid(row=0, column=1, columnspan=2)
username_entry.grid(row=1, column=1, columnspan=2)
password_entry.grid(row=2, column=1, columnspan=2)

# Save Button
Button(root, text="Save Password", width=36, command=save_password).grid(row=3, column=1, columnspan=2, pady=10)

# Run the GUI
root.mainloop()
