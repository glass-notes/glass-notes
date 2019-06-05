# `git-notes` design specification

## ▶ Elevator Pitch

When we students are in class, generally we can focus on taking detailed notes for later or on gaining an in-depth understanding now; almost never can we do both at the same time. We pay hundreds of thousands of dollars for access to these top notch Professors and researchers, yet we'll end up forgetting most of what they tell us. How can we both take good notes and gain that in-depth understanding? We can create a system that displays your digital notes next to your physical lecturer. Heads-up displays (HUDs), like Google Glass, allows us to do this by augmenting our vision of the real world with relevant information from internet. This projects allows students to take notes in a seemless and useful way to gain understanding and depth while saving their precious notes online where they can always find it.

### ▶▶ What students struggle with right now

There are a few main problems this project tackles head-on:

1. **Capturing both content and understanding.**  
    Typically, we can either take really detailed notes or can focus on the lecture and understand the concepts. However, taking detailed notes mean we miss the understanding. Focusing soley on the lectures means that we lose the understanding we could gain by staying engaged with the lecturer.
    
2. **Taking notes where information is received, our eyes.**  
    Usually, we have a notebook or laptop in front of us when we're taking notes. Both involve taking your eyes off of the whiteboard, presentation, or speaker in front of us. Taking notes on a heads-up display (HUD) like Glass allows our eyes to stay engaged with the content while still taking detailed notes. As visual creatures, keeping both the input (lecture) and output (notes) of the classroom setting near the eyes just makes intuitive sense.
    
3. **Syncing our notes online.** . 
   The worst feeling students can possibly have is losing a notebook or accidently losing a Word document with incredibly valuable information. Google Docs allows us to take notes quickly and ensures us that our notes are saved forever. However, presenting a Google Docs GUI on Glass is nearly impossible both in terms of user interface and browser technology. It's just hard to use and hard to get working.
    
### ▶▶ What this project creates

1. **A simple UI for taking notes anytime on the go while staying engaged with the world.** . 
    Simply say an "Ok Glass" command and create up a new note. You'll be taking notes in Markdown (`.md`) which allows for semi-rich text editing through an easy-to-learn and well-designed syntax.

2. **Incremental and frequent notes saved to a Git repository.** . 
    Git is great! We leverage Git for committing incremental changes to notes and syncing them to a Git repository of your choosing!

## ▶ Design

Design for the user. That's the most important, and first, stage of all projects.

### ▶▶ Input mechanism

Glass is an Android device that can connect to Bluetooth-enabled keyboards. This is the primary input mechanism for the device.

### ▶▶ User requirements

#### ▶▶▶ Core aspects

- The note I'm taking
- Where in the document my cursor is (flashing)
- The current scroll location I am in the document
- The current time
- When the last sync was performed with Github
- Visually, if there's an error (check mark or cross)
- A way to manually trigger a save
- Navigating to another document or creating a new document (automatically timestamped for the title for now)
- Saving the document to the local disk if a sync is not possible or has failed

#### ▶▶▶ Nice-to-haves

- A way to toggle between the final rendered Markdown and editing environment
- A way to add pictures/video/audio from the Glass camera into the app and embed into the Markdown document as HTML or Markdown syntax
- A way to manage branching and folders as usful for note-taking, perhaps per the colaborator editing the document
- A short-link to access the notes online
- A way to edit the same document online, enabling multi-person collaboration
- A way to managing merging for versions of the same document online
- LaTeX support
- Type hints from prior notes
- Show prior, relevant notes online
- Remeberance agent
- Scan a QR code to open up a particular notes file
- Ability to create a 'session' for a logged in user by signing in online/through an app and showing a QR code to Glass

## ▶ User interface

Let's focus on the core aspects for now. The canvas is`640 x 320` (`width x height`), the display size of Google Glass, Explorer Edition (XE).

There will be three screens in this workflow:

1. **Session start**: Camera screen to scan QR representing the active session
2. **Document select**: List of all available documents and a button to create a new document
3. **Document edit**: The document editor

### ▶▶ 1) Session start


```
------------------------------------------------------------------
|  Start session       Now: Sun, Jun 2, 1:30pm       ↗ Help      |
|----------------------------------------------------------------|
|                                                                |
|                                                                |
|                LOG IN TO git-notes                             |
|                ACTIVE CAMERA                                   |
|                with QR CODE BOUNDING BOX                       |
|                                                                |
|                                                                |
|                                                                |
|----------------------------------------------------------------|
|  → On your phone/laptop, visit http://git-notes.p13i.app/QR/   |  
------------------------------------------------------------------
```


### ▶▶ 2) Document select


```
------------------------------------------------------------------
|  Select document       Now: Sun, Jun 2, 1:30pm     ↗ Help      |
|----------------------------------------------------------------|
|  Create new note    →                                          |
|  Add new TODO       →                                          |
|                                                                |
|  Open existing:                                                |
|  - 2019-05-28.md  ... Lorem ipsum dolor sit amet, consectetur  |
|  - 2019-05-31.md  ... Lorem ipsum dolor sit amet, consectetur  |
|  - 2019-06-02.md  ... Lorem ipsum dolor sit amet, consectetur  |
|                                                                |
|----------------------------------------------------------------|
|  ← Logout                                                      |  
------------------------------------------------------------------
```

