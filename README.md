# Projekt zaliczeniowy z przedmiotu Uczenie maszynowe w finansach
Repozytorium zespolu projektowego przedmiotu uczenie maszynowe w finansach 

# Cel i założenia projektu

S&P500 cieszy się sporą popularnością wśród inwestorów ze względu na historycznie znany wysoki performance oraz obietnicę wysokich zysków. Pomimo faktu, że inwestowanie w akcje obarczone jest sporym ryzykiem, indeksy takie jak SP500 uznawane są za w miarę bezpieczną inwestycje gdyż dywersyfikują one ryzyko inwestycyjne między wiele dużych i obiecujących spółek. 
Nie wszystkie jednak spółki tworzące indeks wykorzystują swój potencjał oraz wpływają pozytywnie na jego waluację.

Celem projektu jest takie wybranie spółek z pośród SP500, aby stworzony z nich indeks umożliwił uzyskanie wyższych zysków w określonym okresie czasowym, przy zachowaniu podobnego do SP500 poziomu ryzyka. Aby to osiągnąć zostanie użyty klasyfikator typu las losowy.

Inspiracją do projektu jest niniejsza próba wykorzystania:
https://github.com/robertmartin8/MachineLearningStocks#financials
Wykorzystane zostaną również dane pozyskane przez autora powyższego rozwiązania. Sięgają one do 2003 roku oraz zebrane zostały za pomocą różnych metod (głównie parsowania starej wersji strony yahoo finance). Decyzja odnośnie wykorzystania danych zgromadzonych w cudzym projekcie motywowana jest trudnością w darmowym dostępie do tego typu danych.

# 1. Wymagania 
## Wymagania techniczne
- Język programowania: Python
- Biblioteki wykorzystane do realizacji projektu: Scikit-Learn, pandas, datetime, yfinance, numpy.
## Wymagania funkcjonalne
- Model wykorzystuje dane kwartalne oraz jednodniowe z ostatnich 30 lat.
- Model jest w stanie na podstawie wyników finansowych oraz podstawowych wskaźników oszacować czy spółka posiada potenciał inwestycyjny.
- Model jest podany testowaniu przeciw benchmarkowi.


# 2. Źródła Pozyskanych danych
Dane dotyczące S&P 500 oraz tworzących je spółek są zaciągane za pomocą Yahoo Finance API.
Przykładowo:
```python
import yfinance as yf
data = yf.download("SPY AAPL", start="2003-01-01", end="2023-04-30")
```
Dane finansowe spółek są pozyskiwane za pomocą metody znajdującej się w pliku MLF_2.ipynb. 

Powyższe założenie pomimo usilnych prób nie zostało spełnione. Podczas przygotowywania danych doszliśmy do problemu związanego ze ścianą kosztową, która opisana jest w dalszej części. 

Innym źródłem danych są również pliki zawierajace dane parsowane z portalu Yahoo Finance, ktore nie sa juz ogolnodostepne. Pozyskane zostały one w naszym przypadku od użytkownika @robertmartin8
https://github.com/robertmartin8/MachineLearningStocks#financials

* Z racji luki w dostępie do darmowych danych finansowych (pobrane pliki csv są do 2014, natomiast dane pobrane z API od 2018 do 2023) model używa danych do momentu w których została przerwana ich ciągłość (wybieranie spółek do indeksu może więc na podstawie pozyskanych danych nastąpić maskymalnie na początku 2014 roku)

# 3. Opis Pozyskanych danych
Dane przedstawiają wartość indeksu giełdowego SP500 z ostatnich 20 lat (2003-2023)
SP 500 charakteryzowało się trędem wzrostowym o dużej zmienności i odchyleniu od średniej.
![image](https://user-images.githubusercontent.com/63823444/228917161-e3b4664e-d0c9-4fd6-b0c9-759c9d3a4715.png)


- Największa uśredniona zmienność roczna indeksu wynosiła: 32%
- Najmniejsza uśreniona zmienność roczna indeksu wynosiła: 2%

Wartość SP 500:
- Najmniejsza wartość indeksu występuje w badanym okresie w 2009 roku i wyniosła 679.280029$
- Najwięsza watrtość indeksu występuje w badanym okresie w 2021 roku i wyniosła 4818.620117$
- Największy możliwy do zrealizowania zwrot na jednej inwestycji w badanym okresie wynosi 709% (założenie kupna po najmniejszej wartości indeksu i sprzedaży, gdy jego wartość była największa). 

Odchylenie standardowe od średniej w badanym okresie wyniosło: 1012.430192$

Do zrobienia: Dzienne zmiany, Wykres słupowy zmian rocznych, współczynnik zmienności, średni roczny zwrot



Podstawowe statystyki:

![image](https://user-images.githubusercontent.com/63823444/232218363-00bd74cc-426d-4c1c-b378-2e03a3ca5cd1.png)

# Problemy napotkane podczas gromadzenia danych
Dane finansowe dotyczące financial statements spółek giełdowych sprzed roku 2018 nie były dostępne za darmo.
Wykorzystaliśmy więc do trenowania modelu zbiór przygotowany przez pasjonata:

Dane autorstwa: https://github.com/robertmartin8/ 
SP500 Index : Yahoo Finance API
Poszczególne spółki :  stock_prices.csv
Wskaźniki techniczne i finansowe: keystats.csv
Zbiór predykcyjny szacowany: forward_sample.csv

# Transformacje dokonane na danych:
Sprawozdania finansowe dostarczane w różnych okresach spowodowały potrzebe określenia wartości cen akcji oraz SP500 na konkretny dzień dla każdej z nich.
Szacowane wartości następnych sprawozdań uzyskane zostały na okres szacunkowy 02.01.2014.
Konieczność zastosowania cen aktualnych w celu określenia klasyfikatora do weryfikacji wyniku stworzyła potrzebę utworzenia zbioru danych zawierających dzienne ceny akcji wszystkich 500 spółek.
Zaniżona wartość accuracy wywołana naruszeniem cyklu wahań rynkowych poprzez dokonanie predykcji w określonym momencie
Ponieważ rynki akcyjne zamknięte są w weekendy, ale sprawozdania finansowe mogą zostać udostępnione w te dni, ceny akcji oraz sp500 z konkretnego dnia weekendu zostały przyjęte jako ceny z najbliższego dnia z otwartym rynkiem (poniedzialek).


# 4. Model
Wynikiem działania modelu jest zestaw spółek, na którego bazie budować można model manipulujacy wagami oraz tworzący z nich index.
W naszym przypadku analizę zakończyliśmy na wyniku przez nas otrzymanym oraz zawartym w raporcie.

## Przygotowanie datasetu treningowego i walidacyjnego 
```python
training_data = keystats.set_index('Date')
training_data.dropna(axis=0, how='any', inplace=True)
features = training_data.columns[6:]

X_train = training_data[features].values

OUTPERFORMANCE = 10

y_train = [
  1 if stock_p_change - SP500_p_change >= OUTPERFORMANCE else 0
  for stock_p_change, SP500_p_change in zip(
      training_data["stock_p_change"], training_data["SP500_p_change"]
  )]

  ```

W pliku keystats znajdują się różnego rodzaju wskaźniki obliczone na bazie sprawozdań finansowych spółek X_train to właśnie te wskaźniki. Zbiór walidacyjny y_train to w rzeczywistości zmapowany zbiór nazw wszystkich spółek, jednak z wyróżnieniem tych w których przypadku wynik jest przynajmniej lepszy od indeksu o wartość zmiennej OUTPERDORMANCE. Model dokonuje więc predykcji tego, które dostępnych mu spółek osiągną dany Outperformance na podstawie przedstawionego mu wcześniej zbioru statystyk finansowych. 


Jako główny model został wykorzystany las losowy ze 100 estymatorami oraz random_state = 0.

```python
rfc = RandomForestClassifier(n_estimators = 100, random_state=0, criterion= 'gini')
rfc.fit(X_train, y_train)

importances = rfc.feature_importances_
std = np.std([tree.feature_importances_ for tree in rfc.estimators_], axis=0)
```

# Wyniki
Liczba spółek oraz ich symbole, wybrane przez model na początek 2014 roku.
![image](https://github.com/dzejkopjarosinski/ZespolR/assets/63823444/c98a99e0-a215-4ea2-93ca-bbad0688b0a9)


Poniżej algorytm pokazuje na podstawie indeksu Giniego, które cechy są najlepszym splitem oraz wyznacznikiem tego czy spółka powinna trafić do nowego indeksu czy też nie.
![image](https://github.com/dzejkopjarosinski/ZespolR/assets/63823444/3a44592d-0098-4b97-9c9d-9525c50ba62a)


Koszyk spółek może się różnić w zależności od momentu na który algorytm jest uruchomiony. Dla indesku stworzonego ze spółek wybranych przez model na początku 2014 roku, zwrot przyrównany do SP500 wygląda następująco:
![image](https://github.com/dzejkopjarosinski/ZespolR/assets/63823444/adc6d00a-81bf-4f87-8e6f-05320bc685ae)

* Index modelowy przewyższał średnio wynik SP500 o 18.8 %
* Korelacja między indeksem a SP500 wyniosła 99.1%
* Odchylenie standardowe dla obu indesów wyniosło 27.5%

Powyższe informacje pokazują więc, że model zgodnie z założeniem stworzył indeks który charakteryzuje się podobnym do SP500 poziomem ryzyka, zapewniając jednocześnie potencjalnie wyższe zyski.

