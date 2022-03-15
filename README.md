# SQL PRACTICE

### SQL Practice №8 | The Hospital

#### Relational shema
![Relation shema](https://upload.wikimedia.org/wikipedia/commons/b/b8/Sql_hospital.png)

#### The creation code
``` sql
 CREATE TABLE physician (
  EmployeeID INTEGER PRIMARY KEY NOT NULL,
  Name TEXT NOT NULL,
  Position TEXT NOT NULL,
  SSN INTEGER NOT NULL
); 

CREATE TABLE department (
  DepartmentID INTEGER PRIMARY KEY NOT NULL,
  Name TEXT NOT NULL,
  Head INTEGER NOT NULL
    CONSTRAINT fk_Physician_EmployeeID REFERENCES Physician(EmployeeID)
);

CREATE TABLE affiliated_With (
  Physician INTEGER NOT NULL
    CONSTRAINT fk_Physician_EmployeeID REFERENCES Physician(EmployeeID),
  Department INTEGER NOT NULL
    CONSTRAINT fk_Department_DepartmentID REFERENCES Department(DepartmentID),
  PrimaryAffiliation BOOLEAN NOT NULL,
  PRIMARY KEY(Physician, Department)
);

CREATE TABLE procedure (
  Code INTEGER PRIMARY KEY NOT NULL,
  Name TEXT NOT NULL,
  Cost REAL NOT NULL
);

CREATE TABLE trained_In (
  Physician INTEGER NOT NULL
    CONSTRAINT fk_Physician_EmployeeID REFERENCES Physician(EmployeeID),
  Treatment INTEGER NOT NULL
    CONSTRAINT fk_Procedure_Code REFERENCES Procedure(Code),
  CertificationDate DATETIME NOT NULL,
  CertificationExpires DATETIME NOT NULL,
  PRIMARY KEY(Physician, Treatment)
);

CREATE TABLE patient (
  SSN INTEGER PRIMARY KEY NOT NULL,
  Name TEXT NOT NULL,
  Address TEXT NOT NULL,
  Phone TEXT NOT NULL,
  InsuranceID INTEGER NOT NULL,
  PCP INTEGER NOT NULL
    CONSTRAINT fk_Physician_EmployeeID REFERENCES Physician(EmployeeID)
);

CREATE TABLE nurse (
  EmployeeID INTEGER PRIMARY KEY NOT NULL,
  Name TEXT NOT NULL,
  Position TEXT NOT NULL,
  Registered BOOLEAN NOT NULL,
  SSN INTEGER NOT NULL
);

CREATE TABLE appointment (
  AppointmentID INTEGER PRIMARY KEY NOT NULL,
  Patient INTEGER NOT NULL
    CONSTRAINT fk_Patient_SSN REFERENCES Patient(SSN),
  PrepNurse INTEGER
    CONSTRAINT fk_Nurse_EmployeeID REFERENCES Nurse(EmployeeID),
  Physician INTEGER NOT NULL
    CONSTRAINT fk_Physician_EmployeeID REFERENCES Physician(EmployeeID),
  Start DATETIME NOT NULL,
  End DATETIME NOT NULL,
  ExaminationRoom TEXT NOT NULL
);

CREATE TABLE medication (
  Code INTEGER PRIMARY KEY NOT NULL,
  Name TEXT NOT NULL,
  Brand TEXT NOT NULL,
  Description TEXT NOT NULL
);

CREATE TABLE prescribes (
  Physician INTEGER NOT NULL
    CONSTRAINT fk_Physician_EmployeeID REFERENCES Physician(EmployeeID),
  Patient INTEGER NOT NULL
    CONSTRAINT fk_Patient_SSN REFERENCES Patient(SSN),
  Medication INTEGER NOT NULL
    CONSTRAINT fk_Medication_Code REFERENCES Medication(Code),
  Date DATETIME NOT NULL,
  Appointment INTEGER
    CONSTRAINT fk_Appointment_AppointmentID REFERENCES Appointment(AppointmentID),
  Dose TEXT NOT NULL,
  PRIMARY KEY(Physician, Patient, Medication, Date)
);

CREATE TABLE block (
  Floor INTEGER NOT NULL,
  Code INTEGER NOT NULL,
  PRIMARY KEY(Floor, Code)
); 

CREATE TABLE room (
  Number INTEGER PRIMARY KEY NOT NULL,
  Type TEXT NOT NULL,
  BlockFloor INTEGER NOT NULL,
  BlockCode INTEGER NOT NULL,
  Unavailable BOOLEAN NOT NULL,
  FOREIGN KEY(BlockFloor, BlockCode) REFERENCES Block
);

CREATE TABLE on_call (
  Nurse INTEGER NOT NULL
    CONSTRAINT fk_Nurse_EmployeeID REFERENCES Nurse(EmployeeID),
  BlockFloor INTEGER NOT NULL,
  BlockCode INTEGER NOT NULL,
  Start DATETIME NOT NULL,
  End DATETIME NOT NULL,
  PRIMARY KEY(Nurse, BlockFloor, BlockCode, Start, End),
  FOREIGN KEY(BlockFloor, BlockCode) REFERENCES Block
);

CREATE TABLE stay (
  StayID INTEGER PRIMARY KEY NOT NULL,
  Patient INTEGER NOT NULL
    CONSTRAINT fk_Patient_SSN REFERENCES Patient(SSN),
  Room INTEGER NOT NULL
    CONSTRAINT fk_Room_Number REFERENCES Room(Number),
  Start DATETIME NOT NULL,
  End DATETIME NOT NULL
);

CREATE TABLE undergoes (
  Patient INTEGER NOT NULL
    CONSTRAINT fk_Patient_SSN REFERENCES Patient(SSN),
  Procedure INTEGER NOT NULL
    CONSTRAINT fk_Procedure_Code REFERENCES Procedure(Code),
  Stay INTEGER NOT NULL
    CONSTRAINT fk_Stay_StayID REFERENCES Stay(StayID),
  Date DATETIME NOT NULL,
  Physician INTEGER NOT NULL
    CONSTRAINT fk_Physician_EmployeeID REFERENCES Physician(EmployeeID),
  AssistingNurse INTEGER
    CONSTRAINT fk_Nurse_EmployeeID REFERENCES Nurse(EmployeeID),
  PRIMARY KEY(Patient, Procedure, Stay, Date)
);
```
#### Sample database
``` sql
INSERT INTO physician VALUES(1,'John Dorian','Staff Internist',111111111);
INSERT INTO physician VALUES(2,'Elliot Reid','Attending Physician',222222222);
INSERT INTO physician VALUES(3,'Christopher Turk','Surgical Attending Physician',333333333);
INSERT INTO physician VALUES(4,'Percival Cox','Senior Attending Physician',444444444);
INSERT INTO physician VALUES(5,'Bob Kelso','Head Chief of Medicine',555555555);
INSERT INTO physician VALUES(6,'Todd Quinlan','Surgical Attending Physician',666666666);
INSERT INTO physician VALUES(7,'John Wen','Surgical Attending Physician',777777777);
INSERT INTO physician VALUES(8,'Keith Dudemeister','MD Resident',888888888);
INSERT INTO physician VALUES(9,'Molly Clock','Attending Psychiatrist',999999999);

INSERT INTO department VALUES(1,'General Medicine',4);
INSERT INTO department VALUES(2,'Surgery',7);
INSERT INTO department VALUES(3,'Psychiatry',9);

INSERT INTO affiliated_with VALUES(1,1,1);
INSERT INTO affiliated_with VALUES(2,1,1);
INSERT INTO affiliated_with VALUES(3,1,0);
INSERT INTO affiliated_with VALUES(3,2,1);
INSERT INTO affiliated_with VALUES(4,1,1);
INSERT INTO affiliated_with VALUES(5,1,1);
INSERT INTO affiliated_with VALUES(6,2,1);
INSERT INTO affiliated_with VALUES(7,1,0);
INSERT INTO affiliated_with VALUES(7,2,1);
INSERT INTO affiliated_with VALUES(8,1,1);
INSERT INTO affiliated_with VALUES(9,3,1);

INSERT INTO procedures VALUES(1,'Reverse Rhinopodoplasty',1500.0);
INSERT INTO procedures VALUES(2,'Obtuse Pyloric Recombobulation',3750.0);
INSERT INTO procedures VALUES(3,'Folded Demiophtalmectomy',4500.0);
INSERT INTO procedures VALUES(4,'Complete Walletectomy',10000.0);
INSERT INTO procedures VALUES(5,'Obfuscated Dermogastrotomy',4899.0);
INSERT INTO procedures VALUES(6,'Reversible Pancreomyoplasty',5600.0);
INSERT INTO procedures VALUES(7,'Follicular Demiectomy',25.0);

INSERT INTO patient VALUES(100000001,'John Smith','42 Foobar Lane','555-0256',68476213,1);
INSERT INTO patient VALUES(100000002,'Grace Ritchie','37 Snafu Drive','555-0512',36546321,2);
INSERT INTO patient VALUES(100000003,'Random J. Patient','101 Omgbbq Street','555-1204',65465421,2);
INSERT INTO patient VALUES(100000004,'Dennis Doe','1100 Foobaz Avenue','555-2048',68421879,3);

INSERT INTO nurse VALUES(101,'Carla Espinosa','Head Nurse',1,111111110);
INSERT INTO nurse VALUES(102,'Laverne Roberts','Nurse',1,222222220);
INSERT INTO nurse VALUES(103,'Paul Flowers','Nurse',0,333333330);

INSERT INTO appointment VALUES(13216584,100000001,101,1,'2008-04-24 10:00','2008-04-24 11:00','A');
INSERT INTO appointment VALUES(26548913,100000002,101,2,'2008-04-24 10:00','2008-04-24 11:00','B');
INSERT INTO appointment VALUES(36549879,100000001,102,1,'2008-04-25 10:00','2008-04-25 11:00','A');
INSERT INTO appointment VALUES(46846589,100000004,103,4,'2008-04-25 10:00','2008-04-25 11:00','B');
INSERT INTO appointment VALUES(59871321,100000004,NULL,4,'2008-04-26 10:00','2008-04-26 11:00','C');
INSERT INTO appointment VALUES(69879231,100000003,103,2,'2008-04-26 11:00','2008-04-26 12:00','C');
INSERT INTO appointment VALUES(76983231,100000001,NULL,3,'2008-04-26 12:00','2008-04-26 13:00','C');
INSERT INTO appointment VALUES(86213939,100000004,102,9,'2008-04-27 10:00','2008-04-21 11:00','A');
INSERT INTO appointment VALUES(93216548,100000002,101,2,'2008-04-27 10:00','2008-04-27 11:00','B');

INSERT INTO medication VALUES(1,'Procrastin-X','X','N/A');
INSERT INTO medication VALUES(2,'Thesisin','Foo Labs','N/A');
INSERT INTO medication VALUES(3,'Awakin','Bar Laboratories','N/A');
INSERT INTO medication VALUES(4,'Crescavitin','Baz Industries','N/A');
INSERT INTO medication VALUES(5,'Melioraurin','Snafu Pharmaceuticals','N/A');

INSERT INTO prescribes VALUES(1,100000001,1,'2008-04-24 10:47',13216584,'5');
INSERT INTO prescribes VALUES(9,100000004,2,'2008-04-27 10:53',86213939,'10');
INSERT INTO prescribes VALUES(9,100000004,2,'2008-04-30 16:53',NULL,'5');

INSERT INTO block VALUES(1,1);
INSERT INTO block VALUES(1,2);
INSERT INTO block VALUES(1,3);
INSERT INTO block VALUES(2,1);
INSERT INTO block VALUES(2,2);
INSERT INTO block VALUES(2,3);
INSERT INTO block VALUES(3,1);
INSERT INTO block VALUES(3,2);
INSERT INTO block VALUES(3,3);
INSERT INTO block VALUES(4,1);
INSERT INTO block VALUES(4,2);
INSERT INTO block VALUES(4,3);

INSERT INTO room VALUES(101,'Single',1,1,0);
INSERT INTO room VALUES(102,'Single',1,1,0);
INSERT INTO room VALUES(103,'Single',1,1,0);
INSERT INTO room VALUES(111,'Single',1,2,0);
INSERT INTO room VALUES(112,'Single',1,2,1);
INSERT INTO room VALUES(113,'Single',1,2,0);
INSERT INTO room VALUES(121,'Single',1,3,0);
INSERT INTO room VALUES(122,'Single',1,3,0);
INSERT INTO room VALUES(123,'Single',1,3,0);
INSERT INTO room VALUES(201,'Single',2,1,1);
INSERT INTO room VALUES(202,'Single',2,1,0);
INSERT INTO room VALUES(203,'Single',2,1,0);
INSERT INTO room VALUES(211,'Single',2,2,0);
INSERT INTO room VALUES(212,'Single',2,2,0);
INSERT INTO room VALUES(213,'Single',2,2,1);
INSERT INTO room VALUES(221,'Single',2,3,0);
INSERT INTO room VALUES(222,'Single',2,3,0);
INSERT INTO room VALUES(223,'Single',2,3,0);
INSERT INTO room VALUES(301,'Single',3,1,0);
INSERT INTO room VALUES(302,'Single',3,1,1);
INSERT INTO room VALUES(303,'Single',3,1,0);
INSERT INTO room VALUES(311,'Single',3,2,0);
INSERT INTO room VALUES(312,'Single',3,2,0);
INSERT INTO room VALUES(313,'Single',3,2,0);
INSERT INTO room VALUES(321,'Single',3,3,1);
INSERT INTO room VALUES(322,'Single',3,3,0);
INSERT INTO room VALUES(323,'Single',3,3,0);
INSERT INTO room VALUES(401,'Single',4,1,0);
INSERT INTO room VALUES(402,'Single',4,1,1);
INSERT INTO room VALUES(403,'Single',4,1,0);
INSERT INTO room VALUES(411,'Single',4,2,0);
INSERT INTO room VALUES(412,'Single',4,2,0);
INSERT INTO room VALUES(413,'Single',4,2,0);
INSERT INTO room VALUES(421,'Single',4,3,1);
INSERT INTO room VALUES(422,'Single',4,3,0);
INSERT INTO room VALUES(423,'Single',4,3,0);

INSERT INTO on_call VALUES(101,1,1,'2008-11-04 11:00','2008-11-04 19:00');
INSERT INTO on_call VALUES(101,1,2,'2008-11-04 11:00','2008-11-04 19:00');
INSERT INTO on_call VALUES(102,1,3,'2008-11-04 11:00','2008-11-04 19:00');
INSERT INTO on_call VALUES(103,1,1,'2008-11-04 19:00','2008-11-05 03:00');
INSERT INTO on_call VALUES(103,1,2,'2008-11-04 19:00','2008-11-05 03:00');
INSERT INTO on_call VALUES(103,1,3,'2008-11-04 19:00','2008-11-05 03:00');

INSERT INTO stay VALUES(3215,100000001,111,'2008-05-01','2008-05-04');
INSERT INTO stay VALUES(3216,100000003,123,'2008-05-03','2008-05-14');
INSERT INTO stay VALUES(3217,100000004,112,'2008-05-02','2008-05-03');

INSERT INTO undergoes VALUES(100000001,6,3215,'2008-05-02',3,101);
INSERT INTO undergoes VALUES(100000001,2,3215,'2008-05-03',7,101);
INSERT INTO undergoes VALUES(100000004,1,3217,'2008-05-07',3,102);
INSERT INTO undergoes VALUES(100000004,5,3217,'2008-05-09',6,NULL);
INSERT INTO undergoes VALUES(100000001,7,3217,'2008-05-10',7,101);
INSERT INTO undergoes VALUES(100000004,4,3217,'2008-05-13',3,103);

INSERT INTO trained_in VALUES(3,1,'2008-01-01','2008-12-31');
INSERT INTO trained_in VALUES(3,2,'2008-01-01','2008-12-31');
INSERT INTO trained_in VALUES(3,5,'2008-01-01','2008-12-31');
INSERT INTO trained_in VALUES(3,6,'2008-01-01','2008-12-31');
INSERT INTO trained_in VALUES(3,7,'2008-01-01','2008-12-31');
INSERT INTO trained_in VALUES(6,2,'2008-01-01','2008-12-31');
INSERT INTO trained_in VALUES(6,5,'2007-01-01','2007-12-31');
INSERT INTO trained_in VALUES(6,6,'2008-01-01','2008-12-31');
INSERT INTO trained_in VALUES(7,1,'2008-01-01','2008-12-31');
INSERT INTO trained_in VALUES(7,2,'2008-01-01','2008-12-31');
INSERT INTO trained_in VALUES(7,3,'2008-01-01','2008-12-31');
INSERT INTO trained_in VALUES(7,4,'2008-01-01','2008-12-31');
INSERT INTO trained_in VALUES(7,5,'2008-01-01','2008-12-31');
INSERT INTO trained_in VALUES(7,6,'2008-01-01','2008-12-31');
INSERT INTO trained_in VALUES(7,7,'2008-01-01','2008-12-31');
```
#### Exercises
1. Obtain the names of all physicians that have performed a medical procedure they have never been certified to perform.
2. Same as the previous query, but include the following information in the results: Physician name, name of procedure, date when the procedure was carried out, name of the patient the procedure was carried out on.
3. Obtain the names of all physicians that have performed a medical procedure that they are certified to perform, but such that the procedure was done at a date (Undergoes.Date) after the physician's certification expired (Trained_In.CertificationExpires).
4. Same as the previous query, but include the following information in the results: Physician name, name of procedure, date when the procedure was carried out, name of the patient the procedure was carried out on, and date when the certification expired.
5. Obtain the information for appointments where a patient met with a physician other than his/her primary care physician. Show the following information: Patient name, physician name, nurse name (if any), start and end time of appointment, examination room, and the name of the patient's primary care physician.
6. The Patient field in Undergoes is redundant, since we can obtain it from the Stay table. There are no constraints in force to prevent inconsistencies between these two tables. More specifically, the Undergoes table may include a row where the patient ID does not match the one we would obtain from the Stay table through the Undergoes.Stay foreign key. Select all rows from Undergoes that exhibit this inconsistency.
7. Obtain the names of all the nurses who have ever been on call for room 123.
8. The hospital has several examination rooms where appointments take place. Obtain the number of appointments that have taken place in each examination room.
``` sql
1. SELECT Name FROM Physician WHERE EmployeeID IN (SELECT Physician FROM Undergoes AS und WHERE NOT EXISTS(SELECT * FROM Trained_In WHERE Treatment = Procedure AND Physician = Physician));

2. SELECT P.Name FROM Physician AS P, (SELECT Physician, Procedure FROM Undergoes EXCEPT SELECT Physician, Treatment FROM Trained_in) AS Pe WHERE P.EmployeeID=Pe.Physician

3. SELECT Name  FROM Physician WHERE EmployeeID IN (SELECT Undergoes.Physician FROM Undergoes LEFT JOIN Trained_In ON Undergoes.Physician = Trained_In.Physician AND  Undergoes.Procedures=Trained_In.Treatment WHERE Treatment IS NULL);

4. SELECT Physician.Name FROM Physician INNER JOIN Undergoes ON Physician.EmployeeID = Undergoes.Physician LEFT JOIN Trained_In ON Undergoes.Procedure = Trained_In.Treatment  AND Physician.EmployeeID = Trained_In.Physician WHERE Trained_In.Treatment IS NULL GROUP BY Physician.EmployeeID

5. SELECT P.Name AS Physician, Pr.Name AS Procedure, U.Date, Pt.Name AS Patient FROM Physician P, Undergoes U, Patient Pt, Procedure Pr WHERE U.Patient = Pt.SSN AND U.Procedure = Pr.Code AND U.Physician = P.EmployeeID AND NOT EXISTS (SELECT * FROM Trained_In T WHERE T.Treatment = U.Procedure AND T.Physician = U.Physician);

6. SELECT P.Name, Pr.Name, U.Date, Pt.Name FROM Physician AS P, Procedure AS Pr, Undergoes AS U, Patient AS Pt, (SELECT Physician, Procedure FROM Undergoes EXCEPT SELECT, FROM) AS Pe WHERE P.EmployeeID=Pe.Physician AND Pe.Procedure=Pr.Code AND Pe.Physician=U.Physician AND Pe.Procedure=U.Procedure AND U.Patient=Pt.SSN

7. SELECT Name FROM Physician WHERE EmployeeID IN (SELECT Physician FROM Undergoes U WHERE Date > (SELECT CertificationExpires FROM Trained_In T WHERE T.Physician = U.Physician 
AND T.Treatment = U.Procedure));

8. SELECT P.Name FROM Physician AS P, Trained_In T, Undergoes AS U WHERE T.Physician=U.Physician AND T.Treatment = U.Procedure AND U.Date > T.CertificationExpires AND P.EmployeeID=U.Physician

9. SELECT P.Name AS Physician, Pr.Name AS Procedure, U.Date, Pt.Name AS Patient, T.CertificationExpires FROM Physician P, Undergoes U, Patient Pt, Procedure Pr, Trained_In T  WHERE U.Patient = Pt.SSN AND U.Procedure = Pr.Code AND U.Physician = P.EmployeeID AND Pr.Code = T.Treatment AND P.EmployeeID = T.Physician AND U.Date > T.CertificationExpires;

10. SELECT Pt.Name AS Patient, Ph.Name AS Physician, N.Name AS Nurse, A.Start, A.End, A.ExaminationRoom, PhPCP.Name AS PCP FROM Patient Pt, Physician Ph, Physician PhPCP, A LEFT JOIN Nurse N ON A.PrepNurse = N.EmployeeID WHERE A.Patient = Pt.SSN AND A.Physician = Ph.EmployeeID AND Pt.PCP = PhPCP.EmployeeID AND A.Physician <> Pt.PCP;

11. SELECT * FROM Undergoes U WHERE Patient <> (SELECT Patient FROM Stay S WHERE U.Stay = S.StayID);

12. SELECT N.Name FROM Nurse N WHERE EmployeeID IN (SELECT OC.Nurse FROM On_Call OC, Room R WHERE OC.BlockFloor = R.BlockFloor AND OC.BlockCode = R.BlockCode AND R.Number = 123);

13. SELECT ExaminationRoom, COUNT(AppointmentID) AS Number FROM Appointment GROUP BY ExaminationRoom;
```
