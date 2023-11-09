 ```
# Import the necessary modules
from flask import Flask, render_template, request, redirect, url_for
import sqlite3

# Create a Flask application
app = Flask(__name__)

# Define the home page route
@app.route('/')
def home():
    return render_template('home.html')

# Define the registration route
@app.route('/register', methods=['GET', 'POST'])
def register():
    if request.method == 'POST':
        # Get the form data
        email = request.form['email']
        first_name = request.form['first_name']
        last_name = request.form['last_name']

        # Connect to the database
        conn = sqlite3.connect('registrants.db')
        c = conn.cursor()

        # Insert the registrant into the database
        c.execute("INSERT INTO registrants (email, first_name, last_name) VALUES (?, ?, ?)",
                  (email, first_name, last_name))

        # Commit the changes to the database
        conn.commit()

        # Close the connection to the database
        conn.close()

        # Send an email to confirm attendance
        send_confirmation_email(email)

        # Redirect to the home page
        return redirect(url_for('home'))
    else:
        return render_template('register.html')

# Define the function to send the confirmation email
def send_confirmation_email(email):
    # Import the necessary modules
    import smtplib
    from email.mime.multipart import MIMEMultipart
    from email.mime.text import MIMEText

    # Create the email message
    message = MIMEMultipart()
    message['Subject'] = 'Confirm Your Attendance'
    message['From'] = 'noreply@example.com'
    message['To'] = email
    body = 'Thank you for registering for the event. Please click the link below to confirm your attendance.\n\n[link]'
    message.attach(MIMEText(body, 'plain'))

    # Send the email
    server = smtplib.SMTP('localhost', 25)
    server.sendmail('noreply@example.com', email, message.as_string())
    server.quit()

# Run the application
if __name__ == '__main__':
    app.run(debug=True)
```