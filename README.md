STEP 2: Understand and Extract DESIS89

Since the position of DESIS89 can vary and is identified by *89, you need to search each line for the field ending in 89 and extract its value.


---

STEP 3: Implement Filtering Logic in Python

Here's the full working code for filtering and saving to 4 separate files:

import os

# File paths
input_file = "kondorfx.txt"
date_base = "20240429"  # Replace with dynamic date if needed

# Output file names
output_files = {
    "GA": f"KONDORGA_4035_{date_base}.txt",
    "BN": f"KONDORBN_4035_{date_base}.txt",
    "GAA": f"KONDORGA_4035A_{date_base}.txt",
    "TP": f"KONDORTP_4035_{date_base}.txt",
}

# Create/open output files
outputs = {key: open(name, 'w') for key, name in output_files.items()}

# Filtering keywords
ga_keywords = ["PF-AF01", "PF-AN19", "PF-D02011", "PF-NOR18",
               "ALM-LO", "TREAS-DU", "TREAS-BU", "TREAS-LO",
               "BNPPFCTLAY", "GRS-PA", "SBIRD-PAR"]
bn_keywords = ["ALM-LO", "TREAS-DU", "TREAS-BU", "TREAS-LO",
               "BNPPFCTLAY", "GRS-PA", "SBIRD-PAR"]
gaa_keywords = ["GAP", "DF"]
tp_keywords = ["BNPPCB", "BNPPSCF", "POSCENT-PA", "TREASUR-PA"]

# Process the input file
with open(input_file, 'r') as f:
    for line in f:
        parts = line.strip().split(',')
        desis89 = ""

        # Find the *89 entry
        for field in parts:
            if '*89' in field:
                desis89 = field.split('*89')[-1]
                break

        # Apply conditions
        if any(key in desis89 for key in ga_keywords):
            outputs["GA"].write(line)
        elif any(key in desis89 for key in bn_keywords):
            outputs["BN"].write(line)
        elif any(key in desis89 for key in gaa_keywords):
            outputs["GAA"].write(line)
        elif any(key in desis89 for key in tp_keywords):
            outputs["TP"].write(line)

# Close output files
for f in outputs.values():
    f.close()

print("Filtering complete. Files created:")
for name in output_files.values():
    print("-", name)


---

STEP 4: Run the Script in PyCharm

Right-click kondor_filter.py → Run.

Check your project folder: You will see 4 new text files generated.



---

STEP 5: Later Add S3 Upload Code (Once Access Is Given)

Once you receive AWS credentials and bucket details, extend the code like this:

import boto3

s3 = boto3.client('s3')
bucket_name = "your-bucket-name"

for key, filename in output_files.items():
    s3.upload_file(Filename=filename, Bucket=bucket_name, Key=filename)
    print(f"Uploaded {filename} to S3.")
