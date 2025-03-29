# 🗄️ SQLite Advanced Tutorial

<div align="center">
  
![SQLite Logo](https://www.sqlite.org/images/sqlite370_banner.gif)

[![GitHub stars](https://img.shields.io/github/stars/Harrypatria/SQLite_Advanced_Tutorial_Google_Colab?style=social)](https://github.com/Harrypatria/SQLite_Advanced_Tutorial_Google_Colab/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/Harrypatria/SQLite_Advanced_Tutorial_Google_Colab?style=social)](https://github.com/Harrypatria/SQLite_Advanced_Tutorial_Google_Colab/network/members)
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Harrypatria/SQLite_Advanced_Tutorial_Google_Colab/blob/main/SQLite.ipynb)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python Version](https://img.shields.io/badge/python-3.6%2B-blue)](https://www.python.org/downloads/)

</div>

## 🌟 Overview

A comprehensive, production-ready SQLite tutorial for Python developers, from basic operations to advanced implementations. Perfect for data scientists, web developers, embedded systems engineers, and anyone looking to leverage the power of this serverless, zero-configuration database.

This repository contains real-world examples, proven optimization techniques, and industry best practices that you can immediately implement in your projects.

## ✨ Key Features

- **📚 Complete Learning Path**: Progressive examples from beginner to advanced
- **⚡ Performance Optimization**: Practical techniques to squeeze maximum performance
- **🔄 Real-world Applications**: Task manager, IoT sensor data logger, sales analytics dashboard
- **📊 Data Science Integration**: Seamless integration with pandas, numpy, and visualization tools
- **🧪 Interactive Notebooks**: Run all examples directly in Google Colab
- **📱 Mobile & Embedded**: Techniques for resource-constrained environments
- **🛡️ Production-ready Code**: Industry best practices for error handling, security, and robustness

## 🚀 Quick Start

### Option 1: Run in Google Colab (No Installation Required)

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1sbuG_dWd465P7FrDGj4qZRsCfvkefgWI?usp=sharing)

### Option 2: Local Installation

```bash
# Clone the repository
git clone https://github.com/Harrypatria/SQLite_Advanced_Tutorial_Google_Colab.git
cd SQLite_Advanced_Tutorial_Google_Colab

# Set up a virtual environment (optional but recommended)
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Launch Jupyter notebook
jupyter notebook
```

## 📋 Table of Contents

1. **Fundamentals**
   - Database Connection
   - Creating Tables
   - CRUD Operations (Create, Read, Update, Delete)
   - SQL Query Basics

2. **Intermediate Operations**
   - SQLite with Pandas Integration
   - Transactions & Error Handling
   - Using Python Functions with SQLite
   - Relationships & Foreign Keys

3. **Advanced Techniques**
   - Performance Optimization & Indexing
   - Full-Text Search
   - Binary Data (BLOB) Storage
   - Web & Mobile Integration

4. **Real-world Applications**
   - Task Management Application
   - IoT Sensor Data Logger
   - Sales Analytics Dashboard
   - Connection Pooling for Multi-threaded Apps

5. **Production Best Practices**
   - Security Considerations
   - Backup & Recovery
   - Monitoring & Debugging
   - Deployment Strategies

## 🔥 Optimization Techniques Showcase

| Technique | Description | Performance Gain |
|-----------|-------------|-----------------|
| WAL Journal Mode | Write-Ahead Logging for concurrent operations | Up to 50x faster writes |
| Prepared Statements | Pre-compiled SQL for repeated execution | 3-5x faster for repeated queries |
| Bulk Operations | Batch inserts/updates in transactions | 10-100x faster than individual ops |
| Optimized Indexes | Strategic indexing for query patterns | 10-1000x faster lookups |
| Memory-Mapped I/O | Direct memory access for databases | 2-3x faster for large databases |
| Custom Collations | Performance-optimized string comparison | 2-4x faster text operations |

## 💡 Real-world Example: IoT Sensor Dashboard

```python
# Create an optimized database for IoT sensor data
conn = sqlite3.connect('sensors.db')
conn.execute('PRAGMA journal_mode = WAL')
conn.execute('PRAGMA synchronous = NORMAL')

# Define schema with indexes for time-series queries
conn.execute('''
CREATE TABLE sensors (
    id INTEGER PRIMARY KEY,
    timestamp TEXT NOT NULL,
    device_id TEXT NOT NULL,
    temperature REAL,
    humidity REAL
);
''')
conn.execute('CREATE INDEX idx_sensors_time_device ON sensors(timestamp, device_id);')

# Insert 10,000 data points in a single transaction
conn.execute('BEGIN TRANSACTION')
for i in range(10000):
    conn.execute(
        'INSERT INTO sensors VALUES (NULL, ?, ?, ?, ?)',
        (datetime.now().isoformat(), f'device_{i%10}', 20+random.random()*10, 30+random.random()*20)
    )
conn.execute('COMMIT')

# Query with pandas for analysis and visualization
import pandas as pd
df = pd.read_sql_query(
    "SELECT device_id, avg(temperature) as avg_temp FROM sensors GROUP BY device_id", 
    conn
)
```

## 📊 Performance Comparison

The chart below demonstrates the impact of various optimization techniques:

```
┌────────────────────┬────────────────────┬────────────────────┐
│                    │                    │                    │
│                    │                    │                    │
│    Standard SQL    │    Transactions    │  Optimized SQLite  │
│                    │                    │                    │
│      1x speed      │      10x speed     │     100x speed     │
│                    │                    │                    │
└────────────────────┴────────────────────┴────────────────────┘
```

## 🔧 Advanced Use Cases

### Web Application Integration (Flask)

```python
from flask import Flask, g
import sqlite3

app = Flask(__name__)

def get_db():
    db = getattr(g, '_database', None)
    if db is None:
        db = g._database = sqlite3.connect('app.db')
        db.row_factory = sqlite3.Row
    return db

@app.teardown_appcontext
def close_connection(exception):
    db = getattr(g, '_database', None)
    if db is not None:
        db.close()

@app.route('/api/data')
def get_data():
    cursor = get_db().execute('SELECT * FROM items ORDER BY timestamp DESC LIMIT 100')
    items = [dict(row) for row in cursor.fetchall()]
    return {'items': items}
```

### Data Science Pipeline

```python
import pandas as pd
import sqlite3

# Load data
df = pd.read_csv('large_dataset.csv')

# Process and clean data
df_processed = transform_data(df)

# Store in SQLite for efficient querying
conn = sqlite3.connect('analysis.db')
df_processed.to_sql('cleaned_data', conn, if_exists='replace', index=False)

# Run complex aggregations with SQL
results = pd.read_sql_query('''
    SELECT 
        category,
        COUNT(*) as count,
        AVG(value) as avg_value,
        SUM(CASE WHEN flag = 1 THEN 1 ELSE 0 END) as flag_count
    FROM cleaned_data
    GROUP BY category
    HAVING count > 100
    ORDER BY avg_value DESC
''', conn)
```

## 📚 Learning Path

This tutorial is designed to progressively build your SQLite expertise:

1. **Day 1**: Basic operations and CRUD
2. **Day 2**: Relationships and advanced queries
3. **Day 3**: Integration with pandas and data analysis
4. **Day 4**: Performance optimization and indexing
5. **Day 5**: Building real-world applications

## 🤔 Why SQLite?

- **Zero Configuration**: No server setup, just import and use
- **Cross-Platform**: Works identically across Windows, macOS, Linux, Android, iOS
- **Incredibly Reliable**: 100% test coverage and battle-tested in billions of deployments
- **Self-Contained**: Single file database with no external dependencies
- **Full-Featured SQL**: Supports most SQL features including complex joins, views, and triggers
- **Excellent Performance**: Faster than many client-server database setups for most applications
- **Public Domain**: Free for any use (commercial or private)

## 📊 Industry Applications

- **Mobile Apps**: Local data storage on iOS and Android
- **Desktop Applications**: Configuration and data storage
- **Embedded Systems**: IoT devices and edge computing
- **Web Development**: Small to medium websites and prototyping
- **Data Science**: Data cleaning, transformation, and analysis
- **Testing**: Isolated test databases that require no setup

## 🛠️ Troubleshooting Common Issues

<details>
<summary><b>Database Locked Error</b></summary>

When encountering "database is locked" errors:

```python
# Solution 1: Use WAL mode
conn.execute('PRAGMA journal_mode = WAL')

# Solution 2: Increase timeout
conn.execute('PRAGMA busy_timeout = 5000')  # 5 second timeout

# Solution 3: Better transaction management
conn.isolation_level = None  # Control transactions manually
conn.execute('BEGIN IMMEDIATE')
try:
    # Your operations here
    conn.execute('COMMIT')
except Exception:
    conn.execute('ROLLBACK')
```
</details>

<details>
<summary><b>Performance Issues with Large Datasets</b></summary>

When SQLite feels slow with large datasets:

```python
# Solution 1: Strategic indexing
conn.execute('CREATE INDEX idx_column_name ON table_name(column_name)')

# Solution 2: Bulk operations in transactions
conn.execute('BEGIN TRANSACTION')
# Insert many rows
conn.execute('COMMIT')

# Solution 3: Optimize queries
# Instead of:
# SELECT * FROM large_table WHERE condition
# Use:
# SELECT specific_column FROM large_table WHERE condition LIMIT 1000
```
</details>

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Commit your changes: `git commit -m 'Add some amazing feature'`
4. Push to the branch: `git push origin feature/amazing-feature`
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 📞 Contact

Harry Patria 
https://www.linkedin.com/in/harry-patria/

Project Link: [https://github.com/Harrypatria/SQLite_Advanced_Tutorial_Google_Colab](https://github.com/Harrypatria/SQLite_Advanced_Tutorial_Google_Colab)

---

<div align="center">
  
⭐ **Star this repository if you found it helpful!** ⭐

</div>
