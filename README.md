import tkinter as tk
from tkinter import messagebox, filedialog
import os

class ImmobilienbewertungApp(tk.Tk):
    def __init__(self):
        super().__init__()
        self.title("Immobilienbewertung App")
        self.geometry("600x700")
        self.current_frame = None
        self.immobilientyp = None
        self.vermietet = None
        self.basis_infos = {}
        self.zimmer_details = {}
        self.technik_infos = {}
        
        # Startseite anzeigen
        self.show_startseite()

    def show_startseite(self):
        if self.current_frame:
            self.current_frame.destroy()

        self.current_frame = Startseite(self)
        self.current_frame.pack(fill="both", expand=True)

    def show_standortseite(self, immobilientyp, vermietet):
        if self.current_frame:
            self.current_frame.destroy()

        self.current_frame = Standortseite(self, immobilientyp, vermietet)
        self.current_frame.pack(fill="both", expand=True)

    def show_zimmerseite(self, basis_infos):
        if self.current_frame:
            self.current_frame.destroy()

        self.current_frame = Zimmerseite(self, basis_infos)
        self.current_frame.pack(fill="both", expand=True)

    def show_technikseite(self, zimmer_details):
        if self.current_frame:
            self.current_frame.destroy()

        self.current_frame = Technikseite(self, zimmer_details)
        self.current_frame.pack(fill="both", expand=True)

    def show_kosten_seite(self, technik_infos):
        if self.current_frame:
            self.current_frame.destroy()

        self.current_frame = KostenSeite(self, technik_infos)
        self.current_frame.pack(fill="both", expand=True)

# Startseite: Auswahl des Immobilientyps und Vermietungsstatus
class Startseite(tk.Frame):
    def __init__(self, master):
        super().__init__(master)
        self.master = master

        immobilientypen = [
            "Eigentumswohnung", "Einfamilienhaus", "Mehrfamilienhaus", 
            "Grundstück", "Gewerbeeinheit", "Gewerbegrundstück", "Industrieanlage"
        ]

        self.label = tk.Label(self, text="Wählen Sie den Immobilientyp:", font=("Arial", 16))
        self.label.pack(pady=20)

        self.selected_type = tk.StringVar(value="Eigentumswohnung")
        for immobilientyp in immobilientypen:
            radio_btn = tk.Radiobutton(self, text=immobilientyp, variable=self.selected_type, value=immobilientyp)
            radio_btn.pack(anchor="w")

        self.vermietet_label = tk.Label(self, text="Steht die Immobilie leer oder ist sie vermietet?")
        self.vermietet_label.pack(pady=10)
        self.vermietet_var = tk.StringVar(value="Leerstehend")
        tk.Radiobutton(self, text="Leerstehend", variable=self.vermietet_var, value="Leerstehend").pack(anchor="w")
        tk.Radiobutton(self, text="Vermietet", variable=self.vermietet_var, value="Vermietet").pack(anchor="w")

        self.next_button = tk.Button(self, text="Weiter", command=self.go_to_standort)
        self.next_button.pack(pady=20)

    def go_to_standort(self):
        immobilientyp = self.selected_type.get()
        vermietet = self.vermietet_var.get()
        self.master.show_standortseite(immobilientyp, vermietet)

# Standort & Basisinformationen eingeben
class Standortseite(tk.Frame):
    def __init__(self, master, immobilientyp, vermietet):
        super().__init__(master)
        self.master = master
        self.immobilientyp = immobilientyp
        self.vermietet = vermietet

        self.label = tk.Label(self, text=f"Standort und Details der Immobilie ({self.immobilientyp}):", font=("Arial", 16))
        self.label.pack(pady=20)

        self.standort_entry = tk.Entry(self, width=50)
        self.standort_entry.pack(pady=10)
        self.standort_label = tk.Label(self, text="Geben Sie den Standort ein:")
        self.standort_label.pack()

        self.upload_button = tk.Button(self, text="Außenansicht hochladen", command=self.upload_image)
        self.upload_button.pack(pady=10)

        self.baujahr_entry = tk.Entry(self, width=50)
        self.baujahr_entry.insert(0, "Baujahr")
        self.baujahr_entry.pack(pady=10)

        self.qm_entry = tk.Entry(self, width=50)
        self.qm_entry.insert(0, "Quadratmeterzahl")
        self.qm_entry.pack(pady=10)

        self.kaufpreis_entry = tk.Entry(self, width=50)
        self.kaufpreis_entry.insert(0, "Kaufpreis")
        self.kaufpreis_entry.pack(pady=10)

        self.next_button = tk.Button(self, text="Weiter", command=self.go_to_zimmer)
        self.next_button.pack(pady=20)

        self.back_button = tk.Button(self, text="Zurück", command=self.master.show_startseite)
        self.back_button.pack(pady=10)

    def upload_image(self):
        filetypes = (('Image files', '*.jpg *.png'), ('All files', '*.*'))
        filename = filedialog.askopenfilename(title='Bild auswählen', filetypes=filetypes)
        if filename:
            messagebox.showinfo("Bild hochgeladen", f"Bild '{os.path.basename(filename)}' wurde hochgeladen.")

    def go_to_zimmer(self):
        # Sammle die Daten
        basis_infos = {
            "immobilientyp": self.immobilientyp,
            "vermietet": self.vermietet,
            "standort": self.standort_entry.get(),
            "baujahr": self.baujahr_entry.get(),
            "qm": self.qm_entry.get(),
            "kaufpreis": self.kaufpreis_entry.get()
        }
        self.master.show_zimmerseite(basis_infos)

