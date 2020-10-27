---
title: Zálohování a obnovení v Azure Database for MySQL flexibilním serveru
description: Přečtěte si o konceptech zálohování a obnovení pomocí Azure Database for MySQL flexibilního serveru.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 44cfe9bc6cd357cc0c649cecd022d3955bb5a2ce
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545867"
---
# <a name="backup-and-restore-in-azure-database-for-mysql-flexible-server-preview"></a>Zálohování a obnovení v Azure Database for MySQL flexibilním serveru (Preview)

> [!IMPORTANT] 
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Azure Database for MySQL flexibilní server automaticky vytvoří zálohy serveru a bezpečně je ukládá v místním redundantním úložišti v rámci oblasti. Zálohy lze použít k obnovení serveru do určitého bodu v čase. Zálohování a obnovení jsou důležitou součástí jakékoli strategie pro provozní kontinuitu, protože chrání vaše data před náhodným poškozením nebo odstraněním.

## <a name="backup-overview"></a>Přehled služby Backup

Flexibilní Server využívá zálohy snímků datových souborů a ukládá je v místním redundantním úložišti. Server také provádí zálohování protokolů transakcí a ukládá je také v místním redundantním úložišti. Tyto zálohy umožňují obnovit server k jakémukoli časovému okamžiku v rámci nakonfigurované doby uchovávání záloh. Výchozí doba uchovávání záloh je sedm dní. Volitelně můžete nakonfigurovat zálohu databáze od 1 do 35 dnů. Všechny zálohy jsou šifrovány pomocí šifrování AES 256-bit pro data uložená v klidovém stavu.

