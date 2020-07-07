---
title: Provozní kontinuita – Azure Database for MariaDB
description: Přečtěte si o kontinuitě podnikových služeb (obnovení v časovém bodě, výpadku datového centra, geografické obnovení) při používání služby Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c01e0df1f420c8489ca3445d9fa025b251a870f2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "79532387"
---
# <a name="understand-business-continuity-in-azure-database-for-mariadb"></a>Informace o kontinuitě podnikových aplikací v Azure Database for MariaDB

Tento článek popisuje možnosti, které Azure Database for MariaDB poskytuje pro provozní kontinuitu a zotavení po havárii. Seznamte se s možnostmi pro zotavení z rušivých událostí, které by mohly způsobit ztrátu dat nebo způsobit nedostupnost databáze a aplikace. Přečtěte si, co dělat, když dojde k chybě uživatele nebo aplikace při vlivu na integritu dat, když dojde k výpadku oblasti Azure nebo když vaše aplikace vyžaduje údržbu.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkce, které můžete použít k zajištění kontinuity podnikových aplikací

Azure Database for MariaDB poskytuje funkce pro provozní kontinuitu, které zahrnují automatizované zálohování a schopnost uživatelů zahájit geografické obnovení. Každý má různé charakteristiky pro odhadovanou dobu obnovení (ERT) a potenciální ztrátu dat. Jakmile tyto možnosti pochopíte, můžete si je vybrat mezi nimi a použít je společně pro různé scénáře. Při vývoji plánu provozní kontinuity je potřeba pochopit maximální přijatelnou dobu, než aplikace plně obnoví po přerušení události – to je vaše plánovaná doba obnovení (RTO). Také je potřeba pochopit maximální množství nedávných aktualizací dat (časový interval), které může aplikace tolerovat při obnovování po přerušení události – to je váš cíl bodu obnovení (RPO).

Následující tabulka porovnává ERT a RPO pro dostupné funkce:

| **Funkce** | **Základní** | **Pro obecné účely** | **Optimalizované z hlediska paměti** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Obnovení k určitému bodu v čase ze zálohy | Libovolný bod obnovení v rámci doby uchování | Libovolný bod obnovení v rámci doby uchování | Libovolný bod obnovení v rámci doby uchování |
| Geografické obnovení ze geograficky replikovaných záloh | Nepodporuje se | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Odstraníte-li server, budou odstraněny také všechny databáze obsažené na serveru a nelze je obnovit. Odstraněný Server nelze obnovit.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Obnovení serveru po chybě uživatele nebo aplikace

Zálohy služby můžete použít k obnovení serveru z různých rušivých událostí. Uživatel může omylem odstranit některá data, neúmyslně odstranit důležitou tabulku nebo dokonce odstranit celou databázi. Aplikace může omylem přepsat dobrá data s chybnými daty z důvodu vady aplikace atd.

Můžete provést obnovení k určitému bodu v čase a vytvořit tak kopii serveru pro známý dobrý bod v čase. Tento bod v čase musí být v období uchovávání záloh, které jste nakonfigurovali pro váš server. Po obnovení dat na novém serveru můžete původní server nahradit novým obnoveným serverem nebo zkopírovat potřebná data z obnoveného serveru na původní server.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Zotavení z výpadku regionálního datového centra Azure

Přestože je taková situace výjimečná, i u datového centra Azure může dojít k výpadku. Pokud dojde k výpadku, způsobí to narušení podniku, které může trvat jenom několik minut, ale může to trvat i hodinu.

Jednou z možností je počkat, až se váš server vrátí zpátky do režimu online, když dojde k výpadku datového centra. To funguje u aplikací, které můžou umožnit, aby byl server v určitou dobu offline, například vývojové prostředí. Když dojde k výpadku datového centra, nevíte, jak dlouho může výpadek trvat, takže tato možnost funguje jenom v případě, že server ještě nepotřebujete.

Druhou možností je použít funkci geografického obnovení Azure Database for MariaDB, která obnoví Server pomocí geograficky redundantních záloh. Tyto zálohy jsou přístupné i v případě, že je oblast, ve které je server hostovaný, v režimu offline. Můžete obnovit z těchto záloh do jakékoli jiné oblasti a převést Server zpátky do online režimu.

> [!IMPORTANT]
> Geografické obnovení je možné pouze v případě, že jste zřídili Server s geograficky redundantním úložištěm záloh.

## <a name="next-steps"></a>Další kroky

- Další informace o automatizovaných zálohách najdete [v tématu zálohy v Azure Database for MariaDB](concepts-backup.md).
- Obnovení k určitému bodu v čase pomocí Azure Portal najdete v tématu [obnovení databáze k určitému bodu v čase pomocí Azure Portal](howto-restore-server-portal.md).

<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md). 
-->
