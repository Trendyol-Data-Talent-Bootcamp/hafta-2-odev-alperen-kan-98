* İstenilen işlem:
```
MERGE alperen_kan.content_category TAR
USING alperen_kan.content_category_20201222_00_59 SOR
ON TAR.ID = SOR.ID
WHEN NOT MATCHED BY TARGET 
  THEN
    INSERT(CDC_DATE, IS_DELETED, ID, CATEGORY)
    VALUES(SOR.CDC_DATE, SOR.IS_DELETED, SOR.ID, SOR.CATEGORY)
WHEN MATCHED AND FARM_FINGERPRINT(TO_JSON_STRING(TAR)) <> FARM_FINGERPRINT(TO_JSON_STRING(SOR))
  THEN
    UPDATE SET
      TAR.CDC_DATE = SOR.CDC_DATE,
      TAR.IS_DELETED = SOR.IS_DELETED,
      TAR.CATEGORY = SOR.CATEGORY
WHEN NOT MATCHED BY SOURCE 
  THEN
    UPDATE SET
      TAR.IS_DELETED = TRUE;
```
* Kontrol:
```
SELECT * FROM alperen_kan.content_category TST
FULL JOIN alperen_kan.content_category_target SRC
ON FARM_FINGERPRINT(TO_JSON_STRING(TST.ID)) = FARM_FINGERPRINT(TO_JSON_STRING(SRC.ID))
WHERE TST.ID IS NULL OR SRC.ID IS NULL;
```
