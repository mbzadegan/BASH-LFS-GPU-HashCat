#!/bin/bash

# Here's a more advanced version of the script that adds additional complexity, including parallel processing of files and a feature to archive processed files after a certain time:

# Directory to monitor
MONITOR_DIR="$HOME/monitor"

# Log file
LOG_FILE="$HOME/monitor/log.txt"
# Processed files directory
PROCESSED_DIR="$HOME/monitor/processed"
# Archive directory
ARCHIVE_DIR="$HOME/monitor/archive"
# Archive retention period (in seconds)
RETENTION_PERIOD=86400 # 1 day

# Ensure necessary directories exist
mkdir -p "$MONITOR_DIR" "$PROCESSED_DIR" "$ARCHIVE_DIR"

echo "Monitoring directory: $MONITOR_DIR" | tee -a "$LOG_FILE"

# Function to process a file
process_file() {
    local file="$1"
    local base_name="$(basename "$file")"
    local processed_file="$PROCESSED_DIR/$base_name"

    # Skip if the file has already been processed
    if [[ -f "$processed_file" ]]; then
        return
    fi

    echo "Processing file: $file" | tee -a "$LOG_FILE"

    # Custom processing: Example - counting lines in the file
    local line_count
    line_count=$(wc -l < "$file")
    echo "File: $base_name | Line count: $line_count" | tee -a "$LOG_FILE"

    # Move file to the processed directory
    mv "$file" "$PROCESSED_DIR/"
    echo "File moved to: $processed_file" | tee -a "$LOG_FILE"
}

# Function to archive old files
archive_old_files() {
    find "$PROCESSED_DIR" -type f -mtime +$((RETENTION_PERIOD / 86400)) -exec mv {} "$ARCHIVE_DIR/" \;
    echo "Archived files older than $((RETENTION_PERIOD / 86400)) days." | tee -a "$LOG_FILE"
}

# Infinite loop to monitor the directory
while true; do
    # Find new files and process them in parallel
    find "$MONITOR_DIR" -type f | while read -r file; do
        process_file "$file" &
    done

    # Wait for all background processes to finish
    wait

    # Archive old processed files
    archive_old_files

    # Pause to avoid excessive CPU usage
    sleep 5
done
