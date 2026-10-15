import csv

INPUT_FILE = "environmental_raw.txt"
OUTPUT_FILE = "environmental_clean.csv"


def celsius_converter(f_temp):
    """Convert a Fahrenheit string/float to Celsius, rounded to 2 decimals."""
    return round((float(f_temp) - 32) * 5 / 9, 2)


def parse_and_clean(line)
    Returns a list [timestamp, station_id, temp_f, humidity, status]
    for a valid line, or None if the line is corrupted or not ACTIVE.
    """
    line = line.strip()
    if not line:                      
        return None
    parts = [p.strip() for p in line.split("|")]
    if len(parts) != 5:             
        return None
    timestamp, station_id, temp, humidity, status = parts
    if not all(parts):
        return None
    if status != "ACTIVE":
        return None
    try:
        float(temp)
        float(humidity)
    except ValueError:
        return None
    return [timestamp, station_id, temp, humidity, status]


def main():
    skipped = 0
    written = 0
    with open(INPUT_FILE, "r") as infile, \
         open(OUTPUT_FILE, "w", newline="") as outfile:
        writer = csv.writer(outfile)
        writer.writerow(["Timestamp", "StationID", "Temp(C)", "Humidity(%)", "Sensor_Status"])
        first_line = True
        for line in infile:           
            # Skip the header row (not counted as corrupted)
            if first_line:
                first_line = False
                if line.strip().startswith("Timestamp"):
                    continue
            result = parse_and_clean(line)
            if result is None:
                skipped += 1
                continue
            timestamp, station_id, temp_f, humidity, status = result
            temp_c = celsius_converter(temp_f)
            writer.writerow([timestamp, station_id, temp_c, humidity, status])
            written += 1
    print(f"Rows written: {written}")
    print(f"Corrupted/skipped lines: {skipped}")


if __name__ == "__main__":
    main()
