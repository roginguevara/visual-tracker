<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Visual Tracker</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap" rel="stylesheet">
  <style>
    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      padding: 2rem;
      font-family: 'Inter', sans-serif;
      background-color: #f8fafc;
      color: #111827;
    }

    .container {
      max-width: 1200px;
      margin: auto;
    }

    h1 {
      font-size: 2rem;
      font-weight: 600;
      text-align: center;
      margin-bottom: 1.5rem;
    }

    .search-box {
      width: 100%;
      margin-bottom: 1.5rem;
    }

    .search-box input {
      width: 100%;
      padding: 12px 16px;
      font-size: 1rem;
      border: 1px solid #d1d5db;
      border-radius: 8px;
      transition: border 0.3s;
    }

    .search-box input:focus {
      outline: none;
      border-color: #6366f1;
      box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.2);
    }

    .table-wrapper {
      overflow-x: auto;
      background-color: white;
      border-radius: 12px;
      box-shadow: 0 4px 20px rgba(0,0,0,0.05);
    }

    table {
      width: 100%;
      border-collapse: collapse;
      min-width: 900px;
    }

    thead {
      background-color: #f1f5f9;
    }

    th, td {
      padding: 16px;
      text-align: left;
      font-size: 0.95rem;
      border-top: 1px solid #e5e7eb;
      vertical-align: top;
      color: #4b5563;
    }

    th {
      font-weight: 600;
      color: #374151;
    }

    tr:hover {
      background-color: #f9fafb;
    }

    td a {
      color: #2563eb;
      text-decoration: none;
    }

    td a:hover {
      text-decoration: underline;
    }

    /* Article Title column: allow wrapping */
    td.article-title {
      white-space: normal !important;
      max-width: 400px;
    }

    /* Other cells: keep them neat */
    td:not(.article-title) {
      max-width: 250px;
      overflow: hidden;
      white-space: nowrap;
      text-overflow: ellipsis;
    }

    @media (max-width: 768px) {
      th, td {
        padding: 12px;
        font-size: 0.9rem;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>Visual Tracker</h1>
    <div class="search-box">
      <input type="text" id="searchInput" placeholder="🔍 Search articles, brands, types..." />
    </div>

    <div class="table-wrapper">
      <table id="dataTable">
        <thead><tr></tr></thead>
        <tbody></tbody>
      </table>
    </div>
  </div>

  <script>
    const scriptURL = "https://script.google.com/macros/s/AKfycbwrDTIKWct2hVM8efZ523q0dpZ-sBWyXgpDqbhXlByQ-aEsmRldF4PdQmAEBRDJNwITdQ/exec";

    fetch(scriptURL)
      .then(res => res.json())
      .then(data => {
        const tableHead = document.querySelector("thead tr");
        const tableBody = document.querySelector("tbody");

        if (!data || data.length === 0) {
          tableHead.innerHTML = "<th>No Data</th>";
          return;
        }

        // Build headers
        const headers = data[0];
        tableHead.innerHTML = "";
        headers.forEach(header => {
          const th = document.createElement("th");
          th.textContent = header;
          tableHead.appendChild(th);
        });

        // Build rows
        data.slice(1).forEach(row => {
          const tr = document.createElement("tr");
          row.forEach((cell, i) => {
            const td = document.createElement("td");

            // Set special class for "Article Title"
            if (headers[i].toLowerCase().includes("article title")) {
              td.classList.add("article-title");
            }

            // Format date
            if (headers[i].toLowerCase().includes("date")) {
              const d = new Date(cell);
              cell = isNaN(d) ? cell : d.toISOString().split("T")[0];
            }

            // Convert links
            if (typeof cell === "string" && cell.startsWith("http")) {
              td.innerHTML = `<a href="${cell}" target="_blank" title="${cell}">${cell.length > 40 ? cell.slice(0, 40) + "..." : cell}</a>`;
            } else {
              td.textContent = cell;
              td.title = cell;
            }

            tr.appendChild(td);
          });
          tableBody.appendChild(tr);
        });
      })
      .catch(error => {
        console.error("Error loading data:", error);
      });

    // Search
    document.getElementById("searchInput").addEventListener("input", function () {
      const term = this.value.toLowerCase();
      document.querySelectorAll("tbody tr").forEach(row => {
        row.style.display = row.textContent.toLowerCase().includes(term) ? "" : "none";
      });
    });
  </script>
</body>
</html>
