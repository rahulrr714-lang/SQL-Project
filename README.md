CREATE TABLE amazon_products (

    name VARCHAR2(4000),

    main_category VARCHAR2(4000),

    sub_category VARCHAR2(4000),

    image VARCHAR2(4000),

    link VARCHAR2(4000),

    ratings VARCHAR2(100),

    no_of_ratings VARCHAR2(100),

    discount_price VARCHAR2(100),

    actual_price VARCHAR2(100)

);
SELECT COUNT(*) FROM amazon_products;

CREATE TABLE amazon_products_clean AS
SELECT
    name,
    main_category,
    sub_category,
    ratings,

    TO_NUMBER(
        REPLACE(no_of_ratings, ',', '')
    ) AS no_of_ratings,

    TO_NUMBER(
        REPLACE(
            REPLACE(discount_price, '₹', ''),
        ',', '')
    ) AS discount_price,

    TO_NUMBER(
        REPLACE(
            REPLACE(actual_price, '₹', ''),
        ',', '')
    ) AS actual_price

FROM amazon_products;
SELECT discount_price
FROM amazon_products
WHERE NOT REGEXP_LIKE(
    REPLACE(
        REPLACE(discount_price, '₹', ''),
    ',', ''),
    '^[0-9.]+$'
);
SELECT actual_price
FROM amazon_products
WHERE NOT REGEXP_LIKE(
    REPLACE(
        REPLACE(actual_price, '₹', ''),
    ',', ''),
    '^[0-9.]+$'
);
SELECT no_of_ratings
FROM amazon_products
WHERE NOT REGEXP_LIKE(
    REPLACE(no_of_ratings, ',', ''),
    '^[0-9]+$'
);
CREATE TABLE amazon_products_clean AS
SELECT
    name,
    main_category,
    sub_category,
    ratings,

    CASE
        WHEN REGEXP_LIKE(
            REPLACE(no_of_ratings, ',', ''),
            '^[0-9]+$'
        )
        THEN TO_NUMBER(
            REPLACE(no_of_ratings, ',', '')
        )
    END AS no_of_ratings,

    CASE
        WHEN REGEXP_LIKE(
            REPLACE(
                REPLACE(discount_price, '₹', ''),
            ',', ''),
            '^[0-9.]+$'
        )
        THEN TO_NUMBER(
            REPLACE(
                REPLACE(discount_price, '₹', ''),
            ',', '')
        )
    END AS discount_price,

    CASE
        WHEN REGEXP_LIKE(
            REPLACE(
                REPLACE(actual_price, '₹', ''),
            ',', ''),
            '^[0-9.]+$'
        )
        THEN TO_NUMBER(
            REPLACE(
                REPLACE(actual_price, '₹', ''),
            ',', '')
        )
    END AS actual_price

FROM amazon_products;
SELECT * FROM amazon_products_clean;

SELECT COUNT(*) AS total_products
FROM amazon_products_clean;

SELECT DISTINCT main_category
FROM amazon_products_clean;

SELECT ROUND(AVG(ratings),2) AS avg_rating
FROM amazon_products_clean;

SELECT ROUND(
    AVG(
        CASE
            WHEN REGEXP_LIKE(ratings, '^[0-9.]+$')
            THEN TO_NUMBER(ratings)
        END
    ),
2) AS avg_rating
FROM amazon_products_clean;

SELECT name, actual_price
FROM amazon_products_clean
ORDER BY actual_price DESC;

SELECT actual_price
FROM amazon_products
FETCH FIRST 10 ROWS ONLY;

SELECT
    actual_price,
    REPLACE(
        REPLACE(
            TRIM(actual_price),
        '₹', ''),
    ',', '') AS cleaned_price
FROM amazon_products;

SELECT 
    name,
    TO_NUMBER(
        REPLACE(
            REPLACE(
                TRIM(actual_price),
            '₹', ''),
        ',', '')
    ) AS cleaned_price

FROM amazon_products_clean

ORDER BY cleaned_price DESC;

SELECT
    actual_price,
    REPLACE(
        REPLACE(
            TRIM(actual_price),
        '₹', ''),
    ',', '') AS cleaned_price
FROM amazon_products;
SELECT
    name,

    TO_NUMBER(
        REPLACE(
            REPLACE(
                TRIM(actual_price),
            '₹', ''),
        ',', '')
    ) AS cleaned_price

FROM amazon_products

ORDER BY cleaned_price DESC;
SELECT
    actual_price,

    TO_NUMBER(
        REPLACE(
            REPLACE(
                TRIM(actual_price),
            '₹', ''),
        ',', '')
    ) AS cleaned_price

