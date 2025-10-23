SELECT
    (SELECT name FROM v$database) AS db_name,
    t.table_name,
    to_number(
        extractvalue(
            xmltype(
                dbms_xmlgen.getxml('SELECT COUNT(*) c FROM ' || t.table_name)
            ),
            '/ROWSET/ROW/C'
        )
    ) AS oracle_row_count
FROM
    user_tables t;
