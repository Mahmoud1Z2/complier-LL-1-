package application;

import java.io.BufferedReader;
import java.io.File;
import java.io.FileReader;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.HashSet;
import java.util.Iterator;
import java.util.List;
import java.util.Set;
import java.util.StringTokenizer;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

import javafx.application.Application;
import javafx.geometry.Pos;
import javafx.stage.FileChooser;
import javafx.stage.Stage;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.control.TextArea;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.GridPane;
import javafx.scene.layout.VBox;

public class Main extends Application {

	public static int lines = 0;

	static int rows = 39;
	static int cols = 47;
	public static int[][] table = new int[rows][cols];
	public static String[] nonTerminals = new String[39];
	public static String[] terminals = new String[48];
	public static String[] productions = new String[81];
	public static String[] reservedWords = new String[29];
	public static String[] tokens = new String[14];
	public static TextArea stackTextArea = new TextArea();
	public static TextArea inputTextArea = new TextArea(); // Display input in the input TextArea
	public static TextArea productionTextArea = new TextArea();
	public static TextArea smallTextArea = new TextArea();
	public static String theCode;
	public static String error;

	@Override
	public void start(Stage primaryStage) {
		try {
			BorderPane root = new BorderPane();

			// Set the scene to full screen
			primaryStage.setFullScreen(true);

			// Create a VBox to hold components
			VBox vbox = new VBox(10);
			root.setCenter(vbox); // Set the VBox at the center of the BorderPane
			vbox.setAlignment(Pos.CENTER); // Center align the contents of the VBox

			// Create a button for file selection
			Button fileChooserButton = new Button("Open Input File");
			fileChooserButton.setOnAction(e -> {
				FileChooser fileChooser = new FileChooser();
				fileChooser.setTitle("Open Input File");
				File inputFile = fileChooser.showOpenDialog(primaryStage);
				if (inputFile != null) {
					try {
						
						String a = readFile(inputFile);

						theCode = readFile2(inputFile);
						String[] q = a.split(" ");
						
						//check if code start with module
						if (q[0].equals("module")) {
							parse(scan(a));
						} else {
							//print the error
							smallTextArea.setText("Error in line one , should start with module");
						}
						//setVisible(false) to make textAreas visible 
						fileChooserButton.setVisible(false);

						// Set TextArea sizes
						stackTextArea.setPrefHeight(600); // Set height to match scene height
						inputTextArea.setPrefHeight(600); // Set height to match scene height
						productionTextArea.setPrefHeight(600); // Set height to match scene height
						smallTextArea.setPrefHeight(100); // Set height of small TextArea

						// Create Labels for stack, input, and production
						Label stackLabel = new Label("Stack");
						Label inputLabel = new Label("Input");
						Label productionLabel = new Label("Production");

						// Create a GridPane to organize TextAreas and Labels side by side
						GridPane gridPane = new GridPane();
						gridPane.setHgap(10); // Set horizontal gap between components

						// Add Labels and TextAreas to GridPane
						gridPane.add(stackLabel, 0, 0);
						gridPane.add(inputLabel, 1, 0);
						gridPane.add(productionLabel, 2, 0);
						gridPane.add(stackTextArea, 0, 1);
						gridPane.add(inputTextArea, 1, 1);
						gridPane.add(productionTextArea, 2, 1);

						gridPane.add(new Label("The result"), 1, 2);
						gridPane.add(smallTextArea, 1, 3);

						// Add both GridPanes to VBox
						vbox.getChildren().addAll(gridPane);
					} catch (Exception ex) {
						ex.printStackTrace();
					}
				}
			});

			// Add the fileChooserButton to the VBox
			vbox.getChildren().add(fileChooserButton);

			// Create a Scene
			Scene scene = new Scene(root, 800, 600);

			// Set the scene to the stage
			primaryStage.setScene(scene);

			// Show the stage
			primaryStage.show();

		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	
	//this method to read the code exactly
	private String readFile2(File file) throws Exception {
		return new String(Files.readAllBytes(Paths.get(file.toURI())));
	}

	public static void main(String[] args) throws Exception {
		
		//fill the parsing table
		table[0][1] = 1;
		table[1][1] = 2;
		table[2][3] = 3;
		table[3][3] = 4;
		table[3][4] = 4;
		table[3][5] = 4;
		table[3][10] = 4;
		table[4][3] = 6;
		table[4][4] = 5;
		table[4][5] = 6;
		table[4][10] = 6;
		table[5][3] = 8;
		table[5][5] = 8;
		table[5][10] = 8;
		table[5][11] = 7;
		table[6][3] = 10;
		table[6][5] = 9;
		table[6][10] = 10;
		table[7][3] = 12;
		table[7][10] = 12;
		table[7][11] = 11;
		table[8][11] = 13;
		table[9][11] = 14;
		table[10][0] = 15;
		table[10][6] = 16;
		table[10][13] = 16;
		table[11][7] = 17;
		table[11][8] = 18;
		table[11][9] = 19;
		table[12][3] = 21;
		table[12][10] = 20;
		table[13][10] = 22;
		table[14][2] = 23;
		table[14][3] = 23;
		table[14][11] = 23;
		table[14][18] = 23;
		table[14][19] = 23;
		table[14][20] = 23;
		table[14][21] = 23;
		table[14][22] = 23;
		table[14][23] = 23;
		table[14][24] = 23;
		table[14][25] = 23;
		table[14][26] = 23;
		table[14][28] = 24;
		table[14][29] = 24;
		table[14][30] = 23;
		table[14][32] = 23;
		table[14][33] = 24;
		table[14][34] = 23;
		table[14][42] = 23;
		table[15][2] = 34;
		table[15][3] = 33;
		table[15][11] = 25;
		table[15][18] = 26;
		table[15][19] = 26;
		table[15][20] = 26;
		table[15][21] = 26;
		table[15][22] = 27;
		table[15][23] = 27;
		table[15][24] = 27;
		table[15][25] = 27;
		table[15][26] = 28;
		table[15][30] = 29;
		table[15][32] = 30;
		table[15][34] = 32;
		table[15][26] = 28;
		table[15][42] = 31;
		table[16][11] = 35;
		table[17][11] = 36;
		table[17][12] = 36;
		table[17][43] = 36;
		table[17][44] = 36;
		table[18][2] = 38;
		table[18][13] = 38;
		table[18][45] = 37;
		table[18][46] = 37;
		table[19][11] = 39;
		table[19][12] = 39;
		table[19][43] = 39;
		table[19][44] = 39;
		table[20][2] = 41;
		table[20][13] = 41;
		table[20][14] = 40;
		table[20][15] = 40;
		table[20][16] = 40;
		table[20][17] = 40;
		table[20][45] = 41;
		table[20][46] = 41;
		table[21][11] = 43;
		table[21][12] = 42;
		table[21][43] = 43;
		table[21][44] = 43;
		table[22][45] = 44;
		table[22][46] = 45;
		table[23][14] = 46;
		table[23][15] = 47;
		table[23][16] = 48;
		table[23][17] = 49;
		table[24][18] = 50;
		table[24][19] = 51;
		table[24][20] = 52;
		table[24][21] = 53;
		table[25][22] = 54;
		table[25][23] = 55;
		table[25][24] = 56;
		table[25][25] = 57;
		table[26][11] = 58;
		table[26][43] = 58;
		table[26][44] = 58;
		table[27][0] = 59;
		table[27][13] = 60;
		table[28][11] = 61;
		table[28][43] = 62;
		table[28][44] = 62;
		table[29][26] = 63;
		table[30][28] = 65;
		table[30][29] = 64;
		table[31][30] = 66;
		table[32][32] = 67;
		table[33][42] = 68;
		table[34][34] = 69;
		table[35][11] = 70;
		table[35][43] = 70;
		table[35][44] = 70;
		table[36][35] = 71;
		table[36][36] = 72;
		table[36][37] = 73;
		table[36][38] = 74;
		table[36][39] = 75;
		table[36][40] = 76;
		table[37][11] = 77;
		table[37][43] = 78;
		table[37][44] = 78;
		table[38][43] = 79;
		table[38][44] = 80;
		
		//fill the nonTerminals
		nonTerminals[0] = "module-decl";
		nonTerminals[1] = "module-heading";
		nonTerminals[2] = "block";
		nonTerminals[3] = "declarations";
		nonTerminals[4] = "const-decl";
		nonTerminals[5] = "const-list";
		nonTerminals[6] = "var-decl";
		nonTerminals[7] = "var-list";
		nonTerminals[8] = "var-item";
		nonTerminals[9] = "name-list";
		nonTerminals[10] = "more-names";
		nonTerminals[11] = "data-type";
		nonTerminals[12] = "procedure-decl";
		nonTerminals[13] = "procedure-heading";
		nonTerminals[14] = "stmt-list";
		nonTerminals[15] = "statement";
		nonTerminals[16] = "ass-stmt";
		nonTerminals[17] = "exp";
		nonTerminals[18] = "exp-prime";
		nonTerminals[19] = "term";
		nonTerminals[20] = "term-prime";
		nonTerminals[21] = "factor";
		nonTerminals[22] = "add-oper";
		nonTerminals[23] = "mul-oper";
		nonTerminals[24] = "read-stmt";
		nonTerminals[25] = "write-stmt";
		nonTerminals[26] = "write-list";
		nonTerminals[27] = "more-write-value";
		nonTerminals[28] = "write-item";
		nonTerminals[29] = "if-stmt";
		nonTerminals[30] = "else-part";
		nonTerminals[31] = "while-stmt";
		nonTerminals[32] = "loop-stmt";
		nonTerminals[33] = "exit-stmt";
		nonTerminals[34] = "call-stmt";
		nonTerminals[35] = "condition";
		nonTerminals[36] = "relational-oper";
		nonTerminals[37] = "name-value";
		nonTerminals[38] = "value";
		
		//fill the terminals
		terminals[0] = ",";
		terminals[1] = "module";
		terminals[2] = ";";
		terminals[3] = "begin";
		terminals[4] = "const";
		terminals[5] = "var";
		terminals[6] = ":";
		terminals[7] = "integer";
		terminals[8] = "real";
		terminals[9] = "char";
		terminals[10] = "procedure";
		terminals[11] = "name";
		terminals[12] = "(";
		terminals[13] = ")";
		terminals[14] = "*";
		terminals[15] = "/";
		terminals[16] = "mod";
		terminals[17] = "div";
		terminals[18] = "readint";
		terminals[19] = "readreal";
		terminals[20] = "readchar";
		terminals[21] = "readln";
		terminals[22] = "writeint";
		terminals[23] = "writereal";
		terminals[24] = "writechar";
		terminals[25] = "writeln";
		terminals[26] = "if";
		terminals[27] = "then";
		terminals[28] = "end";
		terminals[29] = "else";
		terminals[30] = "while";
		terminals[31] = "do";
		terminals[32] = "loop";
		terminals[33] = "until";
		terminals[34] = "call";
		terminals[35] = "=";
		terminals[36] = "!=";
		terminals[37] = "<";
		terminals[38] = "<=";
		terminals[39] = ">";
		terminals[40] = ">=";
		terminals[41] = ".";
		terminals[42] = "exit";
		terminals[43] = "integer-value";
		terminals[44] = "real-value";
		terminals[45] = "+";
		terminals[46] = "-";
		terminals[47] = ":=";
		
		//fill the productions
		productions[0] = ""; // index 0 is an empty string
		productions[1] = "module-heading declarations block name .";
		productions[2] = "module name ;";
		productions[3] = "begin stmt-list end";
		productions[4] = "const-decl var-decl procedure-decl";
		productions[5] = "const const-list";
		productions[6] = "Lmda";
		productions[7] = "name = value ; const-list";
		productions[8] = "Lmda";
		productions[9] = "var var-list";
		productions[10] = "Lmda";
		productions[11] = "var-item ; var-list";
		productions[12] = "Lmda";
		productions[13] = "name-list : data-type";
		productions[14] = "name more-names";
		productions[15] = ", name-list";
		productions[16] = "Lmda";
		productions[17] = "integer";
		productions[18] = "real";
		productions[19] = "char";
		productions[20] = "procedure-heading declarations block name ; procedure-decl";
		productions[21] = "Lmda";
		productions[22] = "procedure name ;";
		productions[23] = "statement ; stmt-list";
		productions[24] = "Lmda";
		productions[25] = "ass-stmt";
		productions[26] = "read-stmt";
		productions[27] = "write-stmt";
		productions[28] = "if-stmt";
		productions[29] = "while-stmt";
		productions[30] = "loop-stmt";
		productions[31] = "exit-stmt";
		productions[32] = "call-stmt";
		productions[33] = "block";
		productions[34] = "Lmda";
		productions[35] = "name := exp";
		productions[36] = "term exp-prime";
		productions[37] = "add-oper term exp-prime";
		productions[38] = "Lmda";
		productions[39] = "factor term-prime";
		productions[40] = "mul-oper factor term-prime";
		productions[41] = "Lmda";
		productions[42] = "( exp )";
		productions[43] = "name-value";
		productions[44] = "+";
		productions[45] = "-";
		productions[46] = "*";
		productions[47] = "/";
		productions[48] = "mod";
		productions[49] = "div";
		productions[50] = "readint ( name-list )";
		productions[51] = "readreal ( name-list )";
		productions[52] = "readchar ( name-list )";
		productions[53] = "readln";
		productions[54] = "writeint ( write-list )";
		productions[55] = "writereal ( write-list )";
		productions[56] = "writechar ( write-list )";
		productions[57] = "writeln";
		productions[58] = "write-item more-write-value";
		productions[59] = ", write-list";
		productions[60] = "Lmda";
		productions[61] = "name";
		productions[62] = "value";
		productions[63] = "if condition then stmt-list else-part end";
		productions[64] = "else stmt-list";
		productions[65] = "Lmda";
		productions[66] = "while condition do stmt-list end";
		productions[67] = "loop stmt-list until condition";
		productions[68] = "exit";
		productions[69] = "call name";
		productions[70] = "name-value relational-oper name-value";
		productions[71] = "=";
		productions[72] = "!=";
		productions[73] = "<";
		productions[74] = "<=";
		productions[75] = ">";
		productions[76] = ">=";
		productions[77] = "name";
		productions[78] = "value";
		productions[79] = "integer-value";
		productions[80] = "real-value";
		
		//fill the reservedWords
		reservedWords[0] = "module";
		reservedWords[1] = "begin";
		reservedWords[2] = "end";
		reservedWords[3] = "const";
		reservedWords[4] = "var";
		reservedWords[5] = "integer";
		reservedWords[6] = "real";
		reservedWords[7] = "char";
		reservedWords[8] = "procedure";
		reservedWords[9] = "mod";
		reservedWords[10] = "div";
		reservedWords[11] = "readint";
		reservedWords[12] = "readreal";
		reservedWords[13] = "readchar";
		reservedWords[14] = "readln";
		reservedWords[15] = "writeint";
		reservedWords[16] = "writereal";
		reservedWords[17] = "writechar";
		reservedWords[18] = "writeln";
		reservedWords[19] = "if";
		reservedWords[20] = "then";
		reservedWords[21] = "end";
		reservedWords[22] = "else";
		reservedWords[23] = "while";
		reservedWords[24] = "do";
		reservedWords[25] = "loop";
		reservedWords[26] = "until";
		reservedWords[27] = "exit";
		reservedWords[28] = "call";
		
		//fill the tokens
		tokens[0] = ";";
		tokens[1] = ":";
		tokens[2] = "=";
		tokens[3] = ":=";
		tokens[4] = ")";
		tokens[5] = "-";
		tokens[6] = "+";
		tokens[7] = "*";
		tokens[8] = "/";
		tokens[9] = "|=";
		tokens[10] = "<";
		tokens[11] = "<=";
		tokens[12] = ">";
		tokens[13] = ">=";

		launch(args);
	}
	
	//read the words and and tokens behind each others
	private static String readFile(File file) throws Exception {
		StringBuilder sb = new StringBuilder();
		try (BufferedReader br = new BufferedReader(new FileReader(file))) {
			String line;
			while ((line = br.readLine()) != null) {
				sb.append(line).append(" ");
				lines++;
			}
		}
		return sb.toString();
	}

	private static void parse(String s) throws Exception {
		
		//start from her for stack
		String first = "module-decl";
		String[] tok = first.split(" ");
		
		//tokens split the input
		String[] tokens = s.split(" ");
		Stack input = new Stack(tokens.length);
		
		//fill the stack of the input
		for (int i = tokens.length - 1; i >= 0; i--) {
			input.push(tokens[i]);
		}
		
		
		Stack stack = new Stack(100);
		//fill the stack
		for (int i = tok.length - 1; i >= 0; i--) {
			stack.push(tok[i]);
		}

		while (!input.isEmpty()) {
			// left is the peek of stack
			String left = stack.peek();
			// right is the peek of input
			String right = input.peek();

			if (left == null || right == null) {
				break;
			}
			//each roll print the content of stacks
			stackTextArea.appendText(stack.printStack() + "\n*********************\n");
			inputTextArea.appendText(input.printStack() + "\n*********************\n");
			
			//check if we need Pop & advance input
			if (left.equals(right)) {
				stack.pop();
				input.pop();
				productionTextArea.appendText("Pop & advance input\n*********************\n");

			} else {
				// Check if right is a terminal
				boolean isTerminal = false;
				for (String terminal : terminals) {
					if (right.equals(terminal)) {
						isTerminal = true;
						break;
					}
				}

				// If right is not a terminal, check and convert it using regex
				if (!isTerminal) {
					
					//check if the input is name
					String regex = "^[a-zA-Z][a-zA-Z0-9]*$";
					Pattern pattern = Pattern.compile(regex);
					Matcher matcher = pattern.matcher(right);

					if (matcher.matches()) {
						//if the input match the instead it with name
						productionTextArea.appendText("Set name instead " + right + "\n*********************\n");
						input.pop();
						input.push("name");
						continue;
					} else {
						//check if the input is integer-value
						String regex2 = "^[0-9]+$";
						Pattern pattern2 = Pattern.compile(regex2);
						Matcher matcher2 = pattern2.matcher(right);

						if (matcher2.matches()) {
							//if the input match the instead it with integer-value
							productionTextArea.appendText("Set integer-value" + right + "\n*********************\n");
							input.pop();
							input.push("integer-value");

							continue;
						} else {
							//check if the input is real-value
							String regex3 = "^[0-9]+\\.[0-9]+$";
							Pattern pattern3 = Pattern.compile(regex3);
							Matcher matcher3 = pattern3.matcher(right);

							if (matcher3.matches()) {
								//if the input match the instead it with real-value
								productionTextArea.appendText("Set real-value instead" + right + "\n*********************\n");
								input.pop();
								input.push("real-value");

								continue;
							} else {
								System.out.println("Token does not match any pattern: " + right);
								break;
							}
						}
					}
				}

				int firstIndex = -1;
				int secIndex = -1;
				
				//find the index of left using nonTerminals array
				for (int j = 0; j < nonTerminals.length; j++) {
					if (nonTerminals[j].equals(left)) {
						firstIndex = j;
						break;
					}
				}
				
				//find the index of right using terminals array
				for (int j = 0; j < terminals.length; j++) {
					if (terminals[j].equals(right)) {
						secIndex = j;
						break;
					}
				}

				if (firstIndex == -1 || secIndex == -1) {
					//error
					smallTextArea.appendText("No matching rule for " + left + " and " + right + "\n");
					error = right;

					String[] lines = theCode.split("\n");

					outerLoop: for (int i = 0; i < lines.length; i++) {
						String line = lines[i];
						StringTokenizer tokenizer = new StringTokenizer(line, " \t\n\r\f");

						while (tokenizer.hasMoreTokens()) {
							String token = tokenizer.nextToken();
							if (token.equals(error)) {
								smallTextArea.appendText("The error is : " + error + " in line : " + (i + 1) + "\n");
								break outerLoop;
							}
							System.out.println("Token: " + token + ", Line: " + (i + 1));
						}
					}
					break;
				}
				//get production number using firstIndex and secIndex
				int productionNumber = table[firstIndex][secIndex];
				
				if (productionNumber == 0) {
					smallTextArea.appendText("No production found for " + left + " and " + right + "\n");
					break;
				}
				
				//get the production from productions using productionNumber
				String productionString = productions[productionNumber];
				productionTextArea.appendText(productionNumber + "\n*********************\n");
				
				//split the production to push them to stack
				String[] newProductionTokens = productionString.split(" ");
				
				stack.pop();
				for (int j = newProductionTokens.length - 1; j >= 0; j--) {
					if (!newProductionTokens[j].equals("Lmda")) {
				 		stack.push(newProductionTokens[j]);
					}
				}
			}
		}

		if (input.isEmpty()) {
			smallTextArea.appendText("Parsing successful.\n");
		} else {
			smallTextArea.appendText("Parsing failed.\n");
		}
	}

	public static String scan(String code) throws Exception {
		// Perform initial replacements
		code = code.replaceAll("\\s+", " ");
		code = code.replaceAll(";", " ;");
		code = code.replaceAll("\\(", " ( ");
		code = code.replaceAll("\\)", " ) ");
		code = code.replaceAll(":", " :");
		code = code.replaceAll("\\.(?!\\d)", " . ");
		code = code.replaceAll("\\bprocedure\\b", "procedure ");
		code = code.replaceAll("\\bbegin\\b", "begin ");
		code = code.replaceAll("\\bend\\b", "end ");
		code = code.replaceAll("\\bvar\\b", "var ");
		code = code.replaceAll("\\bcall\\b", "call ");

		String[] words = code.split("\\s+");
		StringBuilder correctedCode = new StringBuilder();
		for (String word : words) {
			correctedCode.append(word).append(" ");
		}

		return correctedCode.toString().trim();
	}

	public static String scanWithNewLines(String code) throws Exception {
		// Perform initial replacements
		code = code.replaceAll("\\s+", " ");
		code = code.replaceAll(";", " ;");
		code = code.replaceAll("\\(", " ( ");
		code = code.replaceAll("\\)", " ) ");
		code = code.replaceAll(":", " :");
		code = code.replaceAll("\\.(?!\\d)", " . ");
		code = code.replaceAll("\\bprocedure\\b", "procedure ");
		code = code.replaceAll("\\bbegin\\b", "begin ");
		code = code.replaceAll("\\bend\\b", "end ");
		code = code.replaceAll("\\bvar\\b", "var ");
		code = code.replaceAll("\\bcall\\b", "call ");

		String[] words = code.split("\\s+");
		StringBuilder correctedCode = new StringBuilder();
		for (String word : words) {
			if (word.equals(";")) {
				correctedCode.append(word).append("\n");
			} else {
				correctedCode.append(word).append(" ");
			}
		}

		return correctedCode.toString().trim();
	}

}