FROM amazon_products;
SELECT
    name,

    TO_NUMBER(
        REPLACE(
            REPLACE(
                TRIM(actual_price),
            '₹', ''),
        ',', '')
    ) AS cleaned_price

FROM amazon_products

ORDER BY cleaned_price DESC;
SELECT 
    name, 
    actual_price
FROM amazon_products
ORDER BY cleaned_price DESC;


SELECT name, discount_price
FROM amazon_products_clean
ORDER BY discount_price ASC;
SELECT name, ratings
FROM amazon_products_clean
ORDER BY ratings DESC;
SELECT DISTINCT ratings
FROM amazon_products_clean
WHERE NOT REGEXP_LIKE(ratings, '^[0-9.]+$')
   OR ratings IS NULL;
   SELECT
    name,
    TO_NUMBER(ratings) AS numeric_rating
FROM amazon_products_clean
WHERE REGEXP_LIKE(ratings, '^[0-9.]+$')
ORDER BY numeric_rating DESC;

SELECT 
    name,

    TO_NUMBER(
        REPLACE(
            REPLACE(
                TRIM(actual_price),
            '₹', ''),
        ',', '')
    ) AS cleaned_price

FROM amazon_products

ORDER BY cleaned_price DESC NULLS LAST;

SELECT
    main_category,
    COUNT(*) AS total_products
FROM amazon_products_clean
GROUP BY main_category
ORDER BY total_products DESC;

SELECT
    main_category,
    ROUND(AVG(discount_price),2) AS avg_price
FROM amazon_products_clean
GROUP BY main_category
ORDER BY avg_price DESC;

SELECT
    name,
    actual_price,
    discount_price,

    ROUND(
        ((actual_price - discount_price)
        / actual_price) * 100,
    2) AS discount_percent

FROM amazon_products_clean
ORDER BY discount_percent DESC nulls last;
SELECT
    name,
    ratings,
    no_of_ratings
FROM amazon_products_clean
WHERE ratings >= 4.5
AND no_of_ratings >= 1000
ORDER BY ratings DESC;

SELECT
    name,
    ratings,
    no_of_ratings

FROM amazon_products_clean

WHERE ratings >= 4.5
AND TO_NUMBER(REPLACE(no_of_ratings, ',', '')) >= 1000

ORDER BY ratings DESC;
SELECT no_of_ratings
FROM amazon_products_clean
WHERE REGEXP_LIKE(no_of_ratings, '[^0-9,]');

SELECT
    name,
    ratings,
    no_of_ratings
FROM amazon_products_clean
WHERE REGEXP_LIKE(ratings, '^[0-9.]+$')
AND TO_NUMBER(ratings) >= 4.5
AND REGEXP_LIKE(no_of_ratings, '^[0-9,]+$')
AND TO_NUMBER(REPLACE(no_of_ratings, ',', '')) >= 1000
ORDER BY TO_NUMBER(ratings) DESC;

SELECT
    name,
    ratings,
    no_of_ratings
FROM (
    SELECT *
    FROM amazon_products_clean
    WHERE REGEXP_LIKE(ratings, '^[0-9]+(\.[0-9]+)?$')
      AND REGEXP_LIKE(no_of_ratings, '^[0-9,]+$')
)
WHERE TO_NUMBER(ratings) >= 4.5
  AND TO_NUMBER(REPLACE(no_of_ratings, ',', '')) >= 1000
ORDER BY TO_NUMBER(ratings) DESC;

SELECT
    name,
    ratings,
    no_of_ratings
FROM amazon_products_clean
WHERE CASE 
        WHEN REGEXP_LIKE(ratings, '^[0-9]+(\.[0-9]+)?$') 
        THEN TO_NUMBER(ratings)
      END >= 4.5
  AND CASE 
        WHEN REGEXP_LIKE(no_of_ratings, '^[0-9,]+$') 
        THEN TO_NUMBER(REPLACE(no_of_ratings, ',', ''))
      END >= 1000
ORDER BY TO_NUMBER(ratings) DESC;

SELECT
    name,
    ratings,
    no_of_ratings
FROM (
    SELECT *
    FROM amazon_products_clean
    WHERE REGEXP_LIKE(ratings, '^[0-9]+(\.[0-9]+)?$')
      AND REGEXP_LIKE(no_of_ratings, '^[0-9,]+$')
)
WHERE TO_NUMBER(ratings) >= 4.5
  AND TO_NUMBER(REPLACE(no_of_ratings, ',', '')) >= 1000
ORDER BY TO_NUMBER(ratings) DESC;

SELECT
    name,
    ratings,
    no_of_ratings
