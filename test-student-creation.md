# Testing Student Assignment Submission Workflow

## Overview
This document outlines the testing process for student account creation and assignment submission functionality in Cirriculux.

## Prerequisites
1. Plone backend running on `http://localhost:8080/Plone`
2. Frontend running on `http://localhost:3000`
3. Admin user logged in to create classes and assignments

## Test Steps

### 1. Create Test Class and Assignment (As Teacher/Admin)

1. **Login as admin** to the frontend
2. **Create a test class:**
   - Name: "Test Chemistry Lab"
   - Teacher: "Dr. Smith"
   - Subject: "Chemistry"
   - Grade Level: "10"

3. **Create a test assignment:**
   - Class: "Test Chemistry Lab"
   - Title: "Chemical Reactions Lab Report"
   - Description: "Write a lab report analyzing the chemical reactions observed in today's experiment"
   - Due Date: Set to tomorrow
   - Points: 100
   - Instructions: "Include methodology, observations, and conclusions"

### 2. Create Test Student Account

1. **Navigate to Students section**
2. **Click "Create Student"**
3. **Fill in student details:**
   - Name: `Test Student`
   - Email: `teststudent@example.com`
   - Username: `teststudent`
   - Password: `TestPass123!`
   - Class: Select "Test Chemistry Lab"
   - Grade Level: "10"

4. **Submit and verify account creation**

### 3. Test Assignment Submission (As Student)

1. **Logout and login as the test student:**
   - Username: `teststudent`
   - Password: `TestPass123!`

2. **Navigate to "My Assignments"**
3. **Verify the test assignment appears**
4. **Click "Submit" on the assignment**
5. **In the submission dialog:**
   - Add text content: "This is my lab report submission. I observed that..."
   - Upload a test file (create a simple .txt or .pdf file)
   - Click "Submit Assignment"

6. **Verify submission success:**
   - Should show success message
   - Assignment status should change to "Submitted"
   - Should appear in "Submitted" tab

### 4. Verify Backend Storage

1. **Check Plone backend structure:**
   ```
   /classes/test-chemistry-lab/assignments/chemical-reactions-lab-report/submissions/
   └── teststudent-[timestamp]/
       ├── submission (Document with text content)
       └── attachments/
           └── [uploaded file]
   ```

2. **Login to Plone admin interface** (`http://localhost:8080/Plone/@@overview-controlpanel`)
3. **Navigate to:** Site Setup → Content
4. **Browse to:** `/classes/test-chemistry-lab/assignments/chemical-reactions-lab-report/submissions/`
5. **Verify submission folder exists** with student ID and timestamp

### 5. Test File Storage

1. **Check local storage location:**
   ```bash
   cd buildout.coredev/var/blobstorage
   ls -la
   ```

2. **Verify blob files exist** for uploaded attachments
3. **Check file sizes** match uploaded files

### 6. Test Assignment View (As Teacher)

1. **Login as teacher/admin**
2. **Navigate to Assignments view**
3. **Click on the test assignment**
4. **Verify submission appears** in assignment details
5. **Check submission content and attachments**

## Expected Results

✅ **Student Creation:**
- Student account created successfully
- Student can login with provided credentials
- Student appears in class roster

✅ **Assignment Submission:**
- Student can see assignments for their enrolled classes
- Submission dialog opens correctly
- File upload works (drag-and-drop and click to select)
- Text content is saved
- Submission status updates immediately
- Success message displays

✅ **Backend Storage:**
- Submission folder created with correct structure
- Text content stored in submission document
- Files uploaded to attachments folder
- Proper permissions set (student can read, teacher can grade)

✅ **File Storage:**
- Files stored in Plone's blob storage
- File integrity maintained
- No data corruption

## Troubleshooting

### Common Issues:

1. **"Access denied: Student role required"**
   - Check user authentication
   - Verify student role assignment
   - Check security context initialization

2. **"Failed to submit assignment"**
   - Check Plone backend connectivity
   - Verify API endpoints are working
   - Check browser console for errors

3. **File upload fails**
   - Check file size limits (10MB default)
   - Verify file type restrictions
   - Check backend file upload permissions

4. **Assignment doesn't appear**
   - Verify student is enrolled in correct class
   - Check assignment due date
   - Verify getStudentAssignments API call

### Debugging Commands:

```bash
# Check Plone backend logs
cd buildout.coredev
tail -f var/log/instance.log

# Check storage usage
du -sh var/blobstorage/
du -sh var/filestorage/

# Verify class structure in Plone
curl -H "Authorization: Bearer <token>" \
  http://localhost:8080/Plone/classes/test-chemistry-lab/assignments
```

## Clean Up

After testing, clean up test data:

1. **Delete test student account** (as admin)
2. **Delete test assignment**
3. **Delete test class**
4. **Clean up any uploaded test files**

## Next Steps

Once assignment submission is working:

1. ✅ **Enhanced UX:** Add upload progress bars
2. ✅ **Validation:** Improve client-side file validation
3. 🔄 **Grading:** Implement teacher grading interface
4. 🔄 **Analytics:** Add submission analytics and reporting
5. 🔄 **Notifications:** Email notifications for submissions
6. 🔄 **S3 Migration:** Plan for production file storage 