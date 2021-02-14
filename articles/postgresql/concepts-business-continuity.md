---
title: Provozní kontinuita – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje provozní kontinuitu (obnovení včas v čase, výpadky datového centra, geografické obnovení a repliky) při použití Azure Database for PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/07/2020
ms.openlocfilehash: f8d8547c2d4900e6258f7360c50d8dfc17157832
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517240"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Přehled provozní kontinuity pomocí Azure Database for PostgreSQL-Single server

Tento přehled popisuje možnosti, které Azure Database for PostgreSQL poskytuje pro provozní kontinuitu a zotavení po havárii. Seznamte se s možnostmi pro zotavení z rušivých událostí, které by mohly způsobit ztrátu dat nebo způsobit nedostupnost databáze a aplikace. Přečtěte si, co dělat, když dojde k chybě uživatele nebo aplikace při vlivu na integritu dat, když dojde k výpadku oblasti Azure nebo když vaše aplikace vyžaduje údržbu.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkce, které můžete použít k zajištění kontinuity podnikových aplikací

Při vývoji plánu provozní kontinuity je potřeba pochopit maximální přijatelnou dobu, než aplikace plně obnoví po přerušení události – to je vaše plánovaná doba obnovení (RTO). Také je potřeba pochopit maximální množství nedávných aktualizací dat (časový interval), které může aplikace tolerovat při obnovování po přerušení události – to je váš cíl bodu obnovení (RPO).

Azure Database for PostgreSQL poskytuje funkce pro kontinuitu podnikových procesů, které zahrnují geograficky redundantní zálohy s možností iniciovat geografické obnovení a nasazení replik pro čtení v jiné oblasti. Každý má různé charakteristiky pro čas obnovení a možnou ztrátu dat. Díky funkci [geografického obnovení](concepts-backup.md) se vytvoří nový server pomocí zálohovaných dat, která se replikují z jiné oblasti. Celková doba potřebná k obnovení a obnovení závisí na velikosti databáze a množství protokolů, které se mají obnovit. Celková doba vytvoření serveru se liší od několika minut až po několik hodin. V případě [replik čtení](concepts-read-replicas.md)jsou protokoly transakcí z primární služby asynchronně přenášeny do repliky. V případě výpadku primární databáze z důvodu chyby na úrovni zóny nebo na úrovni oblasti, převzetí služeb při selhání do repliky poskytuje kratší RTO a omezenou ztrátu dat.

> [!NOTE]
> Prodleva mezi primárním serverem a replikou závisí na latenci mezi lokalitami, na množství dat, která se mají přenést, a nejdůležitějším způsobem na úlohy zápisu primárního serveru. Úlohy s velkým objemem zápisu můžou způsobit významné prodlevy. 
>
> Z důvodu asynchronního charakteru replikace, která se používá pro repliky pro čtení, **by se neměla** považovat za řešení vysoké dostupnosti (ha), protože vyšší prodlevy může znamenat vyšší RTO a RPO. V případě úloh, u kterých zůstává prodleva menší než v době špičky a mimo špičku úlohy, mohou repliky čtení fungovat jako alternativa HA. V opačném případě jsou repliky čtení určené pro vysoce škálovatelné úlohy a pro scénáře zotavení po havárii, které jsou pro vás připravené.

Následující tabulka porovnává RTO a RPO v **typickém scénáři úlohy** :

| **Funkce** | **Basic** | **Obecné použití** | **Optimalizované pro paměť** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Obnovení k určitému bodu v čase ze zálohy | Libovolný bod obnovení v rámci doby uchování <br/> RTO – různé <br/>RPO < 15 min| Libovolný bod obnovení v rámci doby uchování <br/> RTO – různé <br/>RPO < 15 min | Libovolný bod obnovení v rámci doby uchování <br/> RTO – různé <br/>RPO < 15 min |
| Geografické obnovení ze geograficky replikovaných záloh | Nepodporováno | RTO – různé <br/>RPO < 1 h | RTO – různé <br/>RPO < 1 h |
| Čtení replik | RTO – minuty * <br/>RPO < 5 min * | RTO – minuty * <br/>RPO < 5 min *| RTO – minuty * <br/>RPO < 5 min *|

 \* RTO a RPO **můžou být** v některých případech mnohem vyšší v závislosti na různých faktorech, mezi které patří latence mezi lokalitami, množství dat, která se mají přenést, a důležité úlohy zápisu do primární databáze. 

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
Repliky čtení pro různé oblasti můžete použít ke zvýšení provozní kontinuity a plánování zotavení po havárii. Repliky čtení jsou asynchronně aktualizované pomocí technologie fyzické replikace PostgreSQL a můžou se odčítat primárním serverem. Přečtěte si další informace o replikách pro čtení, dostupných oblastech a o tom, jak převzít služby při selhání v článku věnovaném [konceptům čtení replik](concepts-read-replicas.md). 

## <a name="faq"></a>Časté otázky
### <a name="where-does-azure-database-for-postgresql-store-customer-data"></a>Kam se Azure Database for PostgreSQL ukládají zákaznická data?
Ve výchozím nastavení Azure Database for PostgreSQL nepřesouvá nebo neukládají zákaznická data mimo oblast, ve které je nasazená. Zákazníci si ale můžou volitelně zvolit možnost povolit [geograficky redundantní zálohy](concepts-backup.md#backup-redundancy-options) nebo vytvořit [repliku pro čtení v různých oblastech](concepts-read-replicas.md#cross-region-replication) pro ukládání dat v jiné oblasti.


## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [automatizovaných zálohách v Azure Database for PostgreSQL](concepts-backup.md). 
- Naučte se, jak obnovit pomocí [Azure Portal](howto-restore-server-portal.md) nebo rozhraní příkazového [řádku Azure](howto-restore-server-cli.md).
- Přečtěte si o [replikách pro čtení v Azure Database for PostgreSQL](concepts-read-replicas.md).
