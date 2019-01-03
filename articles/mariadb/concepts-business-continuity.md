---
title: Přehled kontinuity se službou Azure Database pro MariaDB
description: Přehled kontinuity se službou Azure Database pro MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4c64f920bf56195ad53ac8acbf3f9199090f0a8b
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536980"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mariadb"></a>Přehled kontinuity se službou Azure Database pro MariaDB

Tento přehled popisuje možnosti, které poskytuje Azure Database pro MariaDB pro provozní kontinuitu a zotavení po havárii. Další informace o možnosti pro zotavení z ničivých událostí, které by mohly způsobit ztrátu dat nebo nedostupnost databáze a aplikace přestanou být dostupné. Zjistěte, co můžete dělat, když chyba uživatele nebo aplikace ovlivňuje integritu dat, má k výpadku oblasti Azure nebo aplikace vyžaduje údržbu.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkce, které vám umožní poskytovat kontinuita podnikových procesů

Azure Database pro MariaDB poskytuje funkce provozní kontinuity, které zahrnují automatizovaných záloh a možnost uživatelů k zahájení geografické obnovení. Odhadovaný čas obnovení (ERT) a potenciální ztráty dat každá má jiné vlastnosti. Jakmile tyto možnosti pochopíte, můžete si mezi nimi vybírat a současně použít pro různé scénáře. Při vývoji plánu provozní kontinuity musíte pochopit maximální přijatelnou dobu, než úplného obnovení aplikace po ničivé události – to je cíl času obnovení (RTO). Také musíte pochopit maximální objem dat poslední aktualizace (časový interval) aplikace může tolerovat možnost, ztráty při obnovení po ničivé události – to je cíl bodu obnovení (RPO).

Následující tabulka porovnává ERT a RPO pro dostupné funkce:

| **Funkce** | **Basic** | **Obecné účely** | **Optimalizované z hlediska paměti** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Obnovení k určitému bodu v čase ze zálohy | Libovolný bod obnovení v rámci doby uchování | Libovolný bod obnovení v rámci doby uchování | Libovolný bod obnovení v rámci doby uchování |
| Geografické obnovení z geograficky replikovaných záloh | Nepodporuje se | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Při odstranění serveru, odstraní se také všechny databáze, které jsou součástí serveru a nelze obnovit. Nelze obnovit server odstranil.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Obnovení serveru po chybě uživatele nebo aplikace

Zálohy služby lze použít k obnovení serveru různých ničivých událostech. Uživatel může omylem odstranit některá data, nedopatřením smazat důležitou tabulku nebo dokonce smazat celou databázi. Aplikace může nechtěně přepsat dobrá data chybnými daty aplikace kvůli vadě a tak dále.

Můžete provádět obnovení bodu v čas – Chcete-li vytvořit kopii tohoto serveru na známé dobré bodu v čase. Tento bod v čase musí být v rámci doby uchovávání záloh, které jste nakonfigurovali pro váš server. Po obnovení dat na nový server, můžete nahradit původní server nově obnovený server nebo zkopírovat potřebná data z obnovené serveru na původním serveru.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Zotavení z výpadku Azure regionálního datového centra

Přestože je taková situace výjimečná, i u datového centra Azure může dojít k výpadku. Pokud dojde k výpadku, dojde k narušení provozu, které trvat jenom pár minut, ale může trvat hodiny.

Jednou z možností je počkat serveru do režimu online při výpadku datového centra je nad. Tento postup funguje pro aplikace, které si mohou dovolit mít server do offline režimu dobu některé, například vývojové prostředí. Pokud datové centrum má k výpadku, nevíte jak dlouho může trvat výpadek, proto tato možnost funguje jenom v případě nepotřebujete serveru nějakou dobu.

Další možností je používat Azure Database pro MariaDB na geografické obnovení funkce, která obnoví server pomocí geograficky redundantní zálohy. Tyto zálohy jsou dostupné i v případě, že je oblast, kterou váš server je hostovaný v offline. Můžete obnovit ze zálohy do jiné oblasti a přenést váš server zpět do online režimu.

> [!IMPORTANT]
> Geografické obnovení je možné, pouze pokud jste zřídili serveru pomocí geograficky redundantního úložiště záloh.

## <a name="next-steps"></a>Další postup

- Další informace o automatizovaných záloh najdete v tématu [záloh ve službě Azure Database pro MariaDB](concepts-backup.md).
- Obnovení do bodu v čase pomocí webu Azure portal, najdete v článku [obnovit databázi do bodu v čase pomocí webu Azure portal](howto-restore-server-portal.md).

<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md). 
-->
