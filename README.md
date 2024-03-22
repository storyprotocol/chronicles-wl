# ETH Denver Chronicles Results

The ETH Denver Chronicles initiative has garnered an impressive level of engagement, attracting numerous submissions. However, among these, we've identified instances of spam and potential malicious activities. In the following sections, we will meticulously examine each query used to sift through the data, elucidating the intent, rationale, and specific criteria applied to isolate and remove the undesirable elements.

## Spam Filtering Query

```sql
SELECT * FROM public.chapter
WHERE created_at < 1709535600000
AND char_length("content") < 5
```

- Purpose: Filters out spam by removing entries with very short content.
- Logic: Selects all columns from the public.chapter table where:
  - created_at is less than 1709535600000 (a specific timestamp indicating the cutoff date and time before 3/3/2024 ).
  - char_length("content") is less than 5, assuming that spam messages are very short.

## Malicious Activity Detection Query

```sql
SELECT subquery.wallet_address, COUNT(*) AS count
FROM (
    SELECT x.* FROM public.chapter x
    WHERE created_at < 1709535600000
) AS subquery
GROUP BY subquery.wallet_address
HAVING COUNT(*) > 1
ORDER BY COUNT(*) DESC;
```

- Purpose: Identifies potential malicious attackers by counting posts from the same wallet address.
- Logic:
  - Inner query selects all data from public.chapter where created_at is before a certain timestamp.
  - Outer query groups the results by wallet_address and counts the entries, filtering for addresses with more than one post, indicating potential spam or malicious activity.

## Results Interpretation Query

```sql
SELECT subquery.wallet_address, COUNT(*) AS count
FROM (
    SELECT x.* FROM public.chapter x
    WHERE created_at < 1709535600000
    AND char_length("content") > 5
) AS subquery
GROUP BY subquery.wallet_address
HAVING COUNT(*) <= 4
ORDER BY COUNT(*) DESC;

```

- Purpose: Seems to identify single, possibly legitimate posts from wallet addresses before a certain time with content length criteria.
- Logic:
  - Inner query filters records from public.chapter before a set timestamp and with content longer than 5 characters.
  - Outer query groups these by wallet_address, but filters for those with only a single post, which may indicate an attempt to identify non-malicious activity or a mistake in the query logic since it orders by descending count but filters for a count less than 5.
