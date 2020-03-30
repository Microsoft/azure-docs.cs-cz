---
title: Azure SQL auditování
description: Pomocí auditování databáze Azure SQL můžete sledovat události databáze do protokolu auditování.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/27/2020
ms.custom: azure-synapse
ms.openlocfilehash: 8b50cb95e51ef36ed4436a6eb9c9143c9c613cc7
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346439"
---
# <a name="azure-sql-auditing"></a>Azure SQL auditování

Auditování pro Azure [SQL Database](sql-database-technical-overview.md) a [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) sleduje databázové události a zapisuje je do protokolu auditu ve vašem účtu úložiště Azure, pracovním prostoru Log Analytics nebo centru událostí. 

Auditování také:

- Pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a získat přehled o nesrovnalostech a anomáliích, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení.

- Umožňuje a usnadňuje dodržování předpisů, i když soulad s těmito standardy nezaručuje. Další informace o programech Azure, které podporují dodržování standardů, najdete v [Centru zabezpečení Azure,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) kde najdete nejnovější seznam certifikací dodržování předpisů databáze SQL Database.

> [!NOTE] 
> Toto téma se týká databáze Azure SQL Database i Azure Synapse Analytics. Pro jednoduchost sql database se používá při odkazování na Azure SQL Database a Azure Synapse Analytics.

## <a name="overview"></a><a id="subheading-1"></a>Přehled

Auditování SQL Database můžete použít k těmto účelům:

- **Zachovat** záznam auditu vybraných událostí. Můžete definovat akce databáze, které se mají auditovat.
- **Zpráva** o databázové aktivitě. Můžete využít nakonfigurované sestavy a řídicí panel, abyste mohli rychle začít s vytvářením sestav aktivit a událostí.
- **Analyzujte** sestavy. Můžete vyhledávat podezřelé události, neobvyklou aktivitu a trendy.

> [!IMPORTANT]
> - Azure SQL Database auditování je optimalizované pro dostupnost & výkonu. Během velmi vysoké aktivity Azure SQL Database umožňuje operace pokračovat a nemusí zaznamenat některé auditované události.
   
## <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="subheading-8"></a>Definovat zásady auditování na úrovni serveru a databáze

Zásady auditování lze definovat pro konkrétní databázi nebo jako výchozí zásady serveru:

- Zásady serveru platí pro všechny existující a nově vytvořené databáze na serveru.

- Pokud *je auditování objektů blob serveru povoleno*, vztahuje se vždy na *databázi*. Databáze bude auditována bez ohledu na nastavení auditování databáze.

- Povolení auditování objektů blob v databázi nebo datovém skladu, kromě povolení na serveru, *nepřepíše* ani nezmění žádné nastavení auditování objektů blob serveru. Oba audity budou existovat vedle sebe. Jinými slovy, databáze je auditována dvakrát souběžně; jednou zásadami serveru a jednou podle zásad databáze.

   > [!NOTE]
   > Neměli byste povolit auditování objektů blob serveru a auditování objektů blob databáze společně, pokud:
    > - Chcete použít jiný *účet úložiště*, *dobu uchování* nebo *pracovní prostor Log Analytics* pro konkrétní databázi.
    > - Chcete auditovat typy událostí nebo kategorie pro konkrétní databázi, které se liší od ostatních databází na serveru. Můžete mít například vložení tabulky, které je třeba auditovat pouze pro určitou databázi.
   >
   > V opačném případě doporučujeme povolit pouze auditování objektů blob na úrovni serveru a ponechat auditování na úrovni databáze zakázáno pro všechny databáze.

## <a name="set-up-auditing-for-your-server"></a><a id="subheading-2"></a>Nastavení auditování serveru

Následující část popisuje konfiguraci auditování pomocí portálu Azure.

