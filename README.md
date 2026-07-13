# Frontend Code Review Skill

_(ภาษาไทยอยู่ด้านล่าง / Thai version below)_

**Author:** Jammyjam · **Created:** July 14, 2026

A code review skill for our team, built from our own "Frontend convention" doc plus general engineering best practice. It works across every stack we run: **React + Next.js**, **React + TypeScript** (no Next.js), **Node.js** backend/API code, and **React + TanStack** (Query/Router/Table).

## What it checks

- **Our documented conventions**: git branch/commit naming, folder structure (Atomic Design), import sorting/grouping, naming conventions, table text-alignment
- **General best practice**: performance, re-renders, state management, accessibility, security, testing
- **Stack-specific rules** for whichever framework the code under review actually uses

## Installation

You'll need this wherever you talk to Claude from — Claude.ai or Claude Code.

- **Claude.ai** needs the packaged `.skill` file — download it from this repo's Releases
- **Claude Code** works straight from a git clone — no packaging needed

### Claude.ai (web, desktop, mobile)

1. Go to this repo's **Releases** page and download `frontend-code-review.skill` from the latest release.
2. Make sure Skills are enabled:
   - **Free / Pro / Max**: go to **Settings > Capabilities** and turn on **Code execution and file creation**
   - **Team / Enterprise**: go to **Organization settings > Skills** and turn on both **Code execution and file creation** and **Skills** — an org owner can also provision it for the whole org at once, so no one has to install it individually
3. Go to **Customize > Skills** and upload the `.skill` file.
4. Toggle it on.

### Claude Code

Skills live in a `.claude/skills/` folder — either **personal** (`~/.claude/skills/`, available in every project on your machine) or **project-level** (`.claude/skills/` inside a repo, shared with anyone who clones it).

```bash
git clone <this-repo-url>

# personal install — symlink so `git pull` here keeps it up to date everywhere
ln -s "$(pwd)/frontend-code-review" ~/.claude/skills/frontend-code-review

# OR project install — inside whichever project you want it active in
mkdir -p /path/to/your-project/.claude/skills
ln -s "$(pwd)/frontend-code-review" /path/to/your-project/.claude/skills/frontend-code-review
```

Restart your Claude Code session afterward. Claude picks the skill up automatically based on its `SKILL.md` description — no need to invoke it by name.

## How to use it

Just talk to Claude normally — you don't need to name the skill.

