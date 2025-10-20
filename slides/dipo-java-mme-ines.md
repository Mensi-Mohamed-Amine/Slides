---
title: My Slides
revealOptions:
  transition: "slide"
  controls: true
  progress: true
  history: false
  slideNumber: false
separator: "^---$"
verticalSeparator: "^--$"
---

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.8.0/styles/monokai-sublime.min.css">

<link rel="icon" href="./favicon.ico" type="image/x-icon">

# dipo java

this is my work for java mme ines.

---

### Medicament.java

--

<pre><code class="language-java" data-line-numbers="1|3|4-8|10-16|18-24|26-32|34-38|40-49|51-53|55-57|59-61|63-65|66|">package pharmacie;

public class Medicament {
    private long code;
    private String nom;
    private String genre;
    private double prix;
    private long numeroSerie;

    public Medicament() {
        this.code = System.currentTimeMillis();
        this.nom = "";
        this.genre = "";
        this.prix = 0.0;
        this.numeroSerie = 0L;
    }

    public Medicament(String nom, String genre) {
        this.code = System.currentTimeMillis();
        this.nom = nom;
        this.genre = genre;
        this.prix = 0.0;
        this.numeroSerie = 0L;
    }

    public Medicament(String nom, String genre, double prix, long numeroSerie) {
        this.code = System.currentTimeMillis();
        this.nom = nom;
        this.genre = genre;
        this.prix = prix;
        this.numeroSerie = numeroSerie;
    }

    public long getCode() { return code; }
    public String getNom() { return nom; }
    public String getGenre() { return genre; }
    public double getPrix() { return prix; }
    public long getNumeroSerie() { return numeroSerie; }

    @Override
    public String toString() {
        return "Medicament{" +
                "nom='" + nom + '\'' +
                ", genre='" + genre + '\'' +
                ", code=" + code +
                ", numeroSerie=" + numeroSerie +
                ", prix=" + prix +
                '}';
    }

    public int compare_nom(Medicament m) {
        return this.nom.compareTo(m.nom);
    }

    public static int compare_nom(Medicament m1, Medicament m2) {
        return m1.nom.compareTo(m2.nom);
    }

    public int compare_prix(Medicament m) {
        return Double.compare(this.prix, m.prix);
    }

    public static int compare_prix(Medicament m1, Medicament m2) {
        return Double.compare(m1.prix, m2.prix);
    }
}
</code></pre>

---

### ClientFidele.java

--

<pre><code class="language-java" data-line-numbers="1|3|4-6|8-12|14-16|18-25|26|">package pharmacie;

public class ClientFidele {
    private long cin;
    private String nom;
    private String prenom;

    public ClientFidele(long cin, String nom, String prenom) {
        this.cin = cin;
        this.nom = nom;
        this.prenom = prenom;
    }

    public long getCin() { return cin; }
    public String getNom() { return nom; }
    public String getPrenom() { return prenom; }

    @Override
    public String toString() {
        return "ClientFidele{" +
                "cin=" + cin +
                ", nom='" + nom + '\'' +
                ", prenom='" + prenom + '\'' +
                '}';
    }
}
</code></pre>

---

### Pharmacie.java

--

<pre><code class="language-java" data-line-numbers="1|3|5-9|11-16|18-21|23-41|43-45|47-51|53-79|81-84|86-88|90-93|">package pharmacie;

import java.util.*;

public class Pharmacie {
    private List<Medicament> listeMedicaments;
    private List<ClientFidele> listeClientsFideles;
    private Map<Long, Integer> mapMedicaments; // numeroSerie → count
    private Map<Long, Double> mapClientsFideles; // CIN → total spent

    public Pharmacie() {
        this.listeMedicaments = new ArrayList<>();
        this.listeClientsFideles = new ArrayList<>();
        this.mapMedicaments = new HashMap<>();
        this.mapClientsFideles = new HashMap<>();
    }

    public void ajouterMedicament(Medicament m) {
        listeMedicaments.add(m);
        mapMedicaments.put(m.getNumeroSerie(), mapMedicaments.getOrDefault(m.getNumeroSerie(), 0) + 1);
    }

    public void supprimerMedicament(String nomMedicament) {
        Medicament toRemove = null;
        for (Medicament m : listeMedicaments) {
            if (m.getNom().equals(nomMedicament)) {
                toRemove = m;
                break;
            }
        }
        if (toRemove != null) {
            listeMedicaments.remove(toRemove);
            long serie = toRemove.getNumeroSerie();
            int count = mapMedicaments.get(serie);
            if (count > 1) {
                mapMedicaments.put(serie, count - 1);
            } else {
                mapMedicaments.remove(serie);
            }
        }
    }

    public int nombreMedicaments() {
        return listeMedicaments.size();
    }

    public double achatMedicament(Medicament m) {
        // Simple purchase: just return price (no client tracking)
        supprimerMedicament(m.getNom());
        return m.getPrix();
    }

    public double achatMedicament(String nomMedicament, long cin) {
        // Find medicament by name (first match)
        Medicament m = null;
        for (Medicament med : listeMedicaments) {
            if (med.getNom().equals(nomMedicament)) {
                m = med;
                break;
            }
        }
        if (m == null) return 0.0;

        // Remove from stock
        supprimerMedicament(nomMedicament);

        double totalActuel = mapClientsFideles.getOrDefault(cin, 0.0);
        double prix = m.getPrix();
        double prixPaye = prix;

        if (totalActuel >= 100.0) {
            prixPaye = prix * 0.85; // 15% discount
            mapClientsFideles.put(cin, 0.0); // reset total
        } else {
            mapClientsFideles.put(cin, totalActuel + prix);
        }

        return prixPaye;
    }

    // Getters for testing
    public List<Medicament> getListeMedicament() {
        return new ArrayList<>(listeMedicaments);
    }

    public Map<Long, Integer> getMapMedicaments() {
        return new HashMap<>(mapMedicaments);
    }

    public Map<Long, Double> getMapClientsFideles() {
        return new HashMap<>(mapClientsFideles);
    }
}

</code></pre>
