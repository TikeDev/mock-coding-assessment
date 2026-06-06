# What is this repo, and why? 
This repo holds agent skills I created to spin up mock assessment environments, getting you familiarized before the real thing!  
  
I recently had my first online coding assessment ever (CodeSignal ICA) and I was preparing by grinding DSA/leetcode style problems. As I learned more about the framework, I realized that this approach wouldn't help me at all.  
💡 I then had the idea to give all the info I gathered and have it spin up a 4-level problem for me in a .zip file that I could easily open in my IDE, install dependencies, and just start.  
_It worked great!!_ Without it, I would've been stuck like a deer in headlights JUST from having to naviagate the unfamiliar environment and format.   


# Skills
## [codesignal-ica-mock](https://github.com/TikeDev/mock-coding-assessment/tree/main/skills/codesignal-ica-mock/codesignal-ica-mock-skill)  
Agent skill that spins up a mock CodeSignal Industry Coding Assessment (ICA) environment with 4 progressive levels, realistic problem specs, stub files, and failing unit tests. Outputs everything in a single .zip file that you can extract and open in your preferred IDE.

## How to Use
The skill automatically triggers when you says things like "set up a practice assessment", "give me a coding challenge with multiple levels", or "help me prep for a CodeSignal test". It'll ask you a few questions about the challenge before generating.  
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

#### General File Structure

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
