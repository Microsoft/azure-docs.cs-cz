---
title: Kontinuita podnikání – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje kontinuitu podnikání (obnovení bodu v čase, výpadek datového centra, geografické obnovení) při použití Azure Database pro PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: afa03399933bdc8bd8ff869125955cfd9e0abecb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981920"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Přehled kontinuity podnikání s databází Azure pro PostgreSQL – jeden server

Tento přehled popisuje funkce, které Azure Database pro PostgreSQL poskytuje pro kontinuitu podnikání a zotavení po havárii. Další informace o možnostech obnovení z rušivých událostí, které by mohly způsobit ztrátu dat nebo způsobit, že databáze a aplikace nebudou k dispozici. Zjistěte, co dělat, když chyba uživatele nebo aplikace ovlivňuje integritu dat, oblast Azure má výpadek nebo vaše aplikace vyžaduje údržbu.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkce, které můžete použít k zajištění kontinuity provozu

Azure Database for PostgreSQL poskytuje funkce kontinuity podnikání, které zahrnují automatické zálohování a možnost pro uživatele zahájit geografické obnovení. Každý z nich má různé charakteristiky pro odhadovaný čas obnovení (ERT) a potenciální ztrátu dat. Jakmile tyto možnosti pochopíte, můžete si mezi nimi vybrat a použít je společně pro různé scénáře. Při vývoji plánu kontinuity provozu je třeba pochopit maximální přijatelnou dobu, než se aplikace plně zotaví po rušivé události – toto je váš cíl doby obnovení (RTO). Musíte také pochopit maximální množství nedávných aktualizací dat (časový interval), které aplikace může tolerovat ztrátu při obnovení po rušivé události - to je váš cíl bodu obnovení (RPO).

Následující tabulka porovnává ert a rpo pro dostupné funkce:

| **Schopnost** | **Basic** | **Obecný účel** | **Optimalizované z hlediska paměti** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Obnovení k určitému bodu v čase ze zálohy | Jakýkoli bod obnovení během retenční ho období | Jakýkoli bod obnovení během retenční ho období | Jakýkoli bod obnovení během retenční ho období |
| Geografické obnovení z geograficky replikovaných záloh | Nepodporuje se | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Odstraněné servery **nelze** obnovit. Pokud odstraníte server, budou odstraněny také všechny databáze, které patří k serveru, a nelze je obnovit. Pomocí [zámku prostředků Azure](../azure-resource-manager/management/lock-resources.md) můžete zabránit nechtěnému odstranění serveru.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Obnovení serveru po chybě uživatele nebo aplikace

Zálohy služby můžete použít k obnovení serveru z různých rušivých událostí. Uživatel může omylem odstranit některá data, nechtěně vynechat důležitou tabulku nebo dokonce přetažení celé databáze. Aplikace může omylem přepsat dobrá data se špatnými daty z důvodu vady aplikace a tak dále.

Můžete provést **bod-in-time-restore** a vytvořit kopii serveru do známého dobrého bodu v čase. Tento bod v čase musí být v rámci doby uchování zálohy, které jste nakonfigurovali pro server. Po obnovení dat na nový server můžete buď nahradit původní server nově obnoveným serverem, nebo zkopírovat potřebná data z obnoveného serveru na původní server.

## <a name="recover-from-an-azure-data-center-outage"></a>Obnovení výpadku datového centra Azure

Přestože je taková situace výjimečná, i u datového centra Azure může dojít k výpadku. Dojde-li k výpadku, způsobí narušení podnikání, které může trvat pouze několik minut, ale může trvat několik hodin.

Jednou z možností je počkat, až se server vrátí do režimu online, až skončí výpadek datového centra. To funguje pro aplikace, které si mohou dovolit mít server offline po určitou dobu, například vývojové prostředí. Pokud má datové centrum výpadek, nevíte, jak dlouho může výpadek trvat, takže tato možnost funguje pouze v případě, že server nějakou dobu nepotřebujete.

## <a name="geo-restore"></a>Geografické obnovení

Funkce geografického obnovení obnoví server pomocí geograficky redundantních záloh. Zálohy jsou hostovány ve [spárované oblasti](../best-practices-availability-paired-regions.md)serveru . Z těchto záloh můžete obnovit do jakékoli jiné oblasti. Geografické obnovení vytvoří nový server s daty ze záloh. Další informace o geografickém obnovení v [článku o konceptech zálohování a obnovení](concepts-backup.md).

> [!IMPORTANT]
> Geografické obnovení je možné pouze v případě, že jste zřídit server s geograficky redundantní úložiště záloh. Pokud chcete přepnout z místně redundantní zálohy pro existující server, je nutné provést výpis pomocí pg_dump existujícího serveru a obnovit jej na nově vytvořený server nakonfigurovaný s geograficky redundantní zálohy.

## <a name="cross-region-read-replicas"></a>Repliky pro čtení mezi oblastmi
Pomocí replik pro čtení napříč oblastmi můžete zlepšit kontinuitu provozu a plánování zotavení po havárii. Repliky čtení jsou aktualizovány asynchronně pomocí technologie fyzické replikace PostgreSQL. Další informace o čtení repliky, dostupné oblasti a jak převzetí služeb při selhání z [článku koncepty replik čtení](concepts-read-replicas.md). 

## <a name="next-steps"></a>Další kroky
- Další informace o [automatických zálohách v Azure Database for PostgreSQL](concepts-backup.md). 
- Přečtěte si, jak obnovit pomocí [portálu Azure nebo](howto-restore-server-portal.md) [azure cli](howto-restore-server-cli.md).
- Další informace o [replikách pro čtení v Azure Database for PostgreSQL](concepts-read-replicas.md).