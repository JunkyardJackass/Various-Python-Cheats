#!/usr/bin/env python3

import subprocess
import sys
import time
from pynput import keyboard
import threading

# Global stuff
game_window_id = None
terminal_window_id = None
listener = None

# Artillery math stuff.
ARTILLERY_TYPES = {
    "1": {"name": "ENGLISH/GERMAN", "m": -0.23709, "b": 1001.525},
    "2": {"name": "RUSSIAN", "m": -0.2136691176, "b": 1141.7215},
    "3": {"name": "BRITISH", "m": -0.1773, "b": 550.69}
}

# Setting a default, to the most common guns is smrt.
current_artillery = ARTILLERY_TYPES["1"]

def on_key_press(key):
    global terminal_window_id
    
    if key == keyboard.Key.right and terminal_window_id:
        subprocess.run(['xdotool', 'windowactivate', terminal_window_id])
        print("Swapped: READY")
        return True

def start_keyboard_listener():
    global listener
    listener = keyboard.Listener(on_press=on_key_press)
    listener.start()

def SwitchGun():
    global current_artillery
    
    print("\n--- ARTILLERY TYPE SELECTION ---")
    print("1: ENGLISH/GERMAN")
    print("2: RUSSIAN")
    print("3: BRITISH")
    print("Current type: " + current_artillery["name"])
    
    choice = input("Enter artillery type (1-3) or press Enter to cancel: ")
    
    if choice in ARTILLERY_TYPES:
        current_artillery = ARTILLERY_TYPES[choice]
        print(f"\nSwitched to {current_artillery['name']} artillery")
    elif choice != "":
        print("\nInvalid selection. Artillery type unchanged.")
    
    print("")

def yup():
    global game_window_id, terminal_window_id, current_artillery
    
    # First-time setup to capture window IDs
    if game_window_id is None:
        # First capture the terminal window ID
        result = subprocess.run(['xdotool', 'getactivewindow'], capture_output=True, text=True)
        terminal_window_id = result.stdout.strip()
        print(f"Terminal window ID saved: {terminal_window_id}")
        print("\n--- SETUP ---")
        print("We need to capture your game window ID.")
        print("1. After pressing Enter here, you'll have 5 seconds to switch to your game")
        print("2. Make sure your game window is active/focused within that time")
        input("Press Enter to start the 5-second countdown...")
        
        print("Switch to game in:")
        for i in range(5, 0, -1):
            print(f"{i}...")
            time.sleep(1)
            
        result = subprocess.run(['xdotool', 'getactivewindow'], capture_output=True, text=True)
        game_window_id = result.stdout.strip()
        subprocess.run(['xdotool', 'windowactivate', terminal_window_id])
        
        print(f"\nGame window ID saved: {game_window_id}")
        print("--- SETUP COMPLETE ---\n")
        print("Now, whenever you need to calculate artillery:")
        print("1. Press =RIGHT ARROW= in your game to switch to this calculator")
        print("2. Enter the distance (must be between 100-1600)")
        print("3. It will automatically switch back to your game")
        print("")
        print("To switch artillery types, enter *** at the distance prompt")
        print("")
        start_keyboard_listener()
        
    print(f"Current artillery type: {current_artillery['name']}")
    print("(Enter *** to change artillery type)")
    
    try:
        dist = input("Enter Artillery distance (or 'q' to quit): ")

        #If user needs to switch Guns.
        if dist == "***":
            SwitchGun()
        #Close the Door.
        elif dist.lower() == 'q':
            if listener:
                listener.stop()
            print("Exiting artillery calculator...")
            sys.exit(0)
            
        else:
            # Convert input to float (Makes it all stable(r) incase I press something dumb)
            dist_float = float(dist)
            
            # Check if distance is within valid range
            if 100 <= dist_float <= 1600:
                # Calculate mil using current artillery coefficients
                mil = current_artillery["m"] * dist_float + current_artillery["b"]
                rounded_mil = round(mil, 1)  # Round to 1 decimal place (10th)
                
                print('DISTANCE ENT:    ', dist)
                print("+++++++++++++++++******")
                print('MILS TO TGT:     ', rounded_mil)  # Display the rounded value
                print("+++++++++++++++++******")
                print("")
                
                # Switch back to game window after showing the result
                print("Switching back to game...")
                subprocess.run(['xdotool', 'windowactivate', game_window_id])
            else:
                # Alert user if distance is out of range
                print("\n!!! ERROR: Distance must be between 100 and 1600 meters !!!")
                print("Please enter a valid distance.")
                print("")
                # Do NOT switch back to game window
                
    except ValueError:
        print("\n!!! ERROR: Invalid input. Please enter a number. !!!")
        # Do NOT switch back to game window

    # Start again after a short delay, prevents issues with the swap.
    time.sleep(0.5)
    yup()

# Not doing the STARTER UP stuff again, not needed here.
print("HLL Artillery Calculator")
print("=======================")
print("\nArtillery Types Available:")
print("- ENGLISH/GERMAN (default)")
print("- RUSSIAN")
print("- BRITISH")
print("To switch artillery types, enter *** at the distance prompt")
print("Press KEYPAD PLUS (+) in game to switch to calculator")
print("")

yup()
