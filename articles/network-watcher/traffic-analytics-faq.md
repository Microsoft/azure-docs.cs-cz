---
title: Azure traffic analytics nejčastější dotazy | Dokumenty společnosti Microsoft
description: Získejte odpovědi na některé nejčastější dotazy týkající se analýzy provozu.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: damendo
ms.openlocfilehash: 5e31ed905f05070c8715a63ef3386b0006df0a75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840617"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Traffic Analytics nejčastější dotazy

Tento článek shromažďuje na jednom místě mnoho nejčastěji kladených otázek týkajících se analýzy provozu v Azure Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Jaké jsou předpoklady pro použití analýzy provozu?

Traffic Analytics vyžaduje následující požadavky:

- Předplatné s povoleným sledováním sítě.
- Protokoly toku skupiny zabezpečení sítě (NSG) jsou povoleny pro skupiny zabezpečení sítě, které chcete sledovat.
- Účet Azure Storage pro ukládání nezpracovaných protokolů toku.
- Pracovní prostor Azure Log Analytics s přístupem pro čtení a zápis.

Aby byla analýza provozu povolena, musí váš účet splňovat jednu z následujících možností:

- Váš účet musí mít některou z následujících rolí řízení přístupu na základě rolí (RBAC) v oboru předplatného: vlastník, přispěvatel, čtenář nebo přispěvatel sítě.
- Pokud váš účet není přiřazen k jedné z dříve uvedených rolí, musí být přiřazen k vlastní roli, která je přiřazena následující akce, na úrovni předplatného.
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/čtení 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAdresy/čtení
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
Kontrola rolí přiřazených uživateli pro předplatné:

1. Přihlaste se k Azure pomocí **Login-AzAccount**. 

2. Vyberte požadované předplatné pomocí **select-azsubscription**. 

3. Chcete-li vypsat všechny role, které jsou přiřazeny určenému uživateli, použijte **Get-AzRoleAssignment -SignInName [user email] -IncludeClassicAdministrators**. 

