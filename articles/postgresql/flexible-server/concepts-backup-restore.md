---
title: Zálohování a obnovení v Azure Database for PostgreSQL – flexibilní Server
description: Přečtěte si o konceptech zálohování a obnovení pomocí Azure Database for PostgreSQL-flexibilního serveru.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: bed196d1be101ffa75affc389d390ec0fa764b05
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934931"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---flexible-server"></a>Zálohování a obnovení v Azure Database for PostgreSQL – flexibilní Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Zálohy tvoří podstatnou součást strategie pro provozní kontinuitu. Pomůžou se chránit data před náhodným poškozením nebo odstraněním. Azure Database for PostgreSQL – flexibilní server automaticky zálohuje Server a uchovává zálohy po dobu až 35 dnů. Během obnovování můžete zadat datum a čas, do kterého chcete obnovit v rámci doby uchování. Celková doba obnovení a obnovení závisí na velikosti databázových souborů a množství obnovení, které má být provedeno. 

### <a name="backup-process-in-flexible-server"></a>Proces zálohování v flexibilním serveru
První zálohování snímků je naplánované okamžitě po vytvoření flexibilního serveru. Následně se provádí denní záloha snímků datových souborů. Zálohy jsou uloženy v zóně redundantního úložiště v rámci oblasti. Protokoly transakcí (Write-WAL) se také archivují průběžně, takže budete moct obnovit poslední potvrzenou transakci. Tyto zálohy dat a protokolů umožňují obnovit server k jakémukoli bodu v čase v rámci nakonfigurované doby uchovávání záloh. Všechny zálohy se šifrují s využitím 256bitového šifrování AES.

Pokud je databáze nakonfigurovaná s vysokou dostupností, každodenní snímky se provádějí z primárního a průběžné zálohy protokolu se provádějí z úsporného režimu.

> [!IMPORTANT]
>Zálohy se na zastavených serverech neprovádí. Zálohy budou ale obnoveny, když je databáze buď automaticky spuštěna po 7 dnech, nebo ji spustí uživatel.

