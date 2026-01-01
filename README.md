# Computer-Science-Project
# ===============================#
# ONLINE SPORTS SOFTWARE
# Python Frontend + MySQL Backend
# CBSE Class 12 Project
# ===============================#
print("                      ****WELCOME TO XTREME SPORTS****                       ")
print("**HERE YOU WILL GET YOUR FAVOURITE SPORTS PRODUCTS SO YOU CAN PLAY WITH A STYLE**")

import mysql.connector
import random

# ---------- DATABASE CONNECTION ----------#
db = mysql.connector.connect(host="localhost",user="root",password="12345",database="xtreme")
cur = db.cursor()

# ---------- MAIN MENU ----------#
def main_menu():
    while True:
        print("\n========= MAIN MENU =========")
        print("ENTER 0 FOR EXIT")
        print("ENTER 1 FOR SIGN UP")
        print("ENTER 2 FOR ADMIN LOGIN")
        print("ENTER 3 FOR DELETE CUSTOMER DETAILS")
        print("ENTER 4 FOR UPDATE CUSTOMER")
        print("ENTER 5 FOR USER LOGIN")
        ch = int(input("Enter your choice: "))
        if ch == 0:
            print("Thank You For Visiting!!!")
            break
        elif ch == 1:
            signup()
        elif ch == 2:
            admin_login()
        elif ch == 3:
            delete_customer()
        elif ch == 4:
            update_customer()
        elif ch == 5:
            user_login()
        else:
            print("Invalid Choice")

# ---------- SIGN UP ----------#
def signup():
    print("\n--- SIGN UP ---")
    cid = int(input("Enter Customer ID: "))
    name = input("Enter Name: ")
    email = input("Enter Email: ")
    address = input("Enter Address: ")
    phone = int(input("Enter Phone Number: "))
    cur.execute("INSERT INTO customer VALUES (%s,%s,%s,%s,%s)",(cid, name, email, address, phone))
    db.commit()
    print("Registered Successfully")

# ---------- DELETE CUSTOMER ----------#
def delete_customer():
    cid = int(input("Enter Customer ID to delete: "))
    cur.execute("DELETE FROM customer WHERE cust_id=%s", (cid,))
    db.commit()
    print("Customer Deleted Successfully")

# ---------- UPDATE CUSTOMER ----------#
def update_customer():
    cid = int(input("Enter Customer ID: "))
    print("1. Update Name")
    print("2. Update Address")
    print("3. Update Phone")
    ch = int(input("Enter choice: "))
    if ch == 1:
        name = input("Enter New Name: ")
        cur.execute("UPDATE customer SET name=%s WHERE cust_id=%s", (name, cid))
    elif ch == 2:
        address = input("Enter New Address: ")
        cur.execute("UPDATE customer SET address=%s WHERE cust_id=%s", (address, cid))
    elif ch == 3:
        phone = int(input("Enter New Phone: "))
        cur.execute("UPDATE customer SET phone=%s WHERE cust_id=%s", (phone, cid))
    else:
        print("Invalid choice")
        return
    db.commit()
    print("Customer Updated Successfully")

# ---------- USER LOGIN ----------#
def user_login():
    cid = int(input("Enter Customer ID: "))
    cur.execute("SELECT * FROM customer WHERE cust_id=%s", (cid,))
    data = cur.fetchone()
    if data:
        print("Login Successful")
        user_menu(cid)
    else:
        print("Invalid Customer ID")

# ---------- USER MENU ----------#
def user_menu(cid):
    while True:
        print("\n------ USER MENU ------")
        print("1. View All Products")
        print("2. Search Product")
        print("3. View Delivery History")
        print("4. Place Order")
        print("5. Return Product")
        print("6. Logout")
        ch = int(input("Enter choice: "))
        if ch == 1:
            view_products()
        elif ch == 2:
            search_product()
        elif ch == 3:
            delivery_history(cid)
        elif ch == 4:
            place_order(cid)
        elif ch == 5:
            return_product()
        elif ch == 6:
            break
        else:
            print("Invalid Choice")

# ---------- VIEW PRODUCTS ----------#
def view_products():
    cur.execute("SELECT * FROM product")
    data = cur.fetchall()
    for i in data:
        print(i)

# ---------- SEARCH PRODUCT ----------#
def search_product():
    pname = input("Enter Product Name: ")
    cur.execute("SELECT * FROM product WHERE pname=%s", (pname,))
    data = cur.fetchall()
    if data:
        for i in data:
            print(i)
    else:
        print("Product Not Found")

# ---------- PLACE ORDER ----------#
def place_order(cid):
    pid = int(input("Enter Product ID: "))
    qty = int(input("Enter Quantity: "))
    cur.execute("SELECT pname,company,price FROM product WHERE pid=%s", (pid,))
    p = cur.fetchone()
    if p:
        pname, company, price = p
        total = price * qty
        address = input("Enter Delivery Address: ")
        did = random.randint(10000,99999)
        cur.execute("INSERT INTO delivery VALUES (%s,%s,%s,%s,%s,%s,%s)",(did, cid, "User", pname, company, total, address))
        db.commit()
        print("Order Placed Successfully")
        print("Delivery ID:", did)
    else:
        print("Invalid Product ID")

# ---------- DELIVERY HISTORY ----------#
def delivery_history(cid):
    cur.execute("SELECT * FROM delivery WHERE c_id=%s", (cid,))
    data = cur.fetchall()
    if data:
        for i in data:
            print(i)
    else:
        print("No Orders Found")

# ---------- RETURN PRODUCT ----------#
def return_product():
    did = int(input("Enter Delivery ID: "))
    reason = input("Enter Reason: ")
    cur.execute("SELECT customer_name, product_name, total_price, address FROM delivery WHERE delivery_id=%s",(did,))
    data = cur.fetchone()
    if data:
        cname, pname, price, address = data
        cur.execute("INSERT INTO returns VALUES (%s,%s,%s,%s,%s,%s)",(did, cname, pname, price, address, reason))
        db.commit()
        print("Return Stored Successfully")
    else:
        print("Invalid Delivery ID")

# ---------- ADMIN LOGIN ----------#
def admin_login():
    pwd = input("Enter Admin Password: ")
    if pwd == "admin123":
        admin_menu()
    else:
        print("Wrong Password")

# ---------- ADMIN MENU ----------#
def admin_menu():
    while True:
        print("\n------ ADMIN MENU ------")
        print("1. Add Product")
        print("2. View Products")
        print("3. View Deliveries")
        print("4. Logout")
        ch = int(input("Enter choice: "))
        if ch == 1:
            add_product()
        elif ch == 2:
            view_products()
        elif ch == 3:
            view_deliveries()
        elif ch == 4:
            break
        else:
            print("Invalid Choice")

# ---------- ADD PRODUCT ----------#
def add_product():
    pid = int(input("Enter Product ID: "))
    pname = input("Enter Product Name: ")
    company = input("Enter Company: ")
    sport = input("Enter Sport: ")
    price = int(input("Enter Price: "))
    size = input("Enter Size: ")
    cur.execute("INSERT INTO product VALUES (%s,%s,%s,%s,%s,%s)",(pid, pname, company, sport, price, size))
    db.commit()
    print("Product Added Successfully")

# ---------- VIEW DELIVERIES ----------#
def view_deliveries():
    cur.execute("SELECT * FROM delivery")
    for i in cur.fetchall():
        print(i)

# ---------- START PROGRAM ----------#
main_menu()

