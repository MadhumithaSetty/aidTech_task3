# aidTech_task3

Student Record Management System

Create a program that can manage student records, including adding new students, removing students, and displaying student information.

Steps to create the project:

1. Define a Student class that contains information about the student, such as name, roll number, address, and phone number.

2. Implement a Student Record class that contains an array or list of Student objects.

3. Create methods for adding a new student to the record, removing a student from the record, and displaying student information based on the roll number or name.

4. Implement a user interface using Java Swing or JavaFX to allow users to interact with the student record system.

5. Save the student record to a file and load it back when the program starts.


CODE:
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.*;
import java.util.ArrayList;
import java.util.List;

class Student implements Serializable {
    /**
	 * 
	 */
	private static final long serialVersionUID = 1L;
	private String name;
    private int rollNumber;
    private String address;
    private String phoneNumber;

    public Student(String name, int rollNumber, String address, String phoneNumber) {
        this.name = name;
        this.rollNumber = rollNumber;
        this.address = address;
        this.phoneNumber = phoneNumber;
    }

    public String getName() {
        return name;
    }

    public int getRollNumber() {
        return rollNumber;
    }

    public String getAddress() {
        return address;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    @Override
    public String toString() {
        return "Name: " + name +
                "\nRoll Number: " + rollNumber +
                "\nAddress: " + address +
                "\nPhone Number: " + phoneNumber;
    }
}

class StudentRecord {
    private List<Student> studentList;

    public StudentRecord() {
        studentList = new ArrayList<>();
    }

    public void addStudent(Student student) {
        studentList.add(student);
    }

    public void removeStudent(int rollNumber) {
        Student studentToRemove = null;
        for (Student student : studentList) {
            if (student.getRollNumber() == rollNumber) {
                studentToRemove = student;
                break;
            }
        }
        if (studentToRemove != null) {
            studentList.remove(studentToRemove);
        } else {
            JOptionPane.showMessageDialog(null, "Student with roll number " + rollNumber + " not found.",
                    "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    public Student findStudentByRollNumber(int rollNumber) {
        for (Student student : studentList) {
            if (student.getRollNumber() == rollNumber) {
                return student;
            }
        }
        return null;
    }

    public List<Student> getStudentList() {
        return studentList;
    }
}

public class StudentRecordManagement extends JFrame {
    /**
	 * 
	 */
	private static final long serialVersionUID = 1L;
	private static final String FILE_NAME = "student_record.ser";
    private StudentRecord studentRecord;

    private JTextField nameField, rollNumberField, addressField, phoneNumberField;
    private JTextArea outputArea;

    public StudentRecordManagement() {
        studentRecord = loadStudentRecord();
        if (studentRecord == null) {
            studentRecord = new StudentRecord();
        }

        setTitle("Student Record Management");
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setSize(600, 400);
        setLayout(new BorderLayout());

        createInputPanel();
        createOutputPanel();

        setVisible(true);
    }

    private void createInputPanel() {
        JPanel inputPanel = new JPanel();
        inputPanel.setLayout(new GridLayout(5, 2, 10, 10));
        inputPanel.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10));

        JLabel nameLabel = new JLabel("Name:");
        nameField = new JTextField(20);

        JLabel rollNumberLabel = new JLabel("Roll Number:");
        rollNumberField = new JTextField(20);

        JLabel addressLabel = new JLabel("Address:");
        addressField = new JTextField(20);

        JLabel phoneNumberLabel = new JLabel("Phone Number:");
        phoneNumberField = new JTextField(20);

        JButton addButton = new JButton("Add");
        addButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                addStudent();
            }
        });

        JButton removeButton = new JButton("Remove");
        removeButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                removeStudent();
            }
        });

        inputPanel.add(nameLabel);
        inputPanel.add(nameField);
        inputPanel.add(rollNumberLabel);
        inputPanel.add(rollNumberField);
        inputPanel.add(addressLabel);
        inputPanel.add(addressField);
        inputPanel.add(phoneNumberLabel);
        inputPanel.add(phoneNumberField);
        inputPanel.add(addButton);
        inputPanel.add(removeButton);

        add(inputPanel, BorderLayout.NORTH);
    }

    private void createOutputPanel() {
        JPanel outputPanel = new JPanel();
        outputPanel.setLayout(new BorderLayout());
        outputPanel.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10));

        outputArea = new JTextArea(10, 40);
        outputArea.setEditable(false);
        JScrollPane scrollPane = new JScrollPane(outputArea);

        JButton displayButton = new JButton("Display All");
        displayButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                displayAllStudents();
            }
        });

        outputPanel.add(scrollPane, BorderLayout.CENTER);
        outputPanel.add(displayButton, BorderLayout.SOUTH);

        add(outputPanel, BorderLayout.CENTER);
    }

    private void addStudent() {
        String name = nameField.getText();
        int rollNumber = Integer.parseInt(rollNumberField.getText());
        String address = addressField.getText();
        String phoneNumber = phoneNumberField.getText();

        Student newStudent = new Student(name, rollNumber, address, phoneNumber);
        studentRecord.addStudent(newStudent);
        saveStudentRecord(studentRecord);

        JOptionPane.showMessageDialog(null, "Student added successfully.", "Success", JOptionPane.INFORMATION_MESSAGE);

        clearInputFields();
    }

    private void removeStudent() {
        int rollNumber = Integer.parseInt(rollNumberField.getText());
        studentRecord.removeStudent(rollNumber);
        saveStudentRecord(studentRecord);

        clearInputFields();
    }

    private void displayAllStudents() {
        outputArea.setText("");
        for (Student student : studentRecord.getStudentList()) {
            outputArea.append(student.toString() + "\n\n");
        }
    }

    private void clearInputFields() {
        nameField.setText("");
        rollNumberField.setText("");
        addressField.setText("");
        phoneNumberField.setText("");
    }

    private StudentRecord loadStudentRecord() {
        try (FileInputStream fileIn = new FileInputStream(FILE_NAME);
             ObjectInputStream objectIn = new ObjectInputStream(fileIn)) {
            return (StudentRecord) objectIn.readObject();
        } catch (IOException | ClassNotFoundException e) {
            return null;
        }
    }

    private void saveStudentRecord(StudentRecord studentRecord) {
        try (FileOutputStream fileOut = new FileOutputStream(FILE_NAME);
             ObjectOutputStream objectOut = new ObjectOutputStream(fileOut)) {
            objectOut.writeObject(studentRecord);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(new Runnable() {
            @Override
            public void run() {
                new StudentRecordManagement();
            }
        });
    }
}
