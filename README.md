[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/DlAZiTnC)

String[] wordList;
String[] currentWords = new String[5];
float[] wordX = new float[5];
float wordY = 200;
int selected = -1;  
String inputBuffer = "";
boolean fileLoaded = false;  // <— use this instead of 'loaded' to avoid conflicts

void setup() {
  size(800, 400);
  textAlign(CENTER, CENTER);
  textSize(32);
 
  selectInput("eff_large_wordlist.txt", "fileSelected");
}

void fileSelected(File selection) {
  if (selection == null) {
    println("No file selected. Exiting.");
    exit();
  } else {
    println("Loaded file: " + selection.getAbsolutePath());
    wordList = loadStrings(selection);
    if (wordList == null || wordList.length == 0) {
      println("Error: empty or unreadable file.");
      exit();
    }
    generateWords();
    fileLoaded = true;
  }
}

void generateWords() {
  for (int i = 0; i < 5; i++) {
    int index = int(random(wordList.length));
    currentWords[i] = wordList[index].trim();
    wordX[i] = map(i, 0, 4, 100, width - 100);
  }
}

void draw() {
  background(20);
  
    if (!fileLoaded) {
    fill(180);
    text("Waiting for file to load...", width/2, height/2);
    return;
  }
  
  fill(255);
  text("Diceware Generator", width/2, 60);
  
  
  // draw words
  for (int i = 0; i < 5; i++) {
    if (i == selected) fill(100, 200, 255);
    else fill(255);
    text(currentWords[i], wordX[i], wordY);
  }
  
  // if editing
  if (selected != -1) {
    fill(180);
    textSize(20);
    text("Editing word " + (selected+1) + ": " + inputBuffer, width/2, height - 50);
    textSize(32);
  }
}

void mousePressed() {
  if (!fileLoaded) return;
  
  selected = -1;
  for (int i = 0; i < 5; i++) {
    float tw = textWidth(currentWords[i]);
    if (abs(mouseX - wordX[i]) < tw/2 && abs(mouseY - wordY) < 20) {
      selected = i;
      inputBuffer = "";
    }
  }
}

void keyPressed() {
  if (!fileLoaded || selected == -1) return;
  
  if (key == ENTER || key == RETURN) {
    if (inputBuffer.length() == 5) {
      currentWords[selected] = inputBuffer;
    }
    inputBuffer = "";
    selected = -1;
  } else if (key == BACKSPACE) {
    if (inputBuffer.length() > 0)
      inputBuffer = inputBuffer.substring(0, inputBuffer.length()-1);
  } else if (inputBuffer.length() < 5 && Character.isLetter(key)) {
    inputBuffer += key;
  }
}
