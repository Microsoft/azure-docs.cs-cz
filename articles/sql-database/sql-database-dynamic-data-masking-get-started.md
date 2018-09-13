---
title: Maskování dynamických dat Azure SQL Database | Dokumentace Microsoftu
description: Maskování dynamických dat SQL Database omezuje riziko ohrožení citlivých dat jejich maskováním pro neprivilegované uživatele
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: ronitr
ms.openlocfilehash: c4d9eb5f7105e0e1b16ad994d397885b9232a0ea
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719524"
---
# <a name="sql-database-dynamic-data-masking"></a>Maskování dynamických dat SQL Database

Maskování dynamických dat SQL Database omezuje riziko ohrožení citlivých dat pomocí jejich maskování pro neoprávněné uživatele. 

Dynamické maskování dat pomáhá předcházet neoprávněnému přístupu k citlivým datům tím, že uživatelům umožňuje určit, kolik citlivých dat se může odhalit, aby to mělo minimální dopad na aplikační vrstvu. Je to funkce zabezpečení založená na zásadách, která skrývá citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění.

Například pracovníkem služeb v centru volání může identifikovat volající několik číslice čísla platební karty, ale tyto datové položky by neměly být vystaveny plně služeb zákazníkům. Pravidlo maskování může být definován, že všechny masky ale poslední čtyři číslice jakékoli číslo platební karty ve výsledku nastavit libovolného dotazu. Další příklad lze masku příslušná data definovat, chránit data identifikovatelné osobní údaje (PII), aby vývojář mohl zadávat dotazy produkčních prostředí pro účely odstraňování potíží, aniž by byla porušena dodržování předpisů.

## <a name="sql-database-dynamic-data-masking-basics"></a>SQL Database základy maskování dynamických dat
Nastavení dynamické maskování zásady na portálu Azure portal tak, že vyberete maskování operace v okně konfigurace SQL Database nebo okno nastavení dynamických dat dat.

### <a name="dynamic-data-masking-permissions"></a>Oprávnění maskování dynamických dat
Dynamické maskování dat je možné nakonfigurovat správce databáze Azure, správce serveru nebo oddělení rolí zabezpečení.

### <a name="dynamic-data-masking-policy"></a>Zásady maskování dynamických dat
* **Uživatelé SQL vyloučení z maskování** – A nastavení uživatelů SQL nebo výsledky dotazu identity AAD, které získávají odmaskovaná data v SQL. Uživatelé s oprávněními správce jsou vždycky vyloučení z maskování a zobrazit původní data bez jakékoli masky.
* **Pravidla maskování** -sadu pravidel, která definují pole určené k maskována a, který se používá funkce maskování. Určené pole lze definovat pomocí název schématu databáze, název tabulky a název sloupce.
* **Funkce maskování** -sadu metod, které řídí možnost vidět data pro různé scénáře.

| Funkce maskování | Logika maskování |
| --- | --- |
| **Výchozí** |**Úplné maskování podle datové typy určené pole**<br/><br/>• Použijte XXXX nebo méně Xs, pokud je velikost pole menší než 4 znaky pro datové typy řetězec (nchar, ntext, nvarchar).<br/>• Využijte nulovou hodnotu pro číselné datové typy (bigint, bit, decimal, int, peníze, číselné, smallint, smallmoney, tinyint, float, reálném).<br/>• Využijte 01-01-1900 pro datové typy data a času (datum, datetime2, datetime, datetimeoffset, smalldatetime, čas).<br/>• Pro variantu jazyka SQL, výchozí hodnota aktuální typ se používá.<br/>• Pro formát XML dokumentu <masked/> se používá.<br/>• Použijte prázdnou hodnotu pro speciální datové typy (časové razítko tabulky, hierarchyid, GUID, binary, image, varbinary prostorové typy). |
| **Platební karty** |**Maskování metodu, která zveřejňuje poslední čtyři číslice určené pole** a přidá konstantní řetězec jako předponu ve formě platební kartu.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |**Maskování metodu, která zveřejňuje první písmeno jednotky a domény nahradí XXX.com** pomocí předpony konstantní řetězec ve formě e-mailovou adresu.<br/><br/>aXX@XXXX.com |
| **Náhodné číslo** |**Maskování metodu, která generuje náhodné číslo** podle vybrané hranice a skutečné datové typy. Pokud určené oblasti jsou stejné funkce maskování je číslo, konstantní.<br/><br/>![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Vlastní text** |**Metoda, která zpřístupňuje první a poslední znak maskování** a přidá řetězec vlastní odsazení uprostřed. Pokud původní řetězec je kratší než vystavená Předpona a přípona, použije se pouze řetězec odsazení. <br/>prefix[padding]suffix<br/><br/>![Navigační podokno](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Doporučená pole k maskování
Modul recommendations DDM označí jako potenciálně citlivých pole, které mohou být vhodnými kandidáty pro maskování určitá pole z databáze. V okně dynamického maskování dat na portálu zobrazí se doporučené sloupce pro vaši databázi. Je všechno, co potřebujete udělat, klikněte na tlačítko **přidat masku** pro jeden nebo více sloupců a pak **Uložit** použít maska pro tato pole.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Nastavení dynamického maskování pro vaši databázi pomocí rutin prostředí Powershell dat
Zobrazit [rutiny služby Azure SQL Database](https://docs.microsoft.com/powershell/module/azurerm.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Nastavení dynamického maskování dat pro vaši databázi pomocí rozhraní REST API
Zobrazit [operací pro službu Azure SQL Database](https://msdn.microsoft.com/library/dn505719.aspx).

