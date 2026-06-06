# What is this repo, and why? 
This repo holds agent skills I created to spin up mock assessment environments, getting you familiarized before the real thing!  
  
I recently had my first online coding assessment ever (a 90-min [CodeSignal ICA](https://support.codesignal.com/hc/en-us/articles/19116922232983-What-are-the-Industry-Coding-Assessment-ICA-rules)) and I was preparing by grinding DSA/leetcode style problems. As I learned more about the framework, I realized that this approach wouldn't help me at all.  
💡 I then had the idea to give all the info I gathered and have it spin up a 4-level problem for me in a .zip file that I could easily open in my IDE, install dependencies, and just start.  
_It worked great!!_ Without it, I would've been stuck like a deer in headlights JUST from having to naviagate the unfamiliar environment and format.   


# Skills
## [codesignal-ica-mock](https://github.com/TikeDev/mock-coding-assessment/tree/main/skills/codesignal-ica-mock/codesignal-ica-mock-skill)  
Agent skill that spins up a mock CodeSignal Industry Coding Assessment (ICA) environment with 4 progressive levels, realistic problem specs, stub files, and failing unit tests. Outputs everything in a single .zip file that you can extract and open in your preferred IDE.

## How to Use
The skill automatically triggers when you says things like "set up a practice assessment", "give me a coding challenge with multiple levels", or "help me prep for a CodeSignal test".  It'll ask you a few questions about the challenge before generating.  
*_You can often manually invoke the skill by typing the slash command: `/codesignal-ica-mock`_

#### Use with claude.ai
- **download** the codesignal-ica-mock.skill file [here](https://github.com/TikeDev/mock-coding-assessment/tree/main/skills)
- go to the sidebar -> **Customize**
- click on **Skills** -> click on the **+** button
- **add** codesignal-ica-mock.skill file
#### Use with Claude Code and other coding agents: 
- download the [codesignal-ica-mock-skill](https://github.com/TikeDev/mock-coding-assessment/tree/main/skills/codesignal-ica-mock/codesignal-ica-mock-skill) folder
- on your computer, add the folder to your code agent's `skills` folder  
(often `~/<agent>/skills`, ex: `~/codex/skills`)  It should automatically be picked up.

### General File Structure

```
assessment/
├── README.md              ← Full problem spec
├── tests/
│   ├── level1Tests.js     ← Mocha tests, locked after passing
│   ├── level2Tests.js     ← Added when level 2 unlocks
│   └── sandboxTests.js    ← Empty file user can use for scratch testing
├── src/
│   └── solution.js        ← User's implementation file (blank stub)
├── package.json           ← Mocha test setup
└── run_single_test.sh     ← Shell script to run one test by name
```

### Example Usage

#### Generating the challenge
<img width="48%" alt="codesignal-ica-mock_screenshot1" src="https://github.com/user-attachments/assets/b5468092-8725-431e-8566-4cb95b51136e" />
<img width="48%" height="1462" alt="codesignal-ica-mock_screenshot2" src="https://github.com/user-attachments/assets/62105378-a7ce-42e5-99bc-c460e0120ecf" />

#### Level 1 open in IDE
<img width="48%" height="1748" alt="codesignal-ica-mock_screenshot3" src="https://github.com/user-attachments/assets/128c57a5-1168-4d77-b8a7-aa108346809b" />
<img width="48%" height="1748" alt="codesignal-ica-mock_screenshot4" src="https://github.com/user-attachments/assets/7a3a4b9b-25d5-4560-82a6-b55ba99b415c" />  

----
  
Please let me know if you have any feedback!
