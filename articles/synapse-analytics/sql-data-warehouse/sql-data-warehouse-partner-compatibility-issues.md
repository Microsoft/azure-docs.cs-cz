---
title: Problémy s kompatibilitou s aplikacemi třetích stran a Azure synapse Analytics
description: Popisuje známé problémy, které mohou aplikace třetích stran najít pomocí Azure synapse
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: ''
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: 861b963922d64926266b42a23f85e9dc30fda7a3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95903980"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>Problémy s kompatibilitou s aplikacemi třetích stran a Azure synapse Analytics

Aplikace sestavené pro SQL Server budou hladce fungovat s vyhrazenými fondy SQL Azure synapse. V některých případech ale funkce a prvky jazyka, které se běžně používají v SQL Server nemusí být k dispozici v Azure synapse nebo se můžou chovat jinak.

V tomto článku jsou uvedené známé problémy, se kterými se můžete setkat při používání aplikací třetích stran s analýzou Azure synapse. 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Chyba Tableau: pokus o dokončení transakce se nezdařil. Nenašla se žádná odpovídající transakce. "

Od synapse vyhrazeného fondu SQL ve verzi 10.0.11038.0 můžou některé dotazy Tableau, které provádějí volání uložených procedur, selhat s následující chybovou zprávou: "**[Microsoft] [ovladač ODBC Driver 17 pro SQL Server] [SQL Server] 111214; Pokus o dokončení transakce se nezdařil. Nenašla se žádná odpovídající transakce.**"

### <a name="cause"></a>Příčina

Jedná se o problém ve vyhrazeném fondu SQL ve službě Azure synapse, který je způsobený představením nových systémových uložených procedur, které jsou automaticky volány ovladači ODBC a JDBC. Jeden z těchto systémových uložených procedur může způsobit, že otevřené transakce budou přerušeny, pokud dojde k jejich spuštění. K tomuto problému může dojít v závislosti na logice klientské aplikace.

### <a name="solution"></a>Řešení
Zákazníci, kteří uvidí tento konkrétní problém při použití Tableau připojeného k vyhrazeným fondům SQL Azure synapse, by měly v připojení SQL nastavit FMTONLY na Ano. Pro Tableau Desktop a Tableau Server byste měli použít soubor ORS (Tableau data source Customization), abyste zajistili, že Tableau tento parametr předá do ovladače.  

> [!NOTE] 
> Společnost Microsoft neposkytuje podporu pro nástroje třetích stran. I když jsme otestovali, že toto řešení spolupracuje s Tableau desktopovým 2020.3.2, měli byste toto řešení použít na vlastní kapacitu.
>

* [Informace o tom, jak provést globální přizpůsobení pomocí souboru ORS na ploše Tableau, najdete v dokumentaci ke službě Tableau Desktop.](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [Informace o tom, jak provést globální přizpůsobení pomocí souboru ORS na serveru Tableau, najdete v tématu použití. Soubor ORS se serverem Tableau](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

Následující příklad ukazuje soubor Tableau ORS, který předá parametr FMTONLY = YES do připojovacího řetězce SQL:

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
Další podrobnosti o používání souborů ORS získáte od společnosti Tableau support. 

## <a name="see-also"></a>Viz také

* [Prvky jazyka T-SQL pro vyhrazený fond SQL ve službě Azure synapse Analytics.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-reference-tsql-language-elements?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Příkazy T-SQL podporované pro vyhrazený fond SQL ve službě Azure synapse Analytics.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements)
