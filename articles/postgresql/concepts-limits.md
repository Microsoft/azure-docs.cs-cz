---
title: Omezení v Azure databázi PostgreSQL
description: Tento článek popisuje omezení v Azure databázi PostgreSQL, jako je například počet připojení a možnosti úložiště modul.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 7e06cdba7c9c9f7e5c1d621e7421a18c342c0fdb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Omezení v Azure databázi PostgreSQL
Následující části popisují kapacitu a funkční omezení ve službě databáze.

## <a name="pricing-tier-maximums"></a>Maximální hodnoty cenové úrovně
Azure databáze PostgreSQL má více cenové úrovně, které lze vybírat při vytváření serveru. Další informace najdete v tématu [cenové úrovně v databázi Azure pro PostgreSQL](concepts-pricing-tiers.md).  

Takto je maximální počet připojení, výpočetní jednotky a úložiště v jednotlivých cenových úrovní: 

|Cenová úroveň| Výpočetní generování| vCore(s)| Maximální počet připojení |
|---|---|---|---|
|Basic| Gen 4| 1| 50 |
|Basic| Gen 4| 2| 100 |
|Basic| Gen 5| 1| 50 |
|Basic| Gen 5| 2| 100 |
|Obecné použití| Gen 4| 2| 150|
|Obecné použití| Gen 4| 4| 250|
|Obecné použití| Gen 4| 8| 480|
|Obecné použití| Gen 4| 16| 950|
|Obecné použití| Gen 4| 32| 1 500|
|Obecné použití| Gen 5| 2| 150|
|Obecné použití| Gen 5| 4| 250|
|Obecné použití| Gen 5| 8| 480|
|Obecné použití| Gen 5| 16| 950|
|Obecné použití| Gen 5| 32| 1 500|
|Paměťově optimalizované| Gen 5| 2| 150|
|Paměťově optimalizované| Gen 5| 4| 250|
|Paměťově optimalizované| Gen 5| 8| 480|
|Paměťově optimalizované| Gen 5| 16| 950|

Při připojení přesahuje limit, může se zobrazit chybová zpráva:
> Závažná chyba: bohužel již příliš mnoho klientů

Azure systém vyžaduje pět připojení k Azure databáze PostgreSQL serveru. 

## <a name="functional-limitations"></a>Funkční omezení
### <a name="scale-operations"></a>Operace škálování
1.  Dynamické škálování serverů napříč cenové úrovně se aktuálně nepodporuje. To znamená přepínání mezi základní ověřování, obecné účely nebo k paměťově optimalizovaným vrstev.
2.  Zmenšuje velikost úložiště serveru není aktuálně podporováno.

### <a name="server-version-upgrades"></a>Upgrady verze serveru
- Automatické migrace mezi verzemi modul hlavní databáze není aktuálně podporována.

### <a name="subscription-management"></a>Správa předplatného
- Dynamicky přesunutí serverů mezi skupiny prostředků a předplatná se aktuálně nepodporuje.

### <a name="point-in-time-restore-pitr"></a>Bod v čas – obnovení (Možnosti PITR)
1.  Když používáte funkci Možnosti PITR, se vytvoří nový server s stejné konfigurace jako u serveru, který je založen na.
2.  Obnovení odstraněného server není podporováno.

## <a name="next-steps"></a>Další postup
- Pochopení [co je k dispozici v jednotlivých cenových úrovní](concepts-pricing-tiers.md)
- Další informace o [podporované verze databáze PostgreSQL](concepts-supported-versions.md)
- Zkontrolujte [postup zálohování a obnovení serveru v databázi Azure pro PostgreSQL pomocí portálu Azure](howto-restore-server-portal.md)