Tyto záložní soubory nelze exportovat. Zálohy se dají použít jenom pro operace obnovení v flexibilním serveru. K zkopírování databáze můžete použít také [mysqldump](../concepts-migrate-dump-restore.md#dump-and-restore-using-mysqldump-utility) z klienta MySQL.

## <a name="backup-frequency"></a>Frekvence zálohování

Zálohy na flexibilních serverech jsou založené na snímcích. První úplné zálohování snímků je naplánované okamžitě po vytvoření serveru. Tato první úplná záloha snímku se uchová jako základní záloha serveru. Další zálohování snímků je pouze rozdílové.

Rozdílové zálohování snímků se provádí alespoň jednou denně. Rozdílové zálohování snímků se neprovádí podle pevně daného plánu. Rozdílové zálohování snímků se provádí každých 24 hodin, dokud velikost binárních protokolů v MySQL od posledního rozdílového zálohování nepřekročí 50 GB. Každý den je možné provést rozdílové zálohování snímků maximálně šestkrát. Zálohování transakčních protokolů probíhá každých pět minut.

## <a name="backup-retention"></a>Uchování záloh

Zálohy databáze jsou uloženy v místním redundantním úložišti (LRS), které je uloženo ve třech kopiích v rámci oblasti. Zálohy se uchovávají na základě nastavení období uchovávání záloh na serveru. Můžete vybrat dobu uchování 1 až 35 dní, přičemž výchozí doba uchování je sedm dní. Dobu uchovávání můžete nastavit během vytváření serveru nebo později aktualizací konfigurace zálohování pomocí Azure Portal.

Doba uchovávání záloh určuje, jak dlouho se má provést operace obnovení k určitému bodu v čase, protože je založená na dostupných zálohách. Období uchovávání záloh lze také považovat za okno obnovení z perspektivy obnovení. Všechny zálohy potřebné k provedení obnovení k určitému bodu v čase v rámci období uchovávání záloh jsou uchovávány v úložišti záloh. Pokud je například doba uchovávání záloh nastavená na sedm dní, okno obnovení se považuje za posledních sedm dní. V tomto scénáři jsou zachovány všechny zálohy potřebné k obnovení serveru během posledních sedmi dnů. V okně uchovávání záloh po dobu sedmi dnů se snímky databáze a zálohy transakčního protokolu ukládají za posledních osm dní (1 den před oknem).

## <a name="backup-storage-cost"></a>Náklady na úložiště zálohování

Flexibilní Server poskytuje až 100% zřízeného serverového úložiště jako úložiště zálohování bez dalších poplatků. Jakékoli další využité úložiště záloh se účtuje za GB za měsíc. Pokud jste například zřídili Server s 250 GB úložiště, máte k dispozici 250 GB úložiště pro zálohy serveru bez dalších poplatků. Pokud je denní využití zálohování 25 GB, můžete mít až 10 dní bezplatného úložiště zálohování. Úložiště spotřebované za zálohy větší než 250 GB se účtuje podle [cenového modelu](https://azure.microsoft.com/pricing/details/mysql/).

Pomocí metriky [úložiště zálohování](../concepts-monitoring.md) v Azure monitor k dispozici v Azure Portal můžete monitorovat úložiště zálohování spotřebované serverem. Metrika využitého **úložiště záloh** představuje součet úložiště spotřebovaného všemi zálohami databáze a zálohami protokolů, které jsou zachovány na základě nastaveného období uchovávání záloh pro server. Náročné transakční aktivity na serveru můžou způsobit zvýšení využití úložiště zálohování bez ohledu na celkovou velikost databází.

Hlavním prostředkem řízení nákladů na úložiště zálohování je nastavení vhodné doby uchovávání záloh. Můžete vybrat dobu uchování mezi 1 a 35 dny.

> [!IMPORTANT]
> Zálohy z databázového serveru nakonfigurovaného v zóně redundantní konfigurace s vysokou dostupností se provádí z primárního databázového serveru, protože režie je minimální a má zálohy snímků.

> [!IMPORTANT]
> Geograficky redundantní zálohy se v současnosti u flexibilního serveru nepodporují.

## <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase

V Azure Database for MySQL flexibilním serveru vytvoří obnovení k určitému bodu v čase nový server ze zálohy flexibilního serveru ve stejné oblasti jako váš zdrojový server. Vytvoří se s konfigurací původního serveru pro výpočetní vrstvu, počet virtuální jádra, velikost úložiště, dobu uchování zálohy a možnost redundance zálohy. Ze zdrojového serveru se taky dědí značky a nastavení, jako je třeba virtuální síť a brána firewall. Po dokončení obnovení můžete změnit výpočetní a úložnou úroveň obnoveného serveru, konfiguraci a nastavení zabezpečení.

> [!NOTE]
> K dispozici jsou dva parametry serveru, které se resetují na výchozí hodnoty (a po operaci obnovení se nekopírují přes primární server).
> *   time_zone – tato hodnota se nastaví na systém výchozí hodnoty.
> *   event_scheduler – event_scheduler na obnoveném serveru je nastaven na vypnuto

Obnovení k bodu v čase je užitečné ve více scénářích. Mezi běžné případy použití patří –
-   Když uživatel omylem odstraní data v databázi
-   Uživatel si vynechá důležitou tabulku nebo databázi.
-   Uživatelská aplikace náhodně přepíše dobrá data s chybnými daty z důvodu vady aplikace.

Můžete si vybrat mezi posledním bodem obnovení a vlastním bodem obnovení prostřednictvím [Azure Portal](how-to-restore-server-portal.md).

-   **Poslední bod obnovení** : nejnovější bod obnovení vám pomůže obnovit server do poslední zálohy provedené na zdrojovém serveru. Časové razítko pro obnovení se zobrazí také na portálu. Tato možnost je užitečná pro rychlé obnovení serveru do nejaktualizovaného stavu.
-   **Vlastní bod obnovení** : umožní vám zvolit jakýkoli časový okamžik v rámci doby uchování definované pro tento flexibilní Server. Tato možnost je užitečná k obnovení serveru v přesném bodě v čase k obnovení z chyby uživatele.

Odhadovaná doba obnovení závisí na několika faktorech, včetně velikosti databáze, velikosti zálohy transakčního protokolu, výpočetní velikosti skladové jednotky a času obnovení. Obnovení protokolu transakcí je čas, který je v rámci procesu obnovení nejvíce náročný. Pokud je zvolena doba obnovení blíže k plánu úplného nebo rozdílového zálohování snímků, obnovení je rychlejší, protože je aplikace transakčního protokolu minimální. Abyste mohli odhadnout přesný čas obnovení serveru, důrazně doporučujeme, abyste ho otestovali ve vašem prostředí, protože má příliš mnoho proměnných specifických pro prostředí.

> [!IMPORTANT]
> Pokud obnovujete flexibilní server nakonfigurovaný s vysokou dostupností zóny, obnoví se obnovený server ve stejné oblasti a zóně jako primární server a nasazený jako jeden flexibilní Server v režimu bez HA. V případě flexibilního serveru se podívejte na [zónu redundantní vysoké dostupnosti](concepts-high-availability.md) .

> [!IMPORTANT]
> Odstraněné servery **nelze** obnovit. Pokud server odstraníte, odstraní se i všechny databáze patřící do serveru a nebude možné je obnovit. Pro ochranu prostředků serveru, po nasazení, před náhodným odstraněním nebo neočekávaným změnám můžou správci využít [zámky pro správu](../../azure-resource-manager/management/lock-resources.md).

## <a name="perform-post-restore-tasks"></a>Provádění úloh po obnovení

Po obnovení z **posledního bodu obnovení** nebo vlastního mechanismu obnovení **bodu obnovení** byste měli provést následující úlohy, aby se uživatelé a aplikace mohli zálohovat a spustit:

-   Pokud by nový server chtěl nahradit původní server, přesměrujte klienty a klientské aplikace na nový server.
-   Aby se uživatelé mohli připojit, zajistěte, aby byla k dismístě vhodná pravidla brány firewall na úrovni serveru a virtuální sítě.
-   Zajistěte, aby byla zavedena příslušná přihlášení a oprávnění na úrovni databáze.
-   Podle potřeby nakonfigurujte výstrahy.

## <a name="next-steps"></a>Další kroky

-   Informace o [kontinuitě podnikových aplikací](./concepts-business-continuity.md)
-   Přečtěte si o [vysoké dostupnosti zóny jako redundantní](./concepts-high-availability.md) .
-   Další informace o [zálohování a obnovení](./concepts-backup-restore.md)
