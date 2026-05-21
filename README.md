# growfinix-internship-task-3-
File Organizer
import os
import shutil

# ================== FILE TYPE CATEGORIES ==================
FILE_TYPES = {
    "Images": [".jpg", ".jpeg", ".png", ".gif"],
    "Documents": [".pdf", ".docx", ".txt", ".xlsx"],
    "Videos": [".mp4", ".mkv", ".avi"],
    "Music": [".mp3", ".wav"],
    "Archives": [".zip", ".rar"]
}

# ================== FUNCTION ==================
def get_category(extension):
    """Return folder name based on file extension"""
    for category, extensions in FILE_TYPES.items():
        if extension.lower() in extensions:
            return category
    return "Others"  # Unknown file types


def move_file(file_path, destination_folder):
    """Move file safely without overwriting"""
    filename = os.path.basename(file_path)
    name, ext = os.path.splitext(filename)

    new_path = os.path.join(destination_folder, filename)

    # Handle duplicate files
    counter = 1
    while os.path.exists(new_path):
        new_filename = f"{name}_{counter}{ext}"
        new_path = os.path.join(destination_folder, new_filename)
        counter += 1

    shutil.move(file_path, new_path)
    return new_path


def organize_folder(folder_path):
    """Main function to organize files"""
    summary = {}

    # Check if folder exists
    if not os.path.exists(folder_path):
        print("❌ Folder does not exist!")
        return

    for file in os.listdir(folder_path):
        file_path = os.path.join(folder_path, file)

        # Skip directories
        if os.path.isdir(file_path):
            continue

        # Get file extension
        _, extension = os.path.splitext(file)

        # Handle no extension
        if extension == "":
            category = "No_Extension"
        else:
            category = get_category(extension)

        # Create category folder if not exists
        category_folder = os.path.join(folder_path, category)
        os.makedirs(category_folder, exist_ok=True)

        # Move file
        new_location = move_file(file_path, category_folder)

        # Update summary
        summary[category] = summary.get(category, 0) + 1

        print(f"Moved: {file} → {category}/")

    # ================== SUMMARY ==================
    print("\n📊 Summary:")
    for category, count in summary.items():
        print(f"{category}: {count} files")


# ================== USER INPUT ==================
if __name__ == "__main__":
    folder = input("Enter folder path to organize: ")
    organize_folder(folder)