import tkinter as tk
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

