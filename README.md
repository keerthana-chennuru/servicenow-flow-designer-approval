# 🔄 Custom Approval Action in Flow Designer

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&pause=1000&color=6A9FBF&width=600&lines=Custom+Flow+Designer+Action;Approval+Automation+%7C+ServiceNow;Reusable+Action+%7C+Global+Scope" alt="Typing SVG" />

![Platform](https://img.shields.io/badge/Platform-ServiceNow-green?style=for-the-badge&logo=servicenow)
![Scope](https://img.shields.io/badge/Scope-Global-blue?style=for-the-badge)
![Type](https://img.shields.io/badge/Type-Flow%20Automation-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen?style=for-the-badge)

---

## 📌 Overview

Built a custom, reusable **Flow Designer Action** that processes approval records in ServiceNow. The action accepts an approval record's `sys_id` as input, looks up the approval details, updates the approval state dynamically, and returns the approver name and approval state as outputs — ready to be used in any parent flow or automation.

---

## 🎯 Objectives

- ✅ Build a reusable Flow Designer action for processing approvals
- ✅ Accept an approval record `sys_id` and a decision (Approved/Rejected) as inputs
- ✅ Look up the approval record from `sysapproval_approver` table
- ✅ Update the approval state dynamically based on the input
- ✅ Extract and return approver name and approval state as structured outputs
- ✅ Validate the action end-to-end with a live Change Request approval scenario

---

## 🏗️ Action Design

| Property | Value |
|---|---|
| Action Name | `Approve/Reject` |
| Application | Global |
| Table | Approval (`sysapproval_approver`) |

---

## 📋 Step-by-Step Implementation

### Step 1 — Define Inputs

| Input Name | Type | Purpose |
|---|---|---|
| `record_sys_id` | String | Uniquely identifies which approval record to process |
| `remarks` | Choice | The approval decision — mapped to State field of `sysapproval_approver` |

> 💡 The `remarks` input uses **Reference existing choice list** on:
> - Choice table: `Approval [sysapproval_approver]`
> - Choice field: `State`

---

### Step 2 — Look Up Record

- **Table:** Approval (`sysapproval_approver`)
- **Condition:** `Approval for.Sys ID` is `action > Record SysID` AND `State` is `Requested`
- **Sort:** Created, A to Z
- **If multiple records:** Return only the first record

---

### Step 3 — Update Record

- **Record:** Step > Approval Record (from Look Up step)
- **Table:** Approval (`sysapproval_approver`)
- **Field:** `State` = `remarks` input value

---

### Step 4 — Script Step – Extract Output Values

```javascript
(function execute(inputs, outputs) {
    var approvalRecord = inputs.ApprovalRecord;
    outputs.approver_name = approvalRecord.approver.getDisplayValue();
    outputs.approver_state = approvalRecord.state.getDisplayValue();
})(inputs, outputs);
```

> 💡 `getDisplayValue()` returns human-readable names instead of raw `sys_ids`.

---

### Step 5 — Define Outputs

| Output Name | Type | Description |
|---|---|---|
| `approver_name` | String | Full display name of the approver |
| `approver_state` | String | Current state of the approval (Approved/Rejected/Pending) |

---

## 🧪 Testing

Tested using a real Change Request **CHG0000086** in the **Authorize** stage:

| | Value |
|---|---|
| **Record SysID** | `5ecdb552db252200a6a2b31be0b8f5b5` |
| **Remark** | Approved |
| **Approver Name** | Christen Mitchell |
| **Approver State** | Approved |
| **Action Status** | `{"code":0,"message":"Success"}` |

> ✅ Change Request moved from **Authorize → Scheduled** stage automatically after approval!

---

## ⚙️ Technical Details

| Component | Detail |
|---|---|
| Action Type | Custom Action |
| Trigger Table | `sysapproval_approver` |
| Look Up Step | Filters by `sys_id` and `State = Requested` |
| Update Step | Sets `State` field dynamically from input |
| Script Step | Extracts approver name and state using `getDisplayValue()` |
| Output Mapping | `approver_name`, `approver_state` |

---

## ✅ Skills Demonstrated

![Flow Designer](https://img.shields.io/badge/Flow%20Designer-Custom%20Action-green?style=flat-square)
![Scripting](https://img.shields.io/badge/Scripting-getDisplayValue()-blue?style=flat-square)
![Testing](https://img.shields.io/badge/Testing-End--to--End-orange?style=flat-square)

- 🔧 Flow Designer — Custom Action creation
- 📥 Input/Output variable definition and mapping
- 🔍 Look Up Record step configuration
- ✏️ Update Record step with dynamic field values
- 📝 Script step for data extraction
- 🔤 `getDisplayValue()` usage for readable outputs
- 🧪 Action testing and execution validation
- ♻️ Reusable action design patterns

---

## ⚠️ Common Mistakes to Avoid

| ❌ Mistake | ✅ Best Practice |
|---|---|
| Using Output **Label** instead of **Name** in script outputs | Always use the `Name` field — it's case-sensitive |
| Case mismatch in output variable names | Match exactly: `approver_name` not `approverName` |
| Changing output variable names after writing the script | Define names first, then write the script |
| Not validating null values before accessing record fields | Always check for null before accessing fields |
| Small naming mismatches in variable binding | Flow Designer enforces strict variable binding |

---

## 💡 Key Learning

> In Flow Designer, output mapping is strictly tied to the **Name** field, not the Label. Even a small naming mismatch breaks the output. **Consistent naming is critical** when working with Script steps and Action outputs.

---


<div align="center">

**Made with ❤️ by Keerthana Chennuru**

![ServiceNow](https://img.shields.io/badge/Built%20on-ServiceNow-green?style=for-the-badge)

</div>
