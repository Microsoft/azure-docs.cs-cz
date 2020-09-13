---
title: Provozní kontinuita – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje provozní kontinuitu (obnovení včas v čase, výpadky datového centra, geografické obnovení a repliky) při použití Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/07/2020
ms.openlocfilehash: 75cd86bd1587a9294caef00efdf973fe8a26c150
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89612011"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Přehled provozní kontinuity pomocí Azure Database for PostgreSQL-Single server

Tento přehled popisuje možnosti, které Azure Database for PostgreSQL poskytuje pro provozní kontinuitu a zotavení po havárii. Seznamte se s možnostmi pro zotavení z rušivých událostí, které by mohly způsobit ztrátu dat nebo způsobit nedostupnost databáze a aplikace. Přečtěte si, co dělat, když dojde k chybě uživatele nebo aplikace při vlivu na integritu dat, když dojde k výpadku oblasti Azure nebo když vaše aplikace vyžaduje údržbu.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkce, které můžete použít k zajištění kontinuity podnikových aplikací

Azure Database for PostgreSQL poskytuje funkce pro provozní kontinuitu, které zahrnují automatizované zálohování a schopnost uživatelů zahájit geografické obnovení. Každý má různé charakteristiky pro odhadovanou dobu obnovení (ERT) a potenciální ztrátu dat. Odhadovaná doba obnovení (ERT) je předpokládaná doba, po kterou bude databáze plně funkční po žádosti o obnovení nebo převzetí služeb při selhání. Jakmile tyto možnosti pochopíte, můžete si je vybrat mezi nimi a použít je společně pro různé scénáře. Při vývoji plánu provozní kontinuity je potřeba pochopit maximální přijatelnou dobu, než aplikace plně obnoví po přerušení události – to je vaše plánovaná doba obnovení (RTO). Také je potřeba pochopit maximální množství nedávných aktualizací dat (časový interval), které může aplikace tolerovat při obnovování po přerušení události – to je váš cíl bodu obnovení (RPO).

Následující tabulka porovnává ERT a RPO pro dostupné funkce:

| **Funkce** | **Basic** | **Pro obecné účely** | **Optimalizované pro paměť** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Obnovení k určitému bodu v čase ze zálohy | Libovolný bod obnovení v rámci doby uchování | Libovolný bod obnovení v rámci doby uchování | Libovolný bod obnovení v rámci doby uchování |
| Geografické obnovení ze geograficky replikovaných záloh | Nepodporováno | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

Můžete také zvážit použití [replik pro čtení](concepts-read-replicas.md).

## <a name="recover-a-server-after-a-user-or-application-error"></a>Obnovení serveru po chybě uživatele nebo aplikace

Zálohy služby můžete použít k obnovení serveru z různých rušivých událostí. Uživatel může omylem odstranit některá data, neúmyslně odstranit důležitou tabulku nebo dokonce odstranit celou databázi. Aplikace může omylem přepsat dobrá data s chybnými daty z důvodu vady aplikace atd.

Můžete provést obnovení k určitému **bodu v čase** a vytvořit tak kopii serveru pro známý dobrý bod v čase. Tento bod v čase musí být v období uchovávání záloh, které jste nakonfigurovali pro váš server. Po obnovení dat na novém serveru můžete původní server nahradit novým obnoveným serverem nebo zkopírovat potřebná data z obnoveného serveru na původní server.

> [!IMPORTANT]
> Odstraněné servery **nelze** obnovit. Pokud server odstraníte, odstraní se i všechny databáze patřící do serveru a nebude možné je obnovit. Pomocí [zámku prostředků Azure](../azure-resource-manager/management/lock-resources.md) můžete zabránit nechtěnému odstranění serveru.

## <a name="recover-from-an-azure-data-center-outage"></a>Zotavení z výpadku datového centra Azure

Přestože je taková situace výjimečná, i u datového centra Azure může dojít k výpadku. Pokud dojde k výpadku, způsobí to narušení podniku, které může trvat jenom několik minut, ale může to trvat i hodinu.

Jednou z možností je počkat, až se váš server vrátí zpátky do režimu online, když dojde k výpadku datového centra. To funguje u aplikací, které můžou umožnit, aby byl server v určitou dobu offline, například vývojové prostředí. Když dojde k výpadku datového centra, nevíte, jak dlouho může výpadek trvat, takže tato možnost funguje jenom v případě, že server ještě nepotřebujete.

## <a name="geo-restore"></a>Geografické obnovení

Funkce geografického obnovení obnoví Server pomocí geograficky redundantních záloh. Zálohy se hostují v [spárované oblasti](../best-practices-availability-paired-regions.md)vašeho serveru. Z těchto záloh můžete tyto zálohy obnovit do jakékoli jiné oblasti. Geografické obnovení vytvoří nový server s daty ze zálohy. Další informace o geografickém obnovení najdete v [článku o principech zálohování a obnovení](concepts-backup.md).

> [!IMPORTANT]
> Geografické obnovení je možné pouze v případě, že jste zřídili Server s geograficky redundantním úložištěm záloh. Pokud chcete přepnout z místně redundantního na geograficky redundantní zálohy pro existující server, musíte použít výpis pg_dump stávajícího serveru a obnovit ho na nově vytvořený server nakonfigurovaný pomocí geograficky redundantních záloh.

## <a name="cross-region-read-replicas"></a>Repliky čtení mezi oblastmi
Repliky čtení pro různé oblasti můžete použít ke zvýšení provozní kontinuity a plánování zotavení po havárii. Repliky čtení jsou asynchronně aktualizované pomocí technologie fyzické replikace PostgreSQL. Přečtěte si další informace o replikách pro čtení, dostupných oblastech a o tom, jak převzít služby při selhání v článku věnovaném [konceptům čtení replik](concepts-read-replicas.md). 

## <a name="faq"></a>Nejčastější dotazy
### <a name="where-does-azure-database-for-postgresql-store-customer-data"></a>Kam se Azure Database for PostgreSQL ukládají zákaznická data?
Ve výchozím nastavení Azure Database for PostgreSQL nepřesouvá nebo neukládají zákaznická data mimo oblast, ve které je nasazená. Zákazníci si ale můžou volitelně zvolit možnost povolit [geograficky redundantní zálohy](concepts-backup.md#backup-redundancy-options) nebo vytvořit [repliku pro čtení v různých oblastech](concepts-read-replicas.md#cross-region-replication) pro ukládání dat v jiné oblasti.


## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [automatizovaných zálohách v Azure Database for PostgreSQL](concepts-backup.md). 
- Naučte se, jak obnovit pomocí [Azure Portal](howto-restore-server-portal.md) nebo rozhraní příkazového [řádku Azure](howto-restore-server-cli.md).
- Přečtěte si o [replikách pro čtení v Azure Database for PostgreSQL](concepts-read-replicas.md).
