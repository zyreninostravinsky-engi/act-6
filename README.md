import tkinter as tk
from tkinter import ttk, messagebox


class StudentManagementApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Student Management System")
        self.root.geometry("950x700")
        self.root.configure(bg="#f0f4f8")

        # Store student records
        self.students = []

        # Main title
        title_label = tk.Label(
            root,
            text="Student Management System",
            font=("Arial", 20, "bold"),
            bg="#f0f4f8",
            fg="#1f2937"
        )
        title_label.pack(pady=10)

        # Main frame
        main_frame = tk.Frame(root, bg="#f0f4f8")
        main_frame.pack(fill="both", expand=True, padx=15, pady=10)

        # Input frame
        input_frame = tk.LabelFrame(
            main_frame,
            text="Enter Student Information",
            font=("Arial", 12, "bold"),
            padx=10,
            pady=10,
            bg="#ffffff"
        )
        input_frame.pack(fill="x", pady=10)

        # Labels and Entry widgets
        self.fields = {}

        labels = [
            "Student Name",
            "Student ID",
            "Email",
            "Phone Number",
            "Course/Program",
            "Year Level",
            "GPA",
            "Enrollment Status"
        ]

        for i, label in enumerate(labels):
            tk.Label(
                input_frame,
                text=label + ":",
                font=("Arial", 10, "bold"),
                bg="#ffffff"
            ).grid(row=i, column=0, sticky="w", padx=5, pady=5)

            entry = tk.Entry(input_frame, width=45, font=("Arial", 10))
            entry.grid(row=i, column=1, padx=5, pady=5, sticky="w")

            self.fields[label] = entry

        # Button frame
        button_frame = tk.Frame(main_frame, bg="#f0f4f8")
        button_frame.pack(fill="x", pady=10)

        add_button = tk.Button(
            button_frame,
            text="➕ Add Student",
            font=("Arial", 10, "bold"),
            bg="#10b981",
            fg="white",
            padx=10,
            pady=5,
            command=self.add_student
        )
        add_button.pack(side="left", padx=5)

        clear_button = tk.Button(
            button_frame,
            text="🧹 Clear Fields",
            font=("Arial", 10, "bold"),
            bg="#3b82f6",
            fg="white",
            padx=10,
            pady=5,
            command=self.clear_fields
        )
        clear_button.pack(side="left", padx=5)

        delete_button = tk.Button(
            button_frame,
            text="❌ Delete Last Student",
            font=("Arial", 10, "bold"),
            bg="#ef4444",
            fg="white",
            padx=10,
            pady=5,
            command=self.delete_last_student
        )
        delete_button.pack(side="left", padx=5)

        # Display frame
        display_frame = tk.LabelFrame(
            main_frame,
            text="Student Records",
            font=("Arial", 12, "bold"),
            padx=10,
            pady=10,
            bg="#ffffff"
        )
        display_frame.pack(fill="both", expand=True, pady=10)

        # Scrollbar and Text widget
        scrollbar = tk.Scrollbar(display_frame)
        scrollbar.pack(side="right", fill="y")

        self.text_area = tk.Text(
            display_frame,
            wrap="word",
            yscrollcommand=scrollbar.set,
            font=("Courier New", 10),
            bg="#f9fafb"
        )
        self.text_area.pack(fill="both", expand=True)

        scrollbar.config(command=self.text_area.yview)

        # Status bar
        self.status_var = tk.StringVar()
        self.status_var.set("Total Students: 0")

        status_bar = tk.Label(
            root,
            textvariable=self.status_var,
            bd=1,
            relief="sunken",
            anchor="w",
            font=("Arial", 10),
            bg="#e5e7eb"
        )
        status_bar.pack(side="bottom", fill="x")

    def add_student(self):
        """Collect input, validate data, and add student record."""

        # Retrieve values
        name = self.fields["Student Name"].get().strip()
        student_id = self.fields["Student ID"].get().strip()
        email = self.fields["Email"].get().strip()
        phone = self.fields["Phone Number"].get().strip()
        course = self.fields["Course/Program"].get().strip()
        year = self.fields["Year Level"].get().strip()
        gpa = self.fields["GPA"].get().strip()
        status = self.fields["Enrollment Status"].get().strip()

        # Validation: all fields required
        if not all([name, student_id, email, phone, course, year, gpa, status]):
            messagebox.showerror("Input Error", "All fields are required.")
            return

        # GPA validation
        try:
            gpa_value = float(gpa)
            if gpa_value < 0.0 or gpa_value > 4.0:
                raise ValueError
        except ValueError:
            messagebox.showerror("GPA Error", "GPA must be a number between 0.0 and 4.0.")
            return

        # Store data in dictionary
        student = {
            "name": name,
            "id": student_id,
            "email": email,
            "phone": phone,
            "course": course,
            "year": year,
            "gpa": gpa_value,
            "status": status
        }

        # Add to list
        self.students.append(student)

        # Update display
        self.display_students()

        # Clear fields
        self.clear_fields()

        messagebox.showinfo("Success", "Student record added successfully!")

    def display_students(self):
        """Display all students in the text area."""

        self.text_area.delete(1.0, tk.END)

        if not self.students:
            self.text_area.insert(tk.END, "No student records available.\n")
        else:
            for index, student in enumerate(self.students, start=1):
                student_info = (
                    f"{'=' * 60}\n"
                    f"Student #{index}\n"
                    f"{'=' * 60}\n"
                    f"Name           : {student['name']}\n"
                    f"Student ID     : {student['id']}\n"
                    f"Email          : {student['email']}\n"
                    f"Phone          : {student['phone']}\n"
                    f"Course         : {student['course']}\n"
                    f"Year Level     : {student['year']}\n"
                    f"GPA            : {student['gpa']:.2f}\n"
                    f"Status         : {student['status']}\n\n"
                )

                self.text_area.insert(tk.END, student_info)

        # Update status bar
        self.status_var.set(f"Total Students: {len(self.students)}")

    def clear_fields(self):
        """Clear all input fields."""

        for entry in self.fields.values():
            entry.delete(0, tk.END)

    def delete_last_student(self):
        """Delete the last student record from the list."""

        if not self.students:
            messagebox.showwarning("Warning", "No student records to delete.")
            return

        removed_student = self.students.pop()
        self.display_students()

        messagebox.showinfo(
            "Deleted",
            f"Deleted record for {removed_student['name']}"
        )


# Main Program
if __name__ == "__main__":
    root = tk.Tk()
    app = StudentManagementApp(root)
    root.mainloop()
