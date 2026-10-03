const express = require("express");
const axios = require("axios");

const public_users = express.Router();

const BOOKS_URL = "http://localhost:5000/data/books.json";

async function fetchBooks() {
  const response = await axios.get(BOOKS_URL);
  return response.data;
}

// Get all books
public_users.get("/", async (req, res) => {
  try {
    const books = await fetchBooks();
    return res.status(200).json({ books });
  } catch (error) {
    return res.status(500).json({ message: "Error fetching books" });
  }
});

// Get book by ISBN
public_users.get("/isbn/:isbn", async (req, res) => {
  try {
    const books = await fetchBooks();
    const book = books[req.params.isbn];

    if (!book) {
      return res.status(404).json({ message: "Book not found" });
    }

    return res.status(200).json({ book });
  } catch (error) {
    return res.status(500).json({ message: "Error fetching book" });
  }
});

// Get books by author
public_users.get("/author/:author", async (req, res) => {
  try {
    const books = await fetchBooks();
    const author = decodeURIComponent(req.params.author).toLowerCase();
    const result = {};

    for (const isbn of Object.keys(books)) {
      if (books[isbn].author.toLowerCase().includes(author)) {
        result[isbn] = books[isbn];
      }
    }

    if (Object.keys(result).length === 0) {
      return res.status(404).json({
        message: "No books found by this author"
      });
    }

    return res.status(200).json({ books: result });
  } catch (error) {
    return res.status(500).json({ message: "Error fetching books" });
  }
});

// Get books by title
public_users.get("/title/:title", async (req, res) => {
  try {
    const books = await fetchBooks();
    const title = decodeURIComponent(req.params.title).toLowerCase();
    const result = {};

    for (const isbn of Object.keys(books)) {
      if (books[isbn].title.toLowerCase().includes(title)) {
        result[isbn] = books[isbn];
      }
    }

    if (Object.keys(result).length === 0) {
      return res.status(404).json({
        message: "No books found with this title"
      });
    }

    return res.status(200).json({ books: result });
  } catch (error) {
    return res.status(500).json({ message: "Error fetching books" });
  }
});

// Get reviews
public_users.get("/review/:isbn", async (req, res) => {
  try {
    const books = await fetchBooks();
    const book = books[req.params.isbn];

    if (!book) {
      return res.status(404).json({ message: "Book not found" });
    }

    return res.status(200).json({
      reviews: book.reviews
    });
  } catch (error) {
    return res.status(500).json({
      message: "Error fetching review"
    });
  }
});

module.exports.general = public_users;
module.exports.fetchBooks = fetchBooks;
