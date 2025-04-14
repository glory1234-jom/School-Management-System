<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Student Management System</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      margin: 0;
      padding: 0;
      background: #e8f0fe;
    }
    header {
      background: #3f51b5;
      color: white;
      padding: 1rem;
      text-align: center;
    }
    nav {
      display: flex;
      justify-content: space-around;
      background: #5c6bc0;
      flex-wrap: wrap;
    }
    nav button {
      background: #7986cb;
      color: white;
      border: none;
      padding: 1rem;
      cursor: pointer;
      font-weight: bold;
      flex: 1;
    }
    nav button:hover {
      background: #9fa8da;
    }
    .tab-content {
      display: none;
      padding: 2rem;
      background: white;
      margin: 1rem;
      border-radius: 10px;
      box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
    }
    .active {
      display: block;
    }
    form input, form select, form button {
      display: block;
      margin: 0.5rem 0;
      padding: 0.6rem;
      width: 100%;
      max-width: 400px;
      border-radius: 5px;
      border: 1px solid #ccc;
    }
    form button {
      background: #4caf50;
      color: white;
      border: none;
      cursor: pointer;
    }
    form button:hover {
      background: #66bb6a;
    }
    .message {
      margin-top: 1rem;
      padding: 0.7rem;
      background: #d4edda;
      color: #155724;
      border: 1px solid #c3e6cb;
      border-radius: 5px;
      display: none;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 1rem;
    }
    th, td {
      border: 1px solid #ddd;
      padding: 0.75rem;
      text-align: left;
    }
    th {
      background: #3f51b5;
      color: white;
    }
    .pay-btn, .edit-btn, .delete-btn, .delete-attendance-btn {
      background: #ff9800;
      color: white;
      border: none;
      padding: 0.5rem;
      border-radius: 5px;
      cursor: pointer;
    }
    .pay-btn:hover, .edit-btn:hover, .delete-btn:hover, .delete-attendance-btn:hover {
      background: #ffc107;
    }
    #attendanceList div {
      margin-bottom: 0.5rem;
    }
    select[name="attendanceStatus"] {
      width: 150px;
      display: inline-block;
      margin-left: 1rem;
    }
  </style>
