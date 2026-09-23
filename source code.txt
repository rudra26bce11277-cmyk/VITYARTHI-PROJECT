import json
import os
import sys

# The text file that stores our data
FILE_NAME = "movies_inventory.json"

def load_data():
    """Loads records from the JSON file."""
    if not os.path.exists(FILE_NAME):
        return []
    with open(FILE_NAME, 'r') as file:
        return json.load(file)

def save_data(data):
    """Saves records into the JSON file."""
    with open(FILE_NAME, 'w') as file:
        json.dump(data, file, indent=4)

# 1. Insert Record
def insert():
    movies = load_data()
    while True:
        # Auto-increment the unique Movie ID
        movie_id = max([m["Movie_ID"] for m in movies]) + 1 if movies else 1
        
        print(f"\nAdding Movie ID: {movie_id}")
        name = input("Enter Movie Name: ")
        genre = input("Enter Genre: ")
        director = input("Enter Director: ")
        date = input("Enter Release Date (YYYY-MM-DD): ")
        seats = int(input("Enter Available Seats: "))
        price = float(input("Enter Ticket Price: "))
        
        # Save inputs into a dictionary structure
        new_movie = {
            "Movie_ID": movie_id,
            "Movie_Name": name,
            "Genre": genre,
            "Director": director,
            "Release_Date": date,
            "Available_Seats": seats,
            "Ticket_Price": price
        }
        
        movies.append(new_movie)
        cont = input("Do you want to add another movie? (Y/N): ").lower()
        if cont == 'n':
            break
            
    save_data(movies)
    print("✅ Records successfully saved to file!")

# 2. Display All Records
def display():
    movies = load_data()
    print("\n--- Current Movie Inventory ---")
    if not movies:
        print("Inventory is currently empty.")
        return
    for m in movies:
        print(f"ID: {m['Movie_ID']} | Name: {m['Movie_Name']} | Genre: {m['Genre']} | Seats: {m['Available_Seats']} | Price: ${m['Ticket_Price']:.2f}")

# 3. Search Record by ID
def search():
    movie_id = int(input("Enter Movie ID to search: "))
    movies = load_data()
    
    for m in movies:
        if m["Movie_ID"] == movie_id:
            print(f"\n✨ Record Found: {m}")
            return
    print("❌ Movie ID not found.")

# 4. Update Record
def update():
    movie_id = int(input("Enter Movie ID to update: "))
    movies = load_data()
    
    for m in movies:
        if m["Movie_ID"] == movie_id:
            m["Available_Seats"] = int(input("Enter new Available Seats: "))
            m["Ticket_Price"] = float(input("Enter new Ticket Price: "))
            save_data(movies)
            print("✨ Record details updated successfully!")
            return
    print("❌ Movie ID not found.")

# 5. Delete Record
def delete():
    movie_id = int(input("Enter Movie ID to delete: "))
    movies = load_data()
    
    # Keeps everything except the deleted target item
    updated_movies = [m for m in movies if m["Movie_ID"] != movie_id]
    
    if len(updated_movies) < len(movies):
        save_data(updated_movies)
        print("💥 Record deleted successfully!")
    else:
        print("❌ Movie ID not found.")

# 6. Sort Movies by Available Seats
def orderby():
    movies = load_data()
    if not movies:
        print("No items available to sort.")
        return
        
    # Sorts the dictionary objects using Python's native sorting logic
    sorted_movies = sorted(movies, key=lambda x: x["Available_Seats"])
    print("\n--- Movies Sorted by Availability (Low to High) ---")
    for m in sorted_movies:
        print(f"Seats Available: {m['Available_Seats']} | Name: {m['Movie_Name']}")

# 7. Filter by Genre
def conditionsearch():
    genre_target = input("Enter target genre to filter by: ").strip().lower()
    movies = load_data()
    
    print(f"\n--- Results matching category: '{genre_target}' ---")
    found = False
    for m in movies:
        if m["Genre"].lower() == genre_target:
            print(f"• {m['Movie_Name']} (Directed by {m['Director']})")
            found = True
    if not found:
        print("No movies matched your filter criteria.")

# 8. Count Total Entries
def countentries():
    movies = load_data()
    print(f"\n📊 Total active movie entries tracked: {len(movies)}")

# Core Loop Main Driver System
def main():
    # Make sure file is ready on launch
    if not os.path.exists(FILE_NAME):
        save_data([])

    while True:
        print("\n=====================================")
        print("🎬  MOVIE TICKET INVENTORY SYSTEM  🎬")
        print("=====================================")
        print("1. Insert New Movie Record")
        print("2. Display All Movie Records")
        print("3. Search Movie by ID")
        print("4. Update Movie (Seats & Price)")
        print("5. Delete Movie Record")
        print("6. Sort Movies by Seat Availability")
        print("7. Filter Movies by Genre")
        print("8. Check Total Record Count")
        print("9. Exit Application")
        print("=====================================")
        
        try:
            choice = int(input("\nEnter operational choice [1-9]: "))
        except ValueError:
            print("❌ Invalid input. Please enter numbers only.")
            continue
        
        if choice == 1: insert()
        elif choice == 2: display()
        elif choice == 3: search()
        elif choice == 4: update()
        elif choice == 5: delete()
        elif choice == 6: orderby()
        elif choice == 7: conditionsearch()
        elif choice == 8: countentries()
        elif choice == 9:
            print("\n👋 Inventory session successfully closed. Goodbye!")
            sys.exit(0)
        else:
            print("⚠️ Selection choice out of index range. Try again.")

if __name__ == "__main__":
    main()