Zálohy lze použít pouze pro operace obnovení v rámci flexibilního serveru. Pokud chcete exportovat nebo importovat data na flexibilní Server, použijte metodologii [výpisu a obnovení](https://docs.microsoft.com/azure/postgresql/howto-migrate-using-dump-and-restore)   .


### <a name="backup-retention"></a>Uchování záloh

Zálohy se uchovávají na základě nastavení doby uchování záloh serveru. Můžete vybrat dobu uchování mezi 7 a 35 dny. Výchozí doba uchování je sedm dní. Dobu uchovávání můžete nastavit během vytváření serveru nebo ji můžete aktualizovat později. Zálohy se uchovávají i pro zastavené servery.

Doba uchovávání záloh určuje, jak daleko se obnovení k určitému bodu v čase dá načíst, protože je \' založené na dostupných zálohách. Období uchovávání záloh lze také považovat za okno obnovení z perspektivy obnovení. Všechna zálohování nutná k provedení obnovení k určitému bodu v čase v rámci období uchovávání záloh jsou zachována v úložišti zálohování. Pokud je například doba uchovávání záloh nastavená na sedm dní, okno obnovení se považuje za posledních sedm dní. V tomto scénáři se uchovávají všechna data a protokoly, které jsou potřeba k obnovení a obnovení serveru během posledních sedmi dnů. 


### <a name="backup-storage-cost"></a>Náklady na úložiště zálohování

Flexibilní Server poskytuje až 100% zřízeného serverového úložiště jako úložiště zálohování bez dalších poplatků. Jakékoli další využité úložiště záloh se účtuje za GB za měsíc. Pokud jste například zřídili Server s 250 GiB úložiště, budete mít k dispozici ještě 250 GiB kapacity úložiště zálohy bez dalších poplatků. Pokud je denní využití zálohy 25 GiB, můžete mít až 10 dní bezplatného úložiště zálohování. Spotřeba úložiště zálohy vyšší než 250 GiB se účtuje podle [cenového modelu](https://azure.microsoft.com/pricing/details/postgresql/).

 [Backup storage used](https://docs.microsoft.com/azure/postgresql/concepts-monitoring)   K monitorování úložiště záloh spotřebovaného serverem můžete použít metriku využití úložiště zálohování v Azure Portal. Metrika Využité úložiště zálohování představuje celkové úložiště využité všemi uchovávanými zálohami databází a protokolů na základě doby uchovávání záloh nastavené pro server.  Náročné transakční aktivity na serveru můžou způsobit zvýšení využití úložiště zálohování bez ohledu na celkovou velikost databází.

Hlavním prostředkem řízení nákladů na úložiště zálohování je nastavení vhodné doby uchovávání záloh a výběr správné možnosti redundance zálohování, která bude vyhovovat požadovaným cílům obnovení.

> [!IMPORTANT]
> Geograficky redundantní zálohy se v současnosti u flexibilního serveru nepodporují.

## <a name="point-in-time-restore-overview"></a>Přehled obnovení k časovému okamžiku

V případě flexibilního serveru vytvoří obnovení k určitému bodu v čase nový server ze zálohy flexibilního serveru \' ve stejné oblasti jako váš zdrojový server. Vytvoří se s konfigurací zdrojového serveru pro cenovou úroveň, generování výpočtů, počet virtuální jádra, velikost úložiště, dobu uchování zálohy a možnost redundance zálohy. Ze zdrojového serveru se zdědí také značky a nastavení, jako jsou nastavení virtuální sítě a brány firewall. 

 > [!IMPORTANT]
> Pokud obnovujete flexibilní server nakonfigurovaný s vysokou dostupností zóny, obnovený server se nakonfiguruje bez vysoké dostupnosti a ve stejné oblasti jako váš primární server. 

 ### <a name="restore-process"></a>Proces obnovení

Fyzické soubory databáze jsou nejprve obnoveny ze záloh snímků do umístění dat serveru. Příslušná záloha, která byla pořízena dříve, než je požadovaný časový okamžik, se automaticky vybere a obnoví. Proces obnovení se pak iniciuje pomocí souborů WAL, aby se databáze shodovala se stavem. 

 Předpokládejme například, že se zálohy provádějí v 23:00 každé noci. Pokud bod obnovení vychází z 15. srpna 2020 v 10:00 ráno, obnoví se denní záloha 14. srpna 2020. Databáze bude obnovena až do 10am 25. srpna 2020 s použitím zálohování protokolů transakcí mezi 24. srpna 23:00 do 25. srpna 10am. 

 Chcete-li obnovit databázový server, Projděte si [následující postup](./how-to-restore-server-portal.md) .

> [!IMPORTANT]
> Operace obnovení v flexibilním serveru vytvoří nový databázový server a nepřepíše existující databázový server.

Obnovení k bodu v čase je užitečné ve více scénářích. Například když uživatel omylem odstraní data, ponechá důležitou tabulku nebo databázi, nebo pokud aplikace náhodně přepíše dobrá data s chybnými daty z důvodu vady aplikace. Z důvodu průběžného zálohování protokolů transakcí budete moci obnovit poslední transakci.

Můžete si vybrat mezi nejstarším bodem obnovení a vlastním bodem obnovení.

-   **Nejstarší bod obnovení**: v závislosti na době uchování bude to čas, kdy bude možné provést obnovení. Nejstarší čas zálohování se vybere automaticky a zobrazí se na portálu. To je užitečné v případě, že chcete prozkoumat nebo provést některé testování od tohoto bodu v čase.

-   **Vlastní bod obnovení**: Tato možnost umožňuje zvolit jakýkoli časový okamžik v rámci doby uchování definované pro tento flexibilní Server. Ve výchozím nastavení je automaticky vybrán poslední čas ve standardu UTC a užitečný, pokud chcete provést obnovení na poslední potvrzenou transakci pro účely testování. Volitelně můžete zvolit další dny a čas. 

Odhadovaná doba obnovení závisí na několika faktorech, včetně velikosti databáze, objemu protokolů transakcí ke zpracování, šířky pásma sítě a celkového počtu databází obnovování ve stejné oblasti ve stejnou dobu. Celková doba obnovení obvykle trvá několik minut až několik hodin.


> [!IMPORTANT]
> Odstraněné servery **nelze**   obnovit. Pokud server odstraníte, odstraní se i všechny databáze patřící do serveru a nebude možné je obnovit. Pro ochranu prostředků serveru, po nasazení, před náhodným odstraněním nebo neočekávaným změnám můžou správci využít [zámky pro správu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

## <a name="perform-post-restore-tasks"></a>Provádění úloh po obnovení

Po obnovení databáze můžete provádět následující úlohy, aby se uživatelé a aplikace mohli zálohovat a spustit:

-   Pokud by nový server chtěl nahradit původní server, přesměrujte klienty a klientské aplikace na nový server.

-   Aby se uživatelé mohli připojit, zajistěte, aby byla k dismístě vhodná pravidla brány firewall na úrovni serveru a sítě. Tato pravidla se nekopírují z původního serveru.
  
-   Výpočetní výkon obnoveného serveru se dá podle potřeby škálovat nahoru/dolů.

-   Zajistěte, aby byla zavedena příslušná přihlášení a oprávnění na úrovni databáze.

-   Podle potřeby nakonfigurujte výstrahy.
  
-  Pokud jste obnovili databázi nakonfigurovanou vysokou dostupností a pokud chcete obnovit obnovený server s vysokou dostupností, [můžete postupovat podle pokynů.](./how-to-manage-high-availability-portal.md)


## <a name="next-steps"></a>Další kroky

-   Informace o [kontinuitě podnikových aplikací](./concepts-business-continuity.md)
-   Přečtěte si o [vysoké dostupnosti zóny jako redundantní](./concepts-high-availability.md) .
-   Informace [o tom, jak obnovit](./how-to-restore-server-portal.md)