1. Přejděte na [portál Azure](https://portal.azure.com).
2. Přejděte na **auditování** pod nadpisem Zabezpečení v podokně databáze/serveru SQL.
3. Pokud dáváte přednost nastavení zásad auditování serveru, můžete na stránce auditování databáze vybrat odkaz **Zobrazit nastavení serveru.** Potom můžete zobrazit nebo upravit nastavení auditování serveru. Zásady auditování serveru platí pro všechny existující a nově vytvořené databáze na tomto serveru.

    ![Navigační podokno][2]

4. Pokud dáváte přednost povolení auditování na úrovni databáze, přepněte **auditování** na **ZAPNUTO**. Pokud je auditování serveru povoleno, bude audit nakonfigurovaný v databázi existovat souběžně s auditem serveru.

5. Máte několik možností pro konfiguraci, kde budou zapsány protokoly auditu. Protokoly můžete zapisovat do účtu úložiště Azure, do pracovního prostoru Log Analytics pro spotřebu pomocí protokolů Azure Monitor (preview) nebo do centra událostí pro spotřebu pomocí centra událostí (preview). Můžete nakonfigurovat libovolnou kombinaci těchto možností a protokoly auditu budou zapsány do každé z nich.
  
   ![možnosti úložiště](./media/sql-database-auditing-get-started/auditing-select-destination.png)
   
### <a name=""></a><a id="audit-storage-destination">Auditování do cíle úložiště</a>

Chcete-li nakonfigurovat zápis protokolů auditování do účtu úložiště, vyberte **možnost Úložiště** a **otevřete podrobnosti úložiště**. Vyberte účet úložiště Azure, do kterého se protokoly uloží, a pak vyberte dobu uchování. Pak klikněte na **OK**. Protokoly starší než doba uchování jsou odstraněny.

  ![účet úložiště](./media/sql-database-auditing-get-started/auditing_select_storage.png)

#### <a name="log-audits-to-storage-account-behind-vnet-or-firewall"></a>Protokolování auditů k účtu úložiště za virtuální sítí nebo bránou firewall

Protokoly auditování můžete zapisovat do účtu úložiště Azure za virtuální sítí nebo bránou firewall. Konkrétní pokyny najdete v [tématu Zápis auditu do účtu úložiště za virtuální síť a bránu firewall](create-auditing-storage-account-vnet-firewall.md).

#### <a name="remarks"></a>Poznámky

- Podporovány jsou všechny druhy úložiště (v1, v2, blob).
- Podporovány jsou všechny konfigurace replikace úložiště.
- Úložiště za virtuální sítí a bránou firewall je podporováno.
- **Úložiště Premium** není v současné době **podporováno**.
- **Hierarchický obor názvů** pro **účet úložiště Azure Data Lake Storage Gen2** momentálně **není podporovaný**.
- Povolení auditování v pozastaveném **datovém skladu Azure SQL** není podporováno. Chcete-li povolit auditování, pokračujte v datovém skladu.
- Výchozí hodnota pro retenční období je 0 (neomezené uchovávání informací). Tuto hodnotu můžete změnit přesunutím jezdce **Uchovávání informací (Dny)** v **nastavení úložiště** při konfiguraci účtu úložiště pro auditování.
  - Pokud změníte dobu uchovávání z 0 (neomezená uchovávání) na jinou hodnotu, vezměte prosím na vědomí, že uchovávání se bude vztahovat pouze na protokoly napsané po změně hodnoty uchovávání (protokoly napsané během období, kdy bylo uchovávání nastaveno na neomezeno, jsou zachovány i po je povoleno uchovávání informací).
- Zákazník, který chce nakonfigurovat neměnné úložiště protokolů pro události auditu na úrovni serveru nebo databáze, by se měl řídit [pokyny poskytnutými službou Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) (Ujistěte se, že jste při konfiguraci neměnného úložiště objektů blob vybrali možnost **Povolit další připojení).**
- Po konfiguraci nastavení auditování můžete zapnout novou funkci detekce hrozeb a nakonfigurovat e-maily tak, aby přijímaté výstrahy zabezpečení. Při použití detekce hrozeb obdržíte proaktivní výstrahy na neobvyklé databázové aktivity, které mohou indikovat potenciální ohrožení zabezpečení. Další informace naleznete [v tématu Začínáme s detekcí hrozeb](sql-database-threat-detection-get-started.md).
- Podrobnosti o formátu protokolu, hierarchii složky úložiště a konvencích pojmenování naleznete v [tématu Odkaz na formát protokolu auditování objektů Blob](https://go.microsoft.com/fwlink/?linkid=829599).
- Azure SQL Database Audit ukládá 4000 znaků dat pro znaková pole v záznamu auditu. Pokud **příkaz** nebo **data_sensitivity_information** hodnoty vrácené z auditovatelné akce obsahují více než 4000 znaků, budou všechna data přesahující prvních 4000 znaků **zkrácena a nebudou auditována**.
- Protokoly auditu se zapisují do **objektů Blob append** v úložišti objektů Blob Azure v rámci vašeho předplatného Azure.
- Výchozí zásady auditování zahrnují všechny akce a následující sadu skupin akcí, které budou auditovat všechny dotazy a uložené procedury provedené proti databázi, stejně jako úspěšné a neúspěšné přihlášení:
  
  - BATCH_COMPLETED_GROUP
  - SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
  - FAILED_DATABASE_AUTHENTICATION_GROUP
  
- Auditování pro různé typy akcí a skupin akcí můžete nakonfigurovat pomocí Prostředí PowerShell, jak je popsáno v části Správa auditování databáze SQL pomocí prostředí [Azure PowerShell.](#subheading-7)
- Při použití ověřování AAD se v protokolu auditu SQL *nezobrazí* záznamy neúspěšných přihlášení. Chcete-li zobrazit neúspěšné záznamy auditu přihlášení, musíte navštívit [portál Služby Azure Active Directory]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), který zaznamenává podrobnosti o těchto událostech.

### <a name=""></a><a id="audit-log-analytics-destination">Auditování do cíle log analytics</a>
  
Chcete-li nakonfigurovat zápis protokolů auditování do pracovního prostoru Analýzy protokolů, vyberte **Log Analytics (Preview)** a otevřete **podrobnosti analýzy protokolů**. Vyberte nebo vytvořte pracovní prostor Log Analytics, ve kterém budou protokoly zapsány, a klepněte na tlačítko **OK**.
    
  > [!WARNING]
   > Povolení auditování služby Log Analytics bude účtovány náklady na základě míry požití. Mějte prosím na paměti související náklady s použitím této [možnosti](https://azure.microsoft.com/pricing/details/monitor/), nebo zvažte ukládání protokolů auditu v účtu úložiště Azure.
   
   ![Pracovní prostor LogAnalytics](./media/sql-database-auditing-get-started/auditing_select_oms.png)

### <a name=""></a><a id="audit-event-hub-destination">Auditování do cíle centra událostí</a>

> [!WARNING]
> Povolení auditování na serveru, který má fond SQL na něm **má za následek fondu SQL je obnovena a znovu pozastavena znovu,** které mohou vzniknout fakturační poplatky.
> Povolení auditování v pozastaveném fondu SQL není možné. Chcete-li jej povolit, zrušit pozastavení fondu SQL.

Chcete-li nakonfigurovat zápis protokolů auditování do centra událostí, vyberte **Centrum událostí (Preview)** a otevřete **podrobnosti centra událostí**. Vyberte centrum událostí, ve kterém budou protokoly zapsány, a klepněte na tlačítko **OK**. Ujistěte se, že centrum událostí je ve stejné oblasti jako databáze a server.

   ![Eventhub](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Analýza protokolů auditu a sestav

Pokud jste se rozhodli zapsat protokoly auditu do protokolů Azure Monitor:

- Použijte [portál Azure](https://portal.azure.com).  Otevřete příslušnou databázi. V horní části stránky **Auditování** databáze klikněte na **zobrazit protokoly auditu**.

    ![zobrazit protokoly auditu](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Potom máte dva způsoby, jak zobrazit protokoly:
    
    Kliknutím na **Analýzu protokolů** v horní části stránky **Auditovat záznamy** se otevře zobrazení Protokoly v pracovním prostoru Log Analytics, kde můžete přizpůsobit časový rozsah a vyhledávací dotaz.
    
    ![otevřít v pracovním prostoru Log Analytics](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    Kliknutím na **panel Zobrazit v** horní části stránky Záznamů **auditu** se otevře řídicí panel s informacemi o protokolech auditu, kde můžete přejít k podrobnostem přehledů zabezpečení, přístupu k citlivým datům a dalším. Tento řídicí panel je navržen tak, aby vám pomohl získat přehledy zabezpečení pro vaše data.
    Můžete také přizpůsobit časový rozsah a vyhledávací dotaz. 
    ![Zobrazit řídicí panel analýzy protokolů](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Řídicí panel analýzy protokolů](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Přehledy zabezpečení analýzy protokolů](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- Případně můžete také přistupovat k protokolům auditu z okna Log Analytics. Otevřete pracovní prostor Log Analytics a v části **Obecné** klikněte na **Protokoly**. Můžete začít s jednoduchýdotaz, například: *hledání "SQLSecurityAuditEvents"* pro zobrazení protokolů auditu.
    Tady můžete také použít [protokoly Azure Monitor](../log-analytics/log-analytics-log-search.md) ke spuštění rozšířené vyhledávání na data protokolu auditu. Protokoly Azure Monitoru poskytují provozní přehledy v reálném čase pomocí integrovaného vyhledávání a vlastních řídicích panelů, které umožňují snadno analyzovat miliony záznamů ve všech vašich úlohách a serverech. Další užitečné informace o jazyce hledání protokolů protokolů Azure Monitor a příkazy, najdete v [tématu Azure Monitor protokoly hledání odkaz](../log-analytics/log-analytics-log-search.md).

Pokud jste se rozhodli zapsat protokoly auditu do centra událostí:

- Chcete-li využívat data protokolů auditu z centra událostí, budete muset nastavit datový proud, aby spotřebovával události a zapisovat je do cíle. Další informace najdete v [tématu Dokumentace k centru událostí Azure](../event-hubs/index.yml).
- Protokoly auditu v Centru událostí jsou zachyceny v těle událostí [Apache Avro](https://avro.apache.org/) a uloženy pomocí formátování JSON s kódováním UTF-8. Chcete-li číst protokoly auditu, můžete použít [Nástroje Avro](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) nebo podobné nástroje, které tento formát zpracovávají.

Pokud jste se rozhodli zapsat protokoly auditu do účtu úložiště Azure, existuje několik metod, které můžete použít k zobrazení protokolů:

> [!NOTE] 
> Auditování [replik jen pro čtení](sql-database-read-scale-out.md) je automaticky povoleno. Další podrobnosti o hierarchii složek úložiště, konvencí pojmenování a formátu protokolu naleznete ve [formátu protokolu auditu databáze SQL](sql-database-audit-log-format.md). 

- Protokoly auditu jsou agregovány v účtu, který jste zvolili během instalace. Protokoly auditu můžete prozkoumat pomocí nástroje, jako je [Například Průzkumník a údržby zařízení Azure](https://storageexplorer.com/). V úložišti Azure se protokoly auditování ukládají jako kolekce souborů objektů blob v kontejneru s názvem **sqldbauditlogs**. Další podrobnosti o hierarchii složek úložiště, konvencí pojmenování a formátu protokolu naleznete ve [formátu protokolu auditu databáze SQL](https://go.microsoft.com/fwlink/?linkid=829599).

- Použijte [portál Azure](https://portal.azure.com).  Otevřete příslušnou databázi. V horní části stránky **Auditování** databáze klikněte na **zobrazit protokoly auditu**.

    ![Navigační podokno][7]

    **Otevře** se záznamy auditu, ze kterých budete moci protokoly zobrazit.

  - Konkrétní data můžete zobrazit kliknutím na **filtr v** horní části stránky **Záznamy auditu.**
  - Mezi záznamy auditu vytvořenými *zásadami auditování serveru* a zásadami *auditu databáze* můžete přepínat přepnutím zdroje **auditu**.
  - Můžete zobrazit pouze záznamy auditu související s injekcí SQL zaškrtnutím políčka **Zobrazit pouze záznamy auditu pro injektáže SQL.**

       ![Navigační podokno][8]

- Pomocí systémové funkce **sys.fn_get_audit_file** (T-SQL) vrátíte data protokolu auditu v tabulkovém formátu. Další informace o použití této funkce naleznete [v sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Použití **souborů slučování auditu** v aplikaci SQL Server Management Studio (počínaje ssms 17):
    1. V nabídce SSMS vyberte **Soubor** > **otevřít** > **soubory auditování sloučení**.

        ![Navigační podokno][9]
    2. Otevře se dialogové okno **Přidat soubory auditu.** Vyberte jednu z možností Přidat a **zvolte,** jestli chcete sloučit soubory auditu z místního disku nebo je importovat z Azure Storage. Je nutné zadat podrobnosti o azure úložišti a klíč účtu.

    3. Po přidání všech souborů ke sloučení dokončete operaci sloučení klepnutím na **tlačítko OK.**

    4. Sloučený soubor se otevře v SSMS, kde jej můžete zobrazit a analyzovat, stejně jako exportovat do souboru XEL nebo CSV nebo do tabulky.

- Použijte Power BI. Data protokolu auditu můžete zobrazit a analyzovat v Power BI. Další informace a přístup ke šabloně ke stažení najdete [v tématu Analýza dat protokolu auditování v Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Stáhněte si soubory protokolu z kontejneru objektů blob úložiště Azure prostřednictvím portálu nebo pomocí nástroje, jako je [Azure Storage Explorer](https://storageexplorer.com/).
  - Po místním stažení souboru protokolu poklepejte na soubor a otevřete, zobrazte a analyzujte protokoly v ssms.
  - Můžete také stáhnout více souborů současně prostřednictvím Průzkumníka úložiště Azure. Chcete-li tak učinit, klepněte pravým tlačítkem myši na určitou podsložku a vyberte možnost **Uložit jako** uloženou v místní složce.

- Další metody:

  - Po stažení několika souborů nebo podsložky, která obsahuje soubory protokolu, je můžete sloučit místně, jak je popsáno v pokynech sloučit soubory auditu SSMS popsaných výše.
  - Programové zobrazení protokolů auditování objektů blob:

    - [Dotaz rozšířené události soubory](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) pomocí PowerShellu.

## <a name="production-practices"></a><a id="subheading-5"></a>Výrobní postupy

<!--The description in this section refers to preceding screen captures.-->

### <a name=""></a><a id="subheading-6">Auditování geograficky replikovaných databází</a>

S geograficky replikované databáze, když povolíte auditování v primární databázi sekundární databáze bude mít stejné zásady auditování. Auditování sekundární databáze je také možné nastavit povolením auditování na **sekundárním serveru**nezávisle na primární databázi.

- Úroveň serveru (**doporučeno**): Zapněte auditování na **primárním i** **sekundárním serveru** – primární a sekundární databáze budou auditovány nezávisle na příslušných zásadách na úrovni serveru.
- Na úrovni databáze: Auditování sekundárních databází na úrovni databáze lze konfigurovat pouze z nastavení primárního auditování databáze.
  - Auditování musí být povoleno v *samotné primární databázi*, nikoli na serveru.
  - Po auditování je povolena v primární databázi, bude také povolit v sekundární databázi.

    >[!IMPORTANT]
    >Při auditování na úrovni databáze bude nastavení úložiště pro sekundární databázi shodné s nastavením primární databáze, což způsobí meziregionální provoz. Doporučujeme povolit pouze auditování na úrovni serveru a ponechat auditování na úrovni databáze zakázáno pro všechny databáze.

### <a name=""></a><a id="subheading-6">Regenerace klíče úložiště</a>

V produkčním prostředí je pravděpodobné, že budete pravidelně obnovovat klíče úložiště. Při psaní protokolů auditu do úložiště Azure je potřeba při aktualizaci klíčů znovu uložit zásady auditování. Postup je následující:

1. Otevřete **podrobnosti úložiště**. V poli **Přístupkový klíč úložiště** vyberte **Sekundární**a klepněte na **ok**. Potom klikněte na **Uložit** v horní části stránky konfigurace auditování.

    ![Navigační podokno][5]
2. Přejděte na stránku konfigurace úložiště a znovu vygenerujte primární přístupový klíč.

    ![Navigační podokno][6]
3. Vraťte se na stránku konfigurace auditování, přepněte přístupový klíč úložiště ze sekundárního na primární a klepněte na tlačítko **OK**. Potom klikněte na **Uložit** v horní části stránky konfigurace auditování.
4. Vraťte se na stránku konfigurace úložiště a znovu vygenerujte sekundární přístupový klíč (v rámci přípravy na cyklus aktualizace dalšího klíče).

## <a name="manage-azure-sql-server-and-database-auditing-using-azure-powershell"></a><a id="subheading-7"></a>Správa auditování Azure SQL Serveru a databáze pomocí Azure PowerShellu

**Rutiny prostředí PowerShell (včetně podpory klauzule WHERE pro další filtrování):**

- [Vytvořit nebo aktualizovat zásady auditování databáze (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Vytvořit nebo aktualizovat zásady auditování serveru (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Zásady auditování databáze (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Získat zásady auditování serveru (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Odebrat zásady auditování databáze (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Odebrat zásady auditování serveru (Odebrat-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Příklad skriptu [najdete v tématu Konfigurace auditování a detekce hrozeb pomocí prostředí PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="manage-azure-sql-server-and-database-auditing-using-rest-api"></a><a id="subheading-8"></a>Správa auditování Azure SQL Serveru a databáze pomocí rozhraní REST API

**REST API**:

- [Vytvořit nebo aktualizovat zásady auditování databáze](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Vytvořit nebo aktualizovat zásady auditování serveru](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Zásady získání auditování databáze](/rest/api/sql/database%20auditing%20settings/get)
- [Získat zásady auditování serveru](/rest/api/sql/server%20auditing%20settings/get)

Rozšířené zásady s klauzulí WHERE pro další filtrování:

- [Vytvořit nebo aktualizovat zásady *rozšířeného* auditování databáze](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Vytvořit nebo aktualizovat zásady *rozšířeného* auditování serveru](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Získat *zásady rozšířeného* auditování databáze](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Získat *zásady rozšířeného* auditování serveru](/rest/api/sql/server%20auditing%20settings/get)

## <a name="manage-azure-sql-server-and-database-auditing-using-azure-resource-manager-templates"></a><a id="subheading-9"></a>Správa auditování Azure SQL Serveru a databáze pomocí šablon Azure Resource Manager

Auditování databáze Azure SQL můžete spravovat pomocí šablon [Azure Resource Manager,](../azure-resource-manager/management/overview.md) jak je znázorněno v těchto příkladech:

- [Nasazení Azure SQL Serveru s povoleným auditováním pro zápis protokolů auditování do účtu úložiště objektů Blob Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Nasazení Serveru Azure SQL Server s povoleným auditováním pro zápis protokolů auditování do analýzy protokolů](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Nasazení Azure SQL Serveru s povoleným auditováním pro zápis protokolů auditování do center událostí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Propojené ukázky jsou v externím veřejném úložišti a jsou poskytovány "tak, jak jsou", bez záruky a nejsou podporovány v žádném programu/službě podpory společnosti Microsoft.

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage Azure SQL Server and Database auditing using Azure PowerShell]: #subheading-7
[Manage SQL database auditing using REST API]: #subheading-8
[Manage Azure SQL Server and Database auditing using ARM templates]: #subheading-9

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png 
