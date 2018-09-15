---
title: Omezení ve službě Azure Database for PostgreSQL
description: Tento článek popisuje omezení ve službě Azure Database for PostgreSQL, jako je třeba počet připojení a možnosti úložiště modul.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/30/2018
ms.openlocfilehash: e096a67d953860ec2e9601f25dbd9ca92a8fb4a3
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45633773"
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Omezení ve službě Azure Database for PostgreSQL
Následující části popisují kapacitu a limity funkční ve službě database.

## <a name="maximum-connections"></a>Maximální počet připojení
Maximální počet připojení na cenová úroveň a virtuálními jádry jsou následující: 

|**Cenová úroveň**| **počet virtuálních jader:**| **Maximální počet připojení** |
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

Při připojení překročí limit, může se zobrazit následující chyba:
> Závažná chyba: je nám líto, už příliš mnoho klientů

Systému Azure vyžaduje pět připojení ke sledování serveru Azure Database for PostgreSQL. 

## <a name="functional-limitations"></a>Funkční omezení
### <a name="scale-operations"></a>Operace škálování
- Dynamické škálování do a ze základní cenové úrovně se aktuálně nepodporuje.
- Snížení velikosti úložiště serveru se aktuálně nepodporuje.

### <a name="server-version-upgrades"></a>Upgrady verze serveru
- Automatizovaný přenos mezi verzí vyhledávacích strojů hlavní databáze se aktuálně nepodporuje. Pokud chcete upgradovat na novou hlavní verzi, [výpisu a obnovení](./howto-migrate-using-dump-and-restore.md) ho na server, který byl vytvořen v nové verzi modulu.

### <a name="subscription-management"></a>Správa předplatného
- Dynamicky přesunu serverů napříč skupiny prostředků a předplatná se aktuálně nepodporuje.

### <a name="vnet-service-endpoints"></a>Koncové body služby virtuální sítě
- Podpora pro koncové body služby virtuální sítě je pouze pro servery pro obecné účely a optimalizovaný pro paměť.

### <a name="restoring-a-server"></a>Obnovení serveru
- Při použití funkce PITR se vytvoří nový server se stejnou cenovou úroveň konfiguraci jako server, který je založen na.
- Nový server vytvořené během obnovení nemá pravidla brány firewall, které existovaly na původním serveru. Pravidla brány firewall musí nastavit zvlášť pro tento nový server.
- Obnovení odstraněné serveru není podporováno.

## <a name="next-steps"></a>Další postup
- Vysvětlení [co je k dispozici v jednotlivých cenových úrovní](concepts-pricing-tiers.md)
- Další informace o [podporované verze databáze PostgreSQL](concepts-supported-versions.md)
- Kontrola [jak zálohovat a obnovovat server ve službě Azure Database for PostgreSQL pomocí webu Azure portal](howto-restore-server-portal.md)
