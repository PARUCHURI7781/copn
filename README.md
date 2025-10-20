SELECT
    'MY_DATABASE' AS database_name,
    c.owner,
    c.table_name,
    c.column_name,
    c.data_type
FROM
    all_tab_columns c
WHERE
    c.owner = 'YOUR_OWNER_NAME'
    AND c.table_name IN (
        SELECT table_name
        FROM all_tables
        WHERE owner = 'YOUR_OWNER_NAME'
    )
    AND c.table_name NOT IN (
        'ACT_TAGOUT_SECTION_TAGS',
        'CLEARANCE_GROUPS',
        'ESOMS_SEARCH',
        'FATIGUE_REPORT',
        'MAS_TAGOUT_SECTION_TAGS',
        'OUTAGE_REPORT',
        'SECTION_VERIF_LEVELS',
        'SITES',
        'TC_RESTRAINT',
        'TEMPORARY_LIFT_TAGS',
        'TOUR',
        'USER_SITES',
        'WATCHBILL_VIOLATIONS_DAYS_OFF'
    )
ORDER BY
    c.table_name,
    c.column_id;
