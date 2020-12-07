# Описание задачи
Many people struggle to get loans due to insufficient or non-existent credit histories. And, unfortunately, this population is often taken advantage of by untrustworthy lenders.

Home Credit strives to broaden financial inclusion for the unbanked population by providing a positive and safe borrowing experience. In order to make sure this underserved population has a positive loan experience, Home Credit makes use of a variety of alternative data--including telco and transactional information--to predict their clients' repayment abilities.

While Home Credit is currently using various statistical and machine learning methods to make these predictions, they're challenging Kagglers to help them unlock the full potential of their data. Doing so will ensure that clients capable of repayment are not rejected and that loans are given with a principal, maturity, and repayment calendar that will empower their clients to be successful.

Submissions are evaluated on area under the ROC curve between the predicted probability and the observed target.

For each loaner in the test set, you must predict a probability for the TARGET variable.

**Data Description**
- application_{train|test}.csv

This is the main table, broken into two files for Train (with TARGET) and Test (without TARGET).
Static data for all applications. One row represents one loan in our data sample.

- bureau.csv

All client's previous credits provided by other financial institutions that were reported to Credit Bureau (for clients who have a loan in our sample).
For every loan in our sample, there are as many rows as number of credits the client had in Credit Bureau before the application date.

- bureau_balance.csv

Monthly balances of previous credits in Credit Bureau.
This table has one row for each month of history of every previous credit reported to Credit Bureau – i.e the table has (#loans in sample * # of relative previous credits * # of months where we have some history observable for the previous credits) rows.

- POS_CASH_balance.csv

Monthly balance snapshots of previous POS (point of sales) and cash loans that the applicant had with Home Credit.
This table has one row for each month of history of every previous credit in Home Credit (consumer credit and cash loans) related to loans in our sample – i.e. the table has (#loans in sample * # of relative previous credits * # of months in which we have some history observable for the previous credits) rows.

- credit_card_balance.csv

Monthly balance snapshots of previous credit cards that the applicant has with Home Credit.
This table has one row for each month of history of every previous credit in Home Credit (consumer credit and cash loans) related to loans in our sample – i.e. the table has (#loans in sample * # of relative previous credit cards * # of months where we have some history observable for the previous credit card) rows.

- previous_application.csv

All previous applications for Home Credit loans of clients who have loans in our sample.
There is one row for each previous application related to loans in our data sample.

- installments_payments.csv

Repayment history for the previously disbursed credits in Home Credit related to the loans in our sample.
There is a) one row for every payment that was made plus b) one row each for missed payment.
One row is equivalent to one payment of one installment OR one installment corresponding to one payment of one previous Home Credit credit related to loans in our sample.

- HomeCredit_columns_description.csv

This file contains descriptions for the columns in the various data files.

**Указанные выше файлы можно скачать по ссылке - https://www.kaggle.com/c/home-credit-default-risk/data**

# Этапы решения задачи
**1. Работаем с Application data**
- определили и удалили аномальные значения признаков, а также создали признаки метки для аномальных значений
- создали логические фичи (например, определили отношение дохода к ежемесячному платежу, отношение цены товара к сумме кредита и др.)
- провели бинаризацию категориальных данных

**2. Работаем с Bureau data**
- определили количество кредитов на заемщика в других банках согласно данным кредитного бюро (в т.ч.открытых, закрытых, просроченных и т.д.)
- определили общую сумму открытых кредитов
- бинаризовали и агрерировали категориальные данные
- определили количество месяцев до погашения по открытым кредитам на каждого клиента
- определили суммы по кждому типу кредита (на наличные, потребительский, жилье, машины и т.д.)
- определили суммы просроченных кредитов
- рассчитали коэффициенты использования кредитных карт (сумма кредита по карте к лимиту)

**3. Previous application data**
- определили количество кредитов ранее полученных в банке (в т.ч. закрытых и открытых), заявки на кредиты в прошлом 
- определили аномальные значения и их удалили
- определили суммы закрытых и открытых кредитов
- бинаризовали и агрегировали данные категориальные данные
- агрегировали оставшиеся признаки (среднее, сумма, ст.отклонение, мин. макс). Потом при проверке модели удалили не значимые и удалили соответствующие команды агрегации
- рассчитали дополнительные коэффициенты

**4. Previous credit card balance data**
- определили количество ранее открытых кредитных карт
- бинаризировали и агрегировали категориальные данные
- агрегировали оставшиеся признаки (среднее, сумма, ст.отклонение, мин. макс). Потом при проверке модели удалили не значимые и удалили соответствующие команды агрегации
- рассчитали дополнительные коэффициенты

**5. Previous POS-CASH balance**
- определили количество балансов по каждому кредиту и сгруппировали их по заемщику (сумма)
- бинаризовали и агрегировали категориальные данные
- агрегировали оставшиеся признаки (среднее, сумма, ст.отклонение, мин. макс). Потом при проверке модели удалили не значимые и удалили соответствующие команды агрегации

**6. Previous installments data**
- агрегировали данные

**7. Объединили данные в одну таблицу**

**8. Создали несколько коэффициентов на основании данных из объединенной таблицы**
- суммировали все открытые кредиты
- посчитали коэффициенты по всем открытым кредитам к доходам
- посчитали все будущие платежы и т.д.

**9. Моделирование**
- ручная настройка модели (ручной подбор параметров). В будущем можно попробовать байесовскую оптимизацию гиперпараметров модели
- определение признаков, которые не использовались и их удаление

**Заметки на будущее**
1. Байесовскую модель оптимизации гиперпараметров поюзать
2. Попробовать применить линейные модели (логистическая регрессия) предварительно провести обработку данных: заменить правильно пропущенные значения, сгладить данные - биннинг
3. Лучше настроить кросс-валидацию
4. Подумать над тем как правильнее исследовать ошибки алгоритма (визуализация значений признаков ошибочных объектов)
5. Подумать об оптимизации кода, в т.ч.распаралеливании процессов при обучении модели
6. Больше дополнительных коэффициентов, в т.ч.не только экспертных но и полиномиальных признаков
