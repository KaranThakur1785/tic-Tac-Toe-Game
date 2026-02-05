# tic-Tac-Toe-Game
Single Player AI integrated
public TicTacToeGUI2() {
    setTitle("Tic Tac Toe");
    setSize(420,530);
    setDefaultCloseOperation(EXIT_ON_CLOSE);
    setLayout(new BorderLayout());

    status = new JLabel("Player X's turn", SwingConstants.CENTER);
    status.setFont(new Font("Arial", Font.BOLD, 16));
    add(status, BorderLayout.NORTH);

    JPanel grid = new JPanel(new GridLayout(3,3));
    Font f = new Font("Arial", Font.BOLD, 40);
    int n = 1;
    for (int i=0;i<3;i++){
        for (int j=0;j<3;j++){
            b[i][j] = new JButton(String.valueOf(n++));
            b[i][j].setFont(f);
            b[i][j].setFocusPainted(false);
            b[i][j].setBackground(DEFAULT_BTN_BG);
            b[i][j].addActionListener(this);
            grid.add(b[i][j]);
        }
    }
    add(grid, BorderLayout.CENTER);

    JPanel bottom = new JPanel(new GridLayout(3,1));
    score = new JLabel(getScoreText(), SwingConstants.CENTER);
    bottom.add(score);

    JButton restart = new JButton("Restart");
    restart.addActionListener(e -> resetBoard());
    bottom.add(restart);

    JPanel settings = new JPanel(new FlowLayout());
    aiBox = new JCheckBox("Play vs AI");
    aiBox.addItemListener(e -> singlePlayer = aiBox.isSelected());
    themeToggle = new JToggleButton("Dark Theme");
    themeToggle.addActionListener(e -> toggleTheme());
    settings.add(aiBox);
    settings.add(themeToggle);

    bottom.add(settings);
    add(bottom, BorderLayout.SOUTH);

    setVisible(true);
}

@Override
public void actionPerformed(ActionEvent e){
    JButton btn = (JButton)e.getSource();
    String t = btn.getText();
    if (t.equals("X") || t.equals("O")) return;

    btn.setText(String.valueOf(cur));

    if (checkWin()) {
        endRound("Player " + cur + " wins!");
    } else if (isDraw()) {
        endRound("It's a draw!");
    } else {
        cur = (cur=='X') ? 'O' : 'X';
        status.setText("Player " + cur + "'s turn");

        if (singlePlayer && cur=='O'){
            Timer tmr = new Timer(300, e2 -> aiMove());
            tmr.setRepeats(false);
            tmr.start();
        }
    }
}

private void aiMove(){
    Random r = new Random();
    int i,j;
    do {
        i = r.nextInt(3);
        j = r.nextInt(3);
    } while (b[i][j].getText().equals("X") || b[i][j].getText().equals("O"));
    b[i][j].doClick();
}

private boolean checkWin(){
    String p = String.valueOf(cur);
    for (int i=0;i<3;i++){
        if (p.equals(b[i][0].getText()) && p.equals(b[i][1].getText()) && p.equals(b[i][2].getText())){ highlight(b[i][0],b[i][1],b[i][2]); return true; }
        if (p.equals(b[0][i].getText()) && p.equals(b[1][i].getText()) && p.equals(b[2][i].getText())){ highlight(b[0][i],b[1][i],b[2][i]); return true; }
    }
    if (p.equals(b[0][0].getText()) && p.equals(b[1][1].getText()) && p.equals(b[2][2].getText())){ highlight(b[0][0],b[1][1],b[2][2]); return true; }
    if (p.equals(b[0][2].getText()) && p.equals(b[1][1].getText()) && p.equals(b[2][0].getText())){ highlight(b[0][2],b[1][1],b[2][0]); return true; }
    return false;
}

private void highlight(JButton b1, JButton b2, JButton b3){
    b1.setBackground(WIN_COLOR);
    b2.setBackground(WIN_COLOR);
    b3.setBackground(WIN_COLOR);
}

private boolean isDraw(){
    for (JButton[] row : b)
        for (JButton btn : row)
            if (!btn.getText().equals("X") && !btn.getText().equals("O"))
                return false;
    return true;
}

private void endRound(String msg){
    JOptionPane.showMessageDialog(this, msg);
    if (msg.contains("wins")) {
        if (cur=='X') xWins++; else oWins++;
    } else draws++;

    score.setText(getScoreText());
    disableButtons();
}

private void disableButtons(){
    for (JButton[] row : b)
        for (JButton btn : row)
            btn.setEnabled(false);
}

private void resetBoard(){
    cur = 'X';
    status.setText("Player X's turn");
    int n = 1;
    for (JButton[] row : b)
        for (JButton btn : row){
            btn.setText(String.valueOf(n++));
            btn.setEnabled(true);
            btn.setBackground(DEFAULT_BTN_BG);
            btn.setForeground(dark ? Color.WHITE : Color.BLACK);
        }
}

private String getScoreText(){
    return "Score — X: " + xWins + " | O: " + oWins + " | Draws: " + draws;
}

private void toggleTheme(){
    dark = !dark;
    Color bg = dark ? Color.DARK_GRAY : new Color(230,230,250);
    Color fg = dark ? Color.WHITE : Color.BLACK;

    getContentPane().setBackground(bg);
    status.setForeground(fg);
    score.setForeground(fg);

    for (JButton[] row : b)
        for (JButton btn : row){
            if (!btn.getBackground().equals(WIN_COLOR))
                btn.setBackground(DEFAULT_BTN_BG);
            btn.setForeground(fg);
        }

    themeToggle.setText(dark ? "Light Theme" : "Dark Theme");
}

public static void main(String[] args){
    SwingUtilities.invokeLater(TicTacToeGUI2::new);
}
