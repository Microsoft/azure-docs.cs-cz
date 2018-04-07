---
title: Maskování dynamická data Azure SQL Database | Microsoft docs
description: Maskování dynamická data SQL Database omezuje zranitelnost citlivá data pomocí maskování uživatelům bez oprávnění
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 04/01/2018
ms.author: ronitr
ms.openlocfilehash: 4d948786d70ffc1350825b04dbddc128cf89466e
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="sql-database-dynamic-data-masking"></a>Maskování dynamická data databáze SQL

Databáze SQL dynamická data maskování omezuje ohrožení citlivých dat pomocí maskování uživatelům bez oprávnění. 

Dynamické maskování dat pomáhá předcházet neoprávněnému přístupu k citlivým datům tím, že uživatelům umožňuje určit, kolik citlivých dat se může odhalit, aby to mělo minimální dopad na aplikační vrstvu. Je to funkce zabezpečení založená na zásadách, která skrývá citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění.

Například na zástupce služby v telefonickém centru může identifikovat volající podle několik číslic jejich číslo platební karty, ale tyto datové položky by neměly být vystaveny plně služeb zákazníkům. Všechny masky ale poslední čtyři číslice jakékoli číslo platební karty ve výsledku sadu jakýkoli dotaz, může být definováno pravidlo maskování. Například může být definováno masku příslušná data k ochraně dat identifikovatelné osobní údaje (PII), tak, aby vývojář může dotazovat provozní prostředí pro účely odstraňování potíží, aniž bychom při tom porušili předpisy.

## <a name="sql-database-dynamic-data-masking-basics"></a>Dynamické maskování základy dat databáze SQL
Nastavit dynamické maskování zásada na portálu Azure tak, že vyberete dynamické maskování operace v okně Konfigurace databázi SQL nebo okno nastavení dat dat.

### <a name="dynamic-data-masking-permissions"></a>Dynamická data maskování oprávnění
Dynamická data maskování dá nakonfigurovat pomocí Správce Azure databáze, správce serveru nebo úředník role zabezpečení.

### <a name="dynamic-data-masking-policy"></a>Dynamická data maskování zásad
* **Uživatelé SQL vyloučení z maskování** – A sada uživatelů SQL nebo AAD identity, které získat odmaskovaná data v SQL výsledky dotazu. Uživatelé s oprávněními správce jsou vždycky vyloučení z maskování a zobrazit původní data bez jakékoli masky.
* **Maskování pravidla** -sadu pravidla, která definují určené pole k maskování a funkci maskování, který se používá. Určené pole lze definovat pomocí název schématu databáze, název tabulky a název sloupce.
* **Funkce maskování** -sadu metod, které řídí úniku dat pro různé scénáře.

| Funkci maskování | Maskování logiky |
| --- | --- |
| **Výchozí** |**Úplné maskování podle datové typy určené polí**<br/><br/>• Použijte XXXX nebo méně Xs, pokud je velikost pole menší než 4 znaky pro datové typy řetězec (ntext nchar, nvarchar).<br/>• Použijte hodnotu 0 pro číselné datové typy (bigint, bit, decimal, int, peníze, číselné, smallint, smallmoney, tinyint, float, reálné).<br/>• Použijte 01-01-1900 pro datum a čas datové typy (datum, datetime2, datetime, datetimeoffset, smalldatetime, čas).<br/>• Pro SQL variant, výchozí hodnota má aktuální typ se používá.<br/>• Pro formát XML dokumentu <masked/> se používá.<br/>• Použijte prázdnou hodnotu pro speciální typy dat. (časové razítko tabulky, hierarchyid, identifikátor GUID, binární, image, varbinary prostorové typy). |
| **Platební karty** |**Maskování metoda, která zpřístupňuje poslední čtyři číslice určené polí** a přidá konstantní řetězec jako předpona ve formě platební karty.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |**Maskování metoda, která zveřejňuje první písmeno a nahradí domény XXX.com** pomocí předpony konstantní řetězec ve formátu e-mailovou adresu.<br/><br/>aXX@XXXX.com |
| **Náhodné číslo** |**Maskování metodu, která generuje náhodné číslo** podle vybrané hranice a skutečný datové typy. Pokud určené oblasti jsou stejné, je funkce maskování konstantní číslo.<br/><br/>![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Vlastní text** |**Maskování metoda, která zpřístupňuje první a poslední znak** a přidá řetězec vlastní odsazení uprostřed. Pokud původní řetězec je kratší než zveřejněné předponu a příponu, pouze odsazení řetězec se používá. <br/>prefix[padding]suffix<br/><br/>![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Doporučená pole k maskování
Modul doporučení DDM příznaky určitá pole z databáze jako potenciálně citlivých pole, které můžou být vhodnými kandidáty pro maskování. V okně dynamického maskování dat na portálu se zobrazí doporučená sloupce pro vaši databázi. Všechny musíte udělat je, klikněte na tlačítko **přidat masku** pro jeden nebo více sloupců a potom **Uložit** použít masku pro tato pole.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Nastavit dynamické maskování dat pro vaši databázi pomocí rutin prostředí Powershell
V tématu [rutiny databáze Azure SQL](https://msdn.microsoft.com/library/azure/mt574084.aspx).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Nastavit maskování dynamická data pro vaši databázi pomocí rozhraní REST API
V tématu [operací pro databáze Azure SQL](https://msdn.microsoft.com/library/dn505719.aspx).

