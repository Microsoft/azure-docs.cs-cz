---
title: Použití nastavení diagnostiky pro trezory služby Recovery Services
description: Článek popisující použití starých a nových diagnostických událostí pro azure backup
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: d10bedf3818559971eff12624152d0e797f6c3cc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672781"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Použití nastavení diagnostiky pro trezory služby Recovery Services

Azure Backup odesílá diagnostické události, které lze shromažďovat a používat pro účely analýzy, upozorňování a vytváření sestav. 

Nastavení diagnostiky trezoru služby Recovery Services můžete nakonfigurovat prostřednictvím portálu Azure, přejdete do trezoru a kliknete na položku nabídky **Nastavení diagnostiky.** Kliknutím na **+ Přidat diagnostické nastavení** můžete odeslat jednu nebo více diagnostických událostí do pracovního prostoru úložiště, centra událostí nebo do pracovního prostoru Analýzy protokolů (LA).

![Okno nastavení diagnostiky](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Diagnostické události dostupné pro uživatele zálohování Azure

Azure Backup poskytuje následující diagnostické události, z nichž každá poskytuje podrobná data o konkrétní sadě artefaktů souvisejících se zálohováním:

* CoreAzureBackup
* AddonAzureBackupAlerts
* Instance AddonAzureBackupProtectedInstance
* AddonAzureZálohy
* Zásady zálohování AddonAzure
* AddonAzureBackupStorage

[Datový model pro události diagnostiky zálohování Azure](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

Data pro tyto události lze odeslat buď do účtu úložiště, pracovního prostoru LA nebo centra událostí. Pokud tato data odesíláte do pracovního prostoru LA, musíte na obrazovce **Nastavení diagnostiky** vybrat přepínač **Specifické pro daný prostředek** (další informace naleznete v následujících částech).

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Použití nastavení diagnostiky s analýzou protokolů (LA)

V souladu s plánem Azure Log Analytics, Azure Backup nyní umožňuje odesílat data diagnostiky trezoru do vyhrazených tabulek LA pro zálohování. Tyto tabulky se označují jako [tabulky specifické pro prostředky](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific).

Odeslání diagnostických dat trezoru do LA:

1.    Přejděte do trezoru a klikněte na **diagnostické nastavení**. Klepněte na tlačítko **+ Přidat diagnostické nastavení**.
2.    Pojmenujte nastavení Diagnostika.
3.    Zaškrtněte políčko **Odeslat do analýzy protokolů** a vyberte pracovní prostor Analýzy protokolů.
4.    Vyberte v přepěťovém přepínači **konkrétní prostředek** a zkontrolujte následujících šest událostí – **CoreAzureBackup** **AddonAzureBackupPolicy**, **AddonAzureBackupAlerts**, **AddonAzureBackupProtectedInstance**, **AddonAzureBackupJobs**a **AddonAzureBackupStorage**.
5.    Klikněte na **Uložit**.

![Režim specifický pro daný prostředek](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Jakmile data proudí do pracovního prostoru LA, vytvoří se ve vašem pracovním prostoru vyhrazené tabulky pro každou z těchto událostí. Můžete dotaz ovat některou z těchto tabulek přímo a také provádět spojení nebo sjednocení mezi těmito tabulkami v případě potřeby.

> [!IMPORTANT]
> Výše uvedených šest událostí, konkrétně CoreAzureBackup, AddonAzureBackupAlerts, AddonAzureBackupProtectedInstance, AddonAzureBackupJobs, AddonAzureBackupPolicy a AddonAzureBackupStorage, jsou podporovány **pouze** v režimu specifickém pro prostředky v [sestavách zálohování](https://docs.microsoft.com/azure/backup/configure-reports). **Upozorňujeme, že pokud se pokusíte odeslat data pro těchto šest událostí v režimu diagnostiky Azure, žádná data se zobrazí v sestavách zálohování.**

## <a name="legacy-event"></a>Starší událost

Tradičně všechna data diagnostiky související se zálohováním pro trezor byla obsažena v jedné události s názvem AzureBackupReport. Šest výše popsaných událostí je v podstatě rozklad všech dat obsažených v AzureBackupReport. 

V současné době budeme i nadále podporovat AzureBackupReport událost pro zpětnou kompatibilitu, v případech, kdy uživatelé mají existující vlastní dotazy na tuto událost, například vlastní výstrahy protokolu, vlastní vizualizace atd. Doporučujeme však **přejít na [nové události](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users) co nejdříve**, protože to usnadňuje práci s daty v dotazech protokolu, poskytuje lepší zjistitelnost schémat a jejich struktury, zlepšuje výkon v latenci ingestování a časy dotazů. 

**Starší události v režimu Diagnostika Azure nakonec bude zastaralé, a proto výběr nových událostí vám může pomoci vyhnout se složitým migracím později**. Naše [řešení pro vytváření přehledů,](https://docs.microsoft.com/azure/backup/configure-reports) které využívá službu Log Analytics, také přestane podporovat data ze starší události.

### <a name="steps-to-move-to-new-diagnostics-settings-to-log-analytics-workspace"></a>Postup přechodu na nové nastavení diagnostiky (do pracovního prostoru Log Analytics)

1. Určete, které trezory odesílají data do pracovního prostoru analýzy protokolů pomocí starší události a odběry, do kterých patří. Spusťte níže uvedené pracovní prostory k identifikaci těchto trezorů a předplatných:

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

2. Pomocí [integrované zásady Azure](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) Backup přidejte nové nastavení diagnostiky pro všechny trezory v zadaném oboru. Tato zásada přidá nové nastavení diagnostiky do těchto trezorů, které buď nemají nastavení diagnostiky (nebo) mají pouze starší nastavení diagnostiky. Tuto zásadu lze současně přiřadit k celému předplatnému nebo skupině prostředků. Všimněte si, že budete potřebovat přístup vlastníka ke každému předplatnému, pro které je zásada přiřazena.

Můžete zvolit samostatné nastavení diagnostiky pro AzureBackupReport a šest nových událostí, dokud nemigrujete všechny vlastní dotazy k použití dat z nových tabulek. Na obrázku níže je zobrazen příklad trezoru se dvěma diagnostickými nastaveními. První nastavení s názvem **Setting1** odesílá data události AzureBackupReport do pracovního prostoru LA v režimu AzureDiagnostics. Druhé nastavení s názvem **Setting2** odesílá data šesti nových událostí azure backup do pracovního prostoru LA v režimu specifickém pro prostředky.

![Dvě nastavení](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Událost AzureBackupReport je podporovaná **jenom** v režimu diagnostiky Azure. **Vezměte prosím na vědomí, že pokud se pokusíte odeslat data pro tuto událost v režimu specifickém pro prostředky, žádná data nebudou přetékat do pracovního prostoru LA.**

> [!NOTE]
> Přepínač pro Diagnostika Azure / Specifické pro prostředek se zobrazí pouze v případě, že uživatel zkontroluje **Odeslat do analýzy protokolů**. Chcete-li odeslat data do účtu úložiště nebo centra událostí, uživatel může jednoduše vybrat požadovaný cíl a zkontrolovat některou z požadovaných událostí bez dalších vstupů. Opět se doporučuje nevybírat starší událost AzureBackupReport, do budoucna.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Uživatelé odesílající události obnovení webu Azure do analýzy protokolů (LA)

Události zálohování Azure a obnovení webu Azure se odesílají ze stejného trezoru služby Recovery Services. Vzhledem k tomu, že Azure Site Recovery není aktuálně na palubě tabulek specifických pro prostředky, uživatelé, kteří chtějí odesílat události obnovení webu Azure do LA, jsou přesměrováni na použití **pouze** režimu diagnostiky Azure (viz obrázek níže). **Volba režimu specifického pro prostředek pro události obnovení webu Azure zabrání odeslání požadovaných dat do pracovního prostoru LA**.

![Události obnovení webu](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Chcete-li shrnout výše uvedené nuance:

* Pokud už máte la diagnostiku nastavenou pomocí Azure Diagnostics a nato jste napsali vlastní dotazy, ponechejte toto nastavení **beze změny**, dokud nemigrujete dotazy za účelem použití dat z nových událostí.
* Pokud chcete také přejít na nové tabulky (podle doporučení), vytvořte **nové** nastavení diagnostiky, zvolte **Specifické pro prostředky** a vyberte šest nových událostí, jak je uvedeno výše.
* Pokud aktuálně odesíláte události obnovení webu Azure do LA, **nezvolte** pro tyto události režim specifický pro prostředky, jinak data pro tyto události nebudou tok do pracovního prostoru LA. Místo toho vytvořte **další diagnostické nastavení**, vyberte **Diagnostika Azure**a zvolte příslušné události Azure Site Recovery.

Na obrázku na obrázku níže je zobrazen příklad uživatele, který má tři diagnostická nastavení pro trezor. První nastavení s názvem **Setting1** odesílá data z události AzureBackupReport do pracovního prostoru LA v režimu AzureDiagnostics. Druhé nastavení s názvem **Setting2** odesílá data ze šesti nových událostí zálohování Azure do pracovního prostoru LA v režimu specifickém pro prostředky. Třetí nastavení s názvem **Setting3**odesílá data z událostí Obnovení webu Azure do pracovního prostoru LA v režimu diagnostiky Azure.

![Tři nastavení](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Další kroky

[Naučte se datový model analýzy protokolů pro události diagnostiky](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
