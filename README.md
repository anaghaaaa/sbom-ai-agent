# sbom-ai-agent
AI agent to automate SBOM and FOSS metadata enrichment from BDBA scan reports


Creating an AI agent to automate your SBOM and FOSS work involves building a system that can:
1.	Ingest and clean data from BDBA scan reports.
2.	Enrich metadata by searching the internet.
3.	Output a complete Excel file with all required fields.

 ## high-level architecture for the AI agent:
1. Input Handler
•	Accepts CSV or Excel files (BDBA scan reports).
•	Parses and extracts Component Name and Version String.
2. Deduplication Engine
•	Removes duplicate entries.
•	Keeps one entry per unique component-version pair.
3. Metadata Enrichment Module
•	Uses APIs or web scraping to fetch:
o	Author name
o	Supplier name
o	License
o	License type
•	Sources: GitHub, SPDX, Maven Central, PyPI, NPM, etc.
4. Validation & Normalization
•	Ensures consistent formatting (e.g., license names).
•	Flags unknown or ambiguous entries for manual review.
5. Output Generator
•	Writes the enriched data to an Excel file.
•	Matches your preferred format:\ Supplier name, AuthorName, Component Name, Version String, Open source vs proprietary, License, License type

🛠️ Technologies You Can Use
-  Task	Tools
- File parsing	pandas, openpyxl
- Web search	requests, BeautifulSoup, SPDX API, GitHub API
- Deduplication	pandas
- Output	pandas.to_excel()
- Automation	Python scripts, or integrate into a CI/CD pipeline
- UI (optional)	Streamlit or Flask for a simple web interface
________________________________________
🧪 Prototype Agent Flow (Python-based)
## Pseudocode
load_sbom_file()
clean_duplicates()
for each component-version:
    metadata = search_metadata_online(component, version)
    enrich_row(metadata)
save_to_excel()
________________________________________
🚀 Deployment Options
•	Local script: Run manually or via cron job.
•	Web app: Use Flask or FastAPI for a UI.
•	CI/CD integration: Trigger on new scan uploads.
•	Agent in Azure: Deploy as a Function App or Logic App.
________________________________________


##Stage 1 : setting up environment

Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Install the latest PowerShell for new features and improvements! https://aka.ms/PSWindows

PS C:\Users\SESA754157> git clone https://github.com/anaghaaaa/sbom-ai-agent.git
Cloning into 'sbom-ai-agent'...
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (5/5), done.
remote: Total 5 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (5/5), done.
PS C:\Users\SESA754157> cd .\sbom-ai-agent\

PS C:\Users\SESA754157\sbom-ai-agent> mkdir data


    Directory: C:\Users\SESA754157\sbom-ai-agent


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        09-09-2025     09:36                data


PS C:\Users\SESA754157\sbom-ai-agent> mkdir src


    Directory: C:\Users\SESA754157\sbom-ai-agent


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        09-09-2025     09:36                src


PS C:\Users\SESA754157\sbom-ai-agent> New-Item -Path . -Name "requirements.txt" -ItemType "File"


    Directory: C:\Users\SESA754157\sbom-ai-agent


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        09-09-2025     09:37              0 requirements.txt

PS C:\Users\SESA754157\sbom-ai-agent> New-Item -Path . -Name "sbom_agent.py" -ItemType "File"    Directory: C:\Users\SESA754157\sbom-ai-agent


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        09-09-2025     09:37              0 sbom_agent.py


PS C:\Users\SESA754157\sbom-ai-agent> notepad requirements.txt

PS C:\Users\SESA754157\sbom-ai-agent> python --version
Python 3.13.2
PS C:\Users\SESA754157\sbom-ai-agent> Invoke-WebRequest -Uri https://bootstrap.pypa.io/get-pip.py -OutFile get-pip.py
PS C:\Users\SESA754157\sbom-ai-agent> python get-pip.py
Collecting pip
  Downloading pip-25.2-py3-none-any.whl.metadata (4.7 kB)
