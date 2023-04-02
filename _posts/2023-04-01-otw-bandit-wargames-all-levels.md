---
layout: post
title: Over The Wire Bandit - All levels Hints and Solution
subtitle: Learn about Linux by playing a game.
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/thumb.png
share-img: /assets/img/path.jpg
tags: [Linux, OverTheWire, Walkthrough]
---

OverTheWire Bandit Wargames is designed like a Capture the Flag or (CTF) game where we have to capture each flag or password to proceed to the next level.
This makes it very enjoyable and learning a lot of tools and tricks of various basic Linux Concepts.

>This writeup will be heavily **hint** focused, but we will also be disclosing each command to get the job done for each level.

If you are able to understand Nepali Language, then perhaps you could also enjoy the YouTube Series that I have made for this topic going in Depth in each of the Levels, this I would highly recommend for any beginners who want to learn Linux. 

## Recommended Path:
Start by trying to *play this game on your own.* We will be providing the solution part for each level at the end, try to solve it on your own first and follow the various hints and references.

We will be using the following structure for each level
1. What is the Level about.
2. Hints on how we can solve this challenge:
3. Actual solution which needs to be clicked to be seen. 

# Level 0 - Getting Access
In this level we want to know how we can first access the Game and how we can begin playing on it.

## Hints

- Try to read the `man`ual page for SSH. 
- Try to search for `port` - `/` is use to search in the man pages.

- Still unable to get it to work ? Did you try reading the Helpful Reading materials specially the Wiki Article ?

<details>
  <summary> Level 0 - Solution </summary>
  
  #### Commands to Execute
  ```bash
  ssh -p 2220 bandit0@bandit.labs.overthewire.org
  ```
#### Explanation

Where we are supplying the custom port 2220 instead of the default port of `21` for ssh to work. 

</details>


# Level 0 -> Level 1

Can you read normal files in Linux ?

## Hints

- Try to read the `man`ual page for cat. 
  - `man cat`
- Try to use cat with the filename 

<details>
  <summary> Solution </summary>
  
  #### Commands to Execute
  ```bash
  cat readme
  ```
#### Explanation

Where we are reading the file and displaying its content in the Standard Out or in the screen.
</details>

# Level 1 -> Level 2

Can you read "-" files in Linux ?

## Hints

- Googling the "dashed-filename" does help.
- `man cat`

  ```
  Concatenate FILE(s) to standard output.

       With no FILE, or when FILE is -, read standard input.
  ```

  This means that its reading Standard Input for cat. 

<details>
  <summary> Solution </summary>
  
  Any of the following commands are valid.
  #### Commands to Execute
  ```bash
  cat ./-
  ```
#### Explanation
To tell the cat command that "-" here refers to a file we use a relative reference from ./ - which is the current directory.
</details>

