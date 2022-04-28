# inf232-final
Final project for INF 232 course

## ERD 
 ![image](https://user-images.githubusercontent.com/71185943/165450161-3de74b7a-03c5-4815-ab8d-f0b87b172bff.png)

 
 
## Functions and Procedures (at least 4)
### *1.*
```SQL
CREATE OR REPLACE procedure all_adver
 IS
   v_rec Advertisments%ROWTYPE
   CURSOR all_adver
    IS
      SELECT id, user_id, name, image, price, description, category, location, view, slug
      FROM Advertisments;
BEGIN
   OPEN all_adver;
   FETCH all_adver INTO v_rec;
   if all_adver%notfound then
      DBMS_OUTPUT.PUT_LINE("Data not found");
   end if;
   CLOSE all_adver;
END;
```
### *2.*
```SQL
CREATE OR REPLACE procedure all_cat
 IS
   v_rec Category%ROWTYPE
   CURSOR all_cat
    IS
      SELECT id, name, image, slug
      FROM Category;
BEGIN
   OPEN all_cat;
   FETCH all_cat INTO v_rec;
   if all_cat%notfound then
      DBMS_OUTPUT.PUT_LINE("Data not found");
   end if;
   CLOSE all_cat;
END;
```
### *3.*
```SQL
CREATE OR REPLACE procedure price_asc
 IS
   v_rec Advertisments%ROWTYPE
   CURSOR order_price_asc
   IS
     SELECT id, user_id, name, image, price, description, category, location, view, slug
     FROM Advertisments order by price;
BEGIN
   OPEN order_price_asc;
   FETCH order_price_asc INTO v_rec;
   if order_price_asc%notfound then
      DBMS_OUTPUT.PUT_LINE("Data not found");
   end if;
   CLOSE order_price_asc;
END;
```
### *4.*
```SQL
CREATE OR REPLACE procedure price_asc
 IS
   v_rec Advertisments%ROWTYPE
   CURSOR order_price_desc
   IS
     SELECT id, user_id, name, image, price, description, category, location, view, slug
     FROM Advertisments order by price desc;
BEGIN
   OPEN order_price_desc;
   FETCH order_price_desc INTO v_rec;
   if order_price_desc%notfound then
      DBMS_OUTPUT.PUT_LINE("Data not found");
   end if;
   CLOSE order_price_desc;
END;
```
### *5.*
```SQL
CREATE OR REPLACE procedure filter_cat 
 (p1_categoty IN varchar2)
 IS
   v_rec Advertisments%ROWTYPE
   CURSOR filter_cat_name (p_categoty IN varchar2)
     IS
       SELECT id, user_id, name, image, price, description, category, location, view, slug
       FROM Advertisments adv JOIN Category cat on adv.category = cat.id 
       WHERE cat.name = p_category;
BEGIN
   OPEN filter_cat_name(p1_categoty);
   FETCH filter_cat_name INTO v_rec;
   if filter_cat_name%notfound then
      DBMS_OUTPUT.PUT_LINE("Data not found");
   end if;
   CLOSE filter_cat_name;
END;
```
### *6.*
```SQL
CREATE OR REPLACE procedure filter_user 
 (p1_user IN varchar2)
 IS
   v_rec Advertisments%ROWTYPE
   CURSOR filter_user_name (p_user IN varchar2)
     IS
       SELECT id, user_id, name, image, price, description, category, location, view, slug
       FROM Advertisments adv JOIN Category cat on adv.category = cat.id 
       WHERE cat.name = p_category;
BEGIN
   OPEN filter_user_name(p1_user);
   FETCH filter_user_name INTO v_rec;
   if filter_user_name%notfound then
      DBMS_OUTPUT.PUT_LINE("Data not found");
   end if;
   CLOSE filter_user_name;
END;
```
## Cursor (at least 4)
### *1. All advertisments*
```SQL
 CURSOR all_adver
   IS
     SELECT id, user_id, name, image, price, description, category, location, view, slug
     FROM Advertisments;
```
### *2. All Category*
```SQL
CURSOR all_cat
   IS
     SELECT id, name, image, slug
     FROM Category;
```
### *3. Advertisments filter order by price ascending*
```SQL
CURSOR order_price_asc
   IS
     SELECT id, user_id, name, image, price, description, category, location, view, slug
     FROM Advertisments order by price;
```
### *4. Advertisments filter order by price descending*
```SQL
CURSOR order_price_desc
   IS
     SELECT id, user_id, name, image, price, description, category, location, view, slug
     FROM Advertisments order by price desc;
```

### *5. Advertisments filter by category name*
```SQL
CURSOR filter_cat_name (p_categoty IN varchar2)
  IS
    SELECT id, user_id, name, image, price, description, category, location, view, slug
    FROM Advertisments adv JOIN Category cat on adv.category = cat.id 
    WHERE cat.name = p_category;
```

### *6. Advertisments filter by authors*
```SQL
CURSOR filter_user_name (p_username IN varchar2)
  IS
    SELECT id, user_id, name, image, price, description, category, location, view, slug
    FROM Advertisments adv JOIN User u on adv.user_id = u.id 
    WHERE u.username = p_username;
```
## Triggers (at least 3)
### *1. Trigger profile. Insert into profile with Users. Working when we create User.*
```SQL
CREATE OR REPLACE TRIGGER profile_insert_after
  AFTER INSERT
   ON users
  FOR EACH ROW   
BEGIN   
   INSERT INTO Profile(user_id) 
   VALUES (:new.id);
END;
```

### *2. Trigger user delete. When users delete we delete also thir adevtisments, messages and favorite advertisments*
```SQL
CREATE OR REPLACE TRIGGER delete_user
  BEFORE DELETE
   ON users
  FOR EACH ROW   
BEGIN   
   DELETE FROM Advertisments WHERE user_id = :old.id;
   DELETE FROM Favorites WHERE user_id = :old.id;
   DELETE FROM Messages WHERE from_user = :old.id;
END;

```

### *3. Trigger category delete. When category delete we delete all advertisemnts connected with this category*
```SQL
CREATE OR REPLACE TRIGGER delete_category
  BEFORE DELETE
   ON Category
  FOR EACH ROW   
BEGIN   
   DELETE FROM Advertisments WHERE user_id = :old.category;
END;

```

## Transactions (at least 2)

### *1. Insert Advertisment*
```SQL
DECLARE
BEGIN
   SAVEPOINT do_insert;
   INSERT INTO Advertisments(id,user_id, name, image, price, description, category, location, view, slug)
   VALUES(..., ..., ..., ..., ..., ..., ..., ...,);
EXCEPTION
   WHEN DUP_VAL_ON_INDEX THEN
      ROLLBACK TO do_insert;
      DBMS_OUTPUT.PUT_LINE('Insert has been rolled back');
END;
```

### *2. Insert Users*
```SQL
DECLARE
BEGIN
   SAVEPOINT do_insert;
   INSERT INTO User(id, username, first_name, last_name)
   VALUES(..., ..., ..., ..., ..., ..., ..., ...,);
EXCEPTION
   WHEN DUP_VAL_ON_INDEX THEN
      ROLLBACK TO do_insert;
      DBMS_OUTPUT.PUT_LINE('Insert has been rolled back');
END;
```

