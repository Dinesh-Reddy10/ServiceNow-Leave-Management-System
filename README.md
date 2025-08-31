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

<img width="1600" height="447" alt="Screenshot 2025-08-30 173728" src="https://github.com/user-attachments/assets/00cabfb8-c36c-44c4-ae43-93d8363d5eb9" />


**🏗️ System Design**
Tables

u_leave_database → Stores leave balances per user (Casual, Medical, Emergency)

u_leave_management → Stores leave requests (User, Type, Dates, Status, etc.)

**Core Components**
**Client Script (onLoad)**
function onLoad() {
   var ga = new GlideAjax('LeaveBalanceUtils');
   ga.addParam('sysparm_name', 'getLeaveBalance');
   ga.getXMLAnswer(function(response){
      g_form.addInfoMessage("Your Leave Balance: " + response);
   });
}

**Client Script (onChange – Dates)**
function onChange(control, oldValue, newValue) {
   var sdate = g_form.getValue('u_start_date');
   var edate = g_form.getValue('u_end_date');

   if (sdate && edate && sdate > edate) {
      alert("End Date cannot be before Start Date");
      g_form.clearValue('u_end_date');
      return;
   }

   var days = gs.dateDiff(sdate, edate, true) + 1;
   g_form.setValue('u_days', days);
}

**Business Rule (Before Update → State Approved)**
if (current.state.changesTo('approved')) {
   var leaveType = current.u_leave_type;
   var days = current.u_days;

   var balance = new GlideRecord('u_leave_database');
   balance.addQuery('u_user', current.u_user);
   balance.query();

   if (balance.next()) {
      var available = balance.getValue('u_' + leaveType.toLowerCase());
      if (days > available) {
         gs.addErrorMessage("Insufficient leave balance for " + leaveType);
         current.setAbortAction(true);
      } else {
         balance['u_' + leaveType.toLowerCase()] = available - days;
         balance.update();
      }
   }
}

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

