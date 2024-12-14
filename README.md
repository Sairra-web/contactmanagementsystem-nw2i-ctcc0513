package contact;

import javax.swing.*;
import javax.swing.table.DefaultTableModel;
import java.awt.*;
import java.util.ArrayList;
import java.util.Comparator;

public class ContactManagementSystem {
    private ArrayList<Contact> contacts = new ArrayList<>();
    private DefaultTableModel tableModel;

    public static void main(String[] args) {
        SwingUtilities.invokeLater(ContactManagementSystem::new);
    }

    public ContactManagementSystem() {
        JFrame frame = new JFrame("Contact Management System");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(800, 500);
        frame.setLocationRelativeTo(null);

        JPanel mainPanel = new JPanel(new BorderLayout());
        mainPanel.setBackground(Color.LIGHT_GRAY);

        JLabel titleLabel = new JLabel("Contact Management System", SwingConstants.CENTER);
        titleLabel.setFont(new Font("Arial", Font.BOLD, 20));
        titleLabel.setOpaque(true);
        titleLabel.setBackground(new Color(70, 130, 180));
        titleLabel.setForeground(Color.WHITE);
        titleLabel.setPreferredSize(new Dimension(frame.getWidth(), 40));
        mainPanel.add(titleLabel, BorderLayout.NORTH);

        tableModel = new DefaultTableModel(new String[]{"Name", "Phone", "Email"}, 0);
        JTable contactTable = new JTable(tableModel);
        contactTable.getTableHeader().setBackground(new Color(135, 206, 250));
        contactTable.getTableHeader().setForeground(Color.BLACK);
        contactTable.setRowHeight(25);
        JScrollPane scrollPane = new JScrollPane(contactTable);

        JPanel topPanel = new JPanel(new BorderLayout());
        JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.LEFT, 10, 10));
        buttonPanel.setBackground(Color.LIGHT_GRAY);

        JButton addContactButton = new JButton("Add Contact");
        JButton deleteContactButton = new JButton("Delete");
        JButton sortButton = new JButton("Sort");

        buttonPanel.add(addContactButton);
        buttonPanel.add(deleteContactButton);
        buttonPanel.add(sortButton);

        JTextField searchField = new JTextField(20);
        JButton searchButton = new JButton("Search");
        JPanel searchPanel = new JPanel(new FlowLayout(FlowLayout.RIGHT, 10, 10));
        searchPanel.setBackground(Color.LIGHT_GRAY);
        searchPanel.add(searchField);
        searchPanel.add(searchButton);

        topPanel.add(buttonPanel, BorderLayout.WEST);
        topPanel.add(searchPanel, BorderLayout.EAST);

        mainPanel.add(topPanel, BorderLayout.NORTH);
        mainPanel.add(scrollPane, BorderLayout.CENTER);
        frame.add(mainPanel);

        addContactButton.addActionListener(e -> {
            JFrame addContactFrame = new JFrame("Add Contact");
            addContactFrame.setSize(400, 300);
            addContactFrame.setLayout(new GridLayout(5, 2, 10, 10));
            addContactFrame.setDefaultCloseOperation(JFrame.DISPOSE_ON_CLOSE);

            JLabel nameLabel = new JLabel("Name:");
            JTextField nameField = new JTextField();
            JLabel phoneLabel = new JLabel("Phone:");
            JTextField phoneField = new JTextField();
            JLabel emailLabel = new JLabel("Email:");
            JTextField emailField = new JTextField();

            JButton saveButton = new JButton("Save");
            saveButton.setBackground(new Color(144, 238, 144));
            JButton cancelButton = new JButton("Cancel");
            cancelButton.setBackground(new Color(255, 160, 122));

            addContactFrame.add(nameLabel);
            addContactFrame.add(nameField);
            addContactFrame.add(phoneLabel);
            addContactFrame.add(phoneField);
            addContactFrame.add(emailLabel);
            addContactFrame.add(emailField);
            addContactFrame.add(cancelButton);
            addContactFrame.add(saveButton);

            saveButton.addActionListener(saveEvent -> {
                String name = nameField.getText();
                String phone = phoneField.getText();
                String email = emailField.getText();
                if (!name.isEmpty() && !phone.isEmpty() && !email.isEmpty()) {
                    contacts.add(new Contact(name, phone, email));
                    updateTable(contacts);
                    addContactFrame.dispose();
                } else {
                    JOptionPane.showMessageDialog(addContactFrame, "All fields are required!", "Error", JOptionPane.ERROR_MESSAGE);
                }
            });

            cancelButton.addActionListener(cancelEvent -> addContactFrame.dispose());
            addContactFrame.setVisible(true);
        });

        deleteContactButton.addActionListener(e -> {
            int selectedRow = contactTable.getSelectedRow();
            if (selectedRow >= 0) {
                contacts.remove(selectedRow);
                updateTable(contacts);
            } else {
                JOptionPane.showMessageDialog(frame, "Please select a contact to delete!", "Error", JOptionPane.ERROR_MESSAGE);
            }
        });

        sortButton.addActionListener(e -> {
            contacts.sort(Comparator.comparing(Contact::getName));
            updateTable(contacts);
        });

        searchButton.addActionListener(e -> {
            String searchName = searchField.getText().trim();
            if (!searchName.isEmpty()) {
                ArrayList<Contact> filteredContacts = new ArrayList<>();
                for (Contact contact : contacts) {
                    if (contact.getName().equalsIgnoreCase(searchName)) {
                        filteredContacts.add(contact);
                    }
                }
                if (filteredContacts.isEmpty()) {
                    JOptionPane.showMessageDialog(frame, "Contact not found!", "Info", JOptionPane.INFORMATION_MESSAGE);
                } else {
                    updateTable(filteredContacts);
                }
            } else {
                JOptionPane.showMessageDialog(frame, "Please enter a name to search!", "Warning", JOptionPane.WARNING_MESSAGE);
            }
        });

        frame.setVisible(true);
    }

    private void updateTable(ArrayList<Contact> contactList) {
        tableModel.setRowCount(0);
        for (Contact contact : contactList) {
            tableModel.addRow(new Object[]{contact.getName(), contact.getPhone(), contact.getEmail()});
        }
    }

    static class Contact {
        private String name;
        private String phone;
        private String email;

        public Contact(String name, String phone, String email) {
            this.name = name;
            this.phone = phone;
            this.email = email;
        }

        public String getName() {
            return name;
        }

        public String getPhone() {
            return phone;
        }

        public String getEmail() {
            return email;
        }
    }
}
