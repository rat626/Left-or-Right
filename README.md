# Left-or-Right
This project involves using KNN and Logistic Regression models to classify epochs of data from a motor imagery dataset to classify whether an intended movement was in the left or right direction.

I used an open source dataset made by the Wadsworth Center BCI R & D program(https://openneuro.org/datasets/ds004362/versions/1.0.0), which contained several trials of participants opening and closing their left and right fists according to an indication on a screen, as well as trials of them imagining opening their left or right fist in the direction corresponding to the on - screen cue. 

The dataset had the following structure with specific codes that i reference throughout the project:
[Task 1] A target appears on either the left or the right side of the screen. The subject opens and closes the corresponding fist until the target disappears. Then the subject relaxes.
[Task 2] A target appears on either the left or the right side of the screen. The subject imagines opening and closing the corresponding fist until the target disappears. Then the subject relaxes.

[T0] corresponds to rest

[T1] corresponds to onset of motion (real or imagined) of:

the left fist (in runs 3, 4, 7, 8, 11, and 12; for Task 1 (real) and Task 2 (imagined))


[T2] corresponds to onset of motion (real or imagined) of:

the right fist (in runs 3, 4, 7, 8, 11, and 12; Task 1 (real) and Task 2 (imagined))


Based on these codes, I isolated the epochs, or time segments, which contained Task1T1, Task1T2 for the training phase, and Task2T1 and Task2T2 for the testing phase, setting each epoch to be 200 miliseconds before the movement(actual or imagined), and 2000 miliseconds after the movement, to ensure that each epoch contained the time when the movement itself occured, upon which the corresponding EEG data for that point in time in C3 and C4 could then be analyzed.


For the training data, I used run 4 in Task 1 T1 and Task 1 T2 to capture the actual left and right fist movement. When testing the KNN classifier on the imagined movement, I then used run 4 in Task 2 T1 and Task 2 T2, corresponding to the imagined left and right fist movement, for participant 15(later added 14, 16, 17, and 18)

My goal was to start by training a KNN classifier on the EEG data from participants actually opening/closing their fists, then testing the classifier on the data from the imagined left/right movement to see how accurately it could classify whether a participant intended to open their left or right fist. 

To classify whether a movement was intended in the left or right direction, I focused on electrodes C3 and C4, which are common targets for motor imagery, and specifically looked at the mu band frequency(8-12 Hz), as a high mu band power corresponds to idling of movement, and a low mu band power correlates to increased movement. Therefore, if a reduction in mu power, or mu suppression, is found in C3 and C4 - this can be telling of which hand was being(or imagined being) moved. Since C3 is located above the motor area of the left hemisphere, it controls the right hand, and C4 controls the left. This was the main premise of the first part of the project, after which I added new electrodes of interest(Po8, T8, Fc5, and F7). I found these new electrodes by subtracting the right 

I have split up the code used in the project according to different sections
  - **Part 1**: Uploading the data for participant 15, defining epochs of interest, filtering to mu frequency(8-12 Jz), and isolating electrodes C3 and C4[plots also included in files]

<img width="1097" height="543" alt="Screenshot 2025-07-24 at 1 26 55 PM" src="https://github.com/user-attachments/assets/08e5455e-9431-4adc-b176-e888804c1176" />
Plots of the mu band power in electrodes C3 and C4 during the epochs corresponding to actual left fist movement(Task1T1) and actual right fist movement(Task1T2) with accompanying bar graph







  - **Part 2a**: Converting the epoch data into X and Y arrays - X contains average band power for left hand and right hand epochs[actual] - left and right are then stacked - after which the Y array is used to label the rows of the array with a 0(if left fist opened), or 1(if right fist opened)


<img width="687" height="498" alt="Screenshot 2025-07-24 at 1 30 06 PM" src="https://github.com/user-attachments/assets/a91a5e10-be7f-4d0f-bbd4-af4b81ee80d9" />


Diagram of how the X and Y arrays would be processed together - X contains mu band power from C3 and C4 during actual left and right fist movement epochs, and Y labels each row with 0 or 1 depending on the direction


  - **Part 2b**: Split the data into training and testing, trained KNN classifier on it, and obtained results(accuracy, precision, confusion matrix, recall, f1 score)
    
    <img width="725" height="277" alt="Screenshot 2025-07-24 at 1 35 23 PM" src="https://github.com/user-attachments/assets/1a9397d7-2953-4ffb-b35b-b2ad4cbddba4" />
    
    Line graph showing how the number of neighbors needed to classify a point as right/left affects accuracy, alongside a diagram of how KNN classification works

    
    <img width="697" height="419" alt="Screenshot 2025-07-24 at 1 43 05 PM" src="https://github.com/user-attachments/assets/53d20fc9-ffa6-47fd-bdf0-c1ebd915abf4" />





     Results of KNN classifier on **training data**
    - (~0.58 accuracy, 11 total correct predictions(7 act left pred left, 4 act right pred right)




    
    <img width="656" height="404" alt="Screenshot 2025-07-24 at 1 44 34 PM" src="https://github.com/user-attachments/assets/a401e543-fede-4fb1-914f-cc2601b51d56" />


     Results of KNN classifier on **testing data(imagined left/right movement) - benchmark**
    - (~0.53 accuracy, 40 total correct predictions(7 act left pred left, 33 act right pred right)







  - **Part 3**: Created topomaps displaying the difference between right - left actual movement, and right - left imagined movement, and calculated the electrodes with the 2 highest mu power difference(indicates the electrodes with the highest right mu power), and the electrodes with the 2 lowest mu power difference(indicates electrodes with highest left mu power)

    
      <img width="802" height="511" alt="Screenshot 2025-07-24 at 1 46 11 PM" src="https://github.com/user-attachments/assets/aa778ec5-e727-4834-a145-916bf58cb8e7" />


      Difference topomaps of actual movement(right - left) and imagined movement(right - left), with overlaps in high and low mu power differences shown on the EEG map
        - High difference electrodes of interest(P8, **T8**, CP4, CP6, Po4, **Po8**)
        - Low difference electrodes of interest(**F7**, F5, FT7, **FC5**)

  
    <img width="1402" height="51" alt="Screenshot 2025-07-24 at 1 49 41 PM" src="https://github.com/user-attachments/assets/eb90de87-9cf8-4155-8ed2-8d75df1bce78" />
    
      Print output of the electrodes that had the highest right-left difference, and lowest right - left difference for the actual movement data






  - **Part 4**: Created condensed function to process each subject's data, compiled the data for participants 14, 15, 16, 17, and 18 into the X and Y arrays, and reran the KNN classifier
      <img width="771" height="471" alt="Screenshot 2025-07-24 at 1 50 42 PM" src="https://github.com/user-attachments/assets/9e26bfb5-f950-45a3-9d89-40d7f7f12ba1" />
      Comparison of old and new classifier(previous benchmark compared to current classifier output with added participants and new electrodes)

The end results of the KNN classifier with all the included electrodes and subjects 14, 15, 16, 17, and 18(see final_results image) demonstrated 0.4% improvement in accuracy, a higher number of correct predictions for imagined left hand movement, and higher precision and recall compared to the original KNN training results, which were only trained on electrodes C3 and C4 using the data from only participant 15. 

Here is a link to the full project log/description for more details on how I went about the project and made changes along the way to improve accuracy: 
https://medium.com/@tarun26/left-or-right-c20aa64c5b23




