--Q4. Bu data ile kendin bir business question üretip analiz etmeni istesek hangi soruya nasıl bir cevap bulurdun?
--trendyol go'nun resourcelarından biri de çalıştırdığı kuryelerdir.
-- kargo sayıları çok fazla değişiklik gösterdiği için bunları karşılamak için yeterli insan gücü önceden hazır tutulmalıdır ki
--müşter memnuniyetinden taviz verilmesin. İşe alım süreci uzun sürdüğü için öngörülebilir olması için benim cevabını bulmak istediğim soru 
--Trendyol Go kurulduğundan beri elde edilen verileri kullanarak önümüzdeki ay için ne kadar kuryemiz olması gerektiğini hesaplayacağım.
-- Önümüzdeki ay için müşteri memnuniyetinden taviz vermemek için ne kadar kuryenin çalışır vaziyette olması gerekiyor ?
-- Go kurye tahmini

-- --Q4 TABLO OLUSTURMA:
-- declare begin_date timestamp default timestamp("2020-06-10 00:00:00.000 UTC");
-- declare end_date timestamp default timestamp("2021-10-08 00:00:00.000 UTC");

-- create or replace table `analytics-bootcamp-2-340509.tbcase.Q4_Time_Series` as (
-- select Date,count(distinct shipment_number) Cargo from (
-- select extract(date from order_date) Date, extract(Year from order_date) as Year,extract(Month from order_date) as Month,extract(Day from order_date) as Day,a.*
-- from 
-- ( 
--     select * 
--     from 
--     (select * from `analytics-bootcamp-2-340509.tbcase.transaction` t JOIN `analytics-bootcamp-2-340509.tbcase.users` u using(user_id)) 
--     where order_date between begin_date and end_date
-- ) a where Platform="Grocery"order by user_id, order_date desc
--  ) group by Date order by 1);


#standardSQL
-- CREATE OR REPLACE MODEL `analytics-bootcamp-2-340509.tbcase.Trendyol_ARIMA_Model`
-- OPTIONS
--  (model_type = 'ARIMA_PLUS',
--   time_series_timestamp_col = 'Date',
--   time_series_data_col = 'Cargo',
--   auto_arima = TRUE,
--   data_frequency = 'AUTO_FREQUENCY',
--   decompose_time_series = TRUE,
--   AUTO_ARIMA= TRUE,
--   holiday_region='TR'

-- ) AS
-- SELECT *
-- FROM
--  `analytics-bootcamp-2-340509.tbcase.Q4_Time_Series` limit 383;

#standardSQL
with a as(

      select * from (SELECT
      history_timestamp AS timestamp,
      history_value,
      NULL AS forecast_value,
      NULL AS prediction_interval_lower_bound,
      NULL AS prediction_interval_upper_bound
      FROM
      (

        SELECT
          PARSE_TIMESTAMP("%Y-%m-%d", cast(ts.Date as STRING ))  AS history_timestamp,
          ts.Cargo AS history_value
        FROM
          `analytics-bootcamp-2-340509.tbcase.Q4_Time_Series` ts
        ORDER BY ts.Date ASC limit 383
      )
      UNION ALL
      SELECT
      forecast_timestamp AS timestamp,
      NULL AS history_value,
      forecast_value,
      prediction_interval_lower_bound,
      prediction_interval_upper_bound
      FROM
      ML.FORECAST(MODEL `analytics-bootcamp-2-340509.tbcase.Trendyol_ARIMA_Model`,
                  STRUCT(96 AS horizon, 0.9 AS confidence_level))) order by timestamp asc

)
select * from a;

select * from (SELECT
  PARSE_TIMESTAMP("%Y-%m-%d", cast(ts.Date as STRING ))  AS history_timestamp,
  ts.Cargo AS history_value
FROM
  `analytics-bootcamp-2-340509.tbcase.Q4_Time_Series` ts
ORDER BY ts.Date desc limit 96) order by history_timestamp asc;


SELECT
  *
FROM
  ML.EVALUATE(MODEL `analytics-bootcamp-2-340509.tbcase.Trendyol_ARIMA_Model`,
    (
    select * from (SELECT
      PARSE_TIMESTAMP("%Y-%m-%d", cast(ts.Date as STRING ))  AS Date,
      ts.Cargo AS Cargo
    FROM
     `analytics-bootcamp-2-340509.tbcase.Q4_Time_Series` ts order by Date desc limit 96) order by Date asc
     
     ),
    STRUCT(TRUE AS perform_aggregation, 96 AS horizon))
