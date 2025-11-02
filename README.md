      /*
 * Click nbfs://nbhost/SystemFileSystem/Templates/Licenses/license-default.txt to change this license
 */

package com.mycompany.midtermprojectacp;

import java.util.*;
import java.io.*;

class Person {
    String name;
    String contact_information;

    Person() {
        name = null;
        contact_information = null;
    }
}

class User extends Person {
    int userID;
    int borrowed_booksID;

    User() {
        super(); // calling Person constructor
        userID = 0;
        borrowed_booksID = 0;
    }
}

class Book {
    int bookID;
    String title;
    String author;
    String genre;
    boolean availability_status;

    Book() {
        bookID = 0;
        title = null;
        author = null;
        genre = null;
        availability_status = false;
    }
}

class Library {
    Scanner sc = new Scanner(System.in);
    Random random = new Random();

    static ArrayList<Book> bookCollection = new ArrayList<>();
    static ArrayList<User> userCollection = new ArrayList<>();

    void addNewBook() {
        Book newBook = new Book();
        while (true) {
            try {
                System.out.println("Please enter the id seed (number) for random book ID generation:");
                int randomnum = random.nextInt(sc.nextInt());
                newBook.bookID = randomnum;
                System.out.println("Please enter the title of the book:");
                newBook.title = sc.next();
                System.out.println("Please enter the author name:");
                newBook.author = sc.next();
                System.out.println("Please enter the genre of the book:");
                newBook.genre = sc.next();
                newBook.availability_status = true;
                break;
            } catch (Exception e) {
                System.out.println("Invalid Input! Try again");
                sc.next();
            }
        }
        bookCollection.add(newBook);
        bookFileWrite("C:\\BookStorage", newBook);
    }

    void addNewUser() {
        User newUser = new User();
        while (true) {
            try {
                System.out.println("Please enter the id seed (number) for random user ID generation:");
                int randomnum = random.nextInt(sc.nextInt());
                newUser.userID = randomnum;
                System.out.println("Please enter the name of the user:");
                newUser.name = sc.next();
                System.out.println("Please enter the contact information of the user:");
                newUser.contact_information = sc.next();
                newUser.borrowed_booksID = 0;
                break;
            } 
            catch (Exception e) {
                System.out.println("Invalid input! Try again");
                sc.next();
            }
        }
        userCollection.add(newUser);
        userFileWrite("C:\\UserStorage", newUser);
    }

    static void displayBooks(final int a) {
        boolean flag = false;
        for (Book book : bookCollection) {
            if (book.bookID == a) {
                if (book.availability_status) {
                    System.out.println("Book Id\tTitle\tAuthor\tGenre\tAvailable");
                    System.out.printf("%d\t%s\t%s\t%s\t%b%n",
                            book.bookID, book.title, book.author, book.genre, book.availability_status);
                    flag = true;
                    break;
                } 
                else {
                    System.out.println("Sorry, the book has already been borrowed!");
                    flag = true;
                }
            }
        }
        
        if (!flag) System.out.println("Book not found!");
    }

    void borrowBook(int uID, int bID) {
        try (RandomAccessFile reader = new RandomAccessFile("C:\\BookStorage", "r")) {
            String line;
            int i = 0;
            while ((line = reader.readLine()) != null) {
                if (i % 6 == 0 && bID == Integer.parseInt(line)) {
                    try (RandomAccessFile raf = new RandomAccessFile("C:\\BookStorage", "rw")) {
                        raf.seek(0);
                        for (int j = 0; j <= i + 3; j++) raf.readLine();
                        raf.writeBytes("0");
                    }
                }
                i++;
            }
        } 
        catch (Exception e) {
            System.out.println("Error borrowing book: " + e);
        }

        try (RandomAccessFile reader = new RandomAccessFile("C:\\UserStorage", "r")) {
            String line;
            int i = 0;
            while ((line = reader.readLine()) != null) {
                if (i % 5 == 0 && uID == Integer.parseInt(line)) {
                    try (RandomAccessFile raf = new RandomAccessFile("C:\\UserStorage", "rw")) {
                        raf.seek(0);
                        for (int j = 0; j <= 2 + i; j++) raf.readLine();
                        raf.writeBytes(Integer.toString(bID));
                    }
                }
                i++;
            }
        }
        catch (Exception e) {
            System.out.println("Error updating user borrow record: " + e);
        }
    }

    void bookReturn(int uId, int bId) {
        try (RandomAccessFile raf = new RandomAccessFile("C:\\UserStorage", "rw")) {
            String line;
            int i = 0;
            while ((line = raf.readLine()) != null) {
                if (i % 5 == 0 && uId == Integer.parseInt(line)) {
                    raf.seek(0);
                    for (int j = 0; j <= 2 + i; j++) raf.readLine();
                    raf.writeBytes("0");
                    break;
                }
                i++;
            }
        } 
        catch (Exception e) {
            System.out.println("Error returning book for user!");
        }

        try (RandomAccessFile raf = new RandomAccessFile("C:\\BookStorage", "rw")) {
            String line;
            int i = 0;
            while ((line = raf.readLine()) != null) {
                if (i % 6 == 0 && bId == Integer.parseInt(line)) {
                    raf.seek(0);
                    for (int j = 0; j <= i + 3; j++) raf.readLine();
                    raf.writeBytes("1");
                    break;
                }
                i++;
            }
        } 
        catch (Exception e) {
            System.out.println("Error returning book for library!");
        }
    }

    static void fileCreation(String filename) {
        try {
            File file = new File(filename);
            if (file.createNewFile()) System.out.println("New file created!");
        } 
        catch (Exception e) {
            System.out.println("Could not create the file");
        }
    }

    static void fileReadBook() {
        String a = null, y = null, b = null, c = null, d = null;
        boolean x = false;
        try (RandomAccessFile reader = new RandomAccessFile("C:\\BookStorage", "r")) {
            String line;
            int i = 1, n = 0;
            while ((line = reader.readLine()) != null) {
                if (i % 6 == 0) {
                    Book newBook = new Book();
                    newBook.bookID = Integer.parseInt(a);
                    newBook.title = b;
                    newBook.author = c;
                    newBook.genre = d;
                    newBook.availability_status = y.equals("1");
                    bookCollection.add(newBook);
                    n += 6;
                } 
                else if (i == 1 + n) a = line;
                else if (i == 2 + n) b = line;
                else if (i == 3 + n) c = line;
                else if (i == 4 + n) d = line;
                else if (i == 5 + n) y = line;
                i++;
            }
        } 
        catch (Exception e) {
            System.out.println(e);
        }
    }

    static void bookFileWrite(String filename, Book object) {
        fileCreation(filename);
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(filename, true))) {
            writer.write(object.bookID + "\n" + object.title + "\n" + object.author + "\n" +
                    object.genre + "\n" + (object.availability_status ? "1" : "0") + "\n-\n");
        } 
        catch (Exception e) {
            System.out.println("Could not write book to file");
        }
    }

    static void userFileWrite(String filename, User object) {
        fileCreation(filename);
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(filename, true))) {
            writer.write(object.userID + "\n" + object.name + "\n" +
                    object.contact_information + "\n" + object.borrowed_booksID + "\n-\n");
        } catch (Exception e) {
            System.out.println("Could not write user to file");
        }
    }

    static void fileReadUser() {
        String a = null, b = null, c = null, d = null;
        try (RandomAccessFile reader = new RandomAccessFile("C:\\UserStorage", "r")) {
            String line;
            int i = 1, n = 0;
            while ((line = reader.readLine()) != null) {
                if (i % 5 == 0) {
                    User newUser = new User();
                    newUser.userID = Integer.parseInt(a);
                    newUser.name = b;
                    newUser.contact_information = c;
                    newUser.borrowed_booksID = Integer.parseInt(d);
                    userCollection.add(newUser);
                    n += 5;
                } 
                else if (i == 1 + n) a = line;
                else if (i == 2 + n) b = line;
                else if (i == 3 + n) c = line;
                else if (i == 4 + n) d = line;
                i++;
            }
        } 
        catch (Exception e) {
            System.out.println(e);
        }
    }

    static void displayOnlyIdsOfBooks() {
        bookCollection.clear();
        fileReadBook();
        System.out.println("Available books:");
        for (Book book : bookCollection) {
            if (book.availability_status){
            System.out.println(book.bookID);
            }
        }
    }

    static void displayOnlyIdsOfUsers() {
        userCollection.clear();
        fileReadUser();
        System.out.println("Registered users:");
        for (User user : userCollection) {
            System.out.println(user.userID);
        }
    }

    void bookSearch() {
        throw new UnsupportedOperationException("Not supported yet."); // Generated from nbfs://nbhost/SystemFileSystem/Templates/Classes/Code/GeneratedMethodBody
    }
}

public class MidtermProjectACP {
    static void menu() {
        Scanner sc = new Scanner(System.in);
        Library l = new Library();
        int input;

        while (true) {
            try {
                System.out.println("_________________ Library Management System _________________");
                System.out.println("");
                System.out.println("1. Add new book");
                System.out.println("2. Add new user/member");
                System.out.println("3. Display book");
                System.out.println("4. Borrow book");
                System.out.println("5. Return book");
                System.out.println("6. Display available book IDs");
                System.out.println("7. Display user IDs");
                System.out.println("8. Search Book (by user id)");
                System.out.println("9. Exit");
                System.out.println("");
                System.out.println("Enter your choice by pressig the numbers from 1 to 9: ");
                input = sc.nextInt();

                switch (input) {
                    case 1 -> {
                        l.addNewBook();
                        Library.fileReadBook();
                    }
                    case 2 -> {
                        l.addNewUser();
                        Library.fileReadUser();
                    }
                    case 3 -> {
                        System.out.println("Enter book ID:");
                        Library.displayBooks(sc.nextInt());
                    }
                    case 4 -> {
                        System.out.println("Enter user ID:");
                        int uId = sc.nextInt();
                        System.out.println("Enter book ID to borrow:");
                        int bId = sc.nextInt();
                        l.borrowBook(uId, bId);
                    }
                    case 5 -> {
                        System.out.println("Enter user ID:");
                        int uId = sc.nextInt();
                        System.out.println("Enter book ID to return:");
                        int bId = sc.nextInt();
                        l.bookReturn(uId, bId);
                    }
                    case 6 -> Library.displayOnlyIdsOfBooks();
                    case 7 -> Library.displayOnlyIdsOfUsers();
                   case 8 -> l.bookSearch();

                    case 9 -> {
                        System.out.println("Exiting program...");
                        return;
                    }
                    default -> System.out.println("Invalid operation!");
                }

                System.out.println("\nDo you want to continue? (Y/N)");
                String answer = sc.next();
                if (!answer.equalsIgnoreCase("Y")) break;

            } catch (Exception e) {
                System.out.println("Invalid Input! Try again.");
                sc.next();
            }
        }
    }

    public static void main(String[] args) {
        menu();
    }
}