# Zimmerdetails eingeben
class Zimmerseite(tk.Frame):
    def __init__(self, master, basis_infos):
        super().__init__(master)
        self.master = master
        self.basis_infos = basis_infos

        self.label = tk.Label(self, text="Zimmerdetails:", font=("Arial", 16))
        self.label.pack(pady=20)

        self.zimmer_entry = tk.Entry(self, width=50)
        self.zimmer_entry.insert(0, "Anzahl der Zimmer")
        self.zimmer_entry.pack(pady=10)

        self.zimmer_arten = ["Küche", "Bad", "separates WC", "Abstellkammer", "Schlafzimmer", "Wohnzimmer"]
        self.selected_zimmerart = tk.StringVar(value="Schlafzimmer")

        self.zimmerart_label = tk.Label(self, text="Art des Zimmers:")
        self.zimmerart_label.pack(pady=10)
        for zimmerart in self.zimmer_arten:
            radio_btn = tk.Radiobutton(self, text=zimmerart, variable=self.selected_zimmerart, value=zimmerart)
            radio_btn.pack(anchor="w")

        self.keller_var = tk.BooleanVar()
        self.keller_check = tk.Checkbutton(self, text="Keller", variable=self.keller_var)
        self.keller_check.pack(pady=5)

        self.balkon_var = tk.BooleanVar()
        self.balkon_check = tk.Checkbutton(self, text="Balkon", variable=self.balkon_var)
        self.balkon_check.pack(pady=5)

        self.garage_var = tk.BooleanVar()
        self.garage_check = tk.Checkbutton(self, text="Garage", variable=self.garage_var)
        self.garage_check.pack(pady=5)

        self.next_button = tk.Button(self, text="Weiter", command=self.go_to_technik)
        self.next_button.pack(pady=20)

        self.back_button = tk.Button(self, text="Zurück", command=self.master.show_standortseite)
        self.back_button.pack(pady=10)

    def go_to_technik(self):
        zimmer_details = {
            "zimmeranzahl": self.zimmer_entry.get(),
            "zimmerart": self.selected_zimmerart.get(),
            "keller": self.keller_var.get(),
            "balkon": self.balkon_var.get(),
            "garage": self.garage_var.get()
        }
        self.master.show_technikseite(zimmer_details)

# Technikseite: Elektrik, Sanitär, Heizung
class Technikseite(tk.Frame):
    def __init__(self, master, zimmer_details):
        super().__init__(master)
        self.master = master
        self.zimmer_details = zimmer_details

        self.label = tk.Label(self, text="Technische Ausstattung & Sanierung", font=("Arial", 16))
        self.label.pack(pady=20)

        self.elektrik_var = tk.BooleanVar()
        self.elektrik_check = tk.Checkbutton(self, text="Elektrik erneuern", variable=self.elektrik_var)
        self.elektrik_check.pack(pady=5)

        self.sanitaer_var = tk.BooleanVar()
        self.sanitaer_check = tk.Checkbutton(self, text="Sanitäre Anlagen erneuern", variable=self.sanitaer_var)
        self.sanitaer_check.pack(pady=5)

        self.heizung_var = tk.BooleanVar()
        self.heizung_check = tk.Checkbutton(self, text="Heizung erneuern", variable=self.heizung_var)
        self.heizung_check.pack(pady=5)

        self.next_button = tk.Button(self, text="Weiter", command=self.go_to_kosten)
        self.next_button.pack(pady=20)

        self.back_button = tk.Button(self, text="Zurück", command=self.master.show_zimmerseite)
        self.back_button.pack(pady=10)

    def go_to_kosten(self):
        technik_infos = {
            "elektrik": self.elektrik_var.get(),
            "sanitaer": self.sanitaer_var.get(),
            "heizung": self.heizung_var.get()
        }
        self.master.show_kosten_seite(technik_infos)

# Nebenkosten & Cashflow
class KostenSeite(tk.Frame):
    def __init__(self, master, technik_infos):
        super().__init__(master)
        self.master = master
        self.technik_infos = technik_infos

        self.label = tk.Label(self, text="Nebenkosten & Cashflow", font=("Arial", 16))
        self.label.pack(pady=20)

        self.nebenkosten_entry = tk.Entry(self, width=50)
        self.nebenkosten_entry.insert(0, "Nebenkosten")
        self.nebenkosten_entry.pack(pady=10)

        self.submit_button = tk.Button(self, text="Berechnung abschließen", command=self.calculate_cashflow)
        self.submit_button.pack(pady=20)

        self.back_button = tk.Button(self, text="Zurück", command=self.master.show_technikseite)
        self.back_button.pack(pady=10)

    def calculate_cashflow(self):
        nebenkosten = self.nebenkosten_entry.get()
        # Logik zur Berechnung des Cashflows basierend auf den Eingaben
        
        messagebox.showinfo("Berechnung", f"Berechnung abgeschlossen! Nebenkosten: {nebenkosten}")


# Main App
if __name__ == "__main__":
    app = ImmobilienbewertungApp()
    app.mainloop()
