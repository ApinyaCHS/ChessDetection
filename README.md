# Chess tracking systems
This project is part of the course Digital Image Processing (2110651), Chulalongkorn University, Thailand, 2024.
## Overview
- The aim of this project is to create AI model applications capable of generating chess data in PGN format.


## Origin of the Problem
Currently, there are electronic chess boards that can connect to computers or mobile phones, offering the following capabilities:

- Uploading games in PGN (Portable Game Notation) format for reviewing past games, receiving analysis to improve gameplay, or recording scores for ranking purposes.
- Playing online with other players using a physical board.
- If chess gameplay on physical boards can be connected and stored in a database, it could encourage Thai students to take a greater interest in playing chess.
- However, due to the high cost of electronic chess boards, many school chess clubs cannot afford to purchase them. This has led to the idea of using a mobile phone to record video footage of the game and then convert it into PGN data, allowing for the recording of game history and providing recommendations to improve future gameplay.


## Challenges
- The design of chessboards and pieces may vary.
- Camera quality and angles may differ.
- Errors during the game, such as collisions, pieces falling, or other corrections, may occur.
- Illegal moves: Players may make moves that violate chess rules, which can confuse automated tracking and analysis systems.
- Hand obstructions: Hands or other objects may block the camera’s view of the board, making it difficult to accurately detect piece positions and movements.
- PGN Formatt

## PGN Formatt


## Method
1. We divided the video into individual frames.
2. We selected frames without hand obstructions to identify chess pieces—such as black pawns and white pawns—using YOLOv11 to detect both hands and pieces.
3. We applied **Perspective Correction** and **Grid Point Detection** techniques to map YOLO predictions onto the correct positions on the chessboard.
4. We then compared each time step with the previous one to track piece movements.
5. Finally, we developed a logic-based system to record the chess moves accurately.
   
<img src="https://github.com/ApinyaCHS/ChessDetection/raw/main/overview-system.png" width="535" alt="Overview of this system" />

## Hand Removal
<img src="https://github.com/ApinyaCHS/ChessDetection/raw/main/Hand-remove.png" width="535" alt="Hand-removal" />

## Chess-piece-detection.png
<img src="https://github.com/ApinyaCHS/ChessDetection/raw/main/Chess-piece-detection.png" width="535" alt="Chess-piece-detection.png" />
   

## 📸 Correcting Perspective Distortion and Detecting Chessboard Grids from Images
When using chessboard images in automated systems—such as move tracking or digital game recording—it's essential to transform the view to a top-down perspective and accurately detect grid intersection points on the board.

<img src="https://github.com/ApinyaCHS/ChessDetection/raw/main/WARPs Images & Draws Grids.png" width="535" alt="WARPs Images & Draws Grids.png" />



### ✅ Perspective Correction
Since the camera is often positioned at an angle rather than directly above the board, the image usually appears skewed. To address this, a **perspective transformation** is performed to simulate a top-down view. The steps are:
1. **Detect the outer board contour**
   Convert the image to grayscale and apply thresholding and contour detection to find the largest quadrilateral, likely representing the board boundary.
2. **Order the corner points**
   Identify the four corner points and arrange them in a consistent order: top-left, top-right, bottom-right, bottom-left.
3. **Compute and apply the transform**
   Use `cv2.getPerspectiveTransform()` and `cv2.warpPerspective()` to generate a warped image with a flat, square view (e.g., 640×640 pixels).

   
### 🎯 Grid Point Detection
After obtaining a straightened (warped) image of the chessboard, the next task is to detect the grid intersections—typically 8×8 = 64 squares:
1. **Detect inner square contours**
   Identify contours that form small quadrilaterals within the board area.
2. **Find the center of each square**
   For each detected square, calculate its centroid by averaging the four corner points.
3. **Transform the points to the corrected image space**
   Use `cv2.perspectiveTransform()` to convert point coordinates to match the warped (top-down) image.
4. **Sort and filter points to build the grid**
   Sort x/y coordinates, filter out noise or duplicate values, and use `np.meshgrid()` to construct a clean 2D grid of points.


## Pipline (Full Systems)
- This file -->  [Pipeline](https://chess-full-pipeline.ipynb).
- This pipeline integrates methods for tracking chess piece movements, identifying each piece, and filtering out obstructions—such as hands—that interfere with recording. Ultimately, it forms an algorithm that organizes and records the data in standard chess notation format.


## Output
- Predict chess moves in PGN format and save the results to a CSV file.
  
| video_id   | output |
| -------------       |:-------------:|
| 2_move.mp4  |	1... Qh4 2. g3 |
| 4_Move.mp4	    | 1... f4 2. Ng6 cxg3 3. Nxh8  |
| 6_Move.mp4	  | 1... Bxb5 2. Rxb5 b6 3. c4 Ne7 4. Ne7 Rb2  |
| 8_Move.mp4	  | 1. Qe6 Kd8 2. Qf7 c6 3. Qf2 cxd5 4. Qxa7 Rc8 5. Rxb4 Nc6 6. Qb6    
| ...             | ...     |

## other information
```
0: white-queen
  1: white-pawn
  2: black-rook
  3: black-bishop
  4: black-knight
  5: black-queen
  6: black-pawn
  7: black-king
  8: white-rook
  9: white-bishop
  10: white-knight
  11: white-king
```
