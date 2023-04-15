# Projekt zaliczeniowy z przedmiotu Uczenie maszynowe w finansach
Repozytorium zespolu projektowego przedmiotu uczenie maszynowe w finansach 

# Cel i założenia projektu

S&P500 cieszy się sporą popularnością wśród inwestorów ze względu na historycznie znany wysoki performance oraz obietnicę wysokich zysków.
Nie wszystkie jednak spółki tworzące indeks wykorzystują swój potencjał oraz wpływają pozytywnie na jego waluację.

Z tego powodu zdecydowaliśmy się przygotować projekt, którego celem byłoby wybranie 100 spółek spośród S&P500, z których stworzone portfolio umożliwiałoby osiągnięcie wyższych zysków przy zachowaniu podobnego poziomu ryzyka co pierwotny indeks.

Efektywnie stworzone portfolio można traktować jako własny indeks, poprzez określnie odpowiednich parametrów jego struktury (wag - ilościowy udział akcji danej spółki w indeksie), w którego tworzeniu S&P500 posłuży nam jako benchmark. W odróżnieniu od S&P500, które wagi ustala za pomocą równania:
weight = company_market_cap/index_market_cap

Następnie porównamy stworzony przez nas indeks, S&P100 oraz ich prognozy.

# 1. Wymagania funkcjonalne i techniczne
## Wymagania techniczne
- Wykorzystywany jest język programowania Python
- Biblioteki oraz inne dependencies określone zostaną w trakcie przygotowania modelu w zależności od zapotrzebowania. Pełna lista wykorzystanych bibliotek dostępna będzie w postaci pliku requirements.txt
- Do znanych należą: Yahoo Finance, sklearn, pandas, numpy 
- Potencjalne: TensorFlow, Prophet, Keras, matplotlib/seaborn, plotly, requests (API TradingView, Yahoo Finance)
##Wymagania funkcjonalne
- Model będzie w stanie wykorzystywać dane przedstawione w postaci dziennej (timeframe- 1D) na podstawie ostatnich 20 lat
- Model będzie w stanie na podstawie analizy income statements oraz wypowiedzi na temat spółki poprzez NLP określić sentyment z nią związany
- Model będzie w stanie na podstawie wyników finansowych oraz podstawowych wskaźników oszacować potencjalną wartość spółki
- Model będzie w stanie na podstawie wskaźników oszacować możliwość uwzględnienienia spółki w grupie wybranych 100
- Model będzie w stanie wykorzystać wskażniki z Yahoo Finace (Rating) w celu zwiększenia skuteczności doboru spółek
- Model będzie w stanie określić procentowy udział (wagę) poszczegółnych spółek w grupie 100 
- Model będzie w stanie porównać wydajnosć (zwrot z inwestycji) stworzonego indeksu z S&P500
- Model będzie w stanie używać analizy fundamentalnej oraz intrinsic value spółek (Metoda Warrena Buffeta, zakładany poziom 20% uśrednionego zwortu rocznie)

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


Dane dotyczące S&P 500 oraz tworzących je spółek zaciągane będą za pomocą Yahoo Finance API.
Przykładowo:
```python
import yfinance as yf
data = yf.download("SPY AAPL", start="2003-01-01", end="2023-04-30")
```
Podstawowe statystyki:
![image](https://user-images.githubusercontent.com/63823444/232218363-00bd74cc-426d-4c1c-b378-2e03a3ca5cd1.png)