Pokud nevidíte žádný výstup, obraťte se na příslušného správce předplatného a získejte přístup ke spuštění příkazů. Další podrobnosti najdete [v tématu Správa řízení přístupu na základě rolí pomocí Azure PowerShellu](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>Ve kterých oblastech Azure je služba Traffic Analytics dostupná?

Analýzy provozu pro sítě sítě nsg můžete použít v některé z následujících podporovaných oblastí:
- Střední Kanada
- USA – středozápad
- USA – východ
- USA – východ 2
- USA – středosever
- USA – středojih
- USA – střed
- USA – západ
- USA – západ 2
- Francie – střed
- Západní Evropa
- Severní Evropa
- Brazílie – jih
- Spojené království – západ
- Spojené království – jih
- Austrálie – východ
- Austrálie – jihovýchod 
- Východní Asie
- Jihovýchodní Asie
- Jižní Korea – střed
- Indie – střed
- Indie – jih
- Japonsko – východ
- Japonsko – západ
- USA (Gov) – Virginia
- Čína východ 2

Pracovní prostor Log Analytics musí existovat v následujících oblastech:
- Střední Kanada
- USA – středozápad
- USA – východ
- USA – východ 2
- USA – středosever
- USA – středojih
- USA – střed
- USA – západ
- USA – západ 2
- Francie – střed
- Západní Evropa
- Severní Evropa
- Spojené království – západ
- Spojené království – jih
- Austrálie – východ
- Austrálie – jihovýchod
- Východní Asie
- Jihovýchodní Asie 
- Jižní Korea – střed
- Indie – střed
- Japonsko – východ
- USA (Gov) – Virginia
- Čína východ 2

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Mohou nsGy povolit protokoly toku pro být v různých oblastech než můj pracovní prostor?

Ano, tyto soubory nsg mohou být v různých oblastech než váš pracovní prostor Analýzy protokolů.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Může být v rámci jednoho pracovního prostoru nakonfigurováno více nevládních směrovačů sítě?

Ano.

## <a name="can-i-use-an-existing-workspace"></a>Mohu použít existující pracovní prostor?

Ano. Pokud vyberete existující pracovní prostor, ujistěte se, že byl migrován do nového dotazovacího jazyka. Pokud nechcete inovovat pracovní prostor, je třeba vytvořit nový. Další informace o novém dotazovacím jazyce naleznete v [tématu Azure Monitor protokoly upgrade na nové hledání protokolu](../log-analytics/log-analytics-log-search-upgrade.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Může být můj účet úložiště Azure v jednom předplatném a můj pracovní prostor Log Analytics v jiném předplatném?

Ano, váš účet Azure Storage může být v jednom předplatném a váš pracovní prostor Log Analytics může být v jiném předplatném.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Mohu ukládat nezpracované protokoly v jiném předplatném?

Ne. Nezpracovaná protokoly můžete uložit do libovolného účtu úložiště, kde je povolena síť sítě sítě pro protokoly toku. Účet úložiště a nezpracované protokoly však musí být ve stejném předplatném a oblasti.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Co když se mi nepodaří nakonfigurovat službu zabezpečení sítě pro analýzu provozu z důvodu chyby "Nenalezeno"?

Vyberte podporovanou oblast. Pokud vyberete nepodporovanou oblast, zobrazí se chyba "Nebyla nalezena". Podporované oblasti jsou uvedeny výše v tomto článku.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Co když se mi pod stránkou protokolů toku nsg zobrazuje stav "Nepodařilo se načíst"?

Aby protokolování toku fungovalo správně, musí být zaregistrován poskytovatel Microsoft.Insights. Pokud si nejste jisti, zda je poskytovatel Microsoft.Insights registrovaný pro vaše předplatné, nahraďte *xxxxx-xxxxx-xxxxxx-xxxx* v následujícím příkazu a spusťte následující příkazy z prostředí PowerShell:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Nakonfiguroval jsem řešení. Proč na řídicím panelu nic nevidím?

Zobrazení řídicího panelu může trvat až 30 minut, než se zobrazí poprvé. Řešení musí nejprve agregovat dostatek dat pro něj odvodit smysluplné přehledy. Pak generuje sestavy. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Co když se zobrazí tato zpráva: "V tomto pracovním prostoru jsme pro vybraný časový interval nenašli žádná data. Zkuste změnit časový interval nebo vyberte jiný pracovní prostor."?

Vyzkoušejte následující možnosti:
- Změňte časový interval v horním pruhu.
- V horním pruhu vyberte jiný pracovní prostor Log Analytics.
- Zkuste získat přístup k analýze provozu po 30 minutách, pokud byla nedávno povolena.
    
Pokud problémy přetrvávají, vzbuzují obavy v [hlasovém fóru uživatelů](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Co když se mi zobrazí tato zpráva: "Analýza protokolů toku nsg poprvé. Tento proces může trvat 20-30 minut. Po nějaké době se vraťte. 2) Pokud výše uvedený krok nefunguje a váš pracovní prostor je pod volnou skladovou položkou, zkontrolujte zde využití pracovního prostoru, abyste ověřili kvótu, jinak se podívejte na časté dotazy pro další informace."?

Tato zpráva se může zobrazit, protože:
- Služba Traffic Analytics byla nedávno povolena a ještě nemusela agregovat dostatek dat, aby mohla odvodit smysluplné přehledy.
- Používáte bezplatnou verzi pracovního prostoru Log Analytics a překročila limity kvót. Možná budete muset použít pracovní prostor s větší kapacitou.
    
Pokud problémy přetrvávají, vzbuzují obavy v [hlasovém fóru uživatelů](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Co když se zobrazí tato zpráva: "Vypadá to, že máme data o zdrojích (topologie) a žádné informace o tocích. Mezitím klikněte zde pro zobrazení údajů o zdrojích a odkazovat na faq pro další informace."?

Na řídicím panelu se zobrazují informace o zdrojích. nejsou však k dispozici žádné statistiky související s tokem. Data nemusí být k dispozici z důvodu žádné komunikační toky mezi prostředky. Počkejte 60 minut a znovu zkontrolujte stav. Pokud problém přetrvává a jste si jisti, že existují komunikační toky mezi prostředky, vzbuzte obavy ve [fóru hlasuživatelů](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Můžu nakonfigurovat analýzu provozu pomocí PowerShellu nebo šablony nebo klienta Azure Resource Manageru?

Analýzu provozu můžete konfigurovat pomocí prostředí Windows PowerShell od verze 6.2.1. Chcete-li nakonfigurovat protokolování toku a analýzu provozu pro konkrétní soubor zabezpečení sítě pomocí rutiny Set, přečtěte si informace [o nastavení azNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkwatcherconfigflowlog). Chcete-li získat stav protokolování toku a analýzy provozu pro konkrétní soubor zabezpečení sítě, přečtěte si informace [o získání azNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkwatcherflowlogstatus).

V současné době nelze použít šablonu Azure Resource Manager ke konfiguraci analýzy provozu.

Pokud chcete nakonfigurovat analýzu provozu pomocí klienta Azure Resource Manager, podívejte se na následující příklady.

**Nastavit rutinu příklad:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Získat příklad rutiny:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```


## <a name="how-is-traffic-analytics-priced"></a>Jaká je cena traffic analytics?

Analýza provozu se měří. Měření je založeno na zpracování dat protokolu toku službou a ukládání výsledných rozšířených protokolů v pracovním prostoru Log Analytics. 

Například podle [cenového plánu](https://azure.microsoft.com/pricing/details/network-watcher/), s ohledem na oblast – střed USA – střed, pokud tok zaznamenává data uložená v účtu úložiště zpracovávaného službou Traffic Analytics 10 GB a rozšířené protokoly požité v pracovním prostoru Log Analytics je 1 GB, pak příslušné poplatky jsou: 10 x 2.3$ + 1 x 2.76$ = 25.76$

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Jak často služba Traffic Analytics zpracovává data?

Podívejte se na [část agregace dat](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema#data-aggregation) v dokumentu Schéma analýzy provozu a Agregace dat.

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Jak služba Traffic Analytics rozhoduje o tom, že je adresa IP škodlivá? 

Traffic Analytics spoléhá na interní systémy analýzy hrozeb společnosti Microsoft, které považují IP adresu za škodlivou. Tyto systémy využívají různé telemetrické zdroje, jako jsou produkty a služby společnosti Microsoft, jednotka Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC) a externí kanály a vytvářejí k němu spoustu informací. Některá z těchto dat je Microsoft Internal. Pokud je známá adresa IP označena jako škodlivá, požádejte o lístek podpory, abyste znali podrobnosti.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Jak můžu nastavit upozornění na data Traffic Analytics?

Traffic Analytics nemá integrovanou podporu pro výstrahy. Vzhledem k tomu, že data Traffic Analytics jsou uložena v Log Analytics, můžete na ně psát vlastní dotazy a nanět výstrahy. Kroky:
- Zkratku pro Analýzu protokolů můžete použít v Traffic Analytics. 
- K napsání dotazů použijte [zde zdokumentované](traffic-analytics-schema.md) schéma. 
- Chcete-li výstrahu vytvořit, klepněte na tlačítko Nové pravidlo výstrahy.
- Chcete-li výstrahu vytvořit, naleznete [v dokumentaci k upozorněním protokolu.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

## <a name="how-do-i-check-which-vms-are-receiving-most-on-premise-traffic"></a>Jak zkontroluji, které virtuální aplikace přijímají většinu místního provozu

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            | where <Scoping condition>
            | mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
            | where isnotempty(vm) 
             | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
            | make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by vm
            | render timechart

  Pro IP adresy:

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
            | where isnotempty(IP) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
            | make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by IP
            | render timechart

Pro čas, použijte formát : yyyy-mm-dd 00:00:00

## <a name="how-do-i-check-standard-deviation-in-traffic-recieved-by-my-vms-from-on-premise-machines"></a>Jak zkontrolovat směrodatnou odchylku v provozu přijatém virtuálními počítači z místních počítačů

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
            | where isnotempty(vm) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
            | summarize deviation = stdev(traffic)  by vm


Pro IP adresy:

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
            | where isnotempty(IP) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
            | summarize deviation = stdev(traffic)  by IP
            
## <a name="how-do-i-check-which-ports-are-reachable-or-bocked-between-ip-pairs-with-nsg-rules"></a>Jak mohu zkontrolovat, které porty jsou dosažitelné (nebo bocked) mezi páry IP s pravidly NSG

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and TimeGenerated between (startTime .. endTime)
            | extend sourceIPs = iif(isempty(SrcIP_s), split(SrcPublicIPs_s, " ") , pack_array(SrcIP_s)),
            destIPs = iif(isempty(DestIP_s), split(DestPublicIPs_s," ") , pack_array(DestIP_s))
            | mvexpand SourceIp = sourceIPs to typeof(string)
            | mvexpand DestIp = destIPs to typeof(string)
            | project SourceIp = tostring(split(SourceIp, "|")[0]), DestIp = tostring(split(DestIp, "|")[0]), NSGList_s, NSGRule_s, DestPort_d, L4Protocol_s, FlowStatus_s 
            | summarize DestPorts= makeset(DestPort_d) by SourceIp, DestIp, NSGList_s, NSGRule_s, L4Protocol_s, FlowStatus_s

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Jak se mohu pohybovat pomocí klávesnice v zobrazení geografické mapy?

Stránka geografické mapy obsahuje dvě hlavní části:
    
- **Banner**: Banner v horní části geografické mapy obsahuje tlačítka pro výběr filtrů distribuce provozu (například Nasazení, Provoz ze zemí nebo oblastí a Škodlivý). Když vyberete tlačítko, příslušný filtr se použije na mapě. Pokud například vyberete tlačítko Aktivní, mapa zvýrazní aktivní datová centra ve vašem nasazení.
- **Mapa**: Pod hlavičkou se v části mapy zobrazuje distribuce provozu mezi datovými centry Azure a zeměmi nebo oblastmi.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navigace pomocí klávesnice na banneru
    
- Ve výchozím nastavení je výběr na stránce geografické mapy pro banner filtr "Azure DC".
- Chcete-li přejít na `Tab` jiný `Right arrow` filtr, použijte klíč nebo. Chcete-li se posunout `Left arrow` zpět, použijte klíč `Shift+Tab` nebo. Navigace vpřed je zleva doprava, následuje shora dolů.
- Stisknutím `Enter` nebo `Down` klávesou se šipkou použijete vybraný filtr. Na základě výběru filtru a nasazení jsou zvýrazněny jeden nebo více uzlů v části mapy.
- Chcete-li přepínat `Ctrl+F6`mezi nápisem a mapou, stiskněte klávesu .
        
### <a name="keyboard-navigation-on-the-map"></a>Navigace pomocí klávesnice na mapě
    
- Po výběru libovolného filtru na `Ctrl+F6`banneru a stisknutí položky se fokus přesune do jednoho ze zvýrazněných uzlů **(datové centrum Azure** nebo **země/oblast)** v zobrazení mapy.
- Chcete-li přejít na jiné zvýrazněné `Tab` uzly v mapě, použijte buď `Right arrow` klíč pro pohyb vpřed. Použijte `Shift+Tab` nebo `Left arrow` klíč pro zpětný pohyb.
- Chcete-li vybrat libovolný zvýrazněný uzel `Enter` v `Down arrow` mapě, použijte klávesu nebo.
- Při výběru těchto uzlů se fokus přesune do **pole informačního nástroje** pro uzel. Ve výchozím nastavení se fokus přesune na uzavřené tlačítko v **poli informačního nástroje**. Chcete-li se dále pohybovat `Left arrow` uvnitř zobrazení **boxu,** pohybujte se dopředu a dozadu pomocí `Right arrow` tlačítek. Stisknutí `Enter` má stejný účinek jako výběr zaostřeného tlačítka v **poli informačního nástroje**.
- Když stisknete, `Tab` když je fokus na **poli informačního nástroje**, fokus se přesune do koncových bodů na stejném kontinentu jako vybraný uzel. Pomocí `Right arrow` kláves `Left arrow` a můžete procházet těmito koncovými body.
- Chcete-li přejít na jiné koncové `Tab` body toku `Shift+Tab` nebo shluky kontinentu, použijte pro pohyb vpřed a pro pohyb dozadu.
- Pokud je fokus na **clustery kontinentu**, použijte klávesy se šipkami `Enter` nebo `Down` zvýraznit koncové body uvnitř clusteru kontinentu. Chcete-li procházet koncovými body a tlačítkem zavřít v `Right arrow` informačním poli clusteru kontinentu, použijte buď klíč nebo `Left arrow` pro pohyb vpřed a vzad. V libovolném koncovém `Shift+L` bodě můžete přepnout na spojovací čáru z vybraného uzlu do koncového bodu. Dalším stisknutím `Shift+L` se můžete přesunout do vybraného koncového bodu.
        
### <a name="keyboard-navigation-at-any-stage"></a>Navigace pomocí klávesnice v libovolné fázi
    
- `Esc`sbalí rozšířený výběr.
- Klíč `Up arrow` provádí stejnou akci `Esc`jako . Klíč `Down arrow` provádí stejnou akci `Enter`jako .
- Slouží `Shift+Plus` k přiblížení `Shift+Minus` a oddálení.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Jak se dá procházet pomocí klávesnice v zobrazení topologie virtuální sítě?

Stránka topologie virtuálních sítí obsahuje dvě hlavní části:
    
- **Banner**: Banner v horní části topologie virtuálních sítí poskytuje tlačítka pro výběr filtrů distribuce provozu (například připojené virtuální sítě, odpojené virtuální sítě a veřejné IP adresy). Když vyberete tlačítko, použije se příslušný filtr na topologii. Pokud například vyberete tlačítko Aktivní, topologie zvýrazní aktivní virtuální sítě ve vašem nasazení.
- **Topologie**: Pod hlavičkou zobrazuje sekce topologie distribuci provozu mezi virtuálními sítěmi.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navigace pomocí klávesnice na banneru
    
- Ve výchozím nastavení je výběr na stránce topologie virtuálních sítí pro banner "Připojené virtuální sítě" filtr.
- Chcete-li přejít na `Tab` jiný filtr, přesuňte se vpřed pomocí klávesy. Chcete-li se `Shift+Tab` posunout zpět, použijte klávesu. Navigace vpřed je zleva doprava, následuje shora dolů.
- Stisknutím `Enter` tlačítka aplikujete vybraný filtr. Na základě výběru a nasazení filtru se zvýrazní jeden nebo více uzlů (virtuální síť) v části topologie.
- Chcete-li přepnout mezi nápisem `Ctrl+F6`a toplogií, stiskněte klávesu .
        
### <a name="keyboard-navigation-on-the-topology"></a>Navigace pomocí klávesnice v topologii
    
- Po výběru libovolného filtru na `Ctrl+F6`banneru a stisknutí položky se fokus přesune do jednoho ze zvýrazněných uzlů (**Virtuální síť**) v zobrazení topologie.
- Chcete-li přejít na jiné zvýrazněné uzly v zobrazení topologie, použijte `Shift+Right arrow` klíč pro pohyb vpřed. 
- Ve zvýrazněných uzlech se fokus přesune do **pole informačního nástroje** pro uzel. Ve výchozím nastavení se fokus přesune na tlačítko **Další podrobnosti** v **poli informačního nástroje**. Chcete-li se dále pohybovat `Right arrow` `Left arrow` uvnitř zobrazení **pole,** pohybujte se dopředu a dozadu pomocí kláves a. Stisknutí `Enter` má stejný účinek jako výběr zaostřeného tlačítka v **poli informačního nástroje**.
- Při výběru těchto uzlů můžete jedním stisknutím `Shift+Left arrow` tlačítka navštívit všechna jeho připojení. Fokus se přesune do **pole informačního nástroje** tohoto připojení. V libovolném okamžiku lze fokus přesunout zpět `Shift+Right arrow` do uzlu opětovným stisknutím tlačítka.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Jak se lze pohybovat pomocí klávesnice v zobrazení topologie podsítě?

Stránka topologie virtuálních podsítí obsahuje dvě hlavní části:
    
- **Banner**: Banner v horní části topologie virtuálních podsítí poskytuje tlačítka pro výběr filtrů distribuce provozu (například podsítě Aktivní, Střední a Brána). Když vyberete tlačítko, použije se příslušný filtr na topologii. Pokud například vyberete tlačítko Aktivní, topologie zvýrazní aktivní virtuální podsíť ve vašem nasazení.
- **Topologie**: Pod hlavičkou zobrazuje topologie rozložení provozu mezi virtuální podsítěmi.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navigace pomocí klávesnice na banneru
    
- Ve výchozím nastavení je výběrem na stránce topologie virtuálních podsítí pro banner filtr "Podsítě".
- Chcete-li přejít na `Tab` jiný filtr, přesuňte se vpřed pomocí klávesy. Chcete-li se `Shift+Tab` posunout zpět, použijte klávesu. Navigace vpřed je zleva doprava, následuje shora dolů.
- Stisknutím `Enter` tlačítka aplikujete vybraný filtr. Na základě výběru filtru a nasazení jsou zvýrazněny jeden nebo více uzlů (podsíť) v části topologie.
- Chcete-li přepnout mezi nápisem `Ctrl+F6`a toplogií, stiskněte klávesu .
        
### <a name="keyboard-navigation-on-the-topology"></a>Navigace pomocí klávesnice v topologii
    
- Po výběru libovolného filtru na `Ctrl+F6`banneru a stisknutí se fokus přesune do jednoho ze zvýrazněných uzlů (**podsíť**) v zobrazení topologie.
- Chcete-li přejít na jiné zvýrazněné uzly v zobrazení topologie, použijte `Shift+Right arrow` klíč pro pohyb vpřed. 
- Ve zvýrazněných uzlech se fokus přesune do **pole informačního nástroje** pro uzel. Ve výchozím nastavení se fokus přesune na tlačítko **Další podrobnosti** v **poli informačního nástroje**. Chcete-li se dále pohybovat `Left arrow` uvnitř zobrazení **boxu,** pohybujte se dopředu a dozadu pomocí `Right arrow` tlačítek. Stisknutí `Enter` má stejný účinek jako výběr zaostřeného tlačítka v **poli informačního nástroje**.
- Při výběru těchto uzlů můžete jedním stisknutím tlačítka `Shift+Left arrow` navštívit všechna jeho připojení, jeden po druhém. Fokus se přesune do **pole informačního nástroje** tohoto připojení. V libovolném okamžiku lze fokus přesunout zpět `Shift+Right arrow` do uzlu opětovným stisknutím tlačítka.    

