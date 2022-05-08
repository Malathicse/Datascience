from tkinter import *
from functools import partial
from tkinter import messagebox
import pandas as pd
import re
import os.path


#Registration frame Creation
def Register():
    newWindow = Toplevel(root)
    newWindow.geometry('200x200')
    newWindow.title("Registration")
    #Label for Username and Password
    Username = Label(newWindow, text="Username:")
    Password = Label(newWindow, text="Password:")
    Username.grid(row=0, column=0, sticky=W, pady=2)
    Password.grid(row=1, column=0, sticky=W, pady=2)
    #Entry box for Username and Password
    e1 = Entry(newWindow)
    e2 = Entry(newWindow)
    e1.grid(row=0, column=1, pady=2)
    e2.grid(row=1, column=1, pady=2)
    action_with_args = partial(Validation, e1, e2)
    #Submit Button
    btnsubmit = Button(newWindow, text=' Submit ', bd='5',
                       command=action_with_args)
    btnsubmit.grid(row=2, column=1, sticky=W, pady=2)

#Username Password Validation for Register
def Validation(arg1, arg2):
    User = arg1.get()
    Pass = arg2.get()
    val1 = True
    val2 = True
    #Username Validation
    pattern = "^[a-zA-Z][a-zA-Z0-9-_]+@[a-z]+.[a-z]{1,3}$"
    if re.match(pattern, User):
        val1 = True
    else:
        val1 = False
    #Password Validation
    SpecialSym = ['$', '@', '#', '%']
    if len(Pass) < 5:
        print('length should be at least 5')
        val2 = False

    if len(Pass) > 16:
        print('length should be not be greater than 16')
        val2 = False

    if not any(char.isdigit() for char in Pass):
        print('Password should have at least one numeral')
        val2 = False

    if not any(char.isupper() for char in Pass):
        print('Password should have at least one uppercase letter')
        val2 = False

    if not any(char.islower() for char in Pass):
        print('Password should have at least one lowercase letter')
        val2 = False

    if not any(char in SpecialSym for char in Pass):
        val2 = False
    if val1 and val2:
        if (os.path.exists('Credentials.csv')):
            write(User, Pass)
        else:
            fileCreation()
            write(User, Pass)
        messagebox.showinfo('Confirmation', 'Registration Successfull')
    else:
        messagebox.showerror('Registration Status', 'invalid username or password')
    return

#csv file creation
def fileCreation():
    Credential = {
        'Username': [],
        'Password': []
    }
    df = pd.DataFrame(Credential)
    df.to_csv('Credentials.csv', index=False, mode='w')
    return

#Adding credential to csv file
def write(Username, Password):
    Credential = {
        'Username': [Username],
        'Password': [Password]
    }
    df = pd.DataFrame(Credential)
    df.to_csv('Credentials.csv', mode='a', index=False, header=False)
    df.head()
    return

#Login frame creation
def Login():
    newWindow = Toplevel(root)
    newWindow.geometry('300x300')
    newWindow.title("Login")
    Username = Label(newWindow, text="Username:")
    Password = Label(newWindow, text="Password:")
    Username.grid(row=0, column=0, sticky=W, pady=2)
    Password.grid(row=1, column=0, sticky=W, pady=2)
    e1 = Entry(newWindow)
    e2 = Entry(newWindow)
    e1.grid(row=0, column=1, pady=2)
    e2.grid(row=1, column=1, pady=2)
    action_with_args = partial(LoginValidation, e1, e2)
    btnLogin = Button(newWindow, text=' Login ', bd='5',
                      command=action_with_args)
    btnLogin.grid(row=2, column=1, sticky=W, pady=2)
    action_with_args1 = partial(forgotpassword, e1)
    btnforgotpass = Button(newWindow, text=' Forgot Password ', bd='5',
                           command=action_with_args1)
    btnforgotpass.grid(row=2, column=0, sticky=W, pady=2)

#Login Validation
def LoginValidation(User, Pass):
    User1 = User.get()
    Pass1 = Pass.get()
    #CSV file exists check
    if (os.path.exists('Credentials.csv')):
        read(User1, Pass1)
    else:
        fileCreation()
        read(User1, Pass1)
    return

#CSV file reader
def read(Username, Password):
    df = pd.read_csv('Credentials.csv')
    df1 = df.copy()
    Users_list = df['Username'].tolist()
    pass_list=df['Password'].tolist()
    # show the list
    if Username in Users_list and Password in pass_list:
        messagebox.showinfo('Confirmation', 'Login Successfull')
    else:
        messagebox.showerror('Confirmation', 'Invalid username or password')
    return

#Forgot Password Validation
def forgotpassword(User):
    User1 = User.get()
    if (os.path.exists('Credentials.csv')):
        getpassword(User1)

    else:
        fileCreation()
        getpassword(User1)
    return

#Getting Password from CSV file
def getpassword(User):
    df = pd.read_csv('Credentials.csv',index_col=None)
    df1 = df.copy()
    Users_list = df['Username'].tolist()
    if User in Users_list:
       Password = df.loc[df['Username']== User, 'Password']
       Pass=str(Password.tolist())
       Pass1="Password is" + Pass
       messagebox.showinfo('Confirmation', Pass1)
    else:
        messagebox.showinfo('Confirmation', "Username doesn't exist Please Register")
    return


#Mainwindow Frame creation
root = Tk()
root.geometry('200x200')
root.title("Welcome")
#Register Button
btnReg = Button(root, text=' Register ', bd='5',
                command=Register)
btnReg.grid(row=5, column=5, pady=2)
#Login Button
btnLogin = Button(root, text=' Login ', bd='5',
                  command=Login)
btnLogin.grid(row=6, column=5, pady=2)
root.mainloop()
