---
title: Ongoing Quest
date: 2026-03-05 00:00:00 +0900
categories: [piu_quests, features]
tags: [piu_quests, quests]
---

# Ongoing Quest

This document states the ongoing quest specification.

---

## 0. Terms

- Quest states for a collection of charts.
- Quest has start date and end date.
- Charts are composed of 'song name' and 'difficulty'.
- 'song name' stands for the exact sane name as Pump It Up in-game shows.
- 'dificulty' is composed of 2 part: one letter 'S' or 'D', and number follows it.
  - S stands for single, D stands for double.
  - example of difficulty: S19, D20

---

## 1. Ongoing quest (오늘의 숙제)

- Ongoing quest is the quest that has current date within its start date and end date.
  - Start date begins at 00:00, and end date ends at 23:59. In other words, a quest is "ongoing" if `start_date <= today <= end_date`.
- If ongoing quest does not exist, logged-in user may create a new one.
  - to the current empty space with '현재 진행 중인 숙제가 없습니다. 숙제를 등록해주세요.' description, a new button called '숙제 만들기' exists when a user is logged in.

---

## 2. Creating a quest (숙제 만들기 button)

- Creating a new quest has a new page, that is accessible by '숙제 만들기' button.
- Date validation: both start date and end date must include the current date. That is, `start_date <= today <= end_date` must hold. The backend rejects requests that do not satisfy this.
- It has 2 tabs: '추첨하기' and '직접 설정하기'. Default tab is '추첨하기'.
- 2 tabs both have same 8 input form that has difficulties as title: ('/' stands for OR)
  - S19
  - S20/D20
  - S21/D21
  - S22/D22
  - S23/D23
  - S24/D24
  - S25/D25
  - D26
- each input form has:
  - Song name
  - Difficulty
- For '추첨하기' tab:
  - This uses '추첨하기'(pick) feature that is described on the document "2-1. '추첨하기'(pick)".
  - There is a date chooser at the top, that can set start date and end date.
  - There is a button '전부 추첨하기' below date chooser.
    - This 'pick's every 8 difficulties and puts into the form.
  - Every input form of difficulties is disabled: user cannot modify the input form's value. Only 'pick' system decides.
  - Every form has a button that has 'refresh' icon as label: this 'pick's the corresponding difficulty's chart.
  - There is a button '등록하기' at the last buttom.
    - This submits the quest and chart. However, if ongoing quest already exists, this notifies to user that the quest cannot be created.
- For '직접 선택하기' tab:
  - There is a date chooser at the top, that can set start date and end date.
  - Every input form of difficulties is enabled: user manually puts in.
  - There is a button '등록하기' at the last buttom.
    - This submits the quest and chart. However, if ongoing quest already exists, this notifies to user that the quest cannot be created.

### 2-1. '추첨하기'(pick)

- '추첨'(pick) feature stands for:
  - We will request POST to the other service called 'piurise.com'.
  - example of request would be:
```
POST https://piurise.com/api/arcade/song-select
headers: {
  'Content-Type': 'application/json'
}
body: {
    "mode": "single",
    "minLevel": 19,
    "maxLevel": 19,
    "pickCount": 1,
    "excludeSongIds": []
}
```
  - for this request, it has a rule:
    - When requesting for difficulty 'S19', put 'mode' as 'single', and minLevel/maxLevel as 19.
    - When requesting for difficulty 'S20/D20' to 'S25/D25', do not put 'mode' in body, and put minLevel and MaxLevel as given level.
    - When requesting for difficulty 'D26', put 'mode' as 'double', and minLevel/maxLevel as 26.
    - pickCount is always 1.
    - excludeSongIds is always [].
  - example of respose would be:
```
{
  "songs": [
    {
      "id": "e7037390-35b4-470a-8938-67d514869136",
      "songTitle": "\uc194\ud398\uc9c0\uc5d0\ud1a0",
      "artist": "Fiverwater",
      "imageUrl": "/piu-songs/phoenix/89ad08817eede5a099583987e96d64b3.webp",
      "stepLevel": "19",
      "difficultyBgUrl": "/api/uploads/stepball/full/s_bg.png",
      "yearMonth": "202603"
    }
  ],
  "totalCount": 268,
  "filters": {
    "minLevel": 19,
    "maxLevel": 19,
    "pickCount": 1
  }
}
```
  - Please decode 'songTitle' that is encoded in unicode, and put into the charset we use. The value of songTitle is used as 'song name'.
  - If 'difficultyBgUrl' ends with "s_bg.png", it is 'single'. The difficulty starts with 'S'. Otherwise if it ends with "d_bg.png", it is 'double'. The difficulty starts with 'D'.
  - For difficulty, collect 'S' or 'D' from difficultyBgUrl with the rule I've mentioned, and add 'stepLevel' value to it.
    - ex: S19, D21, D25

---

## 3. Ongoing quest view

- Ongoing quest shows the difficulty and song name as a list.
- When user submits their record, there is a score of the user achieved.
- When an item of list is clicked, it shows the all users and their submission state.
  - If an user already submitted, show its name and score.
  - If an user did not submit, show its name and '미제출' as score.
- Below the list of songs, there is a list for every user of submission status.
  - State the user name, and submission status like: "ADMIN: 5/8 submitted".
    - If the user has submitted every song, highlight that user as green font.
    - If the user did not submit any song, hightlight that user as red font.

---

## 4. Quest submission

- There is a button at the right side of quest title called '제출'.
- When '제출' button is clicked, modal appears and it receives multiple photos from user.
  - One photo upload form is opened first time, and '+' button is below.
  - If user has uploaded already, show their previous records.
    - Records should have 'song_name', 'difficulty', 'score'.
      - So, photos.score should be moved to records. We would separate 'photos' for its own role.
  - User can upload up to 8 photos.
  - Do not receive file that is not in photo format.
  - Do not receive file that exceeds 30MB.
  - When each photo is received do this:
    - Create 1 select form and 2 input forms that are disabled for now.
      - First: song name('곡명'): select form
        - This is selected between 'charts.song_name' of ongoing quest from DB.
      - Second: difficulty('난이도'): input form
      - Third: score('점수'): input form
    - Call Gemini API and try to extract 'song name', 'difficulty', and 'score' from photo. While extracting, show a loading indicator and disable the form fields.
      - The photo has 'song name' on the upper side of photo, which has blue background.
      - The difficulty is in the photo:
        - There is a oval-shaped label that has red or green color, and white numbers above it that has number between 1~27.
        - If has red color, place 'S' as the first string of difficulty. if green, place 'D' instead.
        - Place the number inside the label as the rest string of difficulty. For example: 'S19', 'D21'
      - The score is under the text 'SCORE' text with white color, and it is written in number that is between 0~1000000.
      - **Chart matching**: After extraction, match the extracted song_name AND difficulty against the quest's charts. Since the same song can appear with different difficulties (e.g. "이스케이프 S22" and "이스케이프 D26"), both fields must match to auto-select the correct chart. Whitespace is ignored during comparison. If no exact (song_name + difficulty) match is found, fall back to song_name-only match.
        - If no match is found at all, leave the select form empty.
      - photo example:

      ![4291](/assets/img/piu_quests/4291.jpg)
      ![4294](/assets/img/piu_quests/4294.jpg)

    - When Gemini API is finished for each photo, enable 3 forms and let users fix or confirm them.
    - If there are duplicated chart selections (same song_name + difficulty) in the form, alert the user. There should be validation on API level also, that does not accept the same chart_id for one request.
    - Validation on submit:
      - song_name must not be empty.
      - difficulty must match the format: one letter 'S' or 'D' followed by 1-2 digits (e.g. S19, D22).
      - score must be a number between 0 and 1,000,000.
  - User can delete their previous record and re-upload their record. If the same chart (song_name + difficulty) comes in when saving record, remove previous record item and photo.