</head>
<body>
  <header>
    <h1>Student Management System</h1>
  </header>

  <nav>
    <button onclick="showTab('register')">Register</button>
    <button onclick="showTab('view')">View Students</button>
    <button onclick="showTab('attendance')">Mark Attendance</button>
    <button onclick="showTab('analyze')">Analyze Attendance</button>
    <button onclick="showTab('edit')">Edit Student</button>
  </nav>

  <main>
    <!-- Register -->
    <section id="register" class="tab-content active">
      <h2>Register Student</h2>
      <form id="registerForm">
        <input name="name" type="text" placeholder="Name" required />
        <input name="age" type="number" placeholder="Age" required />
        <select name="gender" required>
          <option value="">Gender</option>
          <option value="M">Male</option>
          <option value="F">Female</option>
        </select>
        <input name="email" type="email" placeholder="Email" required />
        <input name="phone" type="text" placeholder="Phone Number" required />
        <select name="class" required>
          <option value="">Class</option>
          <option value="SS1">SS1</option>
          <option value="SS2">SS2</option>
          <option value="SS3">SS3</option>
        </select>
        <select name="arm" required>
          <option value="">Arm</option>
          <option value="Science">Science</option>
          <option value="Art">Art</option>
          <option value="Commercial">Commercial</option>
        </select>
        <button type="submit">Register</button>
      </form>
      <div class="message" id="registerMessage">Student has been registered successfully!</div>
    </section>

    <!-- View -->
    <section id="view" class="tab-content">
      <h2>All Students</h2>
      <table id="studentsTable">
        <thead>
          <tr>
            <th>Name</th><th>Class</th><th>Arm</th><th>Balance</th><th>Actions</th>
          </tr>
        </thead>
        <tbody></tbody>
      </table>
    </section>

    <!-- Mark Attendance -->
    <section id="attendance" class="tab-content">
      <h2>Mark Attendance</h2>
      <form id="attendanceForm">
        <div id="attendanceList"></div>
        <button type="submit">Save Attendance</button>
      </form>
    </section>

    <!-- Analyze Attendance -->
    <section id="analyze" class="tab-content">
      <h2>Analyze Attendance</h2>
      <div id="attendanceAnalysis"></div>
    </section>

    <!-- Edit Student -->
    <section id="edit" class="tab-content">
      <h2>Edit Student</h2>
      <select id="studentSelector"></select>
      <form id="editForm">
        <input name="name" type="text" placeholder="Name" required />
        <input name="age" type="number" placeholder="Age" required />
        <select name="gender" required>
          <option value="M">Male</option>
          <option value="F">Female</option>
        </select>
        <input name="email" type="email" placeholder="Email" required />
        <input name="phone" type="text" placeholder="Phone Number" required />
        <select name="class" required>
          <option value="SS1">SS1</option>
          <option value="SS2">SS2</option>
          <option value="SS3">SS3</option>
        </select>
        <select name="arm" required>
          <option value="Science">Science</option>
          <option value="Art">Art</option>
          <option value="Commercial">Commercial</option>
        </select>
        <button type="submit">Save Changes</button>
      </form>
    </section>
  </main>

  <script>
    function showTab(tabId) {
      document.querySelectorAll(".tab-content").forEach(tab => tab.classList.remove("active"));
      document.getElementById(tabId).classList.add("active");
      if (tabId === "view") renderStudents();
      if (tabId === "attendance") renderAttendanceForm();
      if (tabId === "analyze") renderAttendanceAnalysis();
      if (tabId === "edit") loadEditForm();
    }

    function getStudents() {
      return JSON.parse(localStorage.getItem("students")) || [];
    }

    function saveStudents(students) {
      localStorage.setItem("students", JSON.stringify(students));
    }

    function renderStudents() {
      const students = getStudents();
      const tbody = document.querySelector("#studentsTable tbody");
      tbody.innerHTML = "";
      students.forEach((student, i) => {
        const row = document.createElement("tr");
        row.innerHTML = `
          <td>${student.name}</td>
          <td>${student.class}</td>
          <td>${student.arm}</td>
          <td>₦${student.balance}</td>
          <td>
            <button class="pay-btn" onclick="makePayment(${i})">Pay</button>
            <button class="delete-btn" onclick="deleteStudent(${i})">Delete</button>
          </td>
        `;
        tbody.appendChild(row);
      });
    }

    function deleteStudent(index) {
      const students = getStudents();
      students.splice(index, 1);  // Remove the student at the given index
      saveStudents(students);  // Save the updated list
      renderStudents();  // Re-render the students list
      alert("Student deleted successfully!");
    }

    function makePayment(index) {
      const amount = prompt("Enter payment amount:");
      if (amount && !isNaN(amount)) {
        const students = getStudents();
        students[index].balance = parseInt(students[index].balance) + parseInt(amount);
        saveStudents(students);
        renderStudents();
        alert("Payment successful!");
      }
    }

    document.getElementById("registerForm").addEventListener("submit", function (e) {
      e.preventDefault();
      const formData = new FormData(this);
      const student = {};
      formData.forEach((val, key) => student[key] = val);
      student.balance = 0;
      student.attendance = {};
      const students = getStudents();
      students.push(student);
      saveStudents(students);

      document.getElementById("registerMessage").style.display = "block";
      this.reset();
      setTimeout(() => {
        document.getElementById("registerMessage").style.display = "none";
        showTab("view");
      }, 1500);
    });

    function renderAttendanceForm() {
      const list = document.getElementById("attendanceList");
      list.innerHTML = "";
      const students = getStudents();
      students.forEach((student, i) => {
        const div = document.createElement("div");
        div.innerHTML = `
          <label>
            ${student.name}:
            <select name="attendanceStatus" data-index="${i}">
              <option value="absent">Absent</option>
              <option value="present">Present</option>
            </select>
            <button class="delete-attendance-btn" onclick="deleteAttendance(${i})">Delete Attendance</button>
          </label>
        `;
        list.appendChild(div);
      });
    }

    function deleteAttendance(index) {
      const students = getStudents();
      const today = new Date().toISOString().split("T")[0];
      delete students[index].attendance[today];  // Remove today's attendance record
      saveStudents(students);
      renderAttendanceForm();  // Re-render the attendance form
      alert("Attendance deleted for today!");
    }

    document.getElementById("attendanceForm").addEventListener("submit", function (e) {
      e.preventDefault();
      const selects = document.querySelectorAll('select[name="attendanceStatus"]');
      const today = new Date().toISOString().split("T")[0];
      const students = getStudents();

      selects.forEach(select => {
        const index = parseInt(select.dataset.index);
        if (!students[index].attendance) {
          students[index].attendance = {};
        }
        students[index].attendance[today] = (select.value === "present");
      });

      saveStudents(students);
      alert("Attendance saved for today!");
    });

    function renderAttendanceAnalysis() {
      const container = document.getElementById("attendanceAnalysis");
      const students = getStudents();
      container.innerHTML = "";
      students.forEach(student => {
        const count = Object.values(student.attendance || {}).filter(val => val === true).length;
        const div = document.createElement("div");
        div.textContent = `${student.name}: Present ${count} day(s)`;
        container.appendChild(div);
      });
    }

    function loadEditForm() {
      const students = getStudents();
      const selector = document.getElementById("studentSelector");
      const form = document.getElementById("editForm");
      selector.innerHTML = "";
      students.forEach((s, i) => {
        const option = document.createElement("option");
        option.value = i;
        option.textContent = s.name;
        selector.appendChild(option);
      });
      selector.onchange = () => fillEditForm(students);
      form.onsubmit = (e) => updateStudent(e, students);
    }

    function fillEditForm(students) {
      const student = students[document.getElementById("studentSelector").value];
      const form = document.getElementById("editForm");
      form.name.value = student.name;
      form.age.value = student.age;
      form.gender.value = student.gender;
      form.email.value = student.email;
      form.phone.value = student.phone;
      form.class.value = student.class;
      form.arm.value = student.arm;
    }

    function updateStudent(e, students) {
      e.preventDefault();
      const index = document.getElementById("studentSelector").value;
      const student = students[index];
      const formData = new FormData(e.target);
      formData.forEach((value, key) => {
        student[key] = value;
      });
      saveStudents(students);
      alert("Student information updated successfully!");
    }
  </script>
</body>
</html>
