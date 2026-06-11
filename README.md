# project-2026

# Analiza Wpływu Stylu Życia i Pracy na Produktywność oraz Wypalenie Zawodowe

## 1. Cel Analizy
Głównym celem projektu było zbadanie zależności pomiędzy czynnikami opisującymi styl pracy (godziny pracy, praca w skupieniu), nawykami dobowymi (sen, sport, screen time) a ich bezpośrednim przełożeniem na produktywność pracowników oraz ryzyko wypalenia zawodowego. Analiza opiera się na zaawansowanych algorytmach uczenia maszynowego (regresja i klasyfikacja).

---

## 2. Opis Danych
Zbiór danych zawiera szczegółowe wskaźniki dobowe dla pracowników. Kluczowe zmienne wykorzystane w badaniu to:
* `burnout_risk` – poziom ryzyka wypalenia zawodowego (skala 0 - 100).
* `productivity_score` – ogólny wskaźnik produktywności (skala 0 - 100).
* `deep_work_hours` – liczba godzin przepracowanych w głębokim skupieniu.
* `sleep_hours` – dobowy czas snu w godzinach.
* `physical_activity` – czas aktywności fizycznej.
* `daily_screen_time`, `doomscrolling_duration` – wskaźniki obciążenia cyfrowego.

---

## 3. Struktura Projektu i Wyniki Hipotez

### Wstępna Eksploracja Danych (Macierz Korelacji)
Przed modelowaniem przefiltrowano macierz korelacji (współczynniki $|\pm 0.1|$). 
* **Główni winowajcy wypalenia:** Wyczerpanie emocjonalne ($0.51$) oraz stres ($0.38$).
* **Czynniki ochronne:** Satysfakcja z pracy ($-0.25$) oraz produktywność ($-0.38$).
* **Motor produktywności:** Godziny głębokiej pracy ($0.56$).

---

### Hipoteza 1: Liniowy wpływ czynników na produktywność
* **Model:** Regresja Liniowa (Linear Regression).
* **Wynik:** Potwierdzono stały wpływ kluczowych zmiennych na produktywność.

---

### Hipoteza 2: Nieliniowy wpływ godzin głębokiej pracy (Deep Work)
* **Teza:** Istnienie "złotego środka", po przekroczeniu którego produktywność spada, a wypalenie drastycznie rośnie.
* **Modele i Metryki:** Random Forest Regressor ($RMSE = 17.85$) vs Regresja Liniowa.
* **Zwycięzca:** **Random Forest**, ponieważ lepiej wychwycił nieliniowe spłaszczenie trendu powyżej 8 godzin pracy.
* **Werdykt:** Hipoteza **odrzucona w części o wypaleniu**. Więcej godzin *Deep Work* stale podnosi produktywność do maksimum, a wypalenie przy tym lekko spada (skupienie chroni przed frustracją).

---

### Hipoteza 3: Sen i sport jako "tarcza ochronna"
* **Teza:** Aktywność fizyczna i zdrowy sen drastycznie obniżają wypalenie.
* **Modele:** Istotność cech (Feature Importance) w Random Forest.
* **Werdykt:** Hipoteza **w pełni potwierdzona**. Łączna istotność snu i sportu w modelu wyniosła aż **~85%** (sam sen to ok. 50% decyzyjności modelu), całkowicie dominując nad czasem pracy (15%). Regeneracja to klucz do braku wypalenia.

---

### Hipoteza 4: Krzywoliniowy wpływ snu (Regresja Wielomianowa i Klasyfikacja)
* **Teza:** Zależność ma kształt paraboli (U-shape) – nadmiar snu też szkodzi. Dodatkowo sprawdzono możliwość klasyfikacji grupy ryzyka.
* **Modele i Metryki:** Regresja Wielomianowa (Polynomial Degree 2) vs Regresja Liniowa oraz Drzewo Decyzyjne z optymalizacją **Grid Search**.
* **Wyniki Regresji:** Krzywa wielomianowa automatycznie zredukowała się do linii prostej. **Zwycięzca: Regresja Liniowa** (zgodnie z zasadą brzytwy Ockhama – prostszy model przy identycznym dopasowaniu). Nadmiar snu nie szkodzi.
* **Wyniki Klasyfikacji (Grid Search):** Grid Search wskazał optymalną głębokość drzewa `max_depth = 3`. Model uzyskał jednak **F1-score = 0.00** dla klasy "Wypalony", przypisując wszystkich do klasy większościowej "Bezpieczny".
* **Werdykt:** Sam czas snu to za mało na skuteczną klasyfikację binarnej grupy ryzyka. Zjawisko wypalenia wymaga analizy wielowymiarowej.

---

### Hipoteza 5: Krzywoliniowy wpływ snu (Regresja Wielomianowa i Grid Search)
* Teza: Zależność między snem a wypaleniem ma kształt paraboli (U-shape) — nadmiar snu też szkodzi.
* Modele: Regresja Wielomianowa (Polynomial Degree 2) vs Regresja Liniowa + Drzewo Decyzyjne z optymalizacją Grid Search.
* Wyniki Regresji: Krzywa wielomianowa zredukowała się automatycznie do linii prostej. Zwycięzca: Regresja Liniowa (prostszy model przy identycznym dopasowaniu).
* Wyniki Klasyfikacji: Grid Search wskazał optymalną głębokość drzewa max_depth = 3, jednak F1-score = 0.00 dla klasy "Wypalony".
* Werdykt: Hipoteza odrzucona. Sam czas snu to za mało na skuteczną klasyfikację grupy ryzyka.

---

### Hipoteza 6: Random Forest z optymalnymi hiperparametrami jako najlepszy model predykcyjny
* Teza: Używając wszystkich dostępnych zmiennych (po standaryzacji i One Hot Encoding), Random Forest z automatycznie dobranymi hiperparametrami najlepiej przewiduje poziom wypalenia.
* Modele i Metryki: Random Forest Regressor + Random Search (RMSE = 14.05).
* Najlepsze hiperparametry: n_estimators = 100, max_depth = None, min_samples_split = 5.
* Feature Importance: Największy wpływ mają czynniki psychologiczne — wyczerpanie emocjonalne (25.3%) i poziom stresu (15.6%). Aktywność fizyczna i sen mają zaskakująco mały wpływ (poniżej 3%).
* Werdykt: Hipoteza potwierdzona. Użycie wszystkich zmiennych z standaryzacją i One Hot Encoding znacząco poprawiło jakość modelu (RMSE spadło z 21.52 do 14.05).

---

## 4. Podsumowanie Techniczne
W projekcie z powodzeniem zaimplementowano i porównano wymagane algorytmy:

1. Regresja Liniowa (Linear Regression)
2. Regresja Wielomianowa (Polynomial Regression)
3. Regresja Logistyczna (Logistic Regression)
4. Drzewo Decyzyjne (Decision Tree z GridSearchCV)
5. Lasy Losowe (Random Forest — Regresor i Klasyfikator z RandomizedSearchCV)

Dodatkowo zastosowano: standaryzację danych (StandardScaler), kodowanie zmiennych tekstowych (One Hot Encoding) oraz automatyczny dobór hiperparametrów (Grid Search, Random Search).

Projekt udowodnił, że wypalenie zawodowe jest zjawiskiem przede wszystkim psychologicznym — dominującą rolę odgrywają wyczerpanie emocjonalne i poziom stresu, a nie sam fizyczny styl życia. Najlepszy model (Random Forest z optymalnymi hiperparametrami) osiągnął RMSE = 14.05, co stanowi poprawę o ponad 34% względem bazowej Regresji Liniowej (RMSE = 21.52).
