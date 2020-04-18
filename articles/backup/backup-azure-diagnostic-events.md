---
title: Použití nastavení diagnostiky pro trezory služby Recovery Services
description: Tento článek popisuje, jak používat staré a nové diagnostické události pro Azure Backup.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: f11c9d2a5b48b9cd27ac6e6f8a00eb5ac0ac7a9d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617302"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Použití nastavení diagnostiky pro trezory služby Recovery Services

Azure Backup odesílá diagnostické události, které lze shromažďovat a používat pro účely analýzy, výstrahy a vytváření sestav.

Nastavení diagnostiky trezoru služby Recovery Services můžete nakonfigurovat prostřednictvím portálu Azure tak, že přejdete do trezoru a vyberete **nastavení diagnostiky**. Výběr + **Přidat diagnostické nastavení** umožňuje odeslat jednu nebo více diagnostických událostí do účtu úložiště, centra událostí nebo do pracovního prostoru Analýzy protokolů.

![Podokno nastavení diagnostiky](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Diagnostické události dostupné pro uživatele služby Azure Backup

Azure Backup poskytuje následující diagnostické události. Každá událost poskytuje podrobná data o konkrétní sadě artefaktů souvisejících se zálohováním:

* CoreAzureBackup
* AddonAzureBackupAlerts
* Instance AddonAzureBackupProtectedInstance
* AddonAzureZálohy
* Zásady zálohování AddonAzure
* AddonAzureBackupStorage

Další informace najdete [v tématu Datový model pro události diagnostiky zálohování Azure](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model).

Data pro tyto události lze odeslat buď do účtu úložiště, do pracovního prostoru Log Analytics nebo do centra událostí. Pokud tato data odesíláte do pracovního prostoru Analýzy protokolů, vyberte na obrazovce **Nastavení diagnostiky** přepínač **Specifické pro daný prostředek.** Další informace naleznete v následujících částech.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Použití nastavení diagnostiky pomocí analýzy protokolů

Nyní můžete použít Azure Backup k odeslání dat diagnostiky trezoru do vyhrazených tabulek Analýzy protokolů pro zálohování. Tyto tabulky se nazývají [tabulky specifické pro prostředky](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific).

Odeslání diagnostických dat trezoru do služby Log Analytics:

1. Přejděte do trezoru a vyberte **Nastavení diagnostiky**. Vyberte **+ Přidat diagnostické nastavení**.
1. Pojmenujte nastavení diagnostiky.
1. Zaškrtněte políčko **Odeslat do analýzy protokolů** a vyberte pracovní prostor Analýzy protokolů.
1. Vyberte v přepínači **konkrétní prostředek** a vyberte následujících šest událostí: **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage**a **AddonAzureBackupProtectedInstance**.
1. Vyberte **Uložit**.

   ![Režim specifický pro daný prostředek](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Po toku dat do pracovního prostoru Log Analytics, vyhrazené tabulky pro každou z těchto událostí jsou vytvořeny ve vašem pracovním prostoru. Můžete dotaz ovat přímo z těchto tabulek. V případě potřeby můžete také provádět spojení nebo sjednocení mezi těmito tabulkami.

> [!IMPORTANT]
> Šest událostí, konkrétně CoreAzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupPolicy, AddonAzureBackupStorage a AddonAzureBackupProtectedInstance, je podporováno *pouze* v režimu specifickém pro prostředky v [sestavách zálohování](https://docs.microsoft.com/azure/backup/configure-reports). *Pokud se pokusíte odeslat data pro těchto šest událostí v režimu diagnostiky Azure, žádná data se zobrazí v sestavách zálohování.*

## <a name="legacy-event"></a>Starší událost

Všechna diagnostická data související se zálohováním pro trezor byla tradičně obsažena v jedné události nazvané AzureBackupReport. Šest událostí popsaných zde jsou v podstatě rozklad u všech dat obsažených v AzureBackupReport. 

V současné době budeme i nadále podporovat AzureBackupReport událost pro zpětnou kompatibilitu v případech, kdy uživatelé mají existující vlastní dotazy na tuto událost. Příkladem jsou vlastní výstrahy protokolu a vlastní vizualizace. *Doporučujeme, abyste se k [novým událostem](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users) přesunuli co nejdříve*. Nové události:

- Usnadněte práci s daty v dotazech protokolu.
- Poskytují lepší zjistitelnost schémat a jejich strukturu.
- Zlepšit výkon v latenci ingestování a časy dotazů. 

*Starší události v režimu diagnostiky Azure se nakonec zastaralá. Výběr nových událostí vám může pomoci vyhnout se složitějším migracím později*. Naše [řešení pro vytváření přehledů,](https://docs.microsoft.com/azure/backup/configure-reports) které používá Log Analytics, také přestane podporovat data ze starší události.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Postup přechodu na nové nastavení diagnostiky pro pracovní prostor Log Analytics

1. Zjistěte, které trezory odesílají data do pracovních prostorů Log Analytics pomocí starší události a předplatných, do kterých patří. Spusťte následující pracovní prostory k identifikaci těchto trezorů a předplatných.

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId    
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault" 
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, hence you need to use isFuzzy
    let CombinedVaultTable = (){
        CombinedTable | union isfuzzy = true 
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti ( 
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

1. Pomocí [předdefinované zásady Azure](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) v Azure Backup přidat nové nastavení diagnostiky pro všechny trezory v zadaném oboru. Tato zásada přidá nové nastavení diagnostiky do trezorů, které buď nemají nastavení diagnostiky, nebo mají pouze starší nastavení diagnostiky. Tuto zásadu lze současně přiřadit k celému předplatnému nebo skupině prostředků. Musíte mít přístup vlastníka ke každému předplatnému, pro které je zásada přiřazena.

Můžete zvolit samostatné nastavení diagnostiky pro AzureBackupReport a šest nových událostí, dokud nemigrujete všechny vlastní dotazy k použití dat z nových tabulek. Následující obrázek znázorňuje příklad trezoru, který má dvě diagnostická nastavení. První nastavení s názvem **Setting1**odesílá data události AzureBackupReport do pracovního prostoru Log Analytics v režimu diagnostiky Azure. Druhé nastavení s názvem **Setting2**odesílá data šesti nových událostí zálohování Azure do pracovního prostoru Analýzy protokolů v režimu specifickém pro prostředky.

![Dvě nastavení](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Událost AzureBackupReport je podporovaná *jenom* v režimu diagnostiky Azure. *Pokud se pokusíte odeslat data pro tuto událost v režimu specifickém pro prostředek, žádná data budou tok do pracovního prostoru Log Analytics.*

> [!NOTE]
> Přepínač pro **diagnostiku Azure** nebo **prostředek specifické** pouze v případě, že uživatel vybere **Odeslat do analýzy protokolu**. Chcete-li odeslat data do účtu úložiště nebo centra událostí, uživatel vybere požadovaný cíl a zaškrtne políčka pro některou z požadovaných událostí bez dalších vstupů. Znovu doporučujeme nezvolte starší událost AzureBackupReport do budoucna.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Odeslání událostí obnovení webu Azure do analýzy protokolů

Události zálohování Azure a Obnovení webu Azure se odesílají ze stejného trezoru služby Recovery Services. Azure Site Recovery není momentálně k dispozici pro tabulky specifické pro prostředky. Uživatelé, kteří chtějí odesílat události Azure Site Recovery do Log Analytics, jsou přesměrováni na použití *režimu*diagnostiky Azure , jak je znázorněno na bitové kopii. *Výběr režimu specifického pro prostředky pro události obnovení webu Azure zabrání odeslání požadovaných dat do pracovního prostoru Log Analytics*.

![Události obnovení webu](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Shrnutí:

* Pokud už máte diagnostiku Log Analytics nastavenou pomocí Diagnostika Azure a najdete k ní vlastní dotazy, ponechejte toto nastavení *beze změny,* dokud nemigrujete dotazy za účelem použití dat z nových událostí.
* Pokud chcete také přejít na palubě do nových tabulek, jak doporučujeme, vytvořte **nové** nastavení diagnostiky, vyberte **specifické pro prostředek**a vyberte šest nových událostí.
* Pokud aktuálně odesíláte události obnovení webu Azure do analýzy protokolů, *nevybírejte* pro tyto události režim specifický pro prostředky. V opačném případě data pro tyto události nebudou proudit do pracovního prostoru Log Analytics. Místo toho vytvořte další diagnostické nastavení, vyberte **diagnostiku Azure**a vyberte příslušné události Azure Site Recovery.

Následující obrázek ukazuje příklad uživatele, který má tři nastavení diagnostiky pro trezor. První nastavení s názvem **Setting1**odesílá data z události AzureBackupReport do pracovního prostoru Log Analytics v režimu diagnostiky Azure. Druhé nastavení s názvem **Setting2**odesílá data ze šesti nových událostí zálohování Azure do pracovního prostoru Analýzy protokolů v režimu specifickém pro prostředky. Třetí nastavení s názvem **Setting3**odesílá data z událostí Azure Site Recovery do pracovního prostoru Analýzy protokolů v režimu diagnostiky Azure.

![Tři nastavení](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Další kroky

[Naučte se datový model Log Analytics pro diagnostické události](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
