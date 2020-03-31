---
title: Kontinuita podnikání – databáze Azure pro MariaDB
description: Přečtěte si o kontinuitě provozu (obnovení v čase, výpadek datového centra, geografické obnovení) při použití služby Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c01e0df1f420c8489ca3445d9fa025b251a870f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532387"
---
# <a name="understand-business-continuity-in-azure-database-for-mariadb"></a>Principy kontinuity podnikání v Azure Database pro MariaDB

Tento článek popisuje funkce, které Azure Database pro MariaDB poskytuje pro kontinuitu podnikání a zotavení po havárii. Další informace o možnostech obnovení z rušivých událostí, které by mohly způsobit ztrátu dat nebo způsobit, že databáze a aplikace nebudou k dispozici. Zjistěte, co dělat, když chyba uživatele nebo aplikace ovlivňuje integritu dat, oblast Azure má výpadek nebo vaše aplikace vyžaduje údržbu.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkce, které můžete použít k zajištění kontinuity provozu

Azure Database for MariaDB poskytuje funkce kontinuity podnikání, které zahrnují automatické zálohování a možnost pro uživatele zahájit geografické obnovení. Každý z nich má různé charakteristiky pro odhadovaný čas obnovení (ERT) a potenciální ztrátu dat. Jakmile tyto možnosti pochopíte, můžete si mezi nimi vybrat a použít je společně pro různé scénáře. Při vývoji plánu kontinuity provozu je třeba pochopit maximální přijatelnou dobu, než se aplikace plně zotaví po rušivé události – toto je váš cíl doby obnovení (RTO). Musíte také pochopit maximální množství nedávných aktualizací dat (časový interval), které aplikace může tolerovat ztrátu při obnovení po rušivé události - to je váš cíl bodu obnovení (RPO).

Následující tabulka porovnává ert a rpo pro dostupné funkce:

| **Schopnost** | **Basic** | **Obecný účel** | **Optimalizované z hlediska paměti** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Obnovení k určitému bodu v čase ze zálohy | Jakýkoli bod obnovení během retenční ho období | Jakýkoli bod obnovení během retenční ho období | Jakýkoli bod obnovení během retenční ho období |
| Geografické obnovení z geograficky replikovaných záloh | Nepodporuje se | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Pokud odstraníte server, budou odstraněny také všechny databáze obsažené na serveru a nelze je obnovit. Odstraněný server nelze obnovit.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Obnovení serveru po chybě uživatele nebo aplikace

Zálohy služby můžete použít k obnovení serveru z různých rušivých událostí. Uživatel může omylem odstranit některá data, nechtěně vynechat důležitou tabulku nebo dokonce přetažení celé databáze. Aplikace může omylem přepsat dobrá data se špatnými daty z důvodu vady aplikace a tak dále.

Můžete provést bod-in-time-restore a vytvořit kopii serveru do známého dobrého bodu v čase. Tento bod v čase musí být v rámci doby uchování zálohy, které jste nakonfigurovali pro server. Po obnovení dat na nový server můžete buď nahradit původní server nově obnoveným serverem, nebo zkopírovat potřebná data z obnoveného serveru na původní server.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Obnovení výpadku regionálního datového centra Azure

Přestože je taková situace výjimečná, i u datového centra Azure může dojít k výpadku. Dojde-li k výpadku, způsobí narušení podnikání, které může trvat pouze několik minut, ale může trvat několik hodin.

Jednou z možností je počkat, až se server vrátí do režimu online, až skončí výpadek datového centra. To funguje pro aplikace, které si mohou dovolit mít server offline po určitou dobu, například vývojové prostředí. Pokud má datové centrum výpadek, nevíte, jak dlouho může výpadek trvat, takže tato možnost funguje pouze v případě, že server nějakou dobu nepotřebujete.

Druhou možností je použití databáze Azure pro funkci geografického obnovení MariaDB, která obnovuje server pomocí geograficky redundantních záloh. Tyto zálohy jsou přístupné i v případě, že oblast, ve které je server hostován, je offline. Z těchto záloh můžete obnovit do jakékoli jiné oblasti a převést server zpět do režimu online.

> [!IMPORTANT]
> Geografické obnovení je možné pouze v případě, že jste zřídit server s geograficky redundantní úložiště záloh.

## <a name="next-steps"></a>Další kroky

- Další informace o automatických zálohách najdete [v tématu Zálohování v Azure Database for MariaDB](concepts-backup.md).
- Pokud chcete obnovit do bodu v čase pomocí portálu Azure, přečtěte si přístup [k obnovení databáze do bodu v čase pomocí portálu Azure](howto-restore-server-portal.md).

<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md). 
-->
