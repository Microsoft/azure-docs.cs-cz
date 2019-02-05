---
title: Azure SQL Data Warehouse Gen2 podporuje nižší výpočetní úrovně | Dokumentace Microsoftu
description: Azure SQL Data Warehouse Gen2 teď podporuje nižší výpočetní úrovně
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 12/03/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 22993395d60df23533b67ec48d0ea7f979d73e22
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692458"
---
# <a name="azure-sql-data-warehouse-gen2-support-for-lower-compute-tiers"></a>Podpora Azure SQL Data Warehouse Gen2 pro nižší výpočetní úrovně

Microsoft pomáhá snížit základní náklady na provozování schopná zpracovat vyhovovat i vašim náročným dotazy přidáním nižší výpočetní úrovně pro neuvěřitelně rychlá Azure SQL Data Warehouse Gen2 datového skladu. Zákazníci můžou prostředí funkcí pro výkon, flexibilitu a zabezpečení přední Azure SQL Data Warehouse je od 100 cDWU (jednotky datového skladu) a škálování na 30 000 cDWU během několika minut. Zákazníci mohou těžit z Gen2 výkon a flexibilitu s nižší výpočetní úrovně. 

Přetažením vstupní bod pro další generace datových skladů Microsoft otevře dveře řízené hodnotu zákazníkům, kteří chcete vyhodnotit všechny výhody zabezpečené, vysoce výkonné datového skladu bez opakovaně uhodnout zkušební prostředí, ve kterém je pro ně nejvhodnější.  Zákazníci budou moci spustit v rozsahu od 100 cDWU dolů z aktuální 500 cDWU vstupní bod.  SQL Data Warehouse Gen2 nadále podporují pozastavení a obnovení operací a jenom flexibilitu v výpočetní prostředky.  Gen2 také podporuje neomezené úložiště sloupce kapacita úložiště spolu s 2,5 × více paměti na jeden dotaz, až do 128 souběžných dotazů a adaptivní ukládání do mezipaměti přivedou prostředí průměrně 5 x větší výkon ve srovnání s stejné jednotka datového skladu na funkce Gen1 za stejnou cenu.  Geograficky redundantní zálohy jsou standardní pro Gen2 integrované zaručené ochranu. Azure SQL Data Warehouse Gen2 je připravené ke škálování po.

Momentálně na portálu nepodporuje nasazení nebo škálování instance Gen2 nižší úrovně. Intenzivně pracujeme na povolení této funkce mezitím prosím [odeslat lístek](sql-data-warehouse-get-started-create-support-ticket.md) Pokud chcete využít výhod této nové úrovni.

## <a name="getting-started-with-azure-sql-data-warehouse-gen2"></a>Začínáme s Azure SQL Data Warehouse Gen2 

Zákazníci si mohou vybrat k nasazení nové instance Gen2 nebo upgradovat existující instanci skladu dat Gen1 užít si flexibilitu a výkon další generace datových skladů. 

Zkuste [Azure SQL Data Warehouse – Compute optimalizované Gen2 vrstvy.](https://azure.microsoft.com/services/sql-data-warehouse/?v=17.44)
Upgrade [Azure SQL Data Warehouse – Compute optimalizované Gen1 na Gen2](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation) sledování Azure SQL Data Warehouse Gen2 v akci v tomto [video Microsoft Mechanics.](https://www.youtube.com/watch?v=Ap8I3UZonzI&feature=youtu.be)


## <a name="supported-regions-for-lower-compute-tiers"></a>Podporované oblasti pro nižší výpočetní úrovně

- Východní US1 
- USA – východ 2
- Západní Evropa
- Severní Evropa
- Západní USA 2
- Jihovýchodní Asie
- Střed USA – jih
- USA – střed 
- Východní Asie
- Japonsko – východ
- Indie – střed
- Austrálie – východ
- Austrálie – jihovýchod
- Kanada – střed
- Japonsko – západ 
- Kanada – střed

## <a name="next-steps"></a>Další postup

- [Další informace](upgrade-to-latest-generation.md) o – Compute optimalizované výkonu díky upgradu SQL Data Warehouse. 
