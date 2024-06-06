_Marie Lauriane NIENGUI MOUBELE iage3A_
CREATE TABLE Module (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nom VARCHAR(255) UNIQUE NOT NULL
);

CREATE TABLE Professeur (
    id INT PRIMARY KEY,
    nom VARCHAR(255),
    prenom VARCHAR(255),
    tel VARCHAR(255)
);

CREATE TABLE Cours (
    id INT PRIMARY KEY AUTO_INCREMENT,
    date DATE NOT NULL,
    heureDebut TIME NOT NULL,
    heureFin TIME NOT NULL,
    professeur_id INT,
    module_id INT,
    FOREIGN KEY (professeur_id) REFERENCES Professeur(id),
    FOREIGN KEY (module_id) REFERENCES Module(id)
);

Module.java
public class Module {
    private int id;
    private String nom;

    public Module() {}

    public Module(String nom) {
        this.nom = nom;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getNom() {
        return nom;
    }

    public void setNom(String nom) {
        this.nom = nom;
    }
}


Professeur.java
public class Professeur {
    private int id;
    private String nom;
    private String prenom;
    private String tel;

    public Professeur() {}

    public Professeur(String nom, String prenom, String tel) {
        this.nom = nom;
        this.prenom = prenom;
        this.tel = tel;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getNom() {
        return nom;
    }

    public void setNom(String nom) {
        this.nom = nom;
    }

    public String getPrenom() {
        return prenom;
    }

    public void setPrenom(String prenom) {
        this.prenom = prenom;
    }

    public String getTel() {
        return tel;
    }

    public void setTel(String tel) {
        this.tel = tel;
    }
}


Cours.java
import java.time.LocalDate;
import java.time.LocalTime;

public class Cours {
    private int id;
    private LocalDate date;
    private LocalTime heureDebut;
    private LocalTime heureFin;
    private Professeur professeur;
    private Module module;

    public Cours() {}

    public Cours(LocalDate date, LocalTime heureDebut, LocalTime heureFin, Professeur professeur, Module module) {
        this.date = date;
        this.heureDebut = heureDebut;
        this.heureFin = heureFin;
        this.professeur = professeur;
        this.module = module;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public LocalDate getDate() {
        return date;
    }

    public void setDate(LocalDate date) {
        this.date = date;
    }

    public LocalTime getHeureDebut() {
        return heureDebut;
    }

    public void setHeureDebut(LocalTime heureDebut) {
        this.heureDebut = heureDebut;
    }

    public LocalTime getHeureFin() {
        return heureFin;
    }

    public void setHeureFin(LocalTime heureFin) {
        this.heureFin = heureFin;
    }

    public Professeur getProfesseur() {
        return professeur;
    }

    public void setProfesseur(Professeur professeur) {
        this.professeur = professeur;
    }

    public Module getModule() {
        return module;
    }

    public void setModule(Module module) {
        this.module = module;
    }
}


Database.java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class Database {
    private static final String URL = "jdbc:mysql://localhost:3306/base_de_donnees";
    private static final String USER = "utilisateur";
    private static final String PASSWORD = "votre_mot_de_passe";

    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(URL, USER, PASSWORD);
    }
}

ModuleDAO.java
import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class ModuleDAO {
    public void ajouterModule(Module module) {
        String query = "INSERT INTO Module (nom) VALUES (?)";
        try (Connection conn = Database.getConnection();
             PreparedStatement stmt = conn.prepareStatement(query)) {
            stmt.setString(1, module.getNom());
            stmt.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public List<Module> listerModules() {
        List<Module> modules = new ArrayList<>();
        String query = "SELECT * FROM Module";
        try (Connection conn = Database.getConnection();
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(query)) {
            while (rs.next()) {
                Module module = new Module();
                module.setId(rs.getInt("id"));
                module.setNom(rs.getString("nom"));
                modules.add(module);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return modules;
    }
}

ProfesseurDAO.java
import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class ProfesseurDAO {
    public List<Professeur> listerProfesseurs() {
        List<Professeur> professeurs = new ArrayList<>();
        String query = "SELECT * FROM Professeur";
        try (Connection conn = Database.getConnection();
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(query)) {
            while (rs.next()) {
                Professeur professeur = new Professeur();
                professeur.setId(rs.getInt("id"));
                professeur.setNom(rs.getString("nom"));
                professeur.setPrenom(rs.getString("prenom"));
                professeur.setTel(rs.getString("tel"));
                professeurs.add(professeur);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return professeurs;
    }
}

CoursDAO.java

import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class CoursDAO {
    public void creerCours(Cours cours) {
        String query = "INSERT INTO Cours (date, heureDebut, heureFin, professeur_id, module_id) VALUES (?, ?, ?, ?, ?)";
        try (Connection conn = Database.getConnection();
             PreparedStatement stmt = conn.prepareStatement(query)) {
            stmt.setDate(1, Date.valueOf(cours.getDate()));
            stmt.setTime(2, Time.valueOf(cours.getHeureDebut()));
            stmt.setTime(3, Time.valueOf(cours.getHeureFin()));
            stmt.setInt(4, cours.getProfesseur().getId());
            stmt.setInt(5, cours.getModule().getId());
            stmt.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public List<Cours> listerCours() {
        List<Cours> coursList = new ArrayList<>();
        String query = "SELECT * FROM Cours";
        try (Connection conn = Database.getConnection();
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(query)) {
            while (rs.next()) {
                Cours cours = new Cours();
                cours.setId(rs.getInt("id"));
                cours.setDate(rs.getDate("date").toLocalDate());
                cours.setHeureDebut(rs.getTime("heureDebut").toLocalTime());
                cours.setHeureFin(rs.getTime("heureFin").toLocalTime());

                Professeur professeur = new Professeur();
                professeur.setId(rs.getInt("professeur_id"));
                // Fetch professor details

                Module module = new Module();
                module.setId(rs.getInt("module_id"));
                // Fetch module details

                cours.setProfesseur(professeur);
                cours.setModule(module);

                coursList.add(cours);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return coursList;
    }

    public List<Cours> listerCoursParModuleEtProfesseur(int moduleId, int professeurId) {
        List<Cours> coursList = new ArrayList<>();
        String query = "SELECT * FROM Cours WHERE module_id = ? AND professeur_id = ?";
        try (Connection conn = Database.getConnection();
             PreparedStatement stmt = conn.prepareStatement(query)) {
            stmt.setInt(1, moduleId);
            stmt.setInt(2, professeurId);
            try (ResultSet rs = stmt.executeQuery()) {
                while (rs.next()) {
                    Cours cours = new Cours();
                    cours.setId(rs.getInt("id"));
                    cours.setDate(rs.getDate("date").toLocalDate());
                    cours.setHeureDebut(rs.getTime("heureDebut").toLocalTime());
                    cours.setHeureFin(rs.getTime("heureFin").toLocalTime());

                    Professeur professeur = new Professeur();
                    professeur.setId(rs.getInt("id")
}

Main.java

import java.time.LocalDate;
import java.time.LocalTime;
import java.util.List;
import java.util.Scanner;

public class Main {
    private static ModuleDAO moduleDAO = new ModuleDAO();
    private static CoursDAO coursDAO = new CoursDAO();

    public static void main
}