FROM amazon_products_clean
WHERE REGEXP_LIKE(ratings, '^[0-9]+(\.[0-9]+)?$')
  AND REGEXP_LIKE(no_of_ratings, '^[0-9,]+$')
  AND TO_NUMBER(CASE 
        WHEN REGEXP_LIKE(ratings, '^[0-9]+(\.[0-9]+)?$') 
        THEN ratings 
      END) >= 4.5
  AND TO_NUMBER(CASE 
        WHEN REGEXP_LIKE(no_of_ratings, '^[0-9,]+$') 
        THEN REPLACE(no_of_ratings, ',', '') 
      END) >= 1000
ORDER BY TO_NUMBER(ratings) DESC;

SELECT ratings, no_of_ratings
FROM amazon_products_clean
FETCH FIRST 20 ROWS ONLY;

SELECT
    name,
    ratings,
    no_of_ratings
FROM amazon_products_clean
WHERE REGEXP_LIKE(ratings, '[0-9]')
  AND REGEXP_LIKE(no_of_ratings, '[0-9]')
  AND TO_NUMBER(REGEXP_SUBSTR(ratings, '[0-9]+(\.[0-9]+)?')) >= 4.5
  AND TO_NUMBER(REPLACE(REGEXP_SUBSTR(no_of_ratings, '[0-9,]+'), ',', '')) >= 1000
ORDER BY TO_NUMBER(REGEXP_SUBSTR(ratings, '[0-9]+(\.[0-9]+)?')) DESC;

SELECT *
FROM (
    SELECT
        name,
        ratings,
        no_of_ratings,
        TO_NUMBER(REGEXP_SUBSTR(ratings, '[0-9]+(\.[0-9]+)?')) AS rating_num,
        TO_NUMBER(REPLACE(REGEXP_SUBSTR(no_of_ratings, '[0-9,]+'), ',', '')) AS review_num
    FROM amazon_products_clean
    WHERE REGEXP_LIKE(ratings, '[0-9]')
      AND REGEXP_LIKE(no_of_ratings, '[0-9]')
)
WHERE rating_num >= 4.5
  AND review_num >= 1000
ORDER BY rating_num DESC;

SELECT
    ratings,
    no_of_ratings
FROM amazon_products_clean
WHERE REGEXP_LIKE(ratings, '[0-9]')
AND REGEXP_LIKE(no_of_ratings, '[0-9]')
FETCH FIRST 20 ROWS ONLY;

SELECT *
FROM (
    SELECT
        ratings,
        no_of_ratings
    FROM amazon_products_clean
    WHERE REGEXP_LIKE(ratings, '[0-9]')
      AND REGEXP_LIKE(no_of_ratings, '[0-9]')
)
WHERE ROWNUM <= 20;


SELECT
    name,
    discount_price,

    RANK() OVER(
        ORDER BY discount_price DESC 
    ) AS price_rank

FROM amazon_products_clean;

SELECT
    name,
    discount_price,
    RANK() OVER(
        ORDER BY discount_price DESC NULLS LAST
    ) AS price_rank
FROM amazon_products_clean;

SELECT *
FROM (
    SELECT
        main_category,
        name,
        actual_price,

        ROW_NUMBER() OVER(
            PARTITION BY main_category
            ORDER BY actual_price DESC
        ) AS rn

    FROM amazon_products_clean
)
WHERE rn <= 5;

SELECT *
FROM (
    SELECT
        main_category,
        name,
        TO_NUMBER(REPLACE(actual_price, ',', '')) AS clean_price,

        ROW_NUMBER() OVER(
            PARTITION BY main_category
            ORDER BY TO_NUMBER(REPLACE(actual_price, ',', '')) DESC
        ) AS rn

    FROM amazon_products_clean
)
WHERE rn <= 5;

SELECT actual_price
FROM amazon_products_clean
WHERE actual_price IS NOT NULL
FETCH FIRST 20 ROWS ONLY;

SELECT *
FROM (
    SELECT
        main_category,
        name,
        TO_NUMBER(REGEXP_REPLACE(actual_price, '[^0-9.]', '')) AS clean_price,

        ROW_NUMBER() OVER (
            PARTITION BY main_category
            ORDER BY TO_NUMBER(REGEXP_REPLACE(actual_price, '[^0-9.]', '')) DESC
        ) AS rn

    FROM amazon_products_clean
) t
WHERE rn <= 5;

SELECT *
FROM (
    SELECT
        main_category,
        name,

        CASE
            WHEN REGEXP_REPLACE(actual_price, '[^0-9]', '') IS NULL
            THEN NULL
            ELSE TO_NUMBER(REGEXP_REPLACE(actual_price, '[^0-9]', ''))
        END AS clean_price,

        ROW_NUMBER() OVER (
            PARTITION BY main_category
            ORDER BY
                CASE
                    WHEN REGEXP_REPLACE(actual_price, '[^0-9]', '') IS NULL
                    THEN NULL
                    ELSE TO_NUMBER(REGEXP_REPLACE(actual_price, '[^0-9]', ''))
                END DESC
        ) AS rn

    FROM amazon_products_clean
)
WHERE rn <= 5;

SELECT *
FROM (
    SELECT
        main_category,
        name,

        CASE
            WHEN REGEXP_REPLACE(actual_price, '[^0-9]', '') IS NULL
              OR REGEXP_REPLACE(actual_price, '[^0-9]', '') = ''
            THEN NULL
            ELSE TO_NUMBER(REGEXP_REPLACE(actual_price, '[^0-9]', ''))
        END AS clean_price,

        ROW_NUMBER() OVER (
            PARTITION BY main_category
            ORDER BY
                CASE
                    WHEN REGEXP_REPLACE(actual_price, '[^0-9]', '') IS NULL
                      OR REGEXP_REPLACE(actual_price, '[^0-9]', '') = ''
                    THEN NULL
                    ELSE TO_NUMBER(REGEXP_REPLACE(actual_price, '[^0-9]', ''))
                END DESC
        ) AS rn

    FROM amazon_products_clean
)
WHERE rn <= 5;

SELECT
    COUNT(*) AS total_rows,
    COUNT(actual_price) AS non_null_prices
FROM amazon_products_clean;

SELECT *
FROM (
    SELECT
        main_category,
        name,
        actual_price AS clean_price,

        ROW_NUMBER() OVER (
            PARTITION BY main_category
            ORDER BY actual_price DESC
        ) AS rn

    FROM amazon_products_clean
)
WHERE rn <= 5;

SELECT COUNT(*) 
FROM amazon_products_clean
WHERE actual_price IS NULL;

SELECT *
FROM (
    SELECT
        main_category,
        name,
        actual_price,

        ROW_NUMBER() OVER (
            PARTITION BY main_category
            ORDER BY actual_price DESC
        ) rn

    FROM amazon_products_clean
    WHERE actual_price IS NOT NULL
)
WHERE rn <= 5;

CREATE OR REPLACE VIEW high_rated_products AS
SELECT
    name,
    ratings,
    discount_price
FROM amazon_products_clean
WHERE ratings >= 4.5;

SELECT * FROM high_rated_products;

CREATE OR REPLACE VIEW high_rated_products AS
SELECT
    name,
    ratings,
    discount_price
FROM amazon_products_clean
WHERE
    REGEXP_REPLACE(ratings, '[^0-9.]', '') IS NOT NULL
    AND REGEXP_REPLACE(ratings, '[^0-9.]', '') <> ''
    AND TO_NUMBER(REGEXP_REPLACE(ratings, '[^0-9.]', '')) >= 4.5;
    
    SELECT * FROM high_rated_products;
    
    CREATE OR REPLACE VIEW high_rated_products AS
SELECT
    name AS product_name,
    TO_NUMBER(REGEXP_REPLACE(ratings, '[^0-9.]', '')) AS rating,
    discount_price
FROM amazon_products_clean
WHERE TO_NUMBER(REGEXP_REPLACE(ratings, '[^0-9.]', '')) IS NOT NULL
  AND TO_NUMBER(REGEXP_REPLACE(ratings, '[^0-9.]', '')) >= 4.0;
  
  SELECT COUNT(*) FROM high_rated_products;
  SELECT * FROM high_rated_products;
  
  CREATE INDEX idx_category
ON amazon_products_clean(main_category);

SELECT *
FROM amazon_products_clean
WHERE ratings IS NULL;

CREATE OR REPLACE VIEW rated_products AS
SELECT *
FROM amazon_products_clean
WHERE ratings IS NOT NULL
AND no_of_ratings IS NOT NULL;

SELECT *
FROM amazon_products_clean
WHERE ratings IS NULL;

SELECT *
FROM amazon_products_clean
WHERE ratings IS NOT NULL;
CREATE OR REPLACE VIEW clean_rated_products AS
SELECT *
FROM amazon_products_clean
WHERE ratings IS NOT NULL;



SELECT
    main_category,
    COUNT(*) total_products
FROM amazon_products_clean
GROUP BY main_category
ORDER BY total_products DESC;


SELECT
    name,
    actual_price,
    discount_price
FROM amazon_products_clean
ORDER BY
(actual_price - discount_price) DESC;

SELECT
    main_category,
    ROUND(AVG(actual_price),2)
FROM amazon_products_clean
GROUP BY main_category
ORDER BY AVG(actual_price) DESC;

CREATE TABLE clean_table AS
SELECT * FROM amazon_products_clean
WHERE ratings IS NOT NULL;

SELECT * FROM clean_table;




