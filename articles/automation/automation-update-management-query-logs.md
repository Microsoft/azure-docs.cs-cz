---
title: Dotaz na protokoly správy aktualizací Azure
description: Tento článek popisuje, jak se dotazovat protokoly pro správu aktualizací v pracovním prostoru Analýzy protokolů.
services: automation
ms.subservice: update-management
ms.date: 03/11/2020
ms.topic: conceptual
ms.openlocfilehash: f31168d47f31d8e740c95cb3d9e449f473cc78dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216849"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Záznamy aktualizací dotazů pro správu aktualizací v protokolech monitorování Azure

Kromě podrobností, které jsou k dispozici v řešení správy aktualizací, můžete vyhledávat proti protokoly uložené v pracovním prostoru Log Analytics. Na stránce řešení vyberte v levém podokně **protokoly**. Otevře se stránka **Hledání protokolu.**

Můžete se také dozvědět, jak přizpůsobit dotazy nebo je použít z různých klientů a další na adrese: [Log Analytics vyhledávání rozhraní API dokumentace](https://dev.loganalytics.io/).

## <a name="update-records"></a>Záznamy typu Aktualizace

Záznamy, které jsou shromažďovány službou Update Management pro Windows a Linux virtuální počítače a datové typy, které se zobrazí ve výsledcích hledání protokolu. Následující části popisují tyto záznamy.

### <a name="required-updates"></a>Požadované aktualizace

Je vytvořen záznam `RequiredUpdate` s typem, který představuje aktualizace vyžadované počítačem. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis | 
|----------|-------------|
| Počítač | Plně kvalifikovaný název domény vykazovacího počítače. |
| KBID | ID článku znalostní báze Knowledge Base pro aktualizaci systému Windows. |
| ManagementGroupName | Název skupiny pro správu nástroje Operations Manager nebo pracovního prostoru Log Analytics. | 
| Produkt | Produkty, pro které je aktualizace použitelná. | 
| PublishDate | Datum, kdy je aktualizace připravena ke stažení a instalaci ze služby Windows Update. |
| Server | | 
| SourceHealthServiceId | Jedinečný identifikátor představující ID agenta systému Windows analýzy protokolů. |
| SourceSystem | *OperationsManager* | 
| TenantId | Jedinečný identifikátor představující instanci služby Azure Active Directory vaší organizacemi | 
| TimeGenerated | Datum a čas vytvoření záznamu. | 
| Typ | *Aktualizace* | 
| AktualizovatKlasifikace | Označuje typ aktualizací, které lze použít. Ve Windows:<br> *Důležité aktualizace*<br> *Aktualizace zabezpečení*<br> *Kumulativní aktualizace*<br> *Balíčky funkcí*<br> *Aktualizace Service Pack*<br> *Aktualizace definic*<br> *Nástroje*<br> *Aktualizace*. Pro Linux:<br> *Důležité aktualizace a aktualizace zabezpečení*<br> *Další* |
| Závažnost aktualizace | Hodnocení závažnosti chyby zabezpečení. Hodnoty jsou:<br> *Kritická*<br> *Důležité*<br> *Střední*<br> *Nízká* |
| Název aktualizace | Název aktualizace.|

### <a name="update"></a>Aktualizace

Je vytvořen záznam `Update` s typem, který představuje dostupné aktualizace a stav jejich instalace pro počítač. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis | 
|----------|-------------|
| SchváleníZdroj | Platí pouze pro operační systém Windows. Hodnota je *Microsoft Update*. |
| Schválené | *Pravda* nebo *nepravda* |
| Classification | *Aktualizace* |
| Počítač | Plně kvalifikovaný název domény vykazovacího počítače. |
| Prostředí počítače | *Azure* nebo *jiné než Azure*. |
| MSRCBulletinID | ID bulletinu zabezpečení | 
| MSRCSeverity | Hodnocení závažnosti chyby zabezpečení. Hodnoty jsou:<br> *Kritická*<br> *Důležité*<br> *Střední*<br> *Nízká* |  
| KBID | ID článku znalostní báze Knowledge Base pro aktualizaci systému Windows. |
| ManagementGroupName | Název skupiny pro správu nástroje Operations Manager nebo pracovního prostoru Log Analytics. |
| UpdateID | Jedinečný identifikátor aktualizace softwaru |
| RevisionNumber | Číslo revize konkrétní revize aktualizace. |
| Nepovinné | *Pravda* nebo *nepravda* | 
| RebootBehavior | Chování při restartování po instalaci nebo odinstalaci aktualizace. |
| _ResourceId | Jedinečný identifikátor prostředku, ke kterému je záznam přidružen. |
| Typ | *Aktualizace* |
| VMUUID | Jedinečný identifikátor virtuálního počítače |
| MG | Jedinečný identifikátor pro skupinu pro správu nebo pracovní prostor Log Analytics | 
| TenantId | Jedinečný identifikátor představující instanci služby Azure Active Directory vaší organizacemi | 
| SourceSystem | *OperationsManager* | 
| TimeGenerated | Datum a čas vytvoření záznamu. | 
| SourceComputerId | Jedinečný identifikátor představující zdrojový počítač. | 
| Nadpis | Název aktualizace. |
| Datum publikování (UTC) | Datum, kdy je aktualizace připravena ke stažení a instalaci ze služby Windows Update.  |
| UpdateState | Aktuální stav aktualizace. | 
| Produkt | Produkty, pro které je aktualizace použitelná. |
| SubscriptionId | Jedinečný identifikátor předplatného Azure. | 
| ResourceGroup | Název skupiny prostředků, jejíž je prostředek členem. | 
| ResourceProvider | Určuje zprostředkovatele prostředků. | 
| Prostředek | Název prostředku. | 
| ResourceType | Název typu prostředku. | 

### <a name="update-agent"></a>Aktualizovat agenta

Je vytvořen záznam `UpdateAgent` s typem, který poskytuje podrobnosti o aktualizačním agentovi v počítači. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis | 
|----------|-------------|
| AgeofOldestMissingPožadovanUpdate | | 
| Funkce AutomaticUpdateEnabled | | 
| Počítač | Plně kvalifikovaný název domény vykazovacího počítače. |
| DaySinceLastUpdateBucket | | 
| ManagementGroupName | Název skupiny pro správu nástroje Operations Manager nebo pracovního prostoru Log Analytics. |
| OSVersion | Verze operačního systému. |
| Server | |
| SourceHealthServiceId | Jedinečný identifikátor představující ID agenta systému Windows analýzy protokolů. |
| SourceSystem | *OperationsManager* | 
| TenantId | Jedinečný identifikátor představující instanci služby Azure Active Directory vaší organizacemi |
| TimeGenerated | Datum a čas vytvoření záznamu. |
| Typ | *Aktualizace* | 
| WindowsUpdateAgentVersion | Verze agenta služby Windows Update. |
| WSUSServer | Zobrazuje chyby, pokud má agent služby Windows Update problém s řešením potíží. |

### <a name="update-deployment-status"></a>Aktualizovat stav nasazení 

Je vytvořen záznam `UpdateRunProgress` s typem, který poskytuje stav nasazení aktualizace plánovaného nasazení podle počítače. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis | 
|----------|-------------|
| Počítač | Plně kvalifikovaný název domény vykazovacího počítače. |
| Prostředí počítače | *Azure* nebo *jiné než Azure*. | 
| CorrelationId | Jedinečný identifikátor úlohy sady Runbook spuštěné pro aktualizaci |
| EndTime | Čas ukončení procesu synchronizace. | 
| ChybaVýsledek | Kód chyby služby Windows Update vygenerovaný v případě, že se aktualizace nenainstaluje. | 
| Stav instalace | Možné stavy instalace aktualizace v klientském počítači, *Probíhá*, Úspěšné , *Částečně se* *nezdařilo*. |
| KBID | ID článku znalostní báze Knowledge Base pro aktualizaci systému Windows. | 
| ManagementGroupName | Název skupiny pro správu nástroje Operations Manager nebo pracovního prostoru Log Analytics. |
| OSType | Určuje typ operačního systému, *Windows* nebo *Linux*. | 
| Produkt | Produkty, pro které je aktualizace použitelná. |
| Prostředek | Název prostředku. | 
| ResourceId | Jedinečný identifikátor prostředku, ke kterému je záznam přidružen. |
| ResourceProvider | Určuje zprostředkovatele prostředků. | 
| ResourceType | Název typu prostředku. | 
| SourceComputerId | Jedinečný identifikátor představující zdrojový počítač. | 
| SourceSystem | *OperationsManager* |
| StartTime | Čas, kdy je naplánováno instalaci aktualizace. |
| SubscriptionId | Jedinečný identifikátor předplatného Azure. | 
| SucceededOnRetry | Zobrazuje, kdy se při prvním pokusu nezdařilo spuštění aktualizace a aktuální operace je pokus o opakování. |
| TimeGenerated | Datum a čas vytvoření záznamu. |
| Nadpis | Název aktualizace. |
| Typ | *AktualizovatRunProgress* |
| Aktualizační id | Jedinečný identifikátor aktualizace softwaru |
| VMUUID | Jedinečný identifikátor virtuálního počítače |
| _ResourceId | Jedinečný identifikátor prostředku, ke kterému je záznam přidružen. |

### <a name="update-summary"></a>Aktualizovat souhrn 

Je vytvořen záznam `UpdateSummary` s typem, který poskytuje souhrn aktualizace podle počítače. Tyto záznamy mají vlastnosti v následující tabulce:

| Vlastnost | Popis | 
|----------|-------------|
| Počítač | Plně kvalifikovaný název domény vykazovacího počítače. |
| Prostředí počítače | *Azure* nebo *jiné než Azure*. | 
| CriticalUpdatesMissing | Počet chybějících důležitých aktualizací, které jsou použitelné. | 
| ManagementGroupName | Název skupiny pro správu nástroje Operations Manager nebo pracovního prostoru Log Analytics. |
| NETRuntimeVersion | V počítači se systémem Windows byla nainstalována verze rozhraní .NET Framework. |
| OldestMissingSecurityUpdateBucket | Hodnoty jsou:<br> *Nedávné*<br> *před 30 dny*<br> *před 60 dny*<br> *Starší* | 
| OldestMissingSecurityUpdateInDays | Celkový počet dní pro nejstarší aktualizaci zjištěnou jako použitelná, která nebyla nainstalována. |
| OsVersion | Verze operačního systému. |
| OtherUpdatesMissing | Počet zjištěných aktualizací chybí. |
| Prostředek |  Název prostředku. | 
| ResourceGroup | Název skupiny prostředků, jejíž je prostředek členem. |
| ResourceId | Jedinečný identifikátor prostředku, ke kterému je záznam přidružen. |
| ResourceProvider | Určuje zprostředkovatele prostředků. |
| ResourceType | Název typu prostředku. |
| Restartovat čeká na restartování | *Pravda* nebo *nepravda*. |
| SecurityUpdatesMissing | Počet chybějících aktualizací zabezpečení.| 
| SourceComputerId | Jedinečný identifikátor virtuálního počítače |
| SourceSystem | *OpsManager* | 
| SubscriptionId | Jedinečný identifikátor předplatného Azure. |
| TimeGenerated | Datum a čas vytvoření záznamu. |
| TotalUpdatesMissing | Celkový počet chybějících aktualizací. | 
| Typ | *UpdateSummary* |
| VMUUID | Jedinečný identifikátor virtuálního počítače |
| WindowsUpdateAgentVersion | Verze agenta služby Windows Update. |
| WindowsUpdateSetting | Zobrazuje stav agenta služby Windows Update. Možné hodnoty:<br> *Plánovaná instalace*<br> *Upozornit před instalací*<br> Z nefunkčního agenta WUA byla vrácena chyba. | 
| WSUSServer | Zobrazuje chyby, pokud má agent služby Windows Update problém s řešením potíží. |
| _ResourceId | Jedinečný identifikátor prostředku, ke kterému je záznam přidružen. |

## <a name="sample-queries"></a>Ukázkové dotazy

Následující části obsahují ukázkové dotazy protokolu pro záznamy aktualizací, které jsou shromažďovány pro správu aktualizací.

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Zkontrolujte, zda jsou počítače jiné hospo-

Chcete-li ověřit, že přímo připojené počítače komunikují s protokoly Azure Monitor, spusťte jedno z následujících hledání protokolu.

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

V počítači se systémem Windows můžete zkontrolovat následující informace a ověřit připojení agenta k protokolům Azure Monitoru:

1. V Ovládacích panelech otevřete **microsoft monitoring agenta**. Na kartě **Azure Log Analytics** se agent zobrazí následující zpráva: **Agent monitorování Microsoftu se úspěšně připojil k Log Analytics**.
2. Otevřete protokol událostí systému Windows. Přejděte na **protokoly aplikací a služeb\Operations Manager** a vyhledejte ID události 3000 a ID události 5002 ze zdrojového **konektoru služby**. Tyto události znamenají, že se počítač zaregistroval do pracovního prostoru služby Log Analytics a přijímá konfiguraci.

Pokud agent nemůže komunikovat s protokoly Azure Monitor a agent je nakonfigurovaný pro komunikaci s Internetem přes bránu firewall nebo proxy server, zkontrolujte, že brána firewall nebo proxy server je správně nakonfigurovaný. Informace o tom, jak ověřit správnou konfiguraci brány firewall nebo serveru proxy, naleznete v [tématu Konfigurace sítě pro agenta systému Windows](../azure-monitor/platform/agent-windows.md) nebo [Konfigurace sítě pro agenta Linuxu](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Pokud jsou vaše linuxové systémy nakonfigurovány tak, aby komunikovaly s proxy serverem nebo bránou Log Analytics gateway a vy toto řešení zařazujete, aktualizujte oprávnění *proxy.conf* udělit skupině omiuser oprávnění ke čtení souboru pomocí následujících příkazů:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nově přidané linuxové agenty ukazují stav **Aktualizováno** po provedení posouzení. Tento proces může trvat až 6 hodin.

Pokud chcete ověřit, že skupina správy nástroje Operations Manager komunikuje s protokoly Azure Monitor, [přečtěte si informace o ověření integrace nástroje Operations Manager pomocí protokolů Azure Monitor](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

### <a name="single-azure-vm-assessment-queries-windows"></a>Dotazy na vyhodnocení jednoho virtuálního počítače Azure (Windows)

Nahraďte hodnotu VMUUID identifikátorem GUID virtuálního počítače, na který se dotazujete. Můžete najít VMUUID, který by měl být používán spuštěním následující dotaz v protokolech Azure Monitor:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Chybějící souhrn aktualizací

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Chybějící seznam aktualizací

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

### <a name="single-azure-vm-assessment-queries-linux"></a>Dotazy na vyhodnocení jednoho virtuálního počítače Azure (Linux)

U některých distribucí Linuxu existuje [neshoda endianness](https://en.wikipedia.org/wiki/Endianness) s hodnotou VMUUID, která pochází ze Správce prostředků Azure a co se ukládá v protokolech Azure Monitor. Následující dotaz kontroluje shodu na obou koncovost. Nahraďte hodnoty VMUUID formátem big-endian a little-endian identifikátoru GUID, abyste správně vrátili výsledky. Můžete najít VMUUID, který by měl být používán spuštěním následující dotaz v protokolech Azure Monitor:`Update | where Computer == "<machine name>"
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

#### <a name="missing-updates-list"></a>Chybějící seznam aktualizací

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

### <a name="multi-vm-assessment-queries"></a>Dotazy na vyhodnocení více virtuálních vin

#### <a name="computers-summary"></a>Shrnutí počítačů

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

#### <a name="missing-updates-list"></a>Chybějící seznam aktualizací

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

* Pomocí hledání protokolů v [protokolech Azure Monitor](../log-analytics/log-analytics-log-searches.md) uobrazte podrobná data aktualizací.
* [Vytvořte výstrahy](automation-tutorial-update-management.md#configure-alerts) pro stav nasazení aktualizace.