- Paste a component/file and say **"review this"**, or ask something like _"does this hook look okay?"_
- Point Claude at a file, diff, or PR description
- Ask about a whole PR — it'll review file by file (or one combined table if there are many small files)
- Ask in **Thai or English** — the whole review comes back in whichever language you used (see [Language support](#language-support))

If you haven't shared any code yet, Claude will ask you to paste it or point to the file — it won't guess.

## What the feedback looks like

Every review gives you three things, in this order:

1. **✅ What you did well** — specific, genuine callouts, not generic praise
2. **Review table** — every rule checked, Pass/Fail, with a severity per failing row
3. **🔧 How to improve** — a prioritized, actionable list (highest-impact fix first)

...plus a one-line summary: how many blockers, how many suggestions, and whether it's mergeable as-is.

### Severity — what's a blocker vs. a suggestion

|              | Blocker 🔴 — must fix before merge                                                                                                         | Suggestion 🟡 — flag it, project decides                                                                                                                              |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Includes** | Folder/structure violations, actual bugs, performance-killing patterns, poor state management, bad re-renders, exploitable security issues | Naming conventions, git conventions (branch/commit/squash), formatting/lint, code duplication, reusable-code opportunities, deep nesting, accessibility, testing gaps |

**Why naming and git conventions are suggestions, not blockers:** different projects on our team have their own valid reasons to deviate from the standard (legacy code, client constraints, historical decisions). The skill will always flag a deviation clearly, but it won't tell you a PR is blocked over a branch name or an import-casing choice.

## Language support

Our own docs mix English and Thai, so the skill is built to do the same — write in whichever language you use, and it responds in kind, in natural phrasing (not a literal machine translation). You can also explicitly ask for the other language regardless of what you wrote in. Code, file paths, and branch/commit names always stay exactly as written, in every language.

## Updating this skill

The rules live in `SKILL.md` and the `references/` folder — `team-conventions.md`, `general-best-practices.md`, and one file per stack. If a convention changes, or a project genuinely needs an exception, update the relevant file rather than arguing with Claude about it mid-review.

---

# สกิลรีวิวโค้ด Frontend (ภาษาไทย)

**ผู้จัดทำ:** Jammyjam · **วันที่จัดทำ:** 14 กรกฎาคม 2026

สกิลนี้สร้างจากเอกสาร "Frontend convention" ของทีมเรา รวมกับหลักปฏิบัติที่ดีทั่วไปในวงการ ใช้ได้กับทุก stack ที่ทีมเราใช้: **React + Next.js**, **React + TypeScript** (ไม่มี Next.js), **Node.js** (backend/API) และ **React + TanStack** (Query/Router/Table)

## สิ่งที่สกิลนี้ตรวจสอบ

- **Convention ที่ทีมเรากำหนดไว้**: การตั้งชื่อ branch/commit, โครงสร้างโฟลเดอร์ (Atomic Design), การเรียง import, การตั้งชื่อไฟล์/ตัวแปร, การจัด align ของตาราง
- **หลักปฏิบัติที่ดีทั่วไป**: performance, re-render, state management, accessibility, security, testing
- **กฎเฉพาะของแต่ละ stack** ตามเทคโนโลยีที่ใช้ในโค้ดที่รีวิว

## การติดตั้ง

ต้องติดตั้งบนช่องทางที่คุณใช้ Claude อยู่ — Claude.ai หรือ Claude Code

- **Claude.ai** ต้องใช้ไฟล์ `.skill` ที่แพ็คไว้แล้ว — ดาวน์โหลดจาก Releases ของ repo นี้
- **Claude Code** ใช้ได้เลยจาก git clone ตรงๆ ไม่ต้องแพ็คไฟล์

### Claude.ai (เว็บ, desktop, มือถือ)

1. ไปที่หน้า **Releases** ของ repo นี้ แล้วดาวน์โหลด `frontend-code-review.skill` จาก release ล่าสุด
2. เช็คว่าเปิดใช้งาน Skills แล้ว:
   - แผน **Free / Pro / Max**: ไปที่ **Settings > Capabilities** แล้วเปิด **Code execution and file creation**
   - แผน **Team / Enterprise**: ไปที่ **Organization settings > Skills** แล้วเปิดทั้ง **Code execution and file creation** และ **Skills** — owner ขององค์กรสามารถ provision ให้ทุกคนพร้อมกันได้เลย ไม่ต้องให้แต่ละคนติดตั้งเอง
3. ไปที่ **Customize > Skills** แล้วอัปโหลดไฟล์ `.skill`
4. เปิด toggle ให้ใช้งานได้

### Claude Code

Skill จะอยู่ในโฟลเดอร์ `.claude/skills/` — จะเป็นแบบ **ส่วนตัว** (`~/.claude/skills/` ใช้ได้ทุกโปรเจกต์ในเครื่อง) หรือแบบ **project** (`.claude/skills/` ใน repo แชร์ให้ทุกคนที่ clone repo นี้ไปด้วย)

```bash
git clone <ลิงก์ repo นี้>

# ติดตั้งแบบส่วนตัว — symlink ไว้ พอ git pull ทีหลังจะอัปเดตทุกที่ที่ใช้ทันที
ln -s "$(pwd)/frontend-code-review" ~/.claude/skills/frontend-code-review

# หรือติดตั้งแบบ project — ในโปรเจกต์ที่อยากให้ใช้งานได้
mkdir -p /path/to/your-project/.claude/skills
ln -s "$(pwd)/frontend-code-review" /path/to/your-project/.claude/skills/frontend-code-review
```

หลังจากนั้น restart session ของ Claude Code — Claude จะรู้จักและเรียกใช้ skill นี้เองอัตโนมัติตามคำอธิบายใน `SKILL.md` ไม่ต้องเรียกชื่อ skill เอง

## วิธีใช้งาน

คุยกับ Claude ตามปกติได้เลย ไม่ต้องเรียกชื่อสกิล

- วางโค้ด component/ไฟล์ แล้วพิมพ์ **"ช่วยรีวิวโค้ดนี้หน่อย"** หรือถามแบบ _"hook อันนี้โอเคไหม"_
- ชี้ไปที่ไฟล์, diff, หรือคำอธิบาย PR
- ถามเกี่ยวกับทั้ง PR ได้เลย — Claude จะรีวิวทีละไฟล์ (หรือรวมเป็นตารางเดียวถ้ามีไฟล์เล็กๆ เยอะ)
- ถามเป็น **ภาษาไทยหรือภาษาอังกฤษ** ก็ได้ — รีวิวทั้งหมดจะตอบกลับเป็นภาษาเดียวกับที่คุณใช้

ถ้ายังไม่ได้แชร์โค้ดมา Claude จะขอให้คุณวางโค้ดหรือชี้ไฟล์มาก่อน จะไม่เดาเอาเอง

## หน้าตาของ feedback

ทุกรีวิวจะให้ 3 ส่วนนี้ตามลำดับ:

1. **✅ สิ่งที่ทำได้ดี** — ชมแบบเจาะจง อ้างอิงจากโค้ดจริง ไม่ใช่คำชมทั่วไป
2. **ตารางรีวิว** — ทุกกฎที่ตรวจสอบ ผ่าน/ไม่ผ่าน พร้อมระดับความสำคัญของแต่ละข้อที่ไม่ผ่าน
3. **🔧 วิธีปรับปรุง** — รายการเรียงตามความสำคัญ (แก้ข้อที่กระทบเยอะสุดก่อน)

พร้อมสรุปท้ายบรรทัดเดียว: มี blocker กี่ข้อ, suggestion กี่ข้อ และ merge ได้เลยไหม

### ระดับความสำคัญ — อะไรคือ blocker อะไรคือ suggestion

|            | Blocker 🔴 — ต้องแก้ก่อน merge                                                                                    | Suggestion 🟡 — แจ้งไว้ ให้ทีม/โปรเจกต์ตัดสินใจ                                                                                                                |
| ---------- | ----------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **รวมถึง** | โครงสร้างผิด, บั๊กจริง, โค้ดที่ทำลาย performance, state management ไม่ดี, re-render แย่, security ที่โจมตีได้จริง | naming convention, git convention (branch/commit/squash), format/lint, โค้ดซ้ำ, โอกาสทำ reusable code, if ซ้อนลึกเกินไป, accessibility, ช่องว่างเรื่อง testing |

**ทำไม naming กับ git convention ถึงเป็นแค่ suggestion ไม่ใช่ blocker:** แต่ละโปรเจกต์ในทีมเรามีเหตุผลของตัวเองที่อาจต่างจากมาตรฐาน (โค้ดเก่า, ข้อจำกัดจากลูกค้า, การตัดสินใจในอดีต) สกิลนี้จะแจ้งเตือนทุกครั้งที่เจอความต่าง แต่จะไม่บอกว่า PR ถูกบล็อกเพราะแค่ชื่อ branch หรือการตั้งชื่อตัวแปร

## รองรับภาษาไทย

เอกสารของทีมเรามีทั้งภาษาอังกฤษและไทยปนกัน สกิลนี้เลยถูกออกแบบให้ทำแบบเดียวกัน — ใช้ภาษาไหนถาม ก็ได้คำตอบเป็นภาษานั้น เขียนแบบธรรมชาติ ไม่ใช่แปลตรงตัว จะขอให้ตอบอีกภาษาก็ได้เหมือนกัน ส่วนโค้ด, path ของไฟล์, ชื่อ branch/commit จะคงเดิมเป็นภาษาอังกฤษเสมอไม่ว่าจะรีวิวเป็นภาษาไหน

## การอัปเดตสกิลนี้

กฎทั้งหมดอยู่ใน `SKILL.md` และโฟลเดอร์ `references/` — `team-conventions.md`, `general-best-practices.md` และไฟล์แยกตาม stack ถ้า convention เปลี่ยน หรือโปรเจกต์ไหนต้องการข้อยกเว้นจริงๆ ให้แก้ไฟล์ที่เกี่ยวข้องแทนการโต้แย้งกับ Claude กลางรีวิว