Downloading pip-25.2-py3-none-any.whl (1.8 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 1.8/1.8 MB 3.1 MB/s  0:00:00
Installing collected packages: pip
Successfully installed pip-25.2
PS C:\Users\SESA754157\sbom-ai-agent> pip --version
pip 25.2 from C:\Users\SESA754157\AppData\Local\Programs\Python\Python313\Lib\site-packages\pip (python 3.13)
PS C:\Users\SESA754157\sbom-ai-agent> pip install -r requirements.txt
Collecting pandas (from -r requirements.txt (line 1))
  Downloading pandas-2.3.2-cp313-cp313-win_amd64.whl.metadata (19 kB)
Collecting openpyxl (from -r requirements.txt (line 2))
  Downloading openpyxl-3.1.5-py2.py3-none-any.whl.metadata (2.5 kB)
Collecting requests (from -r requirements.txt (line 3))
  Downloading requests-2.32.5-py3-none-any.whl.metadata (4.9 kB)
Collecting beautifulsoup4 (from -r requirements.txt (line 4))
  Downloading beautifulsoup4-4.13.5-py3-none-any.whl.metadata (3.8 kB)
Collecting tqdm (from -r requirements.txt (line 5))
  Downloading tqdm-4.67.1-py3-none-any.whl.metadata (57 kB)
Collecting numpy>=1.26.0 (from pandas->-r requirements.txt (line 1))
  Downloading numpy-2.3.2-cp313-cp313-win_amd64.whl.metadata (60 kB)
Collecting python-dateutil>=2.8.2 (from pandas->-r requirements.txt (line 1))
  Downloading python_dateutil-2.9.0.post0-py2.py3-none-any.whl.metadata (8.4 kB)
Collecting pytz>=2020.1 (from pandas->-r requirements.txt (line 1))
  Downloading pytz-2025.2-py2.py3-none-any.whl.metadata (22 kB)
Collecting tzdata>=2022.7 (from pandas->-r requirements.txt (line 1))
  Downloading tzdata-2025.2-py2.py3-none-any.whl.metadata (1.4 kB)
Collecting et-xmlfile (from openpyxl->-r requirements.txt (line 2))
  Downloading et_xmlfile-2.0.0-py3-none-any.whl.metadata (2.7 kB)
Collecting charset_normalizer<4,>=2 (from requests->-r requirements.txt (line 3))
  Downloading charset_normalizer-3.4.3-cp313-cp313-win_amd64.whl.metadata (37 kB)
Collecting idna<4,>=2.5 (from requests->-r requirements.txt (line 3))
  Downloading idna-3.10-py3-none-any.whl.metadata (10 kB)
Collecting urllib3<3,>=1.21.1 (from requests->-r requirements.txt (line 3))
  Downloading urllib3-2.5.0-py3-none-any.whl.metadata (6.5 kB)
Collecting certifi>=2017.4.17 (from requests->-r requirements.txt (line 3))
  Downloading certifi-2025.8.3-py3-none-any.whl.metadata (2.4 kB)
Collecting soupsieve>1.2 (from beautifulsoup4->-r requirements.txt (line 4))
  Downloading soupsieve-2.8-py3-none-any.whl.metadata (4.6 kB)
Collecting typing-extensions>=4.0.0 (from beautifulsoup4->-r requirements.txt (line 4))
  Downloading typing_extensions-4.15.0-py3-none-any.whl.metadata (3.3 kB)
Collecting colorama (from tqdm->-r requirements.txt (line 5))
  Downloading colorama-0.4.6-py2.py3-none-any.whl.metadata (17 kB)
Collecting six>=1.5 (from python-dateutil>=2.8.2->pandas->-r requirements.txt (line 1))
  Downloading six-1.17.0-py2.py3-none-any.whl.metadata (1.7 kB)
Downloading pandas-2.3.2-cp313-cp313-win_amd64.whl (11.0 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 11.0/11.0 MB 3.1 MB/s  0:00:03
Downloading openpyxl-3.1.5-py2.py3-none-any.whl (250 kB)
Downloading requests-2.32.5-py3-none-any.whl (64 kB)
Downloading charset_normalizer-3.4.3-cp313-cp313-win_amd64.whl (107 kB)
Downloading idna-3.10-py3-none-any.whl (70 kB)
Downloading urllib3-2.5.0-py3-none-any.whl (129 kB)
Downloading beautifulsoup4-4.13.5-py3-none-any.whl (105 kB)
Downloading tqdm-4.67.1-py3-none-any.whl (78 kB)
Downloading certifi-2025.8.3-py3-none-any.whl (161 kB)
Downloading numpy-2.3.2-cp313-cp313-win_amd64.whl (12.8 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 12.8/12.8 MB 3.6 MB/s  0:00:03
Downloading python_dateutil-2.9.0.post0-py2.py3-none-any.whl (229 kB)
Downloading pytz-2025.2-py2.py3-none-any.whl (509 kB)
Downloading six-1.17.0-py2.py3-none-any.whl (11 kB)
Downloading soupsieve-2.8-py3-none-any.whl (36 kB)
Downloading typing_extensions-4.15.0-py3-none-any.whl (44 kB)
Downloading tzdata-2025.2-py2.py3-none-any.whl (347 kB)
Downloading colorama-0.4.6-py2.py3-none-any.whl (25 kB)
Downloading et_xmlfile-2.0.0-py3-none-any.whl (18 kB)
Installing collected packages: pytz, urllib3, tzdata, typing-extensions, soupsieve, six, numpy, idna, et-xmlfile, colorama, charset_normalizer, certifi, tqdm, requests, python-dateutil, openpyxl, beautifulsoup4, pandas
Successfully installed beautifulsoup4-4.13.5 certifi-2025.8.3 charset_normalizer-3.4.3 colorama-0.4.6 et-xmlfile-2.0.0 idna-3.10 numpy-2.3.2 openpyxl-3.1.5 pandas-2.3.2 python-dateutil-2.9.0.post0 pytz-2025.2 requests-2.32.5 six-1.17.0 soupsieve-2.8 tqdm-4.67.1 typing-extensions-4.15.0 tzdata-2025.2 urllib3-2.5.0
PS C:\Users\SESA754157\sbom-ai-agent>

## 🧩 Stage 2: File Parsing & Deduplication

We’ll write code in `sbom_agent.py` to:

1. Load the SBOM Excel file.
2. Extract `Component Name` and `Version String`.
3. Remove duplicates (keeping unique component-version pairs).
4. Save the cleaned data to a new Excel file.

---

### ✅ **`sbom_agent.py`**

```python
import pandas as pd
import os

# Define input and output file paths
input_file = "data/BDBA_Scan.csv"
output_file = "data/Cleaned_BDBA_Scan.csv"

# Check if input file exists
if not os.path.exists(input_file):
    print(f"❌ Input file not found: {input_file}")
    print("Please make sure the file is placed inside the 'data' folder.")
else:
    try:
        # Read the CSV file
        df = pd.read_csv(input_file)

        # Extract 'Component Name' and 'Version String' columns
        df_clean = df[['Component Name', 'Version String']]

        # Drop duplicate component-version pairs
        df_unique = df_clean.drop_duplicates()

        # Save the cleaned data to a new CSV file
        df_unique.to_csv(output_file, index=False)

        print(f"✅ Cleaned SBOM saved to {output_file}")

    except Exception as e:
        print(f"❌ Error while processing the file: {e}")

```

### 🧪 How to Run

Make sure:
- Your Excel file is named `BDBA_Scan.xlsx`
- It’s placed inside the `data/` folder

Then run:

```powershell
python sbom_agent.py
```
PS C:\Users\SESA754157\sbom-ai-agent> python sbom_agent.py
✅ Cleaned SBOM saved to data/Cleaned_BDBA_Scan.csv
PS C:\Users\SESA754157\sbom-ai-agent>
---
## Stage 3: Metadata Enrichment — where we’ll start building the logic to search and fill in missing fields like AuthorName, Supplier, License, etc.
