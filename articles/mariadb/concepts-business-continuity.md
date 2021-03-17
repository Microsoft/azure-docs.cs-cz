---
title: Provozní kontinuita – Azure Database for MariaDB
description: Přečtěte si o kontinuitě podnikových služeb (obnovení v časovém bodě, výpadku datového centra, geografické obnovení) při používání služby Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: 6439a63b74b6f627b62741c145f763dc3d9552d3
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98659848"
---
# <a name="understand-business-continuity-in-azure-database-for-mariadb"></a>Informace o kontinuitě podnikových aplikací v Azure Database for MariaDB

Tento článek popisuje možnosti, které Azure Database for MariaDB poskytuje pro provozní kontinuitu a zotavení po havárii. Seznamte se s možnostmi pro zotavení z rušivých událostí, které by mohly způsobit ztrátu dat nebo způsobit nedostupnost databáze a aplikace. Přečtěte si, co dělat, když dojde k chybě uživatele nebo aplikace při vlivu na integritu dat, když dojde k výpadku oblasti Azure nebo když vaše aplikace vyžaduje údržbu.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkce, které můžete použít k zajištění kontinuity podnikových aplikací

Azure Database for MariaDB poskytuje funkce pro provozní kontinuitu, které zahrnují automatizované zálohování a schopnost uživatelů zahájit geografické obnovení. Každý má různé charakteristiky pro odhadovanou dobu obnovení (ERT) a potenciální ztrátu dat. Jakmile tyto možnosti pochopíte, můžete si je vybrat mezi nimi a použít je společně pro různé scénáře. Při vývoji plánu provozní kontinuity je potřeba pochopit maximální přijatelnou dobu, než aplikace plně obnoví po přerušení události – to je vaše plánovaná doba obnovení (RTO). Také je potřeba pochopit maximální množství nedávných aktualizací dat (časový interval), které může aplikace tolerovat při obnovování po přerušení události – to je váš cíl bodu obnovení (RPO).

Následující tabulka porovnává ERT a RPO pro dostupné funkce:

| **Funkce** | **Basic** | **Obecné použití** | **Optimalizované pro paměť** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Obnovení k určitému bodu v čase ze zálohy | Libovolný bod obnovení v rámci doby uchování | Libovolný bod obnovení v rámci doby uchování | Libovolný bod obnovení v rámci doby uchování |
| Geografické obnovení ze geograficky replikovaných záloh | Nepodporováno | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

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

## <a name="cross-region-read-replicas"></a>Repliky čtení mezi oblastmi

Repliky čtení pro různé oblasti můžete použít ke zvýšení provozní kontinuity a plánování zotavení po havárii. Repliky čtení jsou asynchronně aktualizované pomocí technologie MariaDB binárního protokolu pro replikaci. Přečtěte si další informace o replikách pro čtení, dostupných oblastech a o tom, jak převzít služby při selhání v článku věnovaném [konceptům čtení replik](concepts-read-replicas.md). 

## <a name="faq"></a>Nejčastější dotazy
### <a name="where-does-azure-database-for-mariadb-store-customer-data"></a>Kam se Azure Database for MariaDB ukládají zákaznická data?
Ve výchozím nastavení Azure Database for MariaDB nepřesouvá nebo neukládají zákaznická data mimo oblast, ve které je nasazená. Zákazníci si ale můžou volitelně zvolit možnost povolit [geograficky redundantní zálohy](concepts-backup.md#backup-redundancy-options) nebo vytvořit [repliku pro čtení v různých oblastech](concepts-read-replicas.md#cross-region-replication) pro ukládání dat v jiné oblasti.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [automatizovaných zálohách v Azure Database for MariaDB](concepts-backup.md).
- Naučte se, jak obnovit pomocí [Azure Portal](howto-restore-server-portal.md) nebo rozhraní příkazového [řádku Azure](howto-restore-server-cli.md).
- Přečtěte si o [replikách pro čtení v Azure Database for MariaDB](concepts-read-replicas.md).