### Form data (POST)
```js
import React, { useState } from "react";
import axios from "axios";

function SearchBar() {
  const [formData, setFormData] = useState({
    name: "",
    email: "",
    message: "",
  });

  const [responseText, setResponseText] = useState("");
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  // Dummy API URL (Replace with your backend)
  const API_URL = "https://jsonplaceholder.typicode.com/posts";

  const handleChange = (e) => {
    setFormData({
      ...formData,
      [e.target.name]: e.target.value,
    });
  };

  const handleSubmit = async () => {
    if (!formData.name.trim() || !formData.email.trim() || !formData.message.trim()) {
      setError("All fields are required.");
      return;
    }

    setLoading(true);
    setError(null);

    try {
      const response = await axios.post(API_URL, formData);
      setResponseText(`Response: ${JSON.stringify(response.data)}`);
    } catch (err) {
      setError("Failed to send request.");
    } finally {
      setLoading(false);
    }
  };

  return (
    <div style={{ textAlign: "center", padding: "20px" }}>
      <h2>Multi-Field POST Request</h2>

      <input
        type="text"
        name="name"
        placeholder="Enter your name..."
        value={formData.name}
        onChange={handleChange}
        style={{
          padding: "10px",
          width: "60%",
          marginBottom: "10px",
          border: "1px solid #ccc",
          borderRadius: "5px",
        }}
      />
      <br />

      <input
        type="email"
        name="email"
        placeholder="Enter your email..."
        value={formData.email}
        onChange={handleChange}
        style={{
          padding: "10px",
          width: "60%",
          marginBottom: "10px",
          border: "1px solid #ccc",
          borderRadius: "5px",
        }}
      />
      <br />

      <textarea
        name="message"
        placeholder="Enter your message..."
        value={formData.message}
        onChange={handleChange}
        rows="4"
        style={{
          padding: "10px",
          width: "60%",
          marginBottom: "10px",
          border: "1px solid #ccc",
          borderRadius: "5px",
        }}
      />
      <br />

      <button
        onClick={handleSubmit}
        style={{
          padding: "10px 15px",
          backgroundColor: "#007bff",
          color: "#fff",
          border: "none",
          borderRadius: "5px",
          cursor: "pointer",
        }}
      >
        Send
      </button>

      {loading && <p>Loading...</p>}
      {error && <p style={{ color: "red" }}>{error}</p>}
      {responseText && <p><strong>{responseText}</strong></p>}
    </div>
  );
}

export default SearchBar;
```
