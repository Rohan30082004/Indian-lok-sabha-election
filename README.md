# Indian-lok-sabha-election-2024

### Dataset Link ()
1) Statewise_Results: https://1drv.ms/x/c/afc4a61c311d62e8/EXVpGjWWoehCo26te19kYcEBVG1MAabUbCeRBif0ltKCFg?e=lnVNjm
2) Constituencywise_Results:
https://1drv.ms/x/c/afc4a61c311d62e8/EZF58toot7BKhJ24b_TECG0Ba18eXBvhQE5G4HpIfPwslA?e=5UrxVj

3) Constituencywise_details:
https://1drv.ms/x/c/afc4a61c311d62e8/EXLnNnmIlStDhu3GLLJ0TIYB-ijFDkiI4y5etg_6MwvbAA?e=HLnJo6

4) States:
https://1drv.ms/x/c/afc4a61c311d62e8/EYCbweJwRVJJt7eEgWte_2sBeteQ1rc7IHtlflFz_G1UAg?e=OjmD6U

5) Statewise_Results:
https://1drv.ms/x/c/afc4a61c311d62e8/EceJj_dGmAFMpUBE1vcxkHwByKPnT00Pg5ghxeUlC2If0g?e=aMHudC



---

## Problem Statement

This project analyzes the Indian Lok Sabha Elections 2024 using SQL queries to derive insights into seat distribution, party performance, alliance trends, and voter behavior. The insights help in understanding electoral outcomes at a granular level, including state and constituency-specific analysis. The project provides actionable intelligence for policymakers, political strategists, and researchers.

---

## Steps Followed

### Step 1: Data Collection and Preparation
- Collected data from reliable sources such as election commission reports and survey data.
- Processed the dataset to remove inconsistencies, duplicates, and null values.

### Step 2: Database Design
- Designed a relational schema with key tables such as:
  - **Constituencywise_Results**: Stores election results at the constituency level.
  - **Partywise_Results**: Contains details of seats won by parties and alliances.
  - **Statewise_Results**: Aggregates results at the state level.
  - **Candidates**: Includes individual candidate details like EVM votes received.

---

## Key SQL Queries and Insights

### 1. Total Number of Seats in Election
```sql
SELECT DISTINCT COUNT(Parliament_Constituency) AS 'Total Seats'
FROM constituencywise_results;
```
- Insight: Determines the total number of parliamentary constituencies contested.

---

### 2. Total Seats in Each State
```sql
SELECT 
    s.State AS State_Name,
    COUNT(cr.Constituency_ID) AS Total_Seats_Available
FROM 
    constituencywise_results cr
JOIN 
    statewise_results sr ON cr.Parliament_Constituency = sr.Parliament_Constituency
JOIN 
    states s ON sr.State_ID = s.State_ID
GROUP BY 
    s.State;
```
- Insight: Provides a breakdown of available seats by state.

---

### 3. Total Seats Won by NDA Alliance
```sql
SELECT 
    SUM(CASE 
            WHEN party IN (
                'Bharatiya Janata Party - BJP', 
                'Telugu Desam - TDP', 
                'Janata Dal (United) - JD(U)', 
                'Shiv Sena - SHS', 
                'AJSU Party - AJSUP', 
                'Apna Dal (Soneylal) - ADAL', 
                'Asom Gana Parishad - AGP',
                'Hindustani Awam Morcha (Secular) - HAMS', 
                'Janasena Party - JnP', 
                'Lok Janshakti Party(Ram Vilas) - LJPRV', 
                'Nationalist Congress Party - NCP',
                'Rashtriya Lok Dal - RLD', 
                'Sikkim Krantikari Morcha - SKM'
            ) THEN [Won]
            ELSE 0 
        END) AS NDA_Total_Seats_Won
FROM 
    partywise_results;
```
- Insight: Calculates the total seats won by NDA alliance parties.

---

### 4. Seats Won by Each NDA Alliance Party
```sql
SELECT 
    party AS Party_Name,
    won AS Seats_Won
FROM 
    partywise_results
WHERE 
    party IN (
        'Bharatiya Janata Party - BJP', 
        'Telugu Desam - TDP', 
        'Janata Dal (United) - JD(U)', 
        'Shiv Sena - SHS', 
        'AJSU Party - AJSUP', 
        'Apna Dal (Soneylal) - ADAL', 
        'Asom Gana Parishad - AGP',
        'Hindustani Awam Morcha (Secular) - HAMS', 
        'Janasena Party - JnP', 
        'Lok Janshakti Party(Ram Vilas) - LJPRV', 
        'Nationalist Congress Party - NCP',
        'Rashtriya Lok Dal - RLD', 
        'Sikkim Krantikari Morcha - SKM'
    )
ORDER BY Seats_Won DESC;
```
- Insight: Displays the seats won by each NDA party in descending order.

---

### 5. Seats Won by Party Alliances Per State
```sql
SELECT 
    s.State AS State_Name,
    SUM(CASE WHEN p.party_alliance = 'NDA' THEN 1 ELSE 0 END) AS NDA_Seats_Won,
    SUM(CASE WHEN p.party_alliance = 'I.N.D.I.A' THEN 1 ELSE 0 END) AS INDIA_Seats_Won,
    SUM(CASE WHEN p.party_alliance = 'OTHER' THEN 1 ELSE 0 END) AS OTHER_Seats_Won
FROM 
    constituencywise_results cr
JOIN 
    partywise_results p ON cr.Party_ID = p.Party_ID
JOIN 
    statewise_results sr ON cr.Parliament_Constituency = sr.Parliament_Constituency
JOIN 
    states s ON sr.State_ID = s.State_ID
WHERE 
    p.party_alliance IN ('NDA', 'I.N.D.I.A', 'OTHER')  
GROUP BY 
    s.State
ORDER BY 
    s.State;
```
- Insight: Breaks down seats won by each party alliance at the state level.

---

### 6. Top 10 Candidates by EVM Votes Received
```sql
SELECT TOP 10
    cr.Constituency_Name,
    cd.Constituency_ID,
    cd.Candidate,
    cd.EVM_Votes
FROM 
    constituencywise_details cd
JOIN 
    constituencywise_results cr ON cd.Constituency_ID = cr.Constituency_ID
WHERE 
    cd.EVM_Votes = (
        SELECT MAX(cd1.EVM_Votes)
        FROM constituencywise_details cd1
        WHERE cd1.Constituency_ID = cd.Constituency_ID
    )
ORDER BY 
    cd.EVM_Votes DESC;
```
- Insight: Lists the top 10 candidates with the highest EVM votes.

---

### 7. Seats Won by Each Party in Andhra Pradesh
```sql
SELECT 
    p.Party,
    COUNT(cr.Constituency_ID) AS Seats_Won
FROM 
    constituencywise_results cr
JOIN 
    partywise_results p ON cr.Party_ID = p.Party_ID
JOIN 
    statewise_results sr ON cr.Parliament_Constituency = sr.Parliament_Constituency
JOIN 
    states s ON sr.State_ID = s.State_ID
WHERE 
    s.State = 'Andhra Pradesh'
GROUP BY 
    p.Party
ORDER BY 
    Seats_Won DESC;
```
- Insight: Shows the number of seats won by each party in Andhra Pradesh.

---

## Insights and Outcomes
1. **Total Seats Available**: Highlights the number of constituencies in the election.
2. **State-Level Analysis**: Unveils the seat distribution and alliances’ performance per state.
3. **Alliance Trends**: Displays the dominance of alliances like NDA, I.N.D.I.A, or others.
4. **Top Candidates**: Identifies candidates with exceptional voter support.
5. **Regional Trends**: Analyzes key battleground states such as Andhra Pradesh.

---

## Tools Used
- SQL Server Management Studio (SSMS)
- SQL for querying and analysis


--- 

 The project can be further enhanced by integrating dashboards using tool like Power BI.
