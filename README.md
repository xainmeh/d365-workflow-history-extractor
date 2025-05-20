# d365-workflow-history-extractor
A tool to extract and summarize workflow history from D365 Finance &amp; Operations for audits and reporting.
## Key Features
- Extract workflow status, step details, approvals, and comments
- Display history in a human-readable format (table view or PDF summary)
- Works for purchase orders, vendor approvals, and custom workflows
- Functional trigger for Power Automate integration or Excel export
- Configurable filters for date, document type, or user

---

## Tech Stack
| Component        | Purpose                                |
|------------------|----------------------------------------|
| X++              | Extract workflow history from D365 F&O |
| Power Automate   | Trigger export and email PDF logs      |
| Power BI / Excel | Display logs in readable format        |
| Logic Apps       | Optional transformation and routing    |

---

## Functional Use Case
**Scenario**: An internal auditor wants to review the full approval cycle of a purchase order.

**Steps:**
1. The auditor opens the form (or clicks an embedded button on the PO form)
2. A Power Automate flow is triggered that:
   - Retrieves the workflow tracking data via OData/Custom API
   - Formats it into a timeline-style table (with usernames, timestamps, outcomes)
   - Emails or stores it in SharePoint or OneDrive
3. Auditor reviews a PDF summary showing:
   - "Requested by: X on 12-Apr"
   - "Approved by Y on 14-Apr (2 days delay)"
   - "Rejected by Z on 15-Apr with comment: 'Incorrect pricing'"

---
## 🧑‍💻 Sample X++ Snippet
```x++
public static void extractWorkflowHistory(RecId _recId)
{
    WorkflowTrackingStatusTable trackingStatus;
    WorkflowTrackingTable trackingTable;

    while select trackingStatus
        where trackingStatus.ContextRecId == _recId
        join trackingTable
        where trackingTable.TrackingStatusId == trackingStatus.TrackingStatusId
    {
        info(strFmt("Step: %1 | Action: %2 | Performed by: %3 | Date: %4",
            trackingTable.Name,
            trackingStatus.ActionType,
            trackingStatus.User,
            trackingStatus.CreatedDateTime));
    }
}
```

---

## 🔁 Sample Power Automate Use Case
**Flow Name:** "Export Workflow History to PDF"

**Trigger:** When a user clicks a Power Apps button on a workflow-enabled form

**Steps:**
1. Use **HTTP with Azure AD** connector to call a custom D365 API (or OData query)
2. Parse the response JSON to extract step-level data
3. Format it in **HTML table format**
4. Convert HTML to PDF using **OneDrive or Encodian connector**
5. Email the PDF to requester or archive in SharePoint

## Repository Structure
```
/d365-workflow-history-extractor
├── src               # X++ classes and extension files
├── flows             # Power Automate templates
├── docs              # Screenshots, examples
├── exports           # Excel/Power BI templates
├── README.md
```

---

## How to Contribute
1. Fork the repo
2. Submit fixes for new modules (e.g., customer workflows, vendor approvals)
3. Improve formatting options (add HTML or PDF templates)
4. Add local language support or accessibility improvements

---

## 🤝 License
MIT License — Free for commercial and personal use

---

## 📌 Disclaimer
This is a community-driven tool and is not affiliated with or officially supported by Microsoft. Use responsibly with appropriate security and data governance policies in production environments.
