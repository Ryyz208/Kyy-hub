# Kyy-hub
import random
import time
from datetime import datetime

class Plant:
    def __init__(self, name):
        self.name = name
        self.stage = "Seed"
        self.water = 100
        self.weed = False
        self.growth_days = 0

class Garden:
    def __init__(self):
        self.plants = []
        self.day = 1
        self.money = 50
        self.seeds = {"Carrot": 10, "Tomato": 5, "Potato": 8}
        self.seed_prices = {"Carrot": 5, "Tomato": 10, "Potato": 8}
        self.plant_values = {"Carrot": 20, "Tomato": 30, "Potato": 25}

    def plant_seed(self, seed_type):
        if self.seeds[seed_type] <= 0:
            return f"Tidak ada benih {seed_type} yang tersedia."
        
        self.seeds[seed_type] -= 1
        self.plants.append(Plant(seed_type))
        return f"Berhasil menanam {seed_type}!"

    def water_plants(self):
        for plant in self.plants:
            plant.water = 100
        return "Semua tanaman disiram!"

    def check_garden(self):
        if not self.plants:
            return "Taman kosong. Tanam sesuatu!"
        
        status = []
        for i, plant in enumerate(self.plants):
            status_line = f"{i+1}. {plant.name} - {plant.stage}, Air: {plant.water}%"
            if plant.weed:
                status_line += " (Ada gulma!)"
            status.append(status_line)
        return "\n".join(status)

    def remove_weeds(self):
        weeds_removed = 0
        for plant in self.plants:
            if plant.weed:
                plant.weed = False
                weeds_removed += 1
        return f"Menghilangkan {weeds_removed} gulma!"

    def next_day(self):
        self.day += 1
        results = []
        
        for plant in self.plants:
            plant.water -= 20
            plant.growth_days += 1
            plant.weed = plant.weed or (random.random() < 0.3)
            
            if plant.water <= 0:
                plant.stage = "Dead"
            elif plant.stage == "Seed":
                if plant.water > 30 and random.random() > 0.5:
                    plant.stage = "Sprout"
            elif plant.stage == "Sprout":
                if plant.water > 20 and random.random() > 0.4:
                    plant.stage = "Growing"
            elif plant.stage == "Growing":
                if plant.water > 10 and random.random() > 0.3:
                    plant.stage = "Mature"
        
        results.append(f"Hari {self.day}:\n")
        
        harvested = []
        for i, plant in enumerate(self.plants):
            if plant.stage == "Mature":
                harvested.append(i)
                self.money += self.plant_values[plant.name]
        
        for i in reversed(harvested):
            self.plants.pop(i)
        
        if harvested:
            results.append(f"Panen: Mendapatkan ${sum([self.plant_values[plant.name] for plant in harvested])}")
        
        return "\n".join(results)

    def buy_seeds(self, seed_type, quantity):
        if seed_type not in self.seeds:
            return f"Benih {seed_type} tidak tersedia."
        
        price_per_seed = self.seed_prices[seed_type]
        total_price = price_per_seed * quantity
        
        if self.money < total_price:
            return f"Uang tidak cukup! Butuh ${total_price} lagi."
        
        self.money -= total_price
        self.seeds[seed_type] += quantity
        return f"Berhasil membeli {quantity} benih {seed_type}!"

def main():
    print("Selamat datang di Grow a Garden!")
    garden = Garden()
    
    while True:
        print("\n" + "="*50)
        print(f"Hari: {garden.day} | Uang: ${garden.money}")
        print("\nBenih tersedia:")
        for seed in garden.seeds:
            print(f"  - {seed}: ${garden.seed_prices[seed]} (Stok: {garden.seeds[seed]})")
        print("\nMenu:")
        print("  1. Tanam Benih")
        print("  2. Siram Tanaman")
        print("  3. Cek Taman")
        print("  4. Hilangkan Gulma")
        print("  5. Lanjut ke Hari Berikutnya")
        print("  6. Beli Benih")
        print("  7. Keluar")
        
        choice = input("Pilih (1-7): ").strip()
        
        if choice == "1":
            seed_type = input("Jenis benih (Carrot/Tomato/Potato): ").strip().title()
            if seed_type in garden.seeds and garden.seeds[seed_type] > 0:
                print(garden.plant_seed(seed_type))
            else:
                print(f"Benih {seed_type} tidak tersedia atau stok habis.")
            
        elif choice == "2":
            print(garden.water_plants())
            
        elif choice == "3":
            print(garden.check_garden())
            
        elif choice == "4":
            print(garden.remove_weeds())
            
        elif choice == "5":
            print(garden.next_day())
            
        elif choice == "6":
            seed_type = input("Jenis benih (Carrot/Tomato/Potato): ").strip().title()
            if seed_type in garden.seeds:
                try:
                    quantity = int(input("Jumlah: "))
                    print(garden.buy_seeds(seed_type, quantity))
                except ValueError:
                    print("Masukkan jumlah yang valid!")
            else:
                print(f"Benih {seed_type} tidak tersedia.")
                
        elif choice == "7":
            print("Terima kasih telah bermain!")
            break
            
        else:
            print("Pilihan tidak valid!")

if __name__ == "__main__":
    main()

