# Flask-Login-MySQL
Flask-Login-MySQL with Session
  
    $ pip install bcrypt
    $ pip install mysql

Important Libraries are:

    from flask import Flask, render_template, request, redirect, url_for, session
    from flask_mysqldb import MySQL,MySQLdb
    import bcrypt
    
Initialize Flask

    app = Flask(__name__)

MYSQL DB Connection: 

    app.config['MYSQL_HOST'] = 'localhost'
    app.config['MYSQL_USER'] = 'root'
    app.config['MYSQL_PASSWORD'] = '' # Password Here
    app.config['MYSQL_DB'] = 'flaskdb' # Database Name 
    app.config['MYSQL_CURSORCLASS'] = 'DictCursor'
    mysql = MySQL(app)

Login Route:
    
    @app.route('/login',methods=["GET","POST"])
    def login():
        if request.method == 'POST':
                email = request.form['email']
                password = request.form['password'].encode('utf-8')

                try:
                    curl = mysql.connection.cursor(MySQLdb.cursors.DictCursor)
                    curl.execute("SELECT * FROM users WHERE email=%s",(email,))
                    user = curl.fetchone()
                    curl.close()
                    print(len(user))
                    if len(user) > 0:
                        if bcrypt.hashpw(password, user["password"].encode('utf-8')) == user["password"].encode('utf-8'):
                            session['name'] = user['name']
                            session['email'] = user['email']
                            return render_template("home.html")
                        else:
                            return "Error password and email not match"
                except Exception as e:
                    return "Error user not found"
        else:
            return render_template("login.html")

Logout Route:

      @app.route('/logout', methods=["GET", "POST"])
      def logout():
          session.clear()
          return render_template("home.html")