### ▶▶ 3) Document edit

```
------------------------------------------------------------------
|  ➤➤➤ 2019-02-02.md     Now: Sun, Jun 2, 1:32pm     ↗ Help      |
|----------------------------------------------------------------|
|  Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed  |
|  do eiusmod tempor incididunt ut labore:                       |
|    - do eiusmod tempor                                         |
|    - incididunt ut labore                                      |
|    - et dolore magna aliqua                                    |
|                                                                |
|  Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed  |
|  do eiusmod tempor incididunt ut labore:                       |
|    1. $$ \dfrac{1}{2} $$                                       |
|                                                                |
|----------------------------------------------------------------|
|  ← Save and exit   |  Last sync:  Sun, Jun 2, 1:31pm (40s) ✓   |  
------------------------------------------------------------------
```

## ▶ System architecture

The running, functional system includes these computers:

1. Google Glass
2. A thin RESTful API that simplifies interaction with the VCS (version control system)
3. GitHub servers storing the saved documents

Essentially, Github will be used as a database for storing all these documents since it provides an incremental update system that's simple and easy to use. No need to implement a collaborative system like Google Docs.

For now, we'll stick to Github gists to avoid dealing with Git repositories and folder structuring, later problems.

### ▶▶ Database/API models

All fields are mandatory except through marked by `?` (nullable). `FK` stands for foriegn key. `FINAL` indicates that the field may not change after first being created.

```
→ User
  ↳ id: Long, Automatic, FINAL
  ↳ uuid: String, UUID, FINAL
  ↳ first_name: String
  ↳ last_name: String
  ↳ email: String
  ↳ github_username: String
```

```
→ Session
  ↳ id: Long, Automatic, FINAL
  ↳ uuid: String, UUID, FINAL
  ↳ user_id: Long, FK=User, FINAL
```
  
```
→ Document
  ↳ id: Long, Automatic, FINAL
  ↳ uuid: String, UUID, FINAL
  ↳ user_id: Long, FK=User, FINAL
  ↳ create_datetime: DateTime, Automatic, FINAL
  ↳ last_revision_datetime: DateTime?
  ↳ github_gist_id: String
  ↳ github_latest_commit_hash: String
  ↳ github_gist_filename: String
  ↳ github_gist_latest_contents: Blob
```

### ▶▶ Our RESTful API design

Endpoints are marked with certain flags:
- 🔐🔐 are only available to super-users
- 🔐 are only available to logged in users, determined by session cookies managed by the API framework
- 🔑 require a Session uuid AND limit access to only that User's data, managed by this API
- 🌏 are public, no authentication needed

```
→ User

  ↳ 🔐🔐 GET /users/
    ⇠ List of User, all fields
  ↳ 🌏 POST /users/
    Used to create a user account
    ⇢ first name
    ⇢ last name
    ⇢ email address
    ⇢ github user
    ⇠ id
  ↳ 🔐 GET /users/<ID>/
    ⇠ User, all fields
```

```
→ Sessions

  ↳ 🔐 GET /users/<ID>/session/
    Gets the active session key or creates a new one
    ⇠ Session, all fields
```

```
→ Documents

  ↳ 🔐🔑 GET /documents/
    Lists all document IDs and filenames for the given user
    ⇠ List of Document, fields:
      ⇠ id
      ⇠ uuid
      ⇠ filename
      ⇠ character_count: Integer, COMPUTED
  ↳ 🔐🔑 GET /documents/<ID>/
    Gets the specified document
    ⇠ Document, all fields
  ↳ 🔐🔑 POST /documents/
    Creates a new document
    ⇠ Document, all fields
  ↳ 🔐🔑 PATCH /documents/<ID>/
    Submits a revision
    ⇠ Document, fields:
      ⇠ last_revision_datetime
      ⇠ github_latest_commit_hash
```

All endpoints return the following HTTP status codes:
- `200` (fetches)
- `201` (creates)
- `300` (client-responsible validation error)
- `500` (server error)

Data is transferred as JSON, using the `_` characters, for simplicity.

## ▶ Development

Clean, well-documented code is expected. Tools like Docker and Vagrant will be used and documented for easy reproduction of development environment. No excuses for this. Code *will* remain open-source and documented.

### ▶▶ Implementation

#### ▶▶▶ Google Glass

Glass targets API level 19 of the Android SDK. A Google Glass and companion Android mobile application will be published under the `src/GitNotesAndroid` directory.

#### ▶▶▶ RESTful API

We'll be using a Python-based, Django REST Framework application to serve our API. We'll be utilizing a PostgreSQL database. The package `PyGitHub` will be used to communicate from Python to GitHub's API. The `src/GitNotesAPI` directory will contain the API. We'll be using Python 3.6 because of it's support of type-annotations and type-linting. Tests will be written.

## ▶ Deployment

### ▶▶ Local

We'll be spinning up a local Docker swarm using `docker-compose`.

### ▶▶ Cloud

The RESTful API will be deployed to Heroku or Docker Cloud. The API will be available to the public Internet under `https://git-notes.p13i.app/api/` standard TCP/IP ports `:80` (HTTP) and `:443` (HTTPS).

---

Pramod Kotipalli  
http://p13i.io/
