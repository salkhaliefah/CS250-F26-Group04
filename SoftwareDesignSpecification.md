# Mountain Lion Detection System

## Software Design Specification

### Team Members:

- Stephen
- Alex
- Uriel
- Blade
- Safa

## System Description

The Mountain Lion Detection System will help the San Diego County Parks and Recreation Department detect and monitor possible mountain lion activity in parks. The system will use noise sensors across areas of up to 5 square miles to detect programmed animal sounds and send alerts to a ranger station computer. Alerts will include the sound type, strength, and location, allowing authorized park rangers and employees to review and classify them as definite, suspected, or false. The system will also store detection data, provide maps and reports, and use a simple interface that requires minimal training. Access will be limited to authorized users, and the system will be designed to support different parks, sensor locations, and animal noises.

## Software Architecture Overview

Architectural Diagram
![Alt text](https://caring-vegan.s3.us-west-2.amazonaws.com/AWS.png)

The workflow starts when a location with sensors in San Diego County Parks and Recreation detects something and sends an alert. A ranger then checks the alert. If the alert is false, it is marked as an invalid report. If it is true, the system stores a detailed record.

After a valid alert is stored, the system checks its age. If the alert is less than 30 days old, it remains as a detailed alert. If it reaches 30 days old, it is converted into a summary record. The system later checks whether that summary is one year old. If not, the summary is kept; if it is one year old, it is archived.

At the same time, a valid alert can sound an alarm at the ranger station. The ranger reviews the alert and can shut off the alarm. The system then checks whether the alert has the same sound and same location as another alert. If it does, the alarm can be turned off; if not, the alert continues through the review process.

The ranger then classifies the alert as True, Maybe, or False. The classification is saved together with the Ranger ID. These saved alerts become available for reports.
The reporting section allows the system to organize the stored information in several ways, including detection by date and classification, detection by sensor location, a map of the park and surrounding two-mile area, and classification by ranger.

In other words: Sensor detects event → alert is sent → ranger checks it → valid alerts are stored → alarm is reviewed → ranger classifies the alert → alert is saved → reports are generated → old records are summarized and eventually archived.

UML
![Alt text](https://caring-vegan.s3.us-west-2.amazonaws.com/UML-PROJECT1.png)

UML class description

Park Management system: This will be the start of the whole system. It will contain the National parks information such as name, location, and area covered. This system is designed so that it can be set up to different national parts around the country allowing versatility.
Attributes

1. parkName: string - This will be the name of the national park and can be changed to suit whatever location
   2.parkLocation: double - This will be the location of the park/park number that was assigned to it
   3.areaCovered: double - This will include the square feet that the Mountain Lion Detection System will cover

Functions

1. getParkName() - This function gets the park name
   2.getParkLocation() - this function gets the park location
   3.getAreaCovered() - this function gets the area covered by the MLDS

Ranger Station: This is where the Park Management System class would then send you to after getting the correct information. This will connect with the controlling computer that is at the main ranger station for the National park that it is at. Here the ranger will be able to login then use this to access all the system setup for their designated park.
Attributes

1. rangerID: double - This will contain the Id of the ranger currently accessing the system
2. rangerUserName: string - This will contain their username used to log in while active
3. rangerPassword: string - This will contain their password used to login while active
4. rangerStationNum: int - This will contain the assigned number to the ranger station in case of multiple stations
   Functions
5. Login() - This function will allow the ranger to enter their information and verify that they are authorized.
6. accessData()- This function is what will be used in order to access the AlertDatabase.
7. accessMap()- This function will allow the ranger to access a map of the national park with live real time alerts.
8. toggleAlarm() - This function is what will be used to toggle the alarm off. As specified, the alarm will stay active until a ranger toggles it off themself.
9. classifyAlert() - This function will be what the ranger will use in order to classify the alert as either true, false, or uncertain.
10. getActiveAlerts() - this function will allow the ranger to look at all current alert reports so that they can handle it accordingly.

Alarm: This class will only be used for the Alarm itself. It will be connected to both the Ranger Station and Noise Detection Nodes. The Ranger Station class will be able to toggle off the alarm as specified and the alarm will be triggered once the conditions from Noise Detection Nodes have been met.
Attributes

1. alarmOn: bool - This will contain a boolean function which will be true if the alarm is still active and has not been turned off. False if there is no current alarm active or if it was turned off
   Functions
1. setStationAlarmOn() - This function is what will be triggered by the Noise Detection Nodes once the right conditions have been met. In this case, when a mountain lion sound has been detected.
1. setStationAlarmOff() - This function is what will be used when the function toggleAlarm() in Ranger Station is triggered to be turned off.

Classification/Report: This class will be used in order to classify each alert. As per specifications each alert can be classified as True, False, or maybe. Report will then be sent back to the ranger station where it will be shuffled into the Alert Database.
Attributes

1.  alertClassification: string - This will contain a string which will have true, false, or maybe.

Functions

1.  setAlertTrue()- This function will set the alert classification as True.
2.  setAlertFalse()- This function will set the alert classification as False
3.  setAlertMaybe()- This function will set the alert classification as Maybe
4.  sendAlertStatus()- This function will send the alert status confirmed by ranger back to Ranger station which will then send it to AlertDatabase.

AlertDatabase: This will be the database that will hold all the alerts, their dates, their locations. It will also be used to compile the alert information after 30 days but before a year. This class will be connected with Ranger Station so that they can access past alerts and also send the new classified alerts. It will also connect with Noise Detection Nodes so that the nodes themselves can directly send the information to the data base which can then be used by Ranger station to classify or get old reports.
Attributes

1. alertSummeryYear: string- This will contain the past alerts for after 30 days but before the 1 year mark.
2. activateAlarm: bool- This will also keep track of if there is an active alert so that the ranger can classify and access the most recent alerts as opposed to searching through the whole list.
3. sentAlerts: int- This will keep track of the amount of current alerts so that the ranger using the system can find out if it is just one alert or multiple at the same time.
4. alarmStatus:bool- Like activeAlarm:bool it is used to keep track of if there is an active alert but this is just true or false as opposed to the int of the previous one which keeps track of the number of active alerts.
5. probabilityofMountainLion: string 4- this will be where the classification report is stored. It will contain whatever the ranger has classified said alert as.

Functions

1. compileOldAlerts()- This function will compile the old reports from between 30 days old and one year old, as per specifications.
2. checkIfML()- This function will be used to check the string of probabilityOfMountainLion to check if there actually was a mountain lion or not.
3. getInfoDataPost30()- This function will be used to access the summary that compileOldAlerts has made.

Noise Detection Nodes: This class will be in charge of each node. They have all the required information needed to set up a net of these around the designated area of the park, find their location, and also the functions needed to filter the possible noises picked up from the nodes in order to make it less likely a false alarm is sounded. This class will be connected with AlertDatabase, so that it may update and send information to the database. Alarm, so that it can activate the alarm when needed. It will also be connected to different Animal classes which will contain the sound files which will be used to filter out every sound from activating the alarm. It's set up this way so that it is easy to add more animals to the detection system.
Attributes

1. location: string- This will contain a string which will have the location of the node. (ex. Near main trail, near waterfall, etc.)
2. Sensor#: double- This will contain the number assigned to the node itself in order to give a more specified location/information from where the sound was recorded.
3. AlertStatus: bool- This contains if a mountain lion or possibility of a mountain lion has been detected and activates the alert status which will then be sent to the Alarm class.
4. mountainLionSample: mp4- this will contain a sample of the mountain lion which will be used to compare the recorded sound
5. noiseMountainLion: bool- this will be true if the sensor picked up sound that couple possibly be a mountain lion and then trigger the alarm class which it is connected to.
6. noisePitch: double- This will contain the value of the noise pitch for further information and filtering.

Functions

1. compareNoise()- this function is what will be used in order to filter out normal noise/other animal noises from that of a mountain lion so that the alarm system is more precise.
2. recordNoise()- this function is used to record the actual noises the Noise Detection Node picks up which will then use the compare noise to determine if an alarm needs to be triggered.
3. getPitch()- This will collect the pitch of the noise recorded to provide better information.
4. getDecibles()- This function will collect the decibels for the recorded noise in order to provide better information.
5. sendAlert()- This function will send the alert to both the alertDatabase, where it will be recorded and Alarm class, where it will trigger the alarm.

NonAnimal: This class will contain normal or the most common sounds in the forest/national parks such as the wind blowing or rocks falling, people talking or anything else the park decides. This is used so that false alarms are less likely. This will be connected to only the noise detection nodes as noise detection nodes is the class that compares and decides if the alarm needs to be sounded.
Attributes

1. natureSounds: mp4- this is just an example of a mp4 file of common nature sounds so that an alert isn’t triggered for every sound.

Functions

1. isNatureSound()- this function will check if the sound recorded by the node is close enough to the natureSounds mp4 and if so then will let the Node know not to sound the alarm.

OtherAnimals: This class can be used and multiplied depending on what other animals the park and rangers want to trigger the system. Currently the system only triggers for mountain lion sounds but this allows easy growth and implementation of other animals.
Attributes

1. otherAnimalSounds: file- example file for other animals will be contained in here.

Functions

1. isOtherAnimal()- This function will check if the sound recorded is the “otheranimal” that it is set to detect. Will send an alert if yes.
2. isntOtherAnimal()- This function will let the node know that it is not cause for alarm.

MountainLion: This is where the information for the mountain lion class will be stored. This will be the trigger point for the whole system. With the sample of the mountain lion sounds being contained in this file this is what the nodes will compare the recorded sounds to then decide if the alarm needs to be triggered or not based on how similar they are.
Attributes

1.  MountainLionMP4: MP4- This file will contain a sample of the mountain lion noises which will be used to compare the recorded noises.

Functions

1. isMountainLion()- This Function will let the node know if the sound is indeed a mountain lion or likely a mountain lion.
2. isntMountainLion()- This function will let the node know that what it recorded was not a mountain lion.

## Testing

### Sensor Testing - (Equipment)

- Alex

### Noise Related Function Testing - (Correctly Detect Mountain lion) – Safa

**Feature:** Correctly identifying mountain lion sounds and distinguishing them from other animal noises.

#### 1. Unit Testing

**What we're testing:** Whether the `isMountainLion()` and `isntMountainLion()` functions correctly identify mountain lion sounds and other animal noises.

**Test Case 1: Mountain Lion Sound**
- **Test Input:** A recorded mountain lion sound.
- **Expected Output:** The function identifies the sound as a mountain lion.
- **Pass:** The function correctly identifies the mountain lion sound.
- **Fail:** The function incorrectly identifies the sound.

**Test Case 2: Other Animal Sound**
- **Test Input:** A recorded dog barking.
- **Expected Output:** The function identifies that the sound is not from a mountain lion.
- **Pass:** The function correctly rejects the dog barking sound.
- **Fail:** The function incorrectly identifies the dog barking as a mountain lion.

#### 2. Integration Testing

**What we're testing:** Whether the sound sensor and noise detection functions work together correctly.

**Test Case 1: Mountain Lion Detection**
- **Test Input:** The sensor receives a recorded mountain lion sound and sends the data to the detection function.
- **Expected Output:** The function receives the sound data and correctly identifies it as a mountain lion.
- **Pass:** The sensor and function communicate correctly and identify the sound.
- **Fail:** The data is not transferred correctly or the function fails to identify the sound.

**Test Case 2: Other Animal Detection**
- **Test Input:** The sensor receives a recorded dog barking.
- **Expected Output:** The detection function identifies that the sound does not belong to a mountain lion.
- **Pass:** The sensor and function correctly process the sound without identifying a mountain lion.
- **Fail:** The system incorrectly identifies the dog barking as a mountain lion.

#### 3. System Testing

**What we're testing:** Whether the entire system correctly detects mountain lion sounds and responds appropriately.

**Test Case 1: Mountain Lion Present**
- **Test Input:** A recorded mountain lion sound is played near the sensor.
- **Expected Output:** The system detects the sound, identifies it as a mountain lion, and activates the appropriate response.
- **Pass:** The system correctly identifies the sound and activates the expected response.
- **Fail:** The system fails to detect the sound or does not activate the appropriate response.

**Test Case 2: No Mountain Lion Present**
- **Test Input:** A recorded dog barking is played near the sensor.
- **Expected Output:** The system identifies that the sound is not from a mountain lion and does not activate a mountain lion alert.
- **Pass:** The system correctly rejects the sound without triggering a false alert.
- **Fail:** The system incorrectly identifies the sound as a mountain lion and triggers an alert.

#### 4. Test Coverage

These tests verify that the system correctly identifies mountain lion sounds at three different testing levels.

- **Unit Testing:** Checks whether the individual detection functions correctly classify recorded sounds.
- **Integration Testing:** Checks whether the sensor and detection functions communicate and work together correctly.
- **System Testing:** Checks whether the entire system correctly detects mountain lion sounds and activates the expected response.

We selected both mountain lion recordings and dog barking recordings to test whether the system can distinguish between different animal sounds. This helps identify two possible failures: failing to recognize a mountain lion and incorrectly identifying another animal as a mountain lion.

By testing these scenarios at all three levels, we can verify the accuracy of the noise detection feature and the communication between different parts of the system.



**Make a report(create)
- Blade

### Database Search (Read)

- We need to verify getInfoDataPost30() works as intended.
We will use these tests to verify that the AlertDatabase can retrieve summarized alerts and that rangers can access this information through the Ranger Station.

Unit Test

TEST getInfoDataPost30


    AlertDatabase.alertSummeryYear = "True - Near main trail"


    results = AlertDatabase.getInfoDataPost30()


    IF results IS EMPTY THEN
        PRINT "FAIL: No summarized alerts were retrieved"
        RETURN
    END IF


    IF results DOES NOT CONTAIN "True - Near main trail" THEN
        PRINT "FAIL: Expected summary information is missing"
        RETURN
    END IF


    PRINT "PASS: Summarized alert was retrieved correctly"


END TEST


Integration Test

TEST rangerStationAccessData


    AlertDatabase.alertSummeryYear = "True - Near main trail"


    RangerStation.accessData()


    results = AlertDatabase.getInfoDataPost30()


    IF results IS EMPTY THEN
        PRINT "FAIL: Ranger Station could not access alert summaries"
        RETURN
    END IF


    IF results DOES NOT CONTAIN "True - Near main trail" THEN
        PRINT "FAIL: Expected summary was not retrieved"
        RETURN
    END IF


    PRINT "PASS: Ranger Station accessed the alert summary"


END TEST


System Test

TEST rangerAccessStoredAlerts


    AlertDatabase.alertSummeryYear = "True - Near main trail"


    RangerStation.Login()


    IF ranger login fails THEN
        PRINT "FAIL: Ranger could not log in"
        RETURN
    END IF


    RangerStation.accessData()


    results = AlertDatabase.getInfoDataPost30()


    IF results IS EMPTY THEN
        PRINT "FAIL: Ranger could not view stored alert summaries"
        RETURN
    END IF


    IF results DOES NOT CONTAIN "True - Near main trail" THEN
        PRINT "FAIL: Expected summary was not displayed"
        RETURN
    END IF


    PRINT "PASS: Ranger logged in and accessed the alert summary"


END TEST

### Automated Updates (Update, Delete)

- We need to verify compileOldAlerts() worked as intended.
- We will use these tests to verify the workflow is updating alerts automaticly.

- Unit Test

```bash
  TEST createSummary

      alert = {
          id: 101,
          type: "Alert",
          classification: "True",
          location: "Main Trail"
      }

      summary = createSummary(alert)

      IF summary IS EMPTY THEN
          PRINT "FAIL: No summary was created"

      ELSE IF summary.type != "Summary" THEN
          PRINT "FAIL: Incorrect record type"

      ELSE IF summary.sourceAlertId != alert.id THEN
          PRINT "FAIL: Summary references the wrong alert"

      ELSE IF summary.classification != alert.classification
           OR summary.location != alert.location THEN

          PRINT "FAIL: Summary information is incorrect"

      ELSE
          PRINT "PASS: Summary was created correctly"
      END IF

END TEST
```

- Integration test

```bash
  TEST updateRecords(currentDate)

      summaryCutoff = currentDate - 30 DAYS
      archiveCutoff = currentDate - 1 CALENDAR YEAR

      FOR EACH record IN getActiveAlerts()

          IF record.originalReceivedDate < archiveCutoff THEN

              IF record.type == "Alert" THEN
                  archiveData = createSummary(record)
              ELSE
                  archiveData = copy(record)
              END IF

              archiveData.originalReceivedDate = record.originalReceivedDate

              IF saveToArchive(archiveData) == SUCCESS THEN
                  removeFromActiveStorage(record)
              ELSE
                  logError("Archive update failed", record.id)
              END IF

          ELSE IF record.type == "Alert"
              AND record.originalReceivedDate < summaryCutoff THEN

              summary = createSummary(record)
              summary.originalReceivedDate = record.originalReceivedDate

              IF saveSummary(summary) == SUCCESS THEN
                  removeOriginalAlert(record)
              ELSE
                  logError("Summary update failed", record.id)
              END IF

          END IF

      END FOR

END FUNCTION
```

- Systems Test

```bash
TEST automaticAlertLifecycle

      startDate = October 15, 2025

      START the complete application with empty test storage
      SET application test clock TO startDate

      SEND a simulated mountain lion alert through the sensor input

      USING the ranger interface:
          Find the alert
          Classify it as "True"
          Save the classification

      RECORD alertId, originalReceivedDate, classification, location

      IF the detailed alert is missing from the ranger interface THEN
          PRINT "FAIL: Initial alert was not displayed"
          RETURN
      END IF

      ADVANCE application test clock TO startDate + 31 DAYS
      WAIT FOR the next scheduled daily update to finish

      IF no summary exists for alertId
         OR the original detailed alert remains in active storage THEN

          PRINT "FAIL: Alert was not converted to a summary"
          RETURN
      END IF

      IF summary classification, location, or originalReceivedDate
         differs from the recorded information THEN

          PRINT "FAIL: Information changed during summarization"
          RETURN
      END IF

      ADVANCE application test clock TO
              startDate + 1 CALENDAR YEAR + 1 DAY
      WAIT FOR the next scheduled daily update to finish

      IF no archived record exists for alertId
         OR its summary remains in active summary storage THEN

          PRINT "FAIL: Summary was not archived correctly"
          RETURN
      END IF

      IF archived classification, location, or originalReceivedDate
         differs from the recorded information THEN

          PRINT "FAIL: Information changed during archiving"
          RETURN
      END IF

      PRINT "PASS: Automatic update workflow completed correctly"

END TEST
```

## Data Management Strategy

For this system, I would use one centralized SQL database to store information about the park’s sensors, alerts, ranger reviews, reports, and archived records. The database could include tables for parks, sensors, alerts, rangers, classifications, detailed alert records, summary records, and reports. Each alert would be connected to the sensor that detected it and the ranger who reviewed it. The database would also record whether the alert was valid, what classification it received, and whether it was kept, summarized, or archived. This would allow users to search reports by date, alert classification, sensor location, map area, or ranger.

I chose to use one database because this system does not seem large enough to require multiple separate databases. Keeping everything together would make it easier to connect related information and avoid duplicate or conflicting records. SQL would also allow the system to use primary keys and foreign keys to make sure that alerts are connected to valid sensors and that classifications are connected to the correct rangers. 

Because the system stores the locations and patterns of possible mountain lion activity, access should be limited to authorized rangers and employees. Rangers could review and classify alerts, while administrators could manage sensor information and create reports. The system should use regular backups, audit logs, and a policy for deciding how long records should be kept. Older alerts could be moved into archive tables after they are converted into summary records instead of being deleted completely.

One alternative would be to use Redis, which is a fast key-value database. For example, a new alert could be stored using a key like alert:123, along with information such as the sound type, strength, location, date, and classification. Redis could help the ranger station access new alerts quickly, but it would not organize the connections between sensors, alerts, rangers, and reports as well as SQL. It could also require extra settings to make sure older alert information is not lost. Another option would be to use two SQL databases, with one for current alerts and another for historical or archived records. This could improve performance, but it would also make the system more complicated to maintain and secure. Another organization option would be to place all alert information in one large table instead of separating it into tables for alerts, sensors, classifications, summaries, and reports. This would be simpler at first, but it could create duplicate information and make the database harder to update accurately.

Using spreadsheets or separate files would probably be less effective because records could be duplicated, lost, or entered inconsistently. Redis could help the ranger station access new alerts quickly, but it would not be as useful for storing and organizing long-term records or creating detailed reports. A cloud-based SQL database could provide automatic backups and additional security, but it might cost more and require the parks department to depend on an outside service. Overall, one centralized SQL database is the best choice because it keeps the system organized, supports accurate reporting, and is practical for the size and purpose of this system.


## Development of Plan and Timeline

We will divide the project into two parts, frontend and backend development. And split the work evenly depending on experience. We will separate the work into roles, but that doesn’t mean developers are stuck in only one role, it just means someone is responsible for tracking progress end to end.

## Tasking Partition

- Project Manager: Safa
- Systems Architecture Developer: Uriel
- Backend Developer: Blade Mata
- Frontend Developer: Stephen
- Test Developer: Alex

## Timeline

The first week consists of developing a plan, confirming roles, requirements, and creating a soft plan from idea to production.

In the second week we begin splitting the tasking. Frontend will create the UI for park rangers to use and keep track of alerts. The Backend will begin creating a database for potential sightings and reports as well as create a machine learning algorithm to train to detect mountain lions. Then, the test developers will create tests to make sure we are able to run the app smoothly. Meanwhile the systems architect will create a pipeline for changes and testing to be automated.

In the Third and Fourth week’s the frontend and backend will begin to merge. The Project Manager will be responsible for keeping track of everyone making progress evenly.
