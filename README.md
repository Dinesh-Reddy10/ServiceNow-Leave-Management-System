**ServiceNow Leave Management System**

A custom-built ServiceNow application for managing employee leave requests with automated validation, approval, and leave balance tracking.

**🔎 Introduction**

This project demonstrates how to design and implement a Leave Management System on ServiceNow from scratch.

It provides:

Real-time leave balance visibility

Automated validation of requests

Deduction of leave balances only after approval

**📷 Screenshots**
1️⃣ Leave Application Form with Balance Info

2️⃣ Date Validation in Action

3️⃣ Leave Deduction after Approval

<img width="1600" height="447" alt="Screenshot 2025-08-30 173728" src="https://github.com/user-attachments/assets/1b3f63b2-7763-48fd-868f-0675125d7b00" />
<img width="1858" height="759" alt="Screenshot 2025-08-31 112801" src="https://github.com/user-attachments/assets/800f54bd-bec4-4ce0-8595-697b6e53666c" />
<img width="1903" height="776" alt="Screenshot 2025-08-31 112814" src="https://github.com/user-attachments/assets/4045b6d0-e0e4-4831-845b-d1f0e5777b03" />
<img width="587" height="475" alt="Screenshot 2025-08-31 121405" src="https://github.com/user-attachments/assets/d9caad31-5a33-486b-9caf-a5b6c5913033" />
<img width="1909" height="710" alt="Screenshot 2025-08-31 121731" src="https://github.com/user-attachments/assets/4f9d524c-1e0d-4b19-80ac-8bbadb5284f5" />
<img width="1913" height="837" alt="Screenshot 2025-08-31 121751" src="https://github.com/user-attachments/assets/98829ef4-3e27-44b2-93fd-cda6d4fccf5a" />
<img width="1916" height="886" alt="Screenshot 2025-08-31 121812" src="https://github.com/user-attachments/assets/ec9c2307-bdd0-4b13-8814-e61f8128689b" />
<img width="1913" height="710" alt="Screenshot 2025-08-31 121838" src="https://github.com/user-attachments/assets/0a08677f-88ab-4ef0-a778-5000a8891eb4" />


**🏗️ System Design**
Tables

u_leave_database → Stores leave balances per user (Casual, Medical, Emergency)

u_leave_management → Stores leave requests (User, Type, Dates, Status, etc.)

**Core Components**<br>
**Client Script (onLoad)**<br>
function onLoad() {<br>
   var ga = new GlideAjax('LeaveBalanceUtils');<br>
   ga.addParam('sysparm_name', 'getLeaveBalance');<br>
   ga.getXMLAnswer(function(response){<br>
      g_form.addInfoMessage("Your Leave Balance: " + response);<br>
   });<br>
}<br>

**Client Script (onChange – Dates)**<br>
function onChange(control, oldValue, newValue) {<br>
   var sdate = g_form.getValue('u_start_date');<br>
   var edate = g_form.getValue('u_end_date');<br>

   if (sdate && edate && sdate > edate) {<br>
      alert("End Date cannot be before Start Date");<br>
      g_form.clearValue('u_end_date');<br>
      return;<br>
   }<br>

   var days = gs.dateDiff(sdate, edate, true) + 1;<br>
   g_form.setValue('u_days', days);<br>
}<br>

**Business Rule (Before Update → State Approved)**<br>
if (current.state.changesTo('approved')) {<br>
   var leaveType = current.u_leave_type;<br>
   var days = current.u_days;<br>

   var balance = new GlideRecord('u_leave_database');<br>
   balance.addQuery('u_user', current.u_user);<br>
   balance.query();<br>

   if (balance.next()) {<br>
      var available = balance.getValue('u_' + leaveType.toLowerCase());<br>
      if (days > available) {<br>
         gs.addErrorMessage("Insufficient leave balance for " + leaveType);<br>
         current.setAbortAction(true);<br>
      } else {<br>
         balance['u_' + leaveType.toLowerCase()] = available - days;<br>
         balance.update();<br>
      }<br>
   }<br>
}<br>

**🔄 Workflow**

New Request → User opens Apply for Leave → Balance shown

Submit → Request goes into New state

Manager Approval →

Approved → Deduction happens

Rejected/Insufficient Balance → Blocked with error message

**✅ Features**

Real-Time Balance Display

Date Validation

Automatic Day Calculation

Leave Deduction only after Approval

Error Messages for Insufficient Balance

**🚀 Future Enhancements**

Multi-level approval workflows

Reports & dashboards for HR

Email notifications

Integration with HRSD



**🎥 Demo Tested Video link**

(https://drive.google.com/file/d/11iwPSYsukRMOyYFqgL4dkFBe6CbVV96C/view?usp=sharing)

