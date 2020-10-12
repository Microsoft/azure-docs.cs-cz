---
title: Použít nastavení diagnostiky pro trezory Recovery Services
description: Tento článek popisuje, jak používat staré a nové diagnostické události pro Azure Backup.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 3d10053bae5148f33dba6d1207a81bdb16c37577
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89182594"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Použít nastavení diagnostiky pro trezory Recovery Services

Azure Backup posílá diagnostické události, které je možné shromažďovat a používat pro účely analýzy, upozorňování a vytváření sestav.

Nastavení diagnostiky pro Recovery Services trezor můžete nakonfigurovat pomocí Azure Portal tak, že do trezoru zadáte **nastavení diagnostiky a vyberete nastavení diagnostiky**. Výběr **+ Přidat nastavení diagnostiky** umožňuje odeslat jednu nebo více diagnostických událostí do účtu úložiště, do centra událostí nebo do pracovního prostoru Log Analytics.

![Podokno nastavení diagnostiky](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Události diagnostiky dostupné pro Azure Backup uživatele

Azure Backup poskytuje následující diagnostické události. Každá událost poskytuje podrobné údaje o konkrétní sadě artefaktů týkajících se zálohování:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

Pokud stále používáte [starší verzi Event](#legacy-event) AzureBackupReport, doporučujeme přepnout na použití výše uvedených událostí.

Další informace najdete v tématu [datový model pro události diagnostiky Azure Backup](./backup-azure-reports-data-model.md).

Data pro tyto události se dají odeslat do účtu úložiště, do Log Analyticsho pracovního prostoru nebo do centra událostí. Pokud tato data posíláte do pracovního prostoru Log Analytics, na obrazovce **nastavení diagnostiky** vyberte přepínač pro **konkrétní prostředek** . Další informace najdete v následujících oddílech.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Použít nastavení diagnostiky s Log Analytics

Nyní můžete použít Azure Backup k posílání diagnostických dat trezoru na vyhrazené Log Analytics tabulky pro zálohování. Tyto tabulky se nazývají [tabulky specifické pro prostředky](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).

K odeslání dat diagnostiky trezoru Log Analytics:

1. Přejít do trezoru a vybrat **nastavení diagnostiky**. Vyberte **+ Přidat nastavení diagnostiky**.
1. Zadejte název nastavení diagnostiky.
1. Zaškrtněte políčko **Odeslat do Log Analytics** a vyberte pracovní prostor Log Analytics.
1. V přepínači vyberte možnost **specifické pro prostředky** a vyberte následující šest událostí: **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage**a **AddonAzureBackupProtectedInstance**.
1. Vyberte **Uložit**.

   ![Režim specifický pro prostředky](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Po toky dat do pracovního prostoru Log Analytics se v pracovním prostoru vytvoří vyhrazené tabulky pro každou z těchto událostí. Na kteroukoli z těchto tabulek se můžete dotazovat přímo. V případě potřeby můžete v případě potřeby také v těchto tabulkách provádět spojení nebo sjednocení.

> [!IMPORTANT]
> Šest událostí, konkrétně CoreAzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupPolicy, AddonAzureBackupStorage a AddonAzureBackupProtectedInstance, jsou podporovány *pouze* v režimu specifickém pro prostředky v [sestavách zálohování](./configure-reports.md). *Pokud se pokusíte odeslat data pro tyto šest událostí v režimu diagnostiky Azure, nebudou se v sestavách zálohování zobrazovat žádná data.*

## <a name="legacy-event"></a>Starší verze události

V rámci jedné události s názvem AzureBackupReport byla tradičně obsažena všechna diagnostická data související s zálohováním pro trezor. Šest událostí popsaných zde jsou v podstatě dekompozice všech dat obsažených v AzureBackupReport.

V současné době i nadále podporujeme událost AzureBackupReport pro zpětnou kompatibilitu v případech, kdy uživatelé mají v této události existující vlastní dotazy. Příklady jsou vlastní výstrahy protokolu a vlastní vizualizace. Doporučujeme *přesunout na [nové události](#diagnostics-events-available-for-azure-backup-users) co nejdříve*. Nové události:

* V protokolových dotazech je lepší pracovat s daty.
* Poskytněte lepší zjistitelnost schémat a jejich struktury.
* Zvyšte výkon v rámci latence příjmu a doby dotazování.

*Starší událost v režimu diagnostiky Azure bude nakonec zastaralá. Výběr nových událostí vám může přispět k tomu, abyste předešli složitým migracím později*. Naše [řešení pro vytváření sestav](./configure-reports.md) , které používá Log Analytics, také zastaví podpůrná data z předchozí události.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Postup přesunutí na nové nastavení diagnostiky pro Log Analytics pracovní prostor

1. Určete, které trezory odesílají data do Log Analytics pracovní prostory pomocí starší události a předplatných, ke kterým patří. Spuštěním následujícího dotazu v každém z vašich pracovních prostorů Identifikujte tyto trezory a odběry.

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
        // Some Workspaces will not have AzureDiagnostics Table, so you need to use isFuzzy
    let CombinedVaultTable = (){
        union isfuzzy = true
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

    Níže je snímek obrazovky dotazu spouštěného v jednom z pracovních prostorů:

    ![Dotaz na pracovní prostor](./media/backup-azure-diagnostics-events/workspace-query.png)

2. Pomocí [integrovaných definic Azure Policy](./azure-policy-configure-diagnostics.md) v Azure Backup můžete přidat nové nastavení diagnostiky pro všechny trezory v zadaném oboru. Tato zásada přidá nové nastavení diagnostiky do trezorů, které nemají nastavení diagnostiky nebo mají pouze starší nastavení diagnostiky. Tato zásada se dá přiřadit k celému předplatnému nebo skupině prostředků v určitou dobu. Musíte mít oprávnění vlastníka pro každé předplatné, ke kterému je zásada přiřazená.

Můžete se rozhodnout, že budete mít samostatná nastavení diagnostiky pro AzureBackupReport a šest nových událostí, dokud nemigrujete všechny vlastní dotazy, aby používaly data z nových tabulek. Následující obrázek ukazuje příklad trezoru, který má dvě nastavení diagnostiky. První nastavení s názvem **Setting1**odesílá data události AzureBackupReport do pracovního prostoru Log Analytics v režimu diagnostiky Azure. Druhé nastavení s názvem **Setting2**odesílá data o šesti nových událostech Azure Backup do pracovního prostoru Log Analytics v režimu specifickém pro prostředky.

![Dvě nastavení](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Událost AzureBackupReport se podporuje *jenom* v režimu diagnostiky Azure. *Pokud se pokusíte odeslat data pro tuto událost v režimu specifickém pro prostředky, nebudou do Log Analyticsho pracovního prostoru naplněna žádná data.*

> [!NOTE]
> Přepínač přepnout pro **diagnostiku Azure** nebo **prostředek** se zobrazí jenom v případě, že uživatel vybere **Odeslat Log Analytics**. Pokud chcete odesílat data do účtu úložiště nebo centra událostí, uživatel vybere požadované místo a vybere zaškrtávací políčka pro jakoukoli z požadovaných událostí bez dalších vstupů. Znovu doporučujeme, abyste nezvolili starší verzi Event AzureBackupReport.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Odeslat Azure Site Recovery události do Log Analytics

Události Azure Backup a Azure Site Recovery jsou odesílány ze stejného trezoru Recovery Services. Azure Site Recovery není aktuálně k dispozici pro tabulky specifické pro prostředky. Uživatelům, kteří chtějí odesílat Azure Site Recovery události Log Analytics jsou směrováni *pouze*na použití režimu Azure Diagnostics, jak je znázorněno na obrázku. *Výběr režimu specifického pro prostředky pro Azure Site Recovery události zabrání odeslání požadovaných dat do pracovního prostoru Log Analytics*.

![Události Site Recovery](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Shrnutí:

* Pokud už máte Log Analytics Diagnostika nastavená s Azure Diagnostics a máte na ní vytvořené vlastní dotazy, nechte toto nastavení *beze změny* , dokud nemigrujete dotazy na použití dat z nových událostí.
* Pokud chcete také zařadit do nových tabulek, protože doporučujeme, vytvořit **nové** nastavení diagnostiky, vybrat **konkrétní prostředky**a vybrat šest nových událostí.
* Pokud aktuálně odesíláte události Azure Site Recovery *Log Analytics, nevybírejte pro* tyto události režim pro konkrétní prostředky. Jinak data pro tyto události nebudou zacházet do pracovního prostoru Log Analytics. Místo toho vytvořte další nastavení diagnostiky, vyberte **Azure Diagnostics**a vyberte relevantní události Azure Site Recovery.

Následující obrázek ukazuje příklad uživatele, který má tři nastavení diagnostiky pro trezor. První nastavení s názvem **Setting1**odesílá data z události AzureBackupReport do pracovního prostoru Log Analytics v režimu diagnostiky Azure. Druhé nastavení s názvem **Setting2**odesílá data z šesti nových událostí Azure Backup do pracovního prostoru Log Analytics v režimu specifickém pro prostředky. Třetí nastavení s názvem **Setting3**odesílá data z událostí Azure Site Recovery do pracovního prostoru Log Analytics v režimu diagnostiky Azure.

![Tři nastavení](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Další kroky

[Naučte se Log Analytics datový model pro diagnostické události](./backup-azure-reports-data-model.md)
