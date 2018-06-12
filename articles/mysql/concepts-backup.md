---
title: Zálohování a obnovení ve službě Azure Database pro databázi MySQL
description: Další informace o automatické zálohování a obnovení databáze Azure pro server databáze MySQL.
services: mysql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: bdc9a0ef393b55563691d7a52f8fa074eacc4594
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264472"
---
# <a name="backup-and-restore-in-azure-database-for-mysql"></a>Zálohování a obnovení ve službě Azure Database pro databázi MySQL

Databáze pro databázi MySQL Azure automaticky vytvoří záloh serveru a ukládá je do uživateli nakonfigurovanému místně redundantní nebo geograficky redundantní úložiště. Zálohy lze použít k obnovení vašeho serveru v daném okamžiku. Zálohování a obnovení jsou nedílnou součást vámi vyžádaných jakékoli strategie pro kontinuitu podnikových, protože se data chránit před náhodnou poškození nebo odstranění.

## <a name="backups"></a>Zálohování

Azure databáze pro databázi MySQL trvá úplné rozdíl a zálohování transakčního protokolu. Tyto zálohy umožňují obnovit server pro všechny v daném okamžiku v rámci vašeho období nakonfigurované uchovávání záloh. Výchozí dobu uchovávání záloh je sedm dní. Volitelně ji můžete nakonfigurovat na 35 dnů nahoru. Všechny zálohy jsou šifrované pomocí šifrování AES 256 bitů.

### <a name="backup-frequency"></a>Frekvence zálohování

Obecně platí, týdně, dojde k úplné zálohy rozdílové zálohy k dojde dvakrát denně, a zálohování transakčního protokolu každých pět minut. První úplné zálohování je naplánováno ihned po vytvoření serveru. Prvotní zálohování na velké obnovené serveru může trvat déle. Nejdřívější bodu v čase, který lze obnovit na nový server je čas, kdy je dokončení počáteční úplné zálohování.

### <a name="backup-redundancy-options"></a>Možnosti zálohování redundance

Databáze pro databázi MySQL Azure poskytuje flexibilitu zvolit si mezi místně redundantní nebo geograficky redundantní úložiště záloh úrovně obecné účely a paměťově optimalizovaná. Když jsou zálohy uložené v geograficky redundantní úložiště záloh, nejsou pouze uložené v rámci oblasti, ve kterém je hostována serverem, ale také replikují do [spárované datového centra](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). To poskytuje lepší ochranu a umožňuje obnovit váš server v jiné oblasti v případě havárie. Základní úroveň nabízí jenom místně redundantní úložiště záloh.

> [!IMPORTANT]
> Konfigurace místně redundantní nebo geograficky redundantní úložiště pro zálohování je povoleno pouze během serveru vytvořit. Po zřízení serveru nelze změnit možnost redundance úložiště záloh.

### <a name="backup-storage-cost"></a>Náklady na úložiště záloh

Databáze pro databázi MySQL Azure poskytuje až o 100 % úložiště zajištěné serveru jako úložiště pro zálohu bez dalších poplatků. Obvykle je to vhodné pro uchovávání záloh sedm dní. Žádné další používá úložiště zálohy je účtován v GB-měsíc.

Například pokud máte zřízenou na server s 250 GB, máte 250 GB úložiště zálohování bez dalších poplatků. Více než 250 GB úložiště je účtován.

## <a name="restore"></a>Obnovení

V databázi Azure pro databázi MySQL vytvoří provádění obnovení na nový server ze zálohy na původní server.

Nejsou k dispozici dva typy obnovení:

- **Obnovení bodu v čase** je k dispozici u obou možností zálohování redundance a vytvoří nový server ve stejné oblasti jako původní server.
- **Geografické obnovení** je k dispozici pouze v případě, že jste nakonfigurovali server pro geograficky redundantní úložiště a umožňuje obnovit váš server v jiné oblasti.

Odhadovaná doba obnovení závisí na několika různými faktory, včetně velikosti databáze, velikosti protokolu transakcí, šířku pásma sítě a celkový počet databází ve stejné oblasti obnovení ve stejnou dobu. Doba obnovení je obvykle menší než 12 hodin.

> [!IMPORTANT]
> Pokud server odstraníte, všechny databáze, které patří k serveru budou také odstraněny a nelze jej obnovit. Nelze obnovit odstraněné serveru.

### <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase

Bez ohledu na svoji možnost zálohování redundance, do libovolného bodu obnovení můžete provést v čase v rámci vaší dobu uchovávání záloh. Nový server se vytvoří ve stejné oblasti Azure jako původní server. Bude vytvořen pomocí konfigurace původního serveru pro cenovou úroveň, výpočetní generace, počet vCores, velikost úložiště, dobu uchovávání záloh a možnost zálohování redundance.

Obnovení bodu v čase je užitečná ve scénářích s více. Například když uživatel omylem odstraní data, zahodí důležité tabulky nebo databáze, nebo pokud aplikace omylem přepíše dobrých dat chybná data z důvodu vadou aplikaci.

Potřebujete čekat na další zálohy protokolu transakcí mají být provedeny, než bude možné obnovit do bodu v čase za posledních pět minut.

### <a name="geo-restore"></a>Geografické obnovení

Pokud jste nakonfigurovali server pro geograficky redundantní zálohy, můžete obnovit server jiné oblasti Azure, kde je služba k dispozici. Geografické obnovení je výchozí možnost, pokud váš server není k dispozici z důvodu incidentu v oblasti, který je hostitelem serveru. Pokud ve velkém měřítku incident v oblasti výsledky v nedostupnosti databázovou aplikaci, můžete obnovit server z geograficky redundantní zálohy na server v jiné oblasti. Dochází ke zpoždění mezi když je provést zálohu a když se replikují do jiné oblasti. Toto zpoždění může být až hodinu, proto, pokud dojde k havárii, může být až ke ztrátě dat. jednu hodinu.

### <a name="perform-post-restore-tasks"></a>Proveďte úkoly po obnovení

Po obnovení z buď obnovení mechanismus měli byste provést následující úkoly a vaši uživatelé a aplikace zpět provozu:

- Pokud na nový server je určená k nahrazení původní server, přesměrujte klienty a klientské aplikace na nový server
- Ujistěte se, že jsou pravidla brány firewall na příslušné úrovni serveru v místní uživatelé připojují
- Zajistěte odpovídající přihlášení a oprávnění na úrovni databáze jsou na místě
- Podle potřeby nakonfigurujte výstrahy.

## <a name="next-steps"></a>Další postup

- Další informace o kontinuity podnikových procesů najdete v tématu [obchodní kontinuity přehled](concepts-business-continuity.md).
- Obnovit k určitému bodu v čase pomocí portálu Azure, najdete v tématu [obnovit databázi k určitému bodu v čase pomocí portálu Azure](howto-restore-server-portal.md).
- Obnovit k určitému bodu v čase pomocí rozhraní příkazového řádku Azure, najdete v tématu [obnovit databázi k určitému bodu v čase pomocí rozhraní příkazového řádku](howto-restore-server-cli.md).