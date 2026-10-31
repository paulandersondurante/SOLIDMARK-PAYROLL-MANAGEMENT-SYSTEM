# SOLIDMARK-PAYROLL-MANAGEMENT-SYSTEM

import javax.swing.*;
import javax.swing.table.DefaultTableModel;
import java.awt.*;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class SolidmarkPayrollSystem extends JFrame {

    JTextField employeeId = new JTextField("EMP001");
    JTextField basicSalary = new JTextField("15000.00");
    JTextField overtimeHours = new JTextField("5");
    JTextField overtimePay = new JTextField("500.00");
    JTextField absenceDeduction = new JTextField("1000.00");
    JTextField cashAdvance = new JTextField("1500.00");

    JTextField grossPay = new JTextField("15500.00");
    JTextField totalDeductions = new JTextField("2500.00");
    JTextField netSalary = new JTextField("13000.00");

    DefaultTableModel model = new DefaultTableModel(
        new String[]{
            "ID", "Employee ID", "Basic Salary",
            "OT Hours", "OT Pay", "Absence",
            "Cash Advance", "Gross Pay",
            "Deductions", "Net Salary", "Date Created"
        }, 0
    ) {
        public boolean isCellEditable(int row, int column) {
            return false;
        }
    };

    JTable table = new JTable(model);
    int nextId = 1;
    int selectedRow = -1;

    Color blue = new Color(20, 116, 210);
    Color navy = new Color(0, 67, 125);
    Color green = new Color(24, 164, 75);
    Color red = new Color(210, 42, 42);

    public SolidmarkPayrollSystem() {

        setTitle("Solidmark Payroll Management System");
        setSize(1400, 850);
        setMinimumSize(new Dimension(1100, 700));
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);

        setLayout(new BorderLayout());

        // HEADER
        JPanel header = new JPanel(new BorderLayout());
        header.setBackground(navy);
        header.setBorder(BorderFactory.createEmptyBorder(20, 30, 20, 30));

        JLabel logo = new JLabel(
            "◆ SOLIDMARK   Payroll Management System"
        );
        logo.setForeground(Color.WHITE);
        logo.setFont(new Font("Arial", Font.BOLD, 25));

        JLabel title = new JLabel("Payroll History");
        title.setForeground(Color.WHITE);
        title.setFont(new Font("Arial", Font.BOLD, 27));

        header.add(logo, BorderLayout.WEST);
        header.add(title, BorderLayout.EAST);

        add(header, BorderLayout.NORTH);

        // MAIN PANEL
        JPanel main = new JPanel(new BorderLayout(10, 15));
        main.setBorder(BorderFactory.createEmptyBorder(20, 20, 20, 20));
        main.setBackground(new Color(239, 245, 249));

        add(main, BorderLayout.CENTER);

        // INPUT PANEL
        JPanel form = new JPanel(new GridLayout(1, 2, 30, 10));
        form.setBackground(new Color(247, 250, 252));
        form.setBorder(BorderFactory.createEmptyBorder(20, 20, 20, 20));

        JPanel left = new JPanel(new GridLayout(6, 2, 10, 15));
        left.setOpaque(false);

        addField(left, "Employee ID:", employeeId);
        addField(left, "Basic Salary:", basicSalary);
        addField(left, "Overtime Hours:", overtimeHours);
        addField(left, "Overtime Pay:", overtimePay);
        addField(left, "Absence Deduction:", absenceDeduction);
        addField(left, "Cash Advance:", cashAdvance);

        JPanel right = new JPanel(new BorderLayout(10, 15));
        right.setOpaque(false);

        JPanel results = new JPanel(new GridLayout(3, 2, 10, 20));
        results.setOpaque(false);

        addField(results, "Gross Pay:", grossPay);
        addField(results, "Total Deductions:", totalDeductions);
        addField(results, "Net Salary:", netSalary);

        grossPay.setEditable(false);
        totalDeductions.setEditable(false);
        netSalary.setEditable(false);

        JPanel buttons = new JPanel(new GridLayout(2, 1, 5, 10));
        buttons.setOpaque(false);

        JPanel buttonRow = new JPanel(new GridLayout(1, 3, 10, 0));
        buttonRow.setOpaque(false);

        JButton calculate = createButton("CALCULATE", blue);
        JButton save = createButton("SAVE", green);
        JButton delete = createButton("DELETE", red);
        JButton clear = createButton("CLEAR", Color.LIGHT_GRAY);

        buttonRow.add(calculate);
        buttonRow.add(save);
        buttonRow.add(delete);

        buttons.add(buttonRow);
        buttons.add(clear);

        right.add(results, BorderLayout.NORTH);
        right.add(buttons, BorderLayout.CENTER);

        form.add(left);
        form.add(right);

        main.add(form, BorderLayout.NORTH);

        // PAYROLL HISTORY
        JPanel history = new JPanel(new BorderLayout(10, 10));
        history.setBackground(Color.WHITE);
        history.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10));

        JLabel historyTitle = new JLabel("Payroll History");
        historyTitle.setFont(new Font("Arial", Font.BOLD, 20));

        history.add(historyTitle, BorderLayout.NORTH);

        table.setRowHeight(35);
        table.setFont(new Font("Arial", Font.PLAIN, 14));
        table.getTableHeader().setFont(
            new Font("Arial", Font.BOLD, 13)
        );
        table.getTableHeader().setBackground(
            new Color(230, 240, 250)
        );

        JScrollPane scroll = new JScrollPane(table);
        history.add(scroll, BorderLayout.CENTER);

        main.add(history, BorderLayout.CENTER);

        // BUTTON ACTIONS
        calculate.addActionListener(e -> calculatePayroll());
        save.addActionListener(e -> savePayroll());
        delete.addActionListener(e -> deletePayroll());
        clear.addActionListener(e -> clearFields());

        table.getSelectionModel().addListSelectionListener(e -> {
            if (!e.getValueIsAdjusting() && table.getSelectedRow() != -1) {
                int row = table.getSelectedRow();
                selectedRow = row;
                loadSelectedRecord(row);
            }
        });

        // SAMPLE RECORDS
        addSampleData();
    }

    // ADD FORM FIELD
    private void addField(JPanel panel, String label, JTextField field) {

        JLabel text = new JLabel(label);
        text.setFont(new Font("Arial", Font.BOLD, 15));

        field.setFont(new Font("Arial", Font.PLAIN, 16));
        field.setBorder(
            BorderFactory.createCompoundBorder(
                BorderFactory.createLineBorder(Color.LIGHT_GRAY),
                BorderFactory.createEmptyBorder(5, 8, 5, 8)
            )
        );

        panel.add(text);
        panel.add(field);
    }

    // CREATE BUTTON
    private JButton createButton(String text, Color color) {

        JButton button = new JButton(text);
        button.setFont(new Font("Arial", Font.BOLD, 15));
        button.setBackground(color);
        button.setForeground(
            color.equals(Color.LIGHT_GRAY) ? Color.BLACK : Color.WHITE
        );
        button.setFocusPainted(false);

        return button;
    }

    // CALCULATE PAYROLL
    private void calculatePayroll() {

        try {

            double basic = Double.parseDouble(basicSalary.getText());
            double overtime = Double.parseDouble(overtimePay.getText());
            double absence = Double.parseDouble(absenceDeduction.getText());
            double advance = Double.parseDouble(cashAdvance.getText());

            if (basic < 0 || overtime < 0 || absence < 0 || advance < 0
                    || Double.parseDouble(overtimeHours.getText()) < 0) {
                throw new NumberFormatException();
            }

            double gross = basic + overtime;
            double deductions = absence + advance;
            double net = gross - deductions;

            grossPay.setText(String.format("%.2f", gross));
            totalDeductions.setText(String.format("%.2f", deductions));
            netSalary.setText(String.format("%.2f", net));

        } catch (NumberFormatException e) {

            JOptionPane.showMessageDialog(
                this,
                "Please enter valid non-negative numbers.",
                "Invalid Input",
                JOptionPane.ERROR_MESSAGE
            );
        }
    }

    // SAVE PAYROLL
    private void savePayroll() {

        try {

            calculatePayroll();

            if (grossPay.getText().isEmpty()) {
                return;
            }

            String id = employeeId.getText().trim();

            if (id.isEmpty()) {
                JOptionPane.showMessageDialog(this, "Enter Employee ID.");
                return;
            }

            String date = LocalDateTime.now().format(
                DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")
            );

            Object[] data = {
                nextId++,
                id,
                basicSalary.getText(),
                overtimeHours.getText(),
                overtimePay.getText(),
                absenceDeduction.getText(),
                cashAdvance.getText(),
                grossPay.getText(),
                totalDeductions.getText(),
                netSalary.getText(),
                date
            };

            if (selectedRow >= 0) {

                data[0] = model.getValueAt(selectedRow, 0);

                for (int i = 0; i < data.length; i++) {
                    model.setValueAt(data[i], selectedRow, i);
                }

            } else {

                model.addRow(data);
            }

            JOptionPane.showMessageDialog(
                this,
                "Payroll saved successfully!"
            );

            clearFields();

        } catch (Exception e) {

            JOptionPane.showMessageDialog(
                this,
                "Unable to save payroll."
            );
        }
    }

    // DELETE PAYROLL
    private void deletePayroll() {

        if (selectedRow < 0) {

            JOptionPane.showMessageDialog(
                this,
                "Please select a payroll record."
            );

            return;
        }

        int confirm = JOptionPane.showConfirmDialog(
            this,
            "Are you sure you want to delete this record?",
            "Confirm Delete",
            JOptionPane.YES_NO_OPTION
        );

        if (confirm == JOptionPane.YES_OPTION) {

            model.removeRow(selectedRow);
            clearFields();

        }
    }

    // LOAD SELECTED RECORD
    private void loadSelectedRecord(int row) {

        employeeId.setText(model.getValueAt(row, 1).toString());
        basicSalary.setText(model.getValueAt(row, 2).toString());
        overtimeHours.setText(model.getValueAt(row, 3).toString());
        overtimePay.setText(model.getValueAt(row, 4).toString());
        absenceDeduction.setText(model.getValueAt(row, 5).toString());
        cashAdvance.setText(model.getValueAt(row, 6).toString());
        grossPay.setText(model.getValueAt(row, 7).toString());
        totalDeductions.setText(model.getValueAt(row, 8).toString());
        netSalary.setText(model.getValueAt(row, 9).toString());
    }

    // CLEAR FIELDS
    private void clearFields() {

        employeeId.setText("");
        basicSalary.setText("");
        overtimeHours.setText("");
        overtimePay.setText("");
        absenceDeduction.setText("");
        cashAdvance.setText("");

        grossPay.setText("");
        totalDeductions.setText("");
        netSalary.setText("");

        selectedRow = -1;
        table.clearSelection();
    }

    // SAMPLE PAYROLL DATA
    private void addSampleData() {

        model.addRow(new Object[]{
            nextId++, "EMP001", "15000.00", "5.00",
            "500.00", "1000.00", "1500.00",
            "15500.00", "2500.00", "13000.00",
            "2025-07-28 10:30:15"
        });

        model.addRow(new Object[]{
            nextId++, "EMP002", "12000.00", "2.00",
            "300.00", "500.00", "1000.00",
            "12300.00", "1500.00", "10800.00",
            "2025-07-27 14:22:03"
        });

        model.addRow(new Object[]{
            nextId++, "EMP003", "18000.00", "4.00",
            "600.00", "0.00", "2000.00",
            "18600.00", "2000.00", "16600.00",
            "2025-07-26 09:12:40"
        });

        model.addRow(new Object[]{
            nextId++, "EMP004", "14000.00", "3.00",
            "450.00", "750.00", "1500.00",
            "14450.00", "2250.00", "12200.00",
            "2025-07-25 16:45:17"
        });

        model.addRow(new Object[]{
            nextId++, "EMP005", "16000.00", "5.00",
            "750.00", "500.00", "1000.00",
            "16750.00", "1500.00", "15250.00",
            "2025-07-24 11:20:33"
        });
    }

    // MAIN METHOD
    public static void main(String[] args) {

        SwingUtilities.invokeLater(() -> {

            try {
                UIManager.setLookAndFeel(
                    UIManager.getSystemLookAndFeelClassName()
                );
            } catch (Exception e) {
                e.printStackTrace();
            }

            new SolidmarkPayrollSystem().setVisible(true);
        });
    }
}