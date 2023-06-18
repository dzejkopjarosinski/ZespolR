# Projekt zaliczeniowy z przedmiotu Uczenie maszynowe w finansach
Repozytorium zespolu projektowego przedmiotu uczenie maszynowe w finansach 

# Cel i założenia projektu

S&P500 cieszy się sporą popularnością wśród inwestorów ze względu na historycznie znany wysoki performance oraz obietnicę wysokich zysków.
Nie wszystkie jednak spółki tworzące indeks wykorzystują swój potencjał oraz wpływają pozytywnie na jego waluację.

Z tego powodu zdecydowaliśmy się przygotować projekt, którego celem byłoby wybranie spółek spośród S&P500, z których stworzone portfolio umożliwiałoby osiągnięcie wyższych zysków w określonym okresie czasowym.

Efektywnie stworzone portfolio można traktować jako własny indeks, w którym każda ze spółek ma równy udział.
Następnie porównamy stworzony przez nas indeks, S&P100 oraz ich prognozy.

W trakcie pracy nad projektem natrafiliśmy na rozwiązanie, które w dużej mierze stało się dla nas inspiracją:
https://github.com/robertmartin8/MachineLearningStocks#financials
W swojej pracy wykorzystaliśmy również dane zebrane przez autora powyższego rozwiązania. Sięgają one do 2003 roku oraz zebrane zostały za pomocą różnych metod.
Zdecydowaliśmy się na to ze względu na ograniczoną dostępnosć danych finansowych dotyczących spółek oferowanych za darmo.

# 1. Wymagania funkcjonalne i techniczne
## Wymagania techniczne
- Wykorzystywany jest język programowania Python
- Biblioteki oraz inne dependencies określone zostaną w trakcie przygotowania modelu w zależności od zapotrzebowania. Pełna lista wykorzystanych bibliotek dostępna będzie w postaci pliku requirements.txt
- Do znanych należą: Yahoo Finance, sklearn, pandas, numpy 
- Potencjalne: TensorFlow, Prophet, Keras, matplotlib/seaborn, plotly, requests (API TradingView, Yahoo Finance)
##Wymagania funkcjonalne
- Model będzie w stanie wykorzystywać dane przedstawione w postaci dziennej (timeframe- 1D) na podstawie ostatnich 20 lat
- Model będzie w stanie na podstawie wyników finansowych oraz podstawowych wskaźników oszacować potencjalną wartość inwestycyjną spółki (w postaci podziału na spółki o prognozowanym wzroście o 10% większym niż S&P500)
- Model będzie w stanie na podstawie wskaźników oszacować możliwość uwzględnienienia spółki w grupie wybranych
- Model będzie w stanie porównać wydajnosć (zwrot z inwestycji) stworzonego indeksu z S&P500


# 2. Opis danych oraz ich źródła
Dane przedstawiają wartość indeksu giełdowego SP500 z ostatnich 20 lat (2003-2023)
SP 500 charakteryzowało się trędem wzrostowym o dużej zmienności i odchyleniu od średniej.
![image](https://user-images.githubusercontent.com/63823444/228917161-e3b4664e-d0c9-4fd6-b0c9-759c9d3a4715.png)


- Największa uśredniona zmienność roczna indeksu wynosiła: 32%
- Najmniejsza uśreniona zmienność roczna indeksu wynosiła: 2%

Wartość SP 500:
- Najmniejsza wartość indeksu występuje w badanym okresie w 2009 roku i wyniosła 679.280029$
- Najwięsza watrtość indeksu występuje w badanym okresie w 2021 roku i wyniosła 4818.620117$

Odchylenie standardowe od średniej w badanym okresie wyniosło: 1012.430192$

Do zrobienia: Dzienne zmiany, Wykres słupowy zmian rocznych, współczynnik zmienności, średni roczny zwrot


Dane dotyczące S&P 500 oraz tworzących je spółek zaciągane będą za pomocą Yahoo Finance API oraz będziemy korzystać z danych przygotowanych w początkowo wymienionym repozytorium.
Przykładowo:
```python
import yfinance as yf
data = yf.download("SPY AAPL", start="2003-01-01", end="2023-04-30")
```
Podstawowe statystyki:

![image](https://user-images.githubusercontent.com/63823444/232218363-00bd74cc-426d-4c1c-b378-2e03a3ca5cd1.png)

## Problemy napotkane podczas gromadzenia danych
Dane finansowe dotyczące financial statements spółek giełdowych sprzed roku 2018 nie były dostępne za darmo.
Wykorzystaliśmy więc do trenowania modelu zbiór przygotowany przez pasjonata:

Dane autorstwa: https://github.com/robertmartin8/ 
SP500 Index : Yahoo Finance API
Poszczególne spółki :  stock_prices.csv
Wskaźniki techniczne i finansowe: keystats.csv
Zbiór predykcyjny szacowany: forward_sample.csv

##Transformacje dokonane na danych:
Sprawozdania finansowe dostarczane w różnych okresach
Szacowane wartości następnych sprawozdań
Konieczność zastosowania cen aktualnych w celu określenia klasyfikatora do weryfikacji wyniku
Zaniżona wartość accuracy wywołana naruszeniem cyklu wahań rynkowych poprzez dokonanie predykcji w określonym momencie
Rynki są zamknięte w Weekend

#3. Wyniki
Wynikiem działania modelu jest zestaw spółek, na którego bazie budować można model manipulujacy wagami oraz tworzący z nich index.
W naszym przypadku analizę zakończyliśmy na wyniku przez nas otrzymanym oraz zawartym w raporrcie

