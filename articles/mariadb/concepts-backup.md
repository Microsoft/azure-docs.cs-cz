---
title: Zálohování a obnovení ve službě Azure Database pro MariaDB
description: Další informace o automatické zálohování a obnovení Azure Database pro MariaDB server.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: e36e718d35f652c139958388491642e98469ecaf
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548836"
---
# <a name="backup-and-restore-in-azure-database-for-mariadb"></a>Zálohování a obnovení ve službě Azure Database pro MariaDB

Azure Database pro MariaDB automaticky vytvoří serverových záloh a ukládá je do nakonfigurovaného uživatele místně redundantní a geograficky redundantní úložiště. Zálohy lze použít k obnovení vašeho serveru v daném okamžiku. Zálohování a obnovení jsou nedílnou součást každé strategie obchodní kontinuity podnikových procesů, protože jejich Chraňte svoje data před náhodným poškozením nebo odstranění.

## <a name="backups"></a>Zálohování

Azure Database pro MariaDB trvá úplné a rozdílové zálohování a zálohování protokolů transakcí. Tyto zálohy bylo možné obnovit server k jakékoli v daném okamžiku v rámci doby nakonfigurované uchovávání záloh. Výchozí období uchování zálohy je sedm dní. Můžete volitelně ho nakonfigurovat až 35 dnů. Všechny zálohy jsou šifrované pomocí šifrování AES 256 bitů.

### <a name="backup-frequency"></a>Frekvence zálohování

Obecně platí, úplné zálohování každý týden, rozdílového zálohování k dojde dvakrát denně, a zálohování protokolů transakcí každých pět minut. Bude první úplná záloha je naplánováno ihned po vytvoření serveru. Prvotní zálohování na velké obnoveného serveru může trvat déle. Nejstarší bod v čase, který lze obnovit na nový server je doba, jakou dokončení prvotní úplné zálohy.

### <a name="backup-redundancy-options"></a>Možnosti redundance zálohy

Azure Database pro MariaDB poskytuje možnost si vybrat mezi místně redundantním nebo geograficky redundantní úložiště záloh na úrovni obecné účely a optimalizované pro paměť. Když jsou zálohy uložené v geograficky redundantní úložiště záloh, nejsou pouze uložené v rámci oblasti, ve kterém je hostovaný váš server, ale také replikují do [spárovaném datovém centru](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). To poskytuje lepší ochranu a umožňuje v případě havárie obnovit server v jiné oblasti. Úroveň Basic nabízí jenom místně redundantní úložiště záloh.

> [!IMPORTANT]
> Konfigurace místně redundantní a geograficky redundantní úložiště pro zálohování je povolený jenom během serveru vytvoříte. Jakmile je server zřizován, nelze změnit možnost redundance úložiště pro zálohování.

### <a name="backup-storage-cost"></a>Náklady na úložiště zálohování

Azure Database pro MariaDB poskytuje až 100 % vaše zajišťovaného úložiště serveru jako úložiště pro zálohování bez dodatečných nákladů. Obvykle je to vhodné pro uchování zálohy 7 dní. Žádné další úložiště zálohování, které jsou použity je účtovat GB měsíčně.

Například pokud zřízení serveru s 250 GB máte 250 GB úložiště pro zálohování bez dodatečných poplatků. Účtuje se nad rámec 250 GB úložiště.

Další informace o náklady na úložiště pro zálohování, přejděte [MariaDB stránce s cenami](https://azure.microsoft.com/pricing/details/mariadb/).

## <a name="restore"></a>Obnovení

Ve službě Azure Database pro MariaDB provádění obnovení vytvoří nový server ze zálohy původního serveru.

K dispozici jsou dva typy obnovení:

- **Obnovení k určitému bodu v čase** je k dispozici obě možnosti redundance zálohy a vytvoří nový server ve stejné oblasti jako váš původním serveru.
- **Geografické obnovení** je k dispozici pouze v případě, že jste nakonfigurovali server pro geograficky redundantní úložiště a umožňuje obnovit váš server v jiné oblasti.

Odhadovaný čas obnovení závisí na několika faktorech včetně velikosti databáze, velikost protokolu transakcí, šířky pásma sítě a celkový počet obnovovaných databází ve stejné oblasti ve stejnou dobu. Čas obnovení je obvykle méně než 12 hodin.

> [!IMPORTANT]
> Odstranit servery **nelze** obnovit. Při odstranění serveru, odstraní se také všechny databáze, které patří k serveru a nelze ji obnovit.

### <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase

Bez ohledu na vaše možnosti redundance zálohy, umožňuje obnovit do libovolného bodu v čase v rámci doby uchovávání záloh. Nový server se vytvoří ve stejné oblasti Azure jako původní server. Bude vytvořen pomocí konfigurace původního serveru pro cenové úrovně, generování výpočtu a počet virtuálních jader, velikosti úložiště, období uchování zálohy a možnosti redundance zálohy.

Obnovení k určitému bodu v čase je užitečné ve scénářích s více. Například když uživatel omylem odstraní data, sníží důležitou tabulku nebo databázi, nebo pokud aplikace náhodně přepíše dobrá data chybnými daty aplikace kvůli vadě.

Budete muset počkat na další zálohování protokolu transakcí mají být provedeny, než bude možné obnovit do bodu v čase za posledních pět minut.

### <a name="geo-restore"></a>Geografické obnovení

Server můžete obnovit do jiné oblasti Azure, kde je k dispozici služba, pokud jste nakonfigurovali pro geograficky redundantní zálohy vašeho serveru. Geografické obnovení je výchozí možnost zotavení, kdy je server není k dispozici z důvodu incidentu v oblasti, kde je hostovaný na serveru. Pokud ve velkém měřítku incidentů v oblasti výsledky v nedostupnost databázovou aplikaci, můžete obnovit server z geograficky redundantní zálohy k serveru v kterékoli jiné oblasti. Dochází ke zpoždění mezi pořizování zálohy a kdy se replikuje do jiné oblasti. Toto zpoždění může být až hodinu, proto, pokud dojde k havárii, může trvat jednu hodinu ztrátu.

Během geografické obnovení, které zahrnují konfigurace serveru, které lze změnit compute generace, vCore, období uchování zálohy a možnosti redundance zálohy. Změna cenovou úroveň (Basic, obecné účely nebo k paměťově optimalizovaným) nebo velikosti úložiště během geografické obnovení není podporováno.

### <a name="perform-post-restore-tasks"></a>Proveďte úkoly po obnovení

Po obnovení z libovolného mechanismu by měl provádět následující úlohy zobrazíte uživatele a aplikace zpět do provozu:

- Pokud nový server je určena k nahrazení původní server, přesměrujte klienty a klientské aplikace na nový server
- Ujistěte se, že pravidla brány firewall na příslušné úrovni serveru jsou nastavené pro připojení uživatelů
- Zkontrolujte, zda jsou na místě odpovídající přihlášení a oprávnění na úrovni databáze
- Podle potřeby nakonfigurujte výstrahy.

## <a name="next-steps"></a>Další postup

- Další informace o kontinuitě, najdete v článku [přehled zajištění provozní kontinuity firmy](concepts-business-continuity.md).
- Obnovení do bodu v čase pomocí webu Azure portal, najdete v článku [obnovit databázi do bodu v čase pomocí webu Azure portal](howto-restore-server-portal.md).
 
<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md).-->
