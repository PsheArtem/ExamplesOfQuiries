# XML запросы
**Создано поле [Производитель] типа XML в таблице [Товары].**

UPDATE Товары
SET [Производитель] = convert(XML, 
(SELECT DISTINCT [Товар] AS '@Goods',
    [Группа товаров] AS '@GroupProduct',
    ' ' AS '@Company',
    ' ' AS '@Country',
	' ' AS '@Adress'
  FROM ТоварыВдоговорах
  WHERE [dbo].[Товары].[Код товара]
 = [dbo].[ТоварыВдоговорах].[Код товара]
  FOR XML PATH('Info'), ELEMENTS))
  
  **Выполняется добавление производителей в таблицу [Товары]**

UPDATE Товары
SET Производитель.modify('replace value of 
(/Info/@Country)[1]
with ( "Россия" )')
WHERE [Товары].[Код товара] in (1,6,7,8,9,10)


UPDATE Товары
SET Производитель.modify('replace value of 
(/Info/@Company)[1]
with ( "ООО Рашаday" )')
WHERE [Товары].[Код товара] in (1,6,7,8,9,10)


UPDATE Товары
SET Производитель.modify('replace value of 
(/Info/@Adress)[1]
with ( "г Челябинск, ул. Мухина 4" )')
WHERE [Товары].[Код товара] in (1,6,7,8,9,10)


**Запрос для извлечения таблицы с полями: [Номер договора], [Товар], [Производитель], [Страна].**

SELECT d.[Номер договора], t.[Товар], a.*
FROM Товары AS t
INNER JOIN [ТоварыВдоговорах] AS d
ON t.[Код товара] = d.[Код товара]
CROSS APPLY (SELECT
 inf.value('@Company', 'nvarchar(50)') AS [Производитель],
 inf.value('@Country', 'nvarchar(50)') AS [Страна]
FROM t.Производитель.nodes('/Info') col(inf)) AS a