---
title: Dotazování protokolů služby Azure Update Management
description: Tento článek popisuje, jak zadat dotaz na protokoly pro Update Management v pracovním prostoru Log Analytics.
services: automation
ms.subservice: update-management
ms.date: 03/11/2020
ms.topic: conceptual
ms.openlocfilehash: f31168d47f31d8e740c95cb3d9e449f473cc78dc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79216849"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Dotaz na aktualizace záznamů pro Update Management v protokolech Azure Monitor

Kromě podrobností, které jsou k dispozici v řešení Update Management, můžete vyhledávat v protokolech uložených v pracovním prostoru Log Analytics. Na stránce řešení vyberte v levém podokně **protokoly**. Otevře se stránka **hledání protokolu** .

Můžete se také seznámit s postupem přizpůsobení dotazů nebo jejich použití od různých klientů a dalších návštěv: [Log Analytics dokumentaci rozhraní API pro hledání](https://dev.loganalytics.io/).

## <a name="update-records"></a>Záznamy typu Aktualizace

Záznamy shromažďované Update Management pro virtuální počítače s Windows a Linux a typy dat, které se zobrazí ve výsledcích prohledávání protokolu. Tyto záznamy jsou popsány v následujících částech.

### <a name="required-updates"></a>Požadované aktualizace

Vytvoří se záznam s typem `RequiredUpdate`, který představuje aktualizace vyžadované počítačem. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis | 
|----------|-------------|
| Computer | Plně kvalifikovaný název domény počítače pro vytváření sestav. |
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
| UpdateClassification | Určuje typ aktualizací, které lze použít. Ve Windows:<br> *Důležité aktualizace*<br> *Aktualizace zabezpečení*<br> *Kumulativní aktualizace*<br> *Balíčky funkcí*<br> *Aktualizace Service Pack*<br> *Aktualizace definic*<br> *Nástroje*<br> *Aktualizace*. Pro Linux:<br> *Kritické aktualizace a aktualizace zabezpečení*<br> *Ostatní* |
| UpdateSeverity | Hodnocení závažnosti pro chybu zabezpečení. Hodnoty jsou:<br> *Kritické*<br> *Důležité upozornění*<br> *Mírná*<br> *Slab* |
| UpdateTitle | Název aktualizace|

### <a name="update"></a>Aktualizovat

Vytvoří se záznam s typem `Update`, který představuje dostupné aktualizace a stav instalace pro daný počítač. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis | 
|----------|-------------|
| ApprovalSource | Platí jenom pro operační systém Windows. Hodnota je *Microsoft Update*. |
| Schváleno | *True* nebo *false* |
| Klasifikace | *Aktualizace* |
| Computer | Plně kvalifikovaný název domény počítače pro vytváření sestav. |
| ComputerEnvironment | *Azure* nebo *mimo Azure*. |
| MSRCBulletinID | Číslo ID bulletinu zabezpečení | 
| MSRCSeverity | Hodnocení závažnosti pro chybu zabezpečení. Hodnoty jsou:<br> *Kritické*<br> *Důležité upozornění*<br> *Mírná*<br> *Slab* |  
| KBID | ID článku znalostní báze pro Windows Update |
| ManagementGroupName | Název Operations Manager skupiny pro správu nebo pracovního prostoru Log Analytics. |
| UpdateID | Jedinečný identifikátor aktualizace softwaru. |
| RevisionNumber | Číslo revize konkrétní revize aktualizace. |
| Volitelné | *True* nebo *false* | 
| RebootBehavior | Chování při restartování po instalaci nebo odinstalaci aktualizace. |
| _ResourceId | Jedinečný identifikátor prostředku, ke kterému je záznam přidružen. |
| Typ | *Aktualizace* |
| VMUUID | Jedinečný identifikátor pro virtuální počítač. |
| MG | Jedinečný identifikátor pro skupinu pro správu nebo pracovní prostor Log Analytics. | 
| TenantId | Jedinečný identifikátor, který představuje instanci vaší organizace Azure Active Directory. | 
| SourceSystem | *OperationsManager* | 
| TimeGenerated | Datum a čas vytvoření záznamu | 
| SourceComputerId | Jedinečný identifikátor představující zdrojový počítač. | 
| Název | Název aktualizace |
| PublishedDate (UTC) | Datum, kdy je aktualizace připravena ke stažení a instalaci z web Windows Update.  |
| UpdateState | Aktuální stav aktualizace. | 
| Produkt | Produkty, pro které je aktualizace platná. |
| SubscriptionId | Jedinečný identifikátor předplatného Azure. | 
| ResourceGroup | Název skupiny prostředků, ve které je prostředek členem | 
| ResourceProvider | Určuje poskytovatele prostředků. | 
| Prostředek | Název prostředku. | 
| ResourceType | Název typu prostředku | 

### <a name="update-agent"></a>Aktualizace agenta

Vytvoří se záznam s typem `UpdateAgent`, který poskytuje podrobnosti o agentovi aktualizace v počítači. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis | 
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | | 
| AutomaticUpdateEnabled | | 
| Computer | Plně kvalifikovaný název domény počítače pro vytváření sestav. |
| DaySinceLastUpdateBucket | | 
| ManagementGroupName | Název Operations Manager skupiny pro správu nebo pracovního prostoru Log Analytics. |
| OSVersion | Verze operačního systému. |
| Server | |
| SourceHealthServiceId | Jedinečný identifikátor představující Log Analytics ID agenta Windows. |
| SourceSystem | *OperationsManager* | 
| TenantId | Jedinečný identifikátor, který představuje instanci vaší organizace Azure Active Directory. |
| TimeGenerated | Datum a čas vytvoření záznamu |
| Typ | *Aktualizace* | 
| WindowsUpdateAgentVersion | Verze agenta web Windows Update. |
| WSUSServer | Zobrazuje chyby, pokud má agent web Windows Update problém při řešení potíží. |

### <a name="update-deployment-status"></a>Stav nasazení aktualizace 

Vytvoří se záznam s typem `UpdateRunProgress`, který poskytuje stav nasazení aktualizace pro plánované nasazení v počítači. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis | 
|----------|-------------|
| Computer | Plně kvalifikovaný název domény počítače pro vytváření sestav. |
| ComputerEnvironment | *Azure* nebo *mimo Azure*. | 
| CorrelationId | Jedinečný identifikátor spuštění úlohy Runbooku pro aktualizaci. |
| EndTime | Čas ukončení procesu synchronizace. | 
| ErrorResult | Při instalaci aktualizace se vygeneroval kód chyby web Windows Update. | 
| InstallationStatus | Možné stavy instalace aktualizace v klientském počítači *probíhají* *úspěšně*, *částečně selhaly*. |
| KBID | ID článku znalostní báze pro Windows Update | 
| ManagementGroupName | Název Operations Manager skupiny pro správu nebo pracovního prostoru Log Analytics. |
| OSType | Určuje typ operačního systému, *Windows* nebo *Linux*. | 
| Produkt | Produkty, pro které je aktualizace platná. |
| Prostředek | Název prostředku. | 
| ResourceId | Jedinečný identifikátor prostředku, ke kterému je záznam přidružen. |
| ResourceProvider | Určuje poskytovatele prostředků. | 
| ResourceType | Název typu prostředku | 
| SourceComputerId | Jedinečný identifikátor představující zdrojový počítač. | 
| SourceSystem | *OperationsManager* |
| StartTime | Čas, kdy má být aktualizace naplánována k instalaci. |
| SubscriptionId | Jedinečný identifikátor předplatného Azure. | 
| SucceededOnRetry | Ukazuje, že při prvním pokusu došlo k chybě při spuštění aktualizace a aktuální operace je pokus o opakování. |
| TimeGenerated | Datum a čas vytvoření záznamu |
| Název | Název aktualizace |
| Typ | *UpdateRunProgress* |
| UpdateID naformátovat | Jedinečný identifikátor aktualizace softwaru. |
| VMUUID | Jedinečný identifikátor pro virtuální počítač. |
| _ResourceId | Jedinečný identifikátor prostředku, ke kterému je záznam přidružen. |

### <a name="update-summary"></a>Souhrn aktualizací 

Vytvoří se záznam s typem `UpdateSummary`, který poskytuje souhrn aktualizací podle počítače. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis | 
|----------|-------------|
| Computer | Plně kvalifikovaný název domény počítače pro vytváření sestav. |
| ComputerEnvironment | *Azure* nebo *mimo Azure*. | 
| CriticalUpdatesMissing | Počet neplatných důležitých aktualizací. | 
| ManagementGroupName | Název Operations Manager skupiny pro správu nebo pracovního prostoru Log Analytics. |
| NETRuntimeVersion | Verze .NET Framework nainstalovaná na počítači s Windows. |
| OldestMissingSecurityUpdateBucket | Hodnoty jsou:<br> *Poslední*<br> *před 30 dny*<br> *před 60 dny*<br> *Verze* | 
| OldestMissingSecurityUpdateInDays | Celkový počet dní pro nejstarší aktualizaci, která se zjistila jako platná, která není nainstalovaná. |
| OsVersion | Verze operačního systému. |
| OtherUpdatesMissing | Počet chybějících zjištěných aktualizací |
| Prostředek |  Název prostředku. | 
| ResourceGroup | Název skupiny prostředků, ve které je prostředek členem |
| ResourceId | Jedinečný identifikátor prostředku, ke kterému je záznam přidružen. |
| ResourceProvider | Určuje poskytovatele prostředků. |
| ResourceType | Název typu prostředku |
| RestartPending | *True* nebo *false*. |
| SecurityUpdatesMissing | Počet použitelných chybějících aktualizací zabezpečení| 
| SourceComputerId | Jedinečný identifikátor pro virtuální počítač. |
| SourceSystem | *OpsManager* | 
| SubscriptionId | Jedinečný identifikátor předplatného Azure. |
| TimeGenerated | Datum a čas vytvoření záznamu |
| TotalUpdatesMissing | Celkový počet platných chybějících aktualizací | 
| Typ | *UpdateSummary* |
| VMUUID | Jedinečný identifikátor pro virtuální počítač. |
| WindowsUpdateAgentVersion | Verze agenta web Windows Update. |
| WindowsUpdateSetting | Zobrazuje stav agenta web Windows Update. Možné hodnoty jsou:<br> *Naplánovaná instalace*<br> *Upozorňovat před instalací*<br> Z chybného agenta WUA byla vrácena chyba. | 
| WSUSServer | Zobrazuje chyby, pokud má agent web Windows Update problém při řešení potíží. |
| _ResourceId | Jedinečný identifikátor prostředku, ke kterému je záznam přidružen. |

## <a name="sample-queries"></a>Ukázkové dotazy

Následující části obsahují Ukázky dotazů protokolu pro záznamy aktualizací, které jsou shromažďovány pro Update Management.

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Potvrďte, že jsou připojené počítače mimo Azure.

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
2. Otevřete protokol událostí systému Windows. V části **Application and Services Logs\Operations Manager** vyhledejte události ID 3000 a id události 5002 ze zdrojového **konektoru služby**. Tyto události signalizují, že je počítač zaregistrován v pracovním prostoru Log Analytics a přijímá konfiguraci.

Pokud Agent nemůže komunikovat s protokoly Azure Monitor a Agent je nakonfigurován pro komunikaci s internetem prostřednictvím brány firewall nebo proxy server, ověřte, zda je správně nakonfigurována brána firewall nebo proxy server. Informace o tom, jak ověřit, jestli je brána firewall nebo proxy server správně nakonfigurovaná, najdete v tématu [Konfigurace sítě pro agenta Windows](../azure-monitor/platform/agent-windows.md) nebo [konfiguraci sítě pro agenta Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Pokud jsou vaše systémy Linux nakonfigurované pro komunikaci s proxy serverem nebo Log Analytics bránou a připojujete se k tomuto řešení, aktualizujte *proxy. conf* oprávnění, které skupině omiuser udělí oprávnění ke čtení souboru pomocí následujících příkazů:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nově přidaní agenti Linux zobrazují po provedení posouzení stav **aktualizovaný** . Tento proces může trvat až 6 hodin.

Pokud chcete ověřit, že skupina pro správu Operations Manager komunikuje s protokoly Azure Monitor, přečtěte si téma [ověření Operations Manager integrace s protokoly Azure monitor](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

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

* K zobrazení podrobných dat aktualizace použijte prohledávání protokolů v protokolu [Azure monitor](../log-analytics/log-analytics-log-searches.md) .
* [Vytvořte výstrahy](automation-tutorial-update-management.md#configure-alerts) pro stav nasazení aktualizace.
