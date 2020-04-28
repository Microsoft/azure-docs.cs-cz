---
title: Provozní kontinuita – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje provozní kontinuitu (obnovení včas v čase, výpadek datového centra, geografické obnovení) při použití Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: afa03399933bdc8bd8ff869125955cfd9e0abecb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75981920"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Přehled provozní kontinuity pomocí Azure Database for PostgreSQL-Single server

Tento přehled popisuje možnosti, které Azure Database for PostgreSQL poskytuje pro provozní kontinuitu a zotavení po havárii. Seznamte se s možnostmi pro zotavení z rušivých událostí, které by mohly způsobit ztrátu dat nebo způsobit nedostupnost databáze a aplikace. Přečtěte si, co dělat, když dojde k chybě uživatele nebo aplikace při vlivu na integritu dat, když dojde k výpadku oblasti Azure nebo když vaše aplikace vyžaduje údržbu.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkce, které můžete použít k zajištění kontinuity podnikových aplikací

Azure Database for PostgreSQL poskytuje funkce pro provozní kontinuitu, které zahrnují automatizované zálohování a schopnost uživatelů zahájit geografické obnovení. Každý má různé charakteristiky pro odhadovanou dobu obnovení (ERT) a potenciální ztrátu dat. Jakmile tyto možnosti pochopíte, můžete si je vybrat mezi nimi a použít je společně pro různé scénáře. Při vývoji plánu provozní kontinuity je potřeba pochopit maximální přijatelnou dobu, než aplikace plně obnoví po přerušení události – to je vaše plánovaná doba obnovení (RTO). Také je potřeba pochopit maximální množství nedávných aktualizací dat (časový interval), které může aplikace tolerovat při obnovování po přerušení události – to je váš cíl bodu obnovení (RPO).

Následující tabulka porovnává ERT a RPO pro dostupné funkce:

| **Schopnost** | **Základní** | **Pro obecné účely** | **Optimalizované z hlediska paměti** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Obnovení k určitému bodu v čase ze zálohy | Libovolný bod obnovení v rámci doby uchování | Libovolný bod obnovení v rámci doby uchování | Libovolný bod obnovení v rámci doby uchování |
| Geografické obnovení ze geograficky replikovaných záloh | Nepodporuje se | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Odstraněné servery **nelze** obnovit. Pokud server odstraníte, odstraní se i všechny databáze patřící do serveru a nebude možné je obnovit. Pomocí [zámku prostředků Azure](../azure-resource-manager/management/lock-resources.md) můžete zabránit nechtěnému odstranění serveru.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Obnovení serveru po chybě uživatele nebo aplikace

Zálohy služby můžete použít k obnovení serveru z různých rušivých událostí. Uživatel může omylem odstranit některá data, neúmyslně odstranit důležitou tabulku nebo dokonce odstranit celou databázi. Aplikace může omylem přepsat dobrá data s chybnými daty z důvodu vady aplikace atd.

Můžete provést obnovení k určitému **bodu v čase** a vytvořit tak kopii serveru pro známý dobrý bod v čase. Tento bod v čase musí být v období uchovávání záloh, které jste nakonfigurovali pro váš server. Po obnovení dat na novém serveru můžete původní server nahradit novým obnoveným serverem nebo zkopírovat potřebná data z obnoveného serveru na původní server.

## <a name="recover-from-an-azure-data-center-outage"></a>Zotavení z výpadku datového centra Azure

Přestože je taková situace výjimečná, i u datového centra Azure může dojít k výpadku. Pokud dojde k výpadku, způsobí to narušení podniku, které může trvat jenom několik minut, ale může to trvat i hodinu.

Jednou z možností je počkat, až se váš server vrátí zpátky do režimu online, když dojde k výpadku datového centra. To funguje u aplikací, které můžou umožnit, aby byl server v určitou dobu offline, například vývojové prostředí. Když dojde k výpadku datového centra, nevíte, jak dlouho může výpadek trvat, takže tato možnost funguje jenom v případě, že server ještě nepotřebujete.

## <a name="geo-restore"></a>Geografické obnovení

Funkce geografického obnovení obnoví Server pomocí geograficky redundantních záloh. Zálohy se hostují v [spárované oblasti](../best-practices-availability-paired-regions.md)vašeho serveru. Z těchto záloh můžete tyto zálohy obnovit do jakékoli jiné oblasti. Geografické obnovení vytvoří nový server s daty ze zálohy. Další informace o geografickém obnovení najdete v [článku o principech zálohování a obnovení](concepts-backup.md).

> [!IMPORTANT]
> Geografické obnovení je možné pouze v případě, že jste zřídili Server s geograficky redundantním úložištěm záloh. Pokud chcete přepnout z místně redundantního na geograficky redundantní zálohy pro existující server, musíte použít výpis pg_dump stávajícího serveru a obnovit ho na nově vytvořený server nakonfigurovaný pomocí geograficky redundantních záloh.

## <a name="cross-region-read-replicas"></a>Repliky čtení mezi oblastmi
Repliky čtení pro různé oblasti můžete použít ke zvýšení provozní kontinuity a plánování zotavení po havárii. Repliky čtení jsou asynchronně aktualizované pomocí technologie fyzické replikace PostgreSQL. Přečtěte si další informace o replikách pro čtení, dostupných oblastech a o tom, jak převzít služby při selhání v článku věnovaném [konceptům čtení replik](concepts-read-replicas.md). 

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [automatizovaných zálohách v Azure Database for PostgreSQL](concepts-backup.md). 
- Naučte se, jak obnovit pomocí [Azure Portal](howto-restore-server-portal.md) nebo rozhraní příkazového [řádku Azure](howto-restore-server-cli.md).
- Přečtěte si o [replikách pro čtení v Azure Database for PostgreSQL](concepts-read-replicas.md).