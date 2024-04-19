#include <SPI.h>
#include <SD.h>

// Defines pin numbers for the ultrasonic sensor
const int trigPin = 9;
const int echoPin = 8;

// Defines pin number for the SD card module
const int chipSelect = 10;

// Variables to store the duration and distance
long duration;
int distance;

// File variable to handle file operations
File myFile;

void setup() {
  // Initialize pin modes for ultrasonic sensor
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);

  // Start the serial communication
  Serial.begin(9600);
  while (!Serial) {
    ; // Wait for the serial port to connect. Needed for native USB port only
  }

  // Initialize the SD card
  Serial.print("Initializing SD card...");
  if (!SD.begin(chipSelect)) {
    Serial.println("initialization failed!");
    while (1);
  }
  Serial.println("initialization done.");

  // Open the file. Note that only one file can be open at a time,
  // so you have to close this one before opening another.
  myFile = SD.open("distance_data.csv", FILE_WRITE);
  
  // If the file opened okay, write the header to it
  if (myFile) {
    myFile.println("Time,Distance");
    myFile.close();
  } else {
    // If the file didn't open, print an error
    Serial.println("error opening distance_data.csv");
  }
}

void loop() 
{
  // Clears the trigPin condition
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  // Sets the trigPin on HIGH state for 10 microseconds
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  
  // Reads the echoPin, returns the sound wave travel time in microseconds
  duration = pulseIn(echoPin, HIGH);
  
  // Calculating the distance
  distance = duration * 0.034 / 2;
  
  // Prints the distance on the Serial Monitor
  Serial.print("Distance: ");
  Serial.println(distance);

  // Write the distance to the SD card in CSV format
  myFile = SD.open("distance_data.csv", FILE_WRITE);
  if (myFile) 
  {
    String dataString = String(millis()) + "," + String(distance);
    myFile.println(dataString);
    myFile.close();
  } 
  else 
  {
    Serial.println("error opening distance_data.csv");
  }

  // Wait a bit before taking the next measurement
  delay(500);

![image](https://github.com/JeewanRai/Arduino-Programming-/assets/137490711/ed7646ce-5c28-4f73-9727-52812e50f4b7)

Output:
Initializing SD card...initialization done.
error opening distance_data.csv
Distance: 392
error opening distance_data.csv
Distance: 395
error opening distance_data.csv
Distance: 395
error opening distance_data.csv
Distance: 395
error opening distance_data.csv
Distance: 395
error opening distance_data.csv
Distance: 395
error opening distance_data.csv
Distance: 395
error opening distance_data.csv


