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
ms.date: 06/04/2018
ms.openlocfilehash: 5cd829236d8d8a58e68f7bf766790aa3f0cb656e
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757412"
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Omezení v Azure databázi PostgreSQL
Následující části popisují kapacitu a funkční omezení ve službě databáze.

## <a name="maximum-connections"></a>Maximální počet připojení
Maximální počet připojení za cenová úroveň a vCores jsou následující: 

|**Cenová úroveň**| **vCore(s)**| **Maximální počet připojení** |
|---|---|---|
|Basic| 1| 50 |
|Basic| 2| 100 |
|Obecné použití| 2| 150|
|Obecné použití| 4| 250|
|Obecné použití| 8| 480|
|Obecné použití| 16| 950|
|Obecné použití| 32| 1 500|
|Paměťově optimalizované| 2| 150|
|Paměťově optimalizované| 4| 250|
|Paměťově optimalizované| 8| 480|
|Paměťově optimalizované| 16| 950|

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
