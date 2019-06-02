# git-notes
Take notes on Google Glass for classes, conversations, and TODOs - syncs to Github

## ▶ Vision

Imagine taking detailed notes in class while staying engaged with the material discussed.

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

Let's focus on the core aspects for now. The canvas is 640x320, the display size of Google Glass, Explorer Edition (XE).

```
------------------------------------------------------------------
|  ➤➤➤ 2019-02-02.md     Now: Sun, Jun 2, 1:32pm     ↗ New note  |
|----------------------------------------------------------------|
|  Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed  |
|  do eiusmod tempor incididunt ut labore:                       |
|    - do eiusmod tempor                                         |
|    - incididunt ut labore                                      |
|    - et dolore magna aliqua                                    |
|                                                                |
|  Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed  |
|  do eiusmod tempor incididunt ut labore:                       |
|    1. $$ \dfrac{1}{2}                                          |
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

```
→ User
  ↳ id
  ↳ uuid
  ↳ first name
  ↳ last name
  ↳ email address
  ↳ github username

→ Session
  ↳ id
  ↳ uuid
  ↳ User uuid
  
→ Document
  ↳ id
  ↳ uuid
  ↳ User uuid
  ↳ create datetime
  ↳ last revision datetime
  ↳ GitHub gist id
  ↳ GitHub latest commit id
  ↳ GitHub gist filename
  ↳ GitHub gist latest contents
  
```

### ▶▶ Our RESTful API design

```

Endpoints are marked with certain flags:
- 🔐🔐 are only available to super-users
- 🔐 require a Session uuid AND limit access to only that User's data
- 🌏 are public, no authentication needed

→ User

  ↳ 🔐🔐 GET /users/
    ↜ list of User
  ↳ 🌏 POST /users/
    ⇢ first name
    ⇢ last name
    ⇢ email address
    ⇢ github user
    ⇠ id
  ↳ 🔐 GET /users/<ID>/
    

→ Sessions

  ↳ 🔐 GET /users/<ID>/session/
    Gets the active session key or creates a new one
  
→ Documents

  ↳ 🔐 GET /documents/
    Lists all documents for the given user
  ↳ 🔐 GET /documents/<ID>/
    Gets the specified document
  ↳ 🔐 POST /documents/
    Creates a new document
  ↳ 🔐 PATCH /documents/<ID>/
    Submits a revision

```

---

Pramod Kotipalli  
http://p13i.io/
