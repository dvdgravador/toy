#include <SoftwareSerial.h>
#include <DFPlayerMini_Fast.h>

#define RFID_SUBJ_SS_PIN 10
#define RFID_SUBJ_RST_PIN 9
#define RFID_VERB_SS_PIN 8
#define RFID_VERB_RST_PIN 7
#define RFID_OBJ_SS_PIN 6
#define RFID_OBJ_RST_PIN 5
#define RFID_PLACE_SS_PIN 4
#define RFID_PLACE_RST_PIN 3
#define BUTTON_PIN 2

SoftwareSerial mySoftwareSerial(3, 2); // RX, TX
DFPlayerMini_Fast myDFPlayer;

String subject = "";
String verb = "";
String object = "";
String place = "";

MFRC522 rfidSubj(RFID_SUBJ_SS_PIN, RFID_SUBJ_RST_PIN);
MFRC522 rfidVerb(RFID_VERB_SS_PIN, RFID_VERB_RST_PIN);
MFRC522 rfidObj(RFID_OBJ_SS_PIN, RFID_OBJ_RST_PIN);
MFRC522 rfidPlace(RFID_PLACE_SS_PIN, RFID_PLACE_RST_PIN);

void setup() {
  Serial.begin(9600);
  mySoftwareSerial.begin(9600);
  myDFPlayer.begin(mySoftwareSerial);
  SPI.begin();
  rfidSubj.PCD_Init();
  rfidVerb.PCD_Init();
  rfidObj.PCD_Init();
  rfidPlace.PCD_Init();
  pinMode(BUTTON_PIN, INPUT_PULLUP);
}

void loop() {
  if (digitalRead(BUTTON_PIN) == LOW) {
    delay(50); // debounce delay
    if (digitalRead(BUTTON_PIN) == LOW) {
      if (readRFID(&rfidSubj, "SUBJ")) {
        subject = readRFID(&rfidSubj);
      }
      if (readRFID(&rfidVerb, "VERB")) {
        verb = readRFID(&rfidVerb);
      }
      if (readRFID(&rfidObj, "OBJ")) {
        object = readRFID(&rfidObj);
      }
      if (readRFID(&rfidPlace, "PLACE")) {
        place = readRFID(&rfidPlace);
      }
      if (subject != "" && verb != "" && object != "" && place != "") {
        playAudio(subject + " " + verb + " " + object + " " + place);
        subject = "";
        verb = "";
        object = "";
        place = "";
      }
    }
  }
}

String readRFID(MFRC522* rfid, String code) {
  if (!rfid->PICC_IsNewCardPresent() || !rfid->PICC_ReadCardSerial()) {
    return "";
  }
  String rfidTag = "";
  for (byte i = 0; i < rfid->uid.size; i++) {
    rfidTag.concat(String(rfid->uid.uidByte[i] < 0x10 ? "0" : ""));
    rfidTag.concat(String(rfid->uid.uidByte[i], HEX));
  }
  Serial.println("RFID " + code + " tag: " + rfidTag);
  rfid->PICC_HaltA();
  rfid->PCD_StopCrypto1();
  return rfidTag;
}

void playAudio(String filename) {
  Serial.println("Playing audio: " + filename);
  myDFPlayer.playMp3FolderFile(1, 1); // adjust folder and file numbers as needed
