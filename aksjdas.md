# 03 — How the findings were captured

**Case:** BJones | **Author:** Codex, methodology synthesis | **Created:** 2026-09-13 (America/Chicago) | **Evidence copy:** `EC-BJONES-ZIP-ORIGINAL`, run `2026-09-12T194700-0500`.

## Scope and safeguards

This document records how the completed findings were obtained, not a new examination. The original `BJones.zip` and E01 chain were hash-verified during preflight; the analysis used a staged, re-hashed `BJones.E01` and read-only exports. No suspect executable, batch file, installer, or document was executed. The evidence container was never mounted read/write or altered. Controlled exports and command logs remain under `case-work/2026-09-12T194700-0500/`.

## Evidence-to-finding method

| Finding family | Artifacts examined | Tools / process | Reasoning and controls |
|---|---|---|---|
| System identity, timezone, and accounts | `SAM`, `SOFTWARE`, `SYSTEM`, `SECURITY`; `Security.evtx` | Sleuth Kit `icat` exported the hives/logs; `regipy`, `hivex`, and an EVTX parser read copies. | SAM RID data and `ProfileList` independently tied SIDs 1000/1001 to Barry/Tomas. Security events supplied actor and UTC timestamps for account creation, Guest enablement, and logons. `SYSTEM` timezone data established MST; UTC/FILETIME values were normalized to EST for the companion timeline. |
| Browser/download activity | Barry/Tomas Chrome `History`; Firefox/Tor SQLite artifacts; supplied PDFs | `sqlite3 -readonly` queries on exported copies; visual PDF review. | Browser URLs/download rows establish visits/downloads, not execution or intent. Results were corroborated against filesystem creation times where available. No online accounts were accessed; DPAPI/NSS secrets were not decrypted. |
| Application installation/execution | `NTUSER.DAT` UserAssist, Prefetch `.pf`, uninstall registry keys, scheduled tasks, service/Run keys | Read-only exports; custom static UserAssist and Windows-7 Prefetch parsers; `hivexregedit`; direct XML reads. | Installation, file presence, and execution were kept separate. UserAssist established Explorer-launch counts (VeraCrypt and the 8 batch-script launches); Prefetch was a corroborating process artifact. Empty Run keys/services and missing execution traces were reported as bounded negatives, with cache-eviction limitations. |
| File ownership, timestamps, deletion, and shortcuts | NTFS MFT records for `Kate Jones.contact`, `Malware.hidden`, the Public batch files, `$I/$R` Recycle-Bin pair, and Tomas Recent `.lnk` files | Sleuth Kit `fls`, `istat`, and `icat`; static MS-SHLLINK parser; raw/hex inspection. | `$STANDARD_INFORMATION` security IDs were mapped through SAM rather than inferred from a path. Recycle Bin `$I` metadata directly supplied the original batch-file path, size, and deletion time. Shortcut embedded times were used as supporting records, with cached-name limitations explicitly preserved. |
| Disguised note and batch scripts | Controlled exports of `Kate Jones.contact`, `$R0YSOP0.bat`, and `oops.txt.bat` | `file`, `od`, `srch_strings`, `sha256sum`, `md5sum`, `cmp`, and `diff`. | Magic bytes and full content inspection showed that `.contact` is plain ASCII, not Contact XML, and that the two equal-sized batch files have different content. Scripts were inspected statically only; no command was run. |
| VeraCrypt outer volume | `Malware.hidden` exported copy | `file`, hashing, a from-scratch PBKDF2-HMAC-SHA512/AES-XTS/CRC header validator, and `cryptsetup 2.8.7 tcryptDump --veracrypt`. | Two independent header-validation paths proved the container type and that `Lunes123` opens the outer header. The decrypted content was produced userspace-only, without mounting, and then statically parsed; this prevents evidence execution and avoids altering the source. |
| Hidden-volume question | Same exported `Malware.hidden` copy, hidden-header slot | `cryptsetup tcryptDump --veracrypt --tcrypt-hidden --dump-master-key`, supplied passwords on standard input; `sha256sum`; logged transcripts. | Twenty limited, reasoned candidates were tried—not a dictionary attack. Each was a header-only, unprivileged operation against the export; all returned the same negative. A labeling issue in three initial batch attempts was caught, and those values were rerun individually before reporting. No inner volume was claimed because no valid hidden header/master key was obtained. |

## Thought process and decision boundaries

1. Start with provenance: verify hashes and work only from the staged E01/export copies.
2. Establish identity before attribution: resolve account SIDs from SAM/ProfileList, then apply those mappings to NTFS owners, Recycle Bins, and user hives.
3. Establish time basis before chronology: use source UTC/FILETIME data; account for the source MST configuration; publish the requested EST conversion.
4. Separate observation from inference: for example, the evidence proves Tomas-owned creation of a note in Barry's profile and proves its deceptive format, but it cannot prove the note's authorial motive.
5. Separate presence, installation, and execution: a Tor download/extraction is not Tor use; an `oops.txt.bat` file is not evidence its delete command ran.
6. Validate exceptional claims with independent sources: VeraCrypt relied on both independent header parsing and `cryptsetup`; SID attribution relied on SAM plus ProfileList; repeated batch execution relied on UserAssist plus a Prefetch path reference.
7. Preserve negative findings and limitations: the hidden-volume test is a negative only for the tested passwords; Prefetch/UserAssist absence has known retention/coverage limitations.

## Reproduction records

- Hidden-volume candidate transcripts and authoritative method: [06-hiddenvolume-findings.md](/home/zak/personal/tory/bjones/case-work/2026-09-12T194700-0500/phase2/bjones-ioc-suspicious/shards/06-hiddenvolume-findings.md).
- IOC/static-content and VeraCrypt outer-volume analysis: [02e-ioc-and-deleted-artifacts.md](/home/zak/personal/tory/bjones/case-output/02e-ioc-and-deleted-artifacts.md).
- Account/SID/timezone foundation: [02a-system-and-accounts.md](/home/zak/personal/tory/bjones/case-output/02a-system-and-accounts.md).
- Execution/lifecycle basis: [02c-application-and-execution.md](/home/zak/personal/tory/bjones/case-output/02c-application-and-execution.md).
- Browser/download basis: [02d-browser-and-communications.md](/home/zak/personal/tory/bjones/case-output/02d-browser-and-communications.md).

The per-command ledger files preserve tool versions, exact argv, input/output paths, hashes, timestamps, and exit status: `case-work/2026-09-12T194700-0500/tool-logs/` and `phase2/bjones-ioc-suspicious/shards/logs/`.
