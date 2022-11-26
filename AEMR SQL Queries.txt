/* AEMR stands for American Energy Market Regulator, and is responsible for ensuring America’s energy network is reliable and has minimal outages. I wrote these queries
to focus on energy stability, market outages, energy losses, and market reliability. The data from these queries was used to develop my Tableau presentation: AEMR Case Study.
You can view my AEMR Case Study visualization in my Tableau public profile. */

-- Counts approved outage events grouped by status and reason for 2016
SELECT 
  COUNT(*) AS Total_Number_Outage_Events,
  Status,
  Reason
FROM AEMR
WHERE YEAR(Start_Time) = 2016 
	AND Status = 'Approved'
GROUP BY Status, Reason
ORDER BY Reason;

-- Counts approved outage events grouped by status and reason for 2017
SELECT 
  COUNT(*) AS Total_Number_Outage_Events,
  Status,
  Reason
FROM AEMR
WHERE YEAR(Start_Time) = 2017 
	AND Status = 'Approved'
GROUP BY Status, Reason
ORDER BY Reason;

-- Counts approved outage events and average duration by status, reason, and year for 2016 and 2017
SELECT 
  Status,
  Reason,
  COUNT(*) AS Total_Number_Outage_Events,
  ROUND(AVG(TIMESTAMPDIFF(MINUTE,Start_Time, End_Time)/(60*24)),2) AS Average_Outage_Duration_Time_Days,
  YEAR(Start_Time) AS Year
FROM AEMR
WHERE YEAR(Start_Time) BETWEEN 2016 AND 2017 
	AND Status = 'Approved'
GROUP BY Status, Reason, Year
ORDER BY Reason, Year;

-- Counts outages by month for 2016 by status and reason
SELECT 
  Status,
  Reason,
  COUNT(*) AS Total_Number_Outage_Events,
  MONTH(Start_Time) AS Month
FROM AEMR
WHERE YEAR(Start_Time) = 2016 
	AND Status = 'Approved'
GROUP BY Status, Reason, Month
ORDER BY Reason, Month;

-- Counts outages by month for 2017 by status and reason
SELECT 
  Status,
  Reason,
  COUNT(*) AS Total_Number_Outage_Events,
  MONTH(Start_Time) AS Month
FROM AEMR
WHERE YEAR(Start_Time) = 2017 
	AND Status = 'Approved'
GROUP BY Status, Reason, Month
ORDER BY Reason, Month;

-- Counts total outages by month for 2016 and 2017 by status and year
SELECT 
  Status,
  COUNT(*) AS Total_Number_Outage_Events,
  MONTH(Start_Time) AS Month,
  Year(Start_Time) AS Year
FROM AEMR
WHERE YEAR(Start_Time) BETWEEN 2016 AND 2017 
  AND Status = 'Approved'
GROUP BY 
  Status, 
  Month,
  Year
ORDER BY 
  Month,
  Year;

-- Counts total outages by paricipant code for 2016 and 2017
SELECT 
  COUNT(*) AS Total_Number_Outage_Events,
  Participant_Code,
  Status,
  Year(Start_Time) AS Year
FROM AEMR
WHERE YEAR(Start_Time) BETWEEN 2016 AND 2017 
  AND Status = 'Approved'
GROUP BY 
  Status, 
  Participant_Code,
  Year
ORDER BY 
  Participant_Code,
  Year;

-- Returns avg outage duration time in days for participant codes for years 2016 and 2017
SELECT 
  Participant_Code,
  Status,
  Year(Start_Time) AS Year,
  ROUND(AVG(TIMESTAMPDIFF(MINUTE,Start_Time,End_Time)/(60*24)) ,2) AS Average_Outage_Duration_Time_Days
FROM AEMR
WHERE YEAR(Start_Time) BETWEEN 2016 AND 2017 
  AND Status = 'Approved'
GROUP BY 
  Status, 
  Participant_Code,
  Year
ORDER BY 
  Average_Outage_Duration_Time_Days DESC,
  Participant_Code,
  YEAR(Start_Time);

-- Counts total outages by reason and year
SELECT 
  COUNT(*) AS Total_Number_Outage_Events,
  Reason,
  YEAR(Start_Time) AS Year
FROM AEMR
WHERE YEAR(Start_Time) BETWEEN 2016 AND 2017
  AND Status = 'Approved'
GROUP BY Reason, Year
ORDER BY Reason, Year;

-- Calculates Percentage of forced outages out of total outages
SELECT
  SUM(
    CASE 
    WHEN Reason = 'Forced' THEN 1
    ELSE 0
    END)
    AS Total_Number_Forced_Outage_Events,
  COUNT(*) AS Total_Number_Outage_Events,
  ROUND(SUM(
    CASE 
    WHEN Reason = 'Forced' THEN 1
    ELSE 0
    END)
    /COUNT(*) * 100, 2) AS Forced_Outage_Percentage,
  YEAR(Start_Time) AS Year
FROM AEMR
WHERE Status = 'Approved'
  AND YEAR(Start_Time) BETWEEN 2016 AND 2017 
GROUP BY Year
ORDER BY Year ASC;

-- Average Outage MW loss and duration
SELECT
  Status,
  YEAR(Start_Time) AS Year,
  ROUND(AVG(Outage_MW),2) AS Avg_Outage_MW_Loss,
  ROUND(AVG(TIMESTAMPDIFF(MINUTE, Start_Time, End_Time)),2) AS Average_Outage_Duration_Time_Minutes
FROM AEMR
WHERE Status = 'Approved'
  AND YEAR(Start_Time) BETWEEN 2016 AND 2017
GROUP BY Status, Year
ORDER BY Year;

-- Average Outage MW loss and duration by Reason
SELECT
  Status,
  Reason,
  YEAR(Start_Time) AS Year,
  ROUND(AVG(Outage_MW),2) AS Avg_Outage_MW_Loss,
  ROUND(AVG(TIMESTAMPDIFF(MINUTE, Start_Time, End_Time)),2) AS Average_Outage_Duration_Time_Minutes
FROM AEMR
WHERE Status = 'Approved'
  AND YEAR(Start_Time) BETWEEN 2016 AND 2017
GROUP BY Status, Reason, Year
ORDER BY Year;

-- Avg outage MW loss and duration by Participant_Code
SELECT
  Participant_Code,
  Status,
  YEAR(Start_Time) AS Year,
  ROUND(AVG(Outage_MW),2) AS Avg_Outage_MW_Loss,
  ROUND(AVG(TIMESTAMPDIFF(MINUTE, Start_Time, End_Time))/(60*24),2) AS Average_Outage_Duration_Time_Days
FROM AEMR
WHERE Status = 'Approved'
  AND YEAR(Start_Time) BETWEEN 2016 AND 2017
  AND Reason = 'Forced'
GROUP BY Participant_Code, Status, Reason, Year
ORDER BY Year;

-- Average and Summed Energy loss by Participant and Facility
SELECT
  Participant_Code,
  Facility_Code,
  Status,
  YEAR(Start_Time) AS Year,
  ROUND(AVG(Outage_MW),2) AS Avg_Outage_MW_Loss,
  ROUND(SUM(Outage_MW),2) AS Summed_Energy_Lost
FROM AEMR
WHERE Status = 'Approved'
  AND YEAR(Start_Time) BETWEEN 2016 AND 2017
  AND Reason = 'Forced'
GROUP BY Participant_Code, Facility_Code, Status, Reason, Year
ORDER BY Summed_Energy_Lost DESC, Year;
