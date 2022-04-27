# inf232-final
Final project for INF 232 course

## ERD 
 ![image](https://user-images.githubusercontent.com/71185943/165450161-3de74b7a-03c5-4815-ab8d-f0b87b172bff.png)

 
 
## Functions and Procedures (at least 4)

## Cursor (at least 4)

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

```

### *2. Insert Users*
```SQL

```

