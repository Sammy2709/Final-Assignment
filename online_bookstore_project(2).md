SENG8071 - Final Assignment

Meetkumar Hareshbhai Prajapati - 8952762
Sameer Pratik Rao - 8888610
Gurkirat Singh - 8882095

# Group Members and Assigned Duties
------------------------------------------------------
| Member Name    | Student ID | Assigned Duty        |
|----------------|------------|----------------------|
| Gurkirat S     | 8882095    | Database Design      |
| Meetkumar HP   | 8952762    | CRUD Operations      |
| Sameer P Rao   | 8888610    | TypeORM              |
------------------------------------------------------

## Tables and Attributes
### Books
-------------------------------
| Attribute Name  | Data Type |
|-----------------|-----------|
| BookId          | INT       |
| Title           | VARCHAR   |
| Genre           | VARCHAR   |
| AuthorId        | INT       |
| PublisherId     | INT       |
| PublishDate     | DATE      |
| Price           | DECIMAL   |
-------------------------------
### Authors
-------------------------------
| Attribute Name  | Data Type |
|-----------------|-----------|
| AuthorId        | INT       |
| Name            | VARCHAR   |
| Biography       | TEXT      |
| Birthdate       | DATE      |
-------------------------------
### BookAuthor
-------------------------------
| Attribute Name  | Data Type |
------------------|-----------|
| BookId          | INT       |
| AuthorId        | INT       |
| AuthorName      | VARCHAR   |
-------------------------------
### Publishers
-------------------------------
| Attribute Name  | Data Type |
|-----------------|-----------|
| PublisherId     | INT       |
| Name            | VARCHAR   |
| ContactInfo     | VARCHAR   |
-------------------------------
### Customers
-------------------------------
| Attribute Name  | Data Type |
|-----------------|-----------|
| CustomerId      | INT       |
| Name            | VARCHAR   |
| Email           | VARCHAR   |
| TotalSpent      | DECIMAL   |
| RegistrationDate| DATE      |
-------------------------------
### Orders
------------------------------
| Attribute Name | Data Type |
|----------------|-----------|
| OrderId        | INT       |
| CustomerId     | INT       |
| OrderDate      | DATE      |
| TotalAmount    | DECIMAL   |
------------------------------
### OrderDetails
-------------------------------
| Attribute Name  | Data Type |
|-----------------|-----------|
| OrderDetailId   | INT       |
| OrderId         | INT       |
| BookId          | INT       |
| Quantity        | INT       |
| Price           | DECIMAL   |
-------------------------------
### Reviews
------------------------------
| Attribute Name | Data Type |
|----------------|-----------|
| ReviewId       | INT       |
| CustomerId     | INT       |
| BookId         | INT       |
| Rating         | INT       |
| ReviewDate     | DATE      |
------------------------------

#### SQL
CREATE TABLE Book (
    BookID INT PRIMARY KEY,
    Title VARCHAR(20) NOT NULL,
    Genre VARCHAR(10) NOT NULL,
    PublicationDate DATE NOT NULL,
    Format ENUM('Physical', 'E-book', 'Audiobook') NOT NULL,
    Price DECIMAL(10, 2) NOT NULL,
    AverageRating DECIMAL(3, 2),
    PublisherID INT,
    FOREIGN KEY (PublisherID) REFERENCES Publisher(PublisherID)
);

-- Create the Author table
CREATE TABLE Author (
    AuthorID INT PRIMARY KEY,
    Name VARCHAR(20) NOT NULL,
    Biography TEXT
);

-- Create the BookAuthor table s
CREATE TABLE BookAuthor (
    BookID INT,
    AuthorID INT,
   AuthorName VARCHAR(20) NOT NULL
    PRIMARY KEY (BookID, AuthorID),
    FOREIGN KEY (BookID) REFERENCES Book(BookID),
    FOREIGN KEY (AuthorID) REFERENCES Author(AuthorID)
);

-- Create the Publisher table
CREATE TABLE Publisher (
    PublisherID INT PRIMARY KEY,
    Name VARCHAR(20) NOT NULL,
    ContactInfo VARCHAR(20)
);

-- Create the Customer table
CREATE TABLE Customer (
    CustomerID INT PRIMARY KEY,
    Name VARCHAR(20) NOT NULL,
    Email VARCHAR(20) NOT NULL UNIQUE,
    RegistrationDate DATE NOT NULL,
    TotalSpentLastYear DECIMAL(10, 2) DEFAULT 0
);

-- Create the Order table
CREATE TABLE `Order` (
    OrderID INT PRIMARY KEY,
    CustomerID INT,
    OrderDate DATETIME NOT NULL,
    TotalAmount DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (CustomerID) REFERENCES Customer(CustomerID)
);

-- Create the OrderItem table
CREATE TABLE OrderItem (
    OrderItemID INT PRIMARY KEY,
    OrderID INT,
    BookID INT,
    Quantity INT NOT NULL,
    Price DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (OrderID) REFERENCES `Order`(OrderID),
    FOREIGN KEY (BookID) REFERENCES Book(BookID)
);

//Create the Review table//

CREATE TABLE Review (
    ReviewID INT PRIMARY KEY,
    BookID INT,
    CustomerID INT,
    Rating INT NOT NULL CHECK (rating >= 1 AND rating <= 5),
    ReviewText TEXT,
    ReviewDate DATETIME NOT NULL,
    FOREIGN KEY (BookID) REFERENCES Book(BookID),
    FOREIGN KEY (CustomerID) REFERENCES Customer(CustomerID)
);

#3. CRUD Functions
Example for Customer Entity
Create

typescript
Copy code
const newCustomer = customerRepository.create({
  name: "John Doe",
  email: "john.doe@example.com",
  totalSpent: 0,
  joinDate: new Date()
});
await customerRepository.save(newCustomer);
Read

typescript
Copy code
const customer = await customerRepository.findOne({ where: { id: 1 } });
Update

typescript
Copy code
const customer = await customerRepository.findOne({ where: { id: 1 } });
customer.totalSpent = 100;
await customerRepository.save(customer);
Delete

typescript
Copy code
await customerRepository.delete({ id: 1 });
4. Unit Tests
Example for Customer CRUD Operations
typescript
Copy code
import { expect } from 'chai';
import { getRepository } from 'typeorm';
import { Customer } from '../entities/Customer';

describe('Customer CRUD operations', () => {
  const customerRepository = getRepository(Customer);

  it('should create a new customer', async () => {
    const newCustomer = customerRepository.create({
      name: "Jane Doe",
      email: "jane.doe@example.com",
      totalSpent: 0,
      joinDate: new Date()
    });
    const savedCustomer = await customerRepository.save(newCustomer);
    expect(savedCustomer).to.have.property('id');
  });

  it('should read a customer', async () => {
    const customer = await customerRepository.findOne({ where: { id: 1 } });
    expect(customer).to.not.be.null;
  });

  it('should update a customer', async () => {
    const customer = await customerRepository.findOne({ where: { id: 1 } });
    customer.totalSpent = 150;
    const updatedCustomer = await customerRepository.save(customer);
    expect(updatedCustomer.totalSpent).to.equal(150);
  });

  it('should delete a customer', async () => {
    await customerRepository.delete({ id: 1 });
    const customer = await customerRepository.findOne({ where: { id: 1 } });
    expect(customer).to.be.null;
  });
});