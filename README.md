# inf232-final
Final project for INF 232 course

## ERD 
 ![image](https://user-images.githubusercontent.com/71185943/165450161-3de74b7a-03c5-4815-ab8d-f0b87b172bff.png)

 
 
## Functions and Procedures (at least 4)
### *1.*
```SQL
CREATE OR REPLACE procedure all_adver
 IS
   v_rec grid_panel_advt%ROWTYPE;
   CURSOR adver
    IS
      SELECT id, advertisement_user_id, advertisement_name, advertisement_image, advertisement_price, advertisement_description, advertisement_category_id, 
      advertisement_location, advertisement_view, advertisement_slug
      FROM grid_panel_advt;
BEGIN
   
   for i in adver loop
    DBMS_OUTPUT.PUT_LINE(i.advertisement_name || ' ' || i.advertisement_price || ' ' || i.advertisement_location);
    end loop;
   
END;
```
### *2.*
```SQL

CREATE OR REPLACE procedure all_cat
 IS
   v_rec grid_panel_category%ROWTYPE;
   CURSOR cats
    IS
      SELECT id, name, image, slug
      FROM grid_panel_category;
BEGIN
   OPEN cats;
    LOOP
        FETCH cats INTO v_rec;
        EXIT WHEN cats%notfound;
        DBMS_OUtPUT.PUT_LINE(v_rec.id || ' ' || v_rec.name || ' ' || v_rec.image || ' ' || v_rec.slug);
    end loop;
   CLOSE cats;
END;
```
### *3.*
```SQL

CREATE OR REPLACE procedure price_asc
 IS
   v_rec grid_panel_advt%ROWTYPE;
   CURSOR order_price_asc
   IS
    SELECT id, advertisement_user_id, advertisement_name, advertisement_image, advertisement_price, advertisement_description, advertisement_category_id, 
      advertisement_location, advertisement_view, advertisement_slug
      FROM grid_panel_advt order by advertisement_price;
BEGIN
    for i in order_price_asc loop
    DBMS_OUTPUT.PUT_LINE(i.advertisement_name);
    END LOOP;
        
END;
```
### *4.*
```SQL
CREATE OR REPLACE procedure price_desc
 IS
   v_rec grid_panel_advt%ROWTYPE;
   CURSOR order_price_desc
   IS
    SELECT id, advertisement_user_id, advertisement_name, advertisement_image, advertisement_price, advertisement_description, advertisement_category_id, 
      advertisement_location, advertisement_view, advertisement_slug
      FROM grid_panel_advt order by advertisement_price desc;
BEGIN
    for i in order_price_desc loop
    DBMS_OUTPUT.PUT_LINE(i.advertisement_name);
    END LOOP;
        
END;
```
### *5.*
```SQL

CREATE OR REPLACE procedure filter_cat 
 (p1_category IN varchar2)
 IS
   v_rec grid_panel_advt%ROWTYPE;
   CURSOR filter_cat_name (p_category IN varchar2)
     IS
       SELECT adv.id, advertisement_user_id, advertisement_name, advertisement_image, advertisement_price, 
       advertisement_description, advertisement_category_id, advertisement_location, advertisement_view, advertisement_slug
       FROM grid_panel_advt adv JOIN grid_panel_category cat on cat.name = p_category;
BEGIN
   for i in filter_cat_name(p1_category) loop
    dbms_output.put_line(i.advertisement_name || ' ' || i.advertisement_price);
    end loop;
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
  CURSOR adver
    IS
      SELECT id, advertisement_user_id, advertisement_name, advertisement_image, advertisement_price, advertisement_description, advertisement_category_id, 
      advertisement_location, advertisement_view, advertisement_slug
      FROM grid_panel_advt;
```
### *2. All Category*
```SQL
CURSOR cats
    IS
      SELECT id, name, image, slug
      FROM grid_panel_category;
```
### *3. Advertisments filter order by price ascending*
```SQL
CURSOR order_price_asc
   IS
    SELECT id, advertisement_user_id, advertisement_name, advertisement_image, advertisement_price, advertisement_description, advertisement_category_id, 
      advertisement_location, advertisement_view, advertisement_slug
      FROM grid_panel_advt order by advertisement_price;
```
### *4. Advertisments filter order by price descending*
```SQL
CURSOR order_price_asc
   IS
    SELECT id, advertisement_user_id, advertisement_name, advertisement_image, advertisement_price, advertisement_description, advertisement_category_id, 
      advertisement_location, advertisement_view, advertisement_slug
      FROM grid_panel_advt order by advertisement_price desc;
```

### *5. Advertisments filter by category name*
```SQL
CURSOR filter_cat_name (p_category IN varchar2)
     IS
       SELECT adv.id, advertisement_user_id, advertisement_name, advertisement_image, advertisement_price, 
       advertisement_description, advertisement_category_id, advertisement_location, advertisement_view, advertisement_slug
       FROM grid_panel_advt adv JOIN grid_panel_category cat on cat.name = p_category;
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

