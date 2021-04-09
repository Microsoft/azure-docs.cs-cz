---
title: Protokoly Update Management Azure Automation dotazů
description: V tomto článku se dozvíte, jak zadat dotaz na protokoly pro Update Management v pracovním prostoru Log Analytics.
services: automation
ms.subservice: update-management
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 5eb0c7d72896cc9a27907743b1b9c3d5a40614dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592855"
---
# <a name="query-update-management-logs"></a>Dotazování na protokoly Update Managementu

Kromě podrobností, které jsou k dispozici během nasazení Update Management, můžete hledat v protokolech uložených v pracovním prostoru Log Analytics. Pokud chcete v protokolech Hledat z účtu Automation, vyberte **Update Management** a otevřete Log Analytics pracovní prostor přidružený k vašemu nasazení.

Můžete také přizpůsobit dotazy protokolu nebo je použít z různých klientů. Viz [dokumentace k rozhraní API pro hledání Log Analytics](https://dev.loganalytics.io/).

## <a name="query-update-records"></a>Dotazy na aktualizace záznamů

Update Management shromažďuje záznamy pro virtuální počítače s Windows a Linux a datové typy, které se zobrazí ve výsledcích prohledávání protokolu. Tyto záznamy jsou popsány v následujících částech.

### <a name="query-required-updates"></a>Dotaz na požadované aktualizace

Vytvoří se záznam s typem `RequiredUpdate` , který představuje aktualizace vyžadované počítačem. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|----------|-------------|
| Počítač | Plně kvalifikovaný název domény počítače pro vytváření sestav. |
| KBID | ID článku znalostní báze pro Windows Update |
| ManagementGroupName | Název Operations Manager skupiny pro správu nebo pracovního prostoru Log Analytics. |
| Produkt | Produkty, pro které je aktualizace platná. |
| PublishDate | Datum, kdy je aktualizace připravena ke stažení a instalaci z web Windows Update. |
| Server | | 
| SourceHealthServiceId | Jedinečný identifikátor představující Log Analytics ID agenta Windows. |
| SourceSystem | *OperationsManager* |
| TenantId | Jedinečný identifikátor, který představuje instanci vaší organizace Azure Active Directory. |
| TimeGenerated | Datum a čas vytvoření záznamu |
| Typ | *Aktualizace* |
| UpdateClassification | Určuje typ aktualizací, které lze použít. Ve Windows:<br> *Důležité aktualizace*<br> *Aktualizace zabezpečení*<br> *Kumulativní aktualizace*<br> *Balíčky funkcí*<br> *Aktualizace Service Pack*<br> *Aktualizace definic*<br> *Nástroje*<br> *Aktualizace*. V Linuxu:<br> *Důležité aktualizace a aktualizace zabezpečení*<br> *Další* |
| UpdateSeverity | Hodnocení závažnosti pro chybu zabezpečení. Hodnoty jsou:<br> *Kritické*<br> *Důležité upozornění*<br> *Pokročilé*<br> *Nízká* |
| UpdateTitle | Název aktualizace|

### <a name="query-update-record"></a>Záznam aktualizace dotazu

Vytvoří se záznam s typem `Update` , který představuje dostupné aktualizace a stav instalace pro daný počítač. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|----------|-------------|
| ApprovalSource | Platí jenom pro operační systém Windows. Zdroj schválení záznamu Hodnota je Microsoft Update. |
| Schválené | True, pokud je záznam schválen, nebo jinak false. |
| Klasifikace | Klasifikace schválení. Hodnota je Updates. |
| Počítač | Plně kvalifikovaný název domény počítače pro vytváření sestav. |
| ComputerEnvironment | Hlediska. Možné hodnoty jsou Azure nebo mimo Azure. |
| MSRCBulletinID | Číslo ID bulletinu zabezpečení |
| MSRCSeverity | Hodnocení závažnosti pro chybu zabezpečení. Hodnoty jsou:<br> Kritické<br> Důležité<br> Pokročilé<br> Nízká |  
| KBID | ID článku znalostní báze pro Windows Update |
| ManagementGroupName | Název Operations Manager skupiny pro správu nebo pracovního prostoru Log Analytics. |
| UpdateID | Jedinečný identifikátor aktualizace softwaru. |
| RevisionNumber | Číslo revize konkrétní revize aktualizace. |
| Volitelné | True, pokud je záznam volitelný, nebo jinak false. |
| RebootBehavior | Chování při restartování po instalaci nebo odinstalaci aktualizace. |
| _ResourceId | Jedinečný identifikátor prostředku přidruženého k záznamu |
| Typ | Typ záznamu Hodnota je aktualizovat. |
| VMUUID | Jedinečný identifikátor pro virtuální počítač. |
| MG | Jedinečný identifikátor pro skupinu pro správu nebo pracovní prostor Log Analytics. |
| TenantId | Jedinečný identifikátor, který představuje instanci Azure Active Directory vaší organizace. |
| SourceSystem | Zdrojový systém záznamu Hodnota je `OperationsManager`. |
| TimeGenerated | Datum a čas vytvoření záznamu. |
| SourceComputerId | Jedinečný identifikátor představující zdrojový počítač. |
| Nadpis | Název aktualizace |
| PublishedDate (UTC) | Datum, kdy je aktualizace připravena ke stažení a instalaci z web Windows Update.  |
| UpdateState | Aktuální stav aktualizace. |
| Produkt | Produkty, pro které je aktualizace platná. |
| SubscriptionId | Jedinečný identifikátor předplatného Azure. |
| ResourceGroup | Název skupiny prostředků, do které prostředek patří |
| ResourceProvider | Poskytovatel prostředků. |
| Prostředek | Název prostředku. |
| ResourceType | Typ prostředku. |

### <a name="query-update-agent-record"></a>Záznam agenta aktualizace dotazu

Vytvoří se záznam s typem `UpdateAgent` , který poskytuje podrobnosti o agentovi aktualizace v počítači. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | |
| AutomaticUpdateEnabled | |
| Počítač | Plně kvalifikovaný název domény počítače pro vytváření sestav. |
| DaySinceLastUpdateBucket | |
| ManagementGroupName | Název Operations Manager skupiny pro správu nebo pracovního prostoru Log Analytics. |
| OSVersion | Verze operačního systému. |
| Server | |
| SourceHealthServiceId | Jedinečný identifikátor představující Log Analytics ID agenta Windows. |
| SourceSystem | Zdrojový systém záznamu Hodnota je `OperationsManager`. |
| TenantId | Jedinečný identifikátor, který představuje instanci Azure Active Directory vaší organizace. |
| TimeGenerated | Datum a čas vytvoření záznamu. |
| Typ | Typ záznamu Hodnota je aktualizovat. |
| WindowsUpdateAgentVersion | Verze agenta web Windows Update. |
| WSUSServer | Chyby, pokud má agent web Windows Update problém, který vám pomůže při řešení potíží. |

### <a name="query-update-deployment-status-record"></a>Záznam stavu nasazení aktualizace dotazu

Vytvoří se záznam s typem `UpdateRunProgress` , který poskytuje stav nasazení aktualizace pro plánované nasazení v počítači. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|----------|-------------|
| Počítač | Plně kvalifikovaný název domény počítače pro vytváření sestav. |
| ComputerEnvironment | Hlediska. Hodnoty jsou Azure nebo mimo Azure. |
| CorrelationId | Jedinečný identifikátor spuštění úlohy Runbooku pro aktualizaci. |
| EndTime | Čas ukončení procesu synchronizace. *Tato vlastnost se v tuto chvíli nepoužívá. Viz TimeGenerated.* |
| ErrorResult | Při instalaci aktualizace se vygeneroval kód chyby web Windows Update. |
| InstallationStatus | Možné stavy instalace aktualizace na klientském počítači,<br> `NotStarted` -úloha se ještě neaktivovala.<br> `FailedToStart` -nelze spustit úlohu v počítači.<br> `Failed` -úloha se spustila, ale došlo k chybě s výjimkou.<br> `InProgress` -úloha probíhá.<br> `MaintenanceWindowExceeded` – Pokud bylo provádění zbývající, ale interval časového období údržby byl dosažen.<br> `Succeeded` -úloha byla úspěšná.<br> `InstallFailed` -aktualizaci se nepovedlo úspěšně nainstalovat.<br> `NotIncluded`<br> `Excluded` |
| KBID | ID článku znalostní báze pro Windows Update |
| ManagementGroupName | Název Operations Manager skupiny pro správu nebo pracovního prostoru Log Analytics. |
| OSType | Typ operačního systému. Hodnoty jsou Windows nebo Linux. |
| Produkt | Produkty, pro které je aktualizace platná. |
| Prostředek | Název prostředku. |
| ResourceId | Jedinečný identifikátor prostředku přidruženého k záznamu |
| ResourceProvider | Poskytovatel prostředků. |
| ResourceType | Typ prostředku. |
| SourceComputerId | Jedinečný identifikátor představující zdrojový počítač. |
| SourceSystem | Zdrojový systém záznamu Hodnota je `OperationsManager`. |
| StartTime | Čas, kdy má být aktualizace naplánována k instalaci. *Tato vlastnost se v tuto chvíli nepoužívá. Viz TimeGenerated.* |
| SubscriptionId | Jedinečný identifikátor předplatného Azure. |
| SucceededOnRetry | Hodnota označující, zda se při prvním pokusu spuštění aktualizace nezdařila, a aktuální operace je pokus o opakování. |
| TimeGenerated | Datum a čas vytvoření záznamu. |
| Nadpis | Název aktualizace |
| Typ | Typ aktualizace: Hodnota je `UpdateRunProgress`. |
| UpdateID naformátovat | Jedinečný identifikátor aktualizace softwaru. |
| VMUUID | Jedinečný identifikátor pro virtuální počítač. |
| ResourceId | Jedinečný identifikátor prostředku přidruženého k záznamu |

### <a name="query-update-summary-record"></a>Souhrnný záznam aktualizace dotazu

Vytvoří se záznam s typem `UpdateSummary` , který poskytuje souhrn aktualizací podle počítače. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis |
|----------|-------------|
| Počítač | Plně kvalifikovaný název domény počítače pro vytváření sestav. |
| ComputerEnvironment | Hlediska. Hodnoty jsou Azure nebo mimo Azure. |
| CriticalUpdatesMissing | Počet neodpovídajících důležitých aktualizací, které chybí. |
| ManagementGroupName | Název Operations Manager skupiny pro správu nebo pracovního prostoru Log Analytics. |
| NETRuntimeVersion | Verze .NET Framework nainstalovaná na počítači s Windows. |
| OldestMissingSecurityUpdateBucket | Specifikátor nejstaršího chybějícího bezpečnostního intervalu. Hodnoty jsou:<br> Nedávná, pokud je hodnota kratší než 30 dní<br> před 30 dny<br> před 60 dny<br> před 90 dny<br> před 120 dny<br> před 150 dny<br> před 180 dny<br> Starší, pokud je hodnota větší než 180 dní. |
| OldestMissingSecurityUpdateInDays | Celkový počet dní pro nejstarší aktualizaci, která se zjistila jako platná, která není nainstalovaná. |
| OsVersion | Verze operačního systému. |
| OtherUpdatesMissing | Počet chybějících zjištěných aktualizací |
| Prostředek | Název prostředku pro záznam |
| ResourceGroup | Název skupiny prostředků, která obsahuje prostředek. |
| ResourceId | Jedinečný identifikátor prostředku přidruženého k záznamu |
| ResourceProvider | Poskytovatel prostředků. |
| ResourceType | Typ prostředku. |
| RestartPending | True, pokud se čeká na restartování, nebo jinak false. |
| SecurityUpdatesMissing | Počet případných chybějících aktualizací zabezpečení|
| SourceComputerId | Jedinečný identifikátor pro virtuální počítač. |
| SourceSystem | Zdrojový systém záznamu Hodnota je `OpsManager`. |
| SubscriptionId | Jedinečný identifikátor předplatného Azure. |
| TimeGenerated | Datum a čas vytvoření záznamu. |
| TotalUpdatesMissing | Celkový počet chybějících aktualizací, které jsou k dispozici. |
| Typ | Typ záznamu Hodnota je `UpdateSummary`. |
| VMUUID | Jedinečný identifikátor pro virtuální počítač. |
| WindowsUpdateAgentVersion | Verze agenta web Windows Update. |
| WindowsUpdateSetting | Stav agenta web Windows Update. Možné hodnoty:<br> `Scheduled installation`<br> `Notify before installation`<br> `Error returned from unhealthy WUA agent` |
| WSUSServer | Chyby, pokud má agent web Windows Update problém, který vám pomůže při řešení potíží. |
| _ResourceId | Jedinečný identifikátor prostředku přidruženého k záznamu |

## <a name="sample-queries"></a>Ukázkové dotazy

Následující části obsahují Ukázky dotazů protokolu pro záznamy aktualizací, které jsou shromažďovány pro Update Management.

### <a name="confirm-that-non-azure-machines-are-enabled-for-update-management"></a>Potvrďte, že jsou pro Update Management povolené jiné počítače než Azure.

Pokud chcete potvrdit, že přímo připojené počítače komunikují s protokoly Azure Monitor, spusťte jedno z následujících hledání protokolu.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Na počítači s Windows můžete zkontrolovat následující informace a ověřit připojení agenta s protokoly Azure Monitor:

1. V Ovládacích panelech otevřete **Microsoft Monitoring Agent**. Na kartě **Azure Log Analytics** se v agentovi zobrazí následující zpráva: **Microsoft Monitoring Agent se úspěšně připojil k Log Analytics**.

1. Otevřete protokol událostí systému Windows. V části **Application and Services Logs\Operations Manager** vyhledejte události ID 3000 a id události 5002 ze zdrojového **konektoru služby**. Tyto události znamenají, že se počítač zaregistroval do pracovního prostoru služby Log Analytics a přijímá konfiguraci.

Pokud Agent nemůže komunikovat s protokoly Azure Monitor a Agent je nakonfigurován pro komunikaci s internetem prostřednictvím brány firewall nebo proxy server, ověřte, zda je správně nakonfigurována brána firewall nebo proxy server. Informace o tom, jak ověřit, jestli je brána firewall nebo proxy server správně nakonfigurovaná, najdete v tématu [Konfigurace sítě pro agenta Windows](../../azure-monitor/agents/agent-windows.md) nebo [konfiguraci sítě pro agenta Linux](../../azure-monitor/vm/quick-collect-linux-computer.md).

> [!NOTE]
> Pokud jsou vaše systémy Linux nakonfigurované pro komunikaci s proxy serverem nebo Log Analytics bránou a povolujete Update Management, aktualizujte `proxy.conf` oprávnění tak, aby skupině omiuser udělil oprávnění ke čtení souboru pomocí následujících příkazů:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nově přidaní agenti Linux zobrazují po provedení posouzení stav **aktualizovaný** . Tento proces může trvat až 6 hodin.

Pokud chcete ověřit, že skupina pro správu Operations Manager komunikuje s protokoly Azure Monitor, přečtěte si téma [ověření Operations Manager integrace s protokoly Azure monitor](../../azure-monitor/agents/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

### <a name="single-azure-vm-assessment-queries-windows"></a>Dotazy na vyhodnocení pro jeden virtuální počítač Azure (Windows)

Hodnotu VMUUID nahraďte identifikátorem GUID virtuálního počítače, na který se dotaz provádí. VMUUID, která se má použít, můžete najít spuštěním následujícího dotazu v protokolech Azure Monitor: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Chybějící souhrn aktualizací

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Seznam chybějících aktualizací

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>Dotazy na posuzování virtuálních počítačů Azure (Linux)

U některých Linux distribuce se [neshoduje s hodnotou](https://en.wikipedia.org/wiki/Endianness) VMUUID, která pochází z Azure Resource Manager a co je uložená v protokolech Azure monitor. Následující dotaz vyhledá shodu na základě typu endian. Vyměňte hodnoty VMUUID pomocí formátu big-endian a little endian identifikátoru GUID, aby byly výsledky vráceny správně. VMUUID, která se má použít, můžete najít spuštěním následujícího dotazu v protokolech Azure Monitor: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Chybějící souhrn aktualizací

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Seznam chybějících aktualizací

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>Dotazy k vyhodnocení pro více virtuálních počítačů

#### <a name="computers-summary"></a>Souhrn počítačů

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount
```

#### <a name="missing-updates-summary"></a>Chybějící souhrn aktualizací

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>Seznam počítačů

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>Seznam chybějících aktualizací

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>Další kroky

* Podrobnosti o protokolech Azure Monitor najdete v tématu [protokoly Azure monitor](../../azure-monitor/logs/log-query-overview.md).
* Nápovědu k výstrahám najdete v tématu [Konfigurace výstrah](configure-alerts.md).
