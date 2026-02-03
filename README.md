# Functions-Class-Method-Serial-Tkinter-UI-Proteus
My Project for Computer Programming
#------------------------ui_display.py------------------------------
class ui_display:
    def __init__(self):
        self

  def forDisplay(self):
        import threading  #Import threading
        import tkinter as tk  #Import tkinter for GUI
        from PIL import Image, ImageTk  #Import PIL for bg image
        import serial  #Import pySerial for serial communication
        import time  #Import time for sleep functionality

  #Initialize serial communication on COM1 with a baud rate of 9600
  mySerial = serial.Serial('COM1', 9600, timeout=1)

  def send_message():
            #Get the message from the input textbox, strip whitespace, and convert to uppercase
            message = input_textbox.get().strip().upper()
            valid_choices = {"SUMMER", "AUTUMN", "WINTER", "SPRING"}  # Define valid choices

  #Check if the message is correct
            if message in valid_choices:
                mySerial.write(message.encode())  #Send valid message to serial
            else:
                mySerial.write(message.encode())  #Send unknown message to serial
                print(f"{message} is UNKNOWN")  #Print unknown message to console

  def clear_message():
            #Clear the input textbox and the display textbox
            input_textbox.delete(0, tk.END)
            display_textbox.config(state=tk.NORMAL)
            display_textbox.delete(1.0, tk.END)
            display_textbox.config(state=tk.DISABLED)

   def display_message():
            input_textbox.delete(0, tk.END)
            display_textbox.config(state=tk.NORMAL)
            display_textbox.delete(1.0, tk.END)
            display_textbox.config(state=tk.DISABLED)

   def listen_serial():
            #Continuously listen for incoming serial data
            while True:
                if mySerial.in_waiting > 0:  #Check if data is available
                    dataReceived = mySerial.readline().decode('utf-8').strip()  #Read and decode the data
                    print(dataReceived)  #Print received data to console
                    display_textbox.config(state=tk.NORMAL)  #Enable the display textbox for editing
                    display_textbox.insert(tk.END, dataReceived + "\n")  #Insert received data into the textbox
                    display_textbox.config(state=tk.DISABLED)  #Disable the textbox to prevent user editing
                time.sleep(0.1)  #Sleep for a short duration to avoid busy waiting

   #Create the main application window
        root = tk.Tk()
        root.title("FAVORITE SEASON?")

  #Hold the background image and other widgets
        canvas = tk.Canvas(root, width=600, height=400)
        canvas.pack()

   #Load and display the background image
        bg_image = Image.open('bg.png')
        bg_image = ImageTk.PhotoImage(bg_image)
        canvas.create_image(0, 0, anchor=tk.NW, image=bg_image)

   #Create a button to send the message
        send_button = tk.Button(root, width=25, text="I'M SATISFIED",
                                font=("Times New Roman", 12), bg="#B2E0B2", command=send_message)
        canvas.create_window(300, 50, window=send_button)

   #Create a button to clear the message
        clear_button = tk.Button(root, width=25, text="I CHANGED MY MIND",
                                 font=("Times New Roman", 12), bg="#FFB3B3", command=clear_message)
        canvas.create_window(300, 100, window=clear_button)

   #Create an input textbox for user input
        input_textbox = tk.Entry(root, width=30, font=("Times New Roman", 12), bg="#DAF0F7")
        canvas.create_window(300, 140, window=input_textbox)

   #Create a label to display the choices
        display_choices = tk.Label(root, width=45, text="CHOOSE: SUMMER, AUTUMN, WINTER, OR SPRING",
                                   font=("Times New Roman", 12))
        canvas.create_window(300, 350, window=display_choices)

   #Create a textbox to display messages
        display_textbox = tk.Text(root, width=40, height=9, font=("Times New Roman", 12), bg="#FFF9C4", wrap=tk.WORD)
        canvas.create_window(300, 245, window=display_textbox)

   #Start the serial listening in a separate thread to avoid blocking the GUI
        serial_thread = threading.Thread(target=listen_serial, daemon=True)
        serial_thread.start()  #Start the thread

   root.mainloop()  #Start the main loop

  mySerial.close()  #Close the serial connection when the application exits


#------------------------MAIN_UI.py------------------------------
from display.ui_display import ui_display  #Import the ui_display class from the display module

display = ui_display()  #Create an instance of the ui_display class

display.forDisplay()  #Call the forDisplay method to run the application

#------------------------ARDUINO----------------------------------
#define YELLOW 6  // Define pin number for the YELLOW LED
#define RED 7     // Define pin number for the RED LED
#define BLUE 8    // Define pin number for the BLUE LED
#define GREEN 9   // Define pin number for the GREEN LED

void setup() {
   Serial.begin(9600); // Initialize serial communication at 9600 baud rate
   pinMode(YELLOW, OUTPUT); // Set the YELLOW pin as an output
   pinMode(RED, OUTPUT);    // Set the RED pin as an output
   pinMode(BLUE, OUTPUT);   // Set the BLUE pin as an output
   pinMode(GREEN, OUTPUT);  // Set the GREEN pin as an output
}

void loop() {
  // Check if there is any data available to read from the serial port
  if (Serial.available() > 0) {
    // Read the incoming string until a newline character is encountered
    String incomingString = Serial.readStringUntil('\n'); 
    Serial.print("Received: "); // Print a message indicating data was received
    Serial.println(incomingString); // Print the received string
    delay(1000); // Wait for 1 second for readability

  // Check the content of the incoming string and respond accordingly
    if (incomingString.equalsIgnoreCase("SUMMER")) {
      Serial.println("Your favorite weather is " + incomingString); // Print the favorite weather
      digitalWrite(YELLOW, HIGH); // Turn on the YELLOW LED for SUMMER
    } else if (incomingString.equalsIgnoreCase("AUTUMN")) {
      Serial.println("Your favorite weather is " + incomingString); // Print the favorite weather
      digitalWrite(RED, HIGH); // Turn on the RED LED for AUTUMN
    } else if (incomingString.equalsIgnoreCase("WINTER")) {
      Serial.println("Your favorite weather is " + incomingString); // Print the favorite weather
      digitalWrite(BLUE, HIGH); // Turn on the BLUE LED for WINTER
    } else if (incomingString.equalsIgnoreCase("SPRING")) {
      Serial.println("Your favorite weather is " + incomingString); // Print the favorite weather
      digitalWrite(GREEN, HIGH); // Turn on the GREEN LED for SPRING
    } else {
      Serial.println(" -> Unknown weather type."); // Print a message for unknown input
    }

    delay(3000); // Wait for 3 seconds to keep the LED on

    // Turn off all LEDs after the delay
    digitalWrite(YELLOW, LOW); 
    digitalWrite(RED, LOW); 
    digitalWrite(BLUE, LOW); 
    digitalWrite(GREEN, LOW); 
  } 
} 
