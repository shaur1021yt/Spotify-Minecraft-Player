#include <Arduino.h>
#include <ArduinoJson.h>
#include <Adafruit_GFX.h>
#include <Adafruit_ST7735.h>
#include <WiFi.h>
#include <SpotifyEsp32.h>
#include <SPI.h>

#define TFT_CS 1
#define TFT_RST 2
#define TFT_DC 3
#define TFT_SCLK 4
#define TFT_MOSI 5

// Minecraft-style colors
#define MC_GRASS 0x03E0
#define MC_DIRT  0x79E0
#define MC_STONE 0x8410
#define MC_TEXT  ST77XX_WHITE

char* SSID = "YOUR WIFI SSID";
const char* PASSWORD = "YOUR WIFI PASSWORD";
const char* CLIENT_ID = "YOUR CLIENT ID FROM THE SPOTIFY DASHBOARD";
const char* CLIENT_SECRET = "YOUR CLIENT SECRET FROM THE SPOTIFY DASHBOARD";

String lastArtist;
String lastTrackname;

Spotify sp(CLIENT_ID, CLIENT_SECRET);
Adafruit_ST7735 tft = Adafruit_ST7735(TFT_CS, TFT_DC, TFT_MOSI, TFT_SCLK, TFT_RST);

// Draw Minecraft-style box
void drawBlockBox(int x, int y, int w, int h, uint16_t color) {
    tft.fillRect(x, y, w, h, color);
    tft.drawRect(x, y, w, h, ST77XX_BLACK);
}

// Draw full UI background
void drawMinecraftUI() {
    tft.fillScreen(MC_STONE);

    // Inner dirt panel
    tft.fillRect(4, 4, 152, 120, MC_DIRT);

    // Grass header
    tft.fillRect(4, 4, 152, 20, MC_GRASS);

    // Title
    tft.setTextColor(ST77XX_BLACK);
    tft.setCursor(10, 8);
    tft.setTextSize(1);
    tft.print("Now Playing");

    // Artist + Track boxes
    drawBlockBox(8, 30, 144, 30, MC_STONE);
    drawBlockBox(8, 70, 144, 30, MC_STONE);
}

void setup() {
    Serial.begin(115200);

    tft.initR(INITR_BLACKTAB);
    tft.setRotation(1);
    Serial.println("TFT Initialized!");

    drawMinecraftUI();

    WiFi.begin(SSID, PASSWORD);
    Serial.print("Connecting to WiFi...");
    while (WiFi.status() != WL_CONNECTED) {
        delay(1000);
        Serial.print(".");
    }
    Serial.println("\nConnected!");

    // Show IP briefly
    tft.setTextColor(MC_TEXT);
    tft.setCursor(10, 110);
    tft.print(WiFi.localIP().toString());

    sp.begin();
    while (!sp.is_auth()) {
        sp.handle_client();
    }
    Serial.println("Authenticated");

    // Redraw UI after auth
    drawMinecraftUI();
}

void loop() {
    String currentArtist = sp.current_artist_names();
    String currentTrackname = sp.current_track_name();

    // Update Artist
    if (lastArtist != currentArtist &&
        currentArtist != "Something went wrong" &&
        !currentArtist.isEmpty()) {

        lastArtist = currentArtist;

        drawBlockBox(8, 30, 144, 30, MC_STONE);

        tft.setTextColor(MC_TEXT);
        tft.setCursor(12, 40);
        tft.setTextSize(1);
        tft.print("Artist:");
        tft.setCursor(12, 50);
        tft.print(lastArtist);

        Serial.println("Artist: " + lastArtist);
    }

    // Update Track
    if (lastTrackname != currentTrackname &&
        currentTrackname != "Something went wrong" &&
        currentTrackname != "null") {

        lastTrackname = currentTrackname;

        drawBlockBox(8, 70, 144, 30, MC_STONE);

        tft.setTextColor(MC_TEXT);
        tft.setCursor(12, 80);
        tft.setTextSize(1);
        tft.print("Track:");
        tft.setCursor(12, 90);
        tft.print(lastTrackname);

        Serial.println("Track: " + lastTrackname);
    }

    delay(2000);
}
