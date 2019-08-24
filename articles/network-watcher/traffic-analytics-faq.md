---
title: Nejčastější dotazy k Azure Traffic Analytics | Microsoft Docs
description: Získejte odpovědi na některé nejčastější dotazy týkající se analýzy provozu.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: kumud
ms.openlocfilehash: 45200e7620326dedcee92c579843e61bb07ff68e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610250"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Analýza provozu nejčastějších dotazech

Tento článek se shromažďuje na jednom místě mnoha nejčastějších dotazů týkajících se analýzy provozu v Azure Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Jaké jsou požadavky na používání analýzy provozu?

Analýza provozu vyžaduje následující předpoklady:

- Network Watcher s povoleným předplatným.
- Protokoly toku NSG (Network Security Group) jsou povolené pro skupin zabezpečení sítě, které chcete monitorovat.
- Účet Azure Storage pro ukládání protokolů nezpracovaných toků.
- Pracovní prostor Azure Log Analytics s přístupem pro čtení a zápis.

Aby bylo možné povolit analýzu provozu, musí váš účet splňovat jednu z následujících možností:

- Váš účet musí mít v oboru předplatného jeden z následujících rolí řízení přístupu na základě role (RBAC): vlastník, přispěvatel, čtenář nebo přispěvatel sítě.
- Pokud váš účet není přiřazený k některé z výše uvedených rolí, musí být přiřazený k vlastní roli, která je přiřazená k následujícím akcím na úrovni předplatného.
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft. Network/Connections/Read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft. Network/routeTables/Read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
Postup kontroly rolí přiřazených uživateli pro předplatné:

1. Přihlaste se k Azure pomocí přihlašovacích údajů **-AzAccount**. 

2. Vyberte požadované předplatné pomocí **Select-AzSubscription**. 

3. Pokud chcete zobrazit seznam všech rolí, které jsou přiřazené k zadanému uživateli, použijte příkaz **Get-AzRoleAssignment-SignInName [User email]-IncludeClassicAdministrators**. 

Pokud nevidíte žádný výstup, obraťte se na příslušného Správce předplatného a získejte přístup ke spuštění příkazů. Další podrobnosti najdete v tématu [Správa řízení přístupu na základě rolí pomocí Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>Ve kterých oblastech Azure je Analýza provozu k dispozici?

Analýzu provozu pro skupin zabezpečení sítě můžete použít v kterékoli z následujících podporovaných oblastí:
- Kanada – střed
- Středozápadní USA
- East US
- Východní USA 2
- Středoseverní USA
- Středojižní USA
- Střední USA
- USA – západ
- USA – západ 2
- Francie – střed
- Západní Evropa
- Severní Evropa
- Brazílie – jih
- Spojené království – západ
- Velká Británie – jih
- Austrálie – východ
- Austrálie – jihovýchod 
- Východní Asie
- Jihovýchodní Asie
- Jižní Korea – střed
- Střed Indie
- Jižní Indie
- Japonsko – východ
- Japonsko – západ
- USA (Gov) – Virginia

Pracovní prostor Log Analytics musí existovat v následujících oblastech:
- Kanada – střed
- Středozápadní USA
- USA – západ
- USA – západ 2
- Středojižní USA
- Střední USA
- East US
- Východní USA 2
- Francie – střed
- Západní Evropa
- Severní Evropa
- Velká Británie – jih
- Austrálie – východ
- Austrálie – jihovýchod
- Východní Asie
- Jihovýchodní Asie 
- Jižní Korea – střed
- Střed Indie
- Japonsko – východ
- USA (Gov) – Virginia

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Může skupin zabezpečení sítě povolit protokolování toků v různých oblastech, než je můj pracovní prostor?

Ano, tyto skupin zabezpečení sítě můžou být v různých oblastech, než je váš pracovní prostor Log Analytics.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Dá se víc skupin zabezpečení sítě nakonfigurovat v jednom pracovním prostoru?

Ano.

## <a name="can-i-use-an-existing-workspace"></a>Můžu použít stávající pracovní prostor?

Ano. Pokud vyberete existující pracovní prostor, ujistěte se, že byl migrován do nového dotazovacího jazyka. Pokud pracovní prostor nechcete upgradovat, je nutné vytvořit nový. Další informace o novém dotazovacím jazyku najdete v tématu [Azure monitor protokoluje upgrade na nové prohledávání protokolu](../log-analytics/log-analytics-log-search-upgrade.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Může být můj účet Azure Storage v jednom předplatném a pracovní prostor Log Analytics má v jiném předplatném?

Ano, váš účet Azure Storage může být v jednom předplatném a váš pracovní prostor Log Analytics může být v jiném předplatném.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Můžu v jiném předplatném ukládat nezpracované protokoly?

Ne. Nezpracované protokoly můžete ukládat do libovolného účtu úložiště, kde je pro protokoly toku povolený NSG. Účet úložiště i nezpracované protokoly se ale musí nacházet ve stejném předplatném a oblasti.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Co když nemůžu nakonfigurovat NSG pro analýzu provozu z důvodu chyby "Nenalezeno"?

Vyberte podporovanou oblast. Pokud vyberete nepodporovanou oblast, zobrazí se chyba "Nenalezeno". Podporované oblasti jsou uvedeny výše v tomto článku.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Co když získám stav, "selhalo načtení" na stránce protokoly toku NSG?

Aby protokolování toků fungovalo správně, musí být zaregistrované poskytovatel Microsoft. Insights. Pokud si nejste jistí, jestli je pro vaše předplatné zaregistrovaný poskytovatel Microsoft. Insights, nahraďte v následujícím příkazu položku *xxxxx-xxxxx-xxxxxx-xxxx* a spusťte z PowerShellu následující příkazy:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Nakonfiguroval (a) jsem řešení. Proč se na řídicím panelu nezobrazuje něco?

První zobrazení řídicího panelu může trvat až 30 minut. Řešení musí nejdřív agregovat dostatek dat, aby bylo možné odvodit smysluplné poznatky. Pak generuje sestavy. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Co když se zobrazí tato zpráva: "V tomto pracovním prostoru jsme nenašli žádná data pro vybraný časový interval. Zkuste změnit časový interval nebo vybrat jiný pracovní prostor. "?

Vyzkoušejte následující možnosti:
- Změňte časový interval na horním panelu.
- V horním panelu vyberte jiný pracovní prostor Log Analytics.
- Zkuste získat přístup k analýze provozu po 30 minutách, pokud bylo nedávno povolené.
    
Pokud potíže přetrvávají, vyvolejte ve [fóru hlas uživatele](https://feedback.azure.com/forums/217313-networking?category_id=195844)obavy.

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Co když se zobrazí tato zpráva: "Probíhá analýza protokolů toku NSG poprvé. Dokončení tohoto procesu může trvat 20-30 minut. Vraťte se později. 2) Pokud výše uvedený krok nefunguje a váš pracovní prostor je pod bezplatnou SKU, zkontrolujte, jestli se tady používá pracovní prostor, abyste ověřili překročení kvóty, jinak najdete další informace v nejčastějších dotazech.

Tato zpráva se může zobrazit, protože:
- Analýza provozu byla nedávno povolena a pravděpodobně ještě nemusela agregovat dostatek dat, aby mohla odvodit smysluplné poznatky.
- Používáte bezplatnou verzi Log Analytics pracovního prostoru a překročili jste limity kvót. Možná budete muset použít pracovní prostor s větší kapacitou.
    
Pokud potíže přetrvávají, vyvolejte ve [fóru hlas uživatele](https://feedback.azure.com/forums/217313-networking?category_id=195844)obavy.
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Co když se zobrazí tato zpráva: "Vypadá to, že máme data prostředků (topologie) a žádné informace o tocích. Mezitím kliknutím sem zobrazíte data prostředků a další informace najdete v nejčastějších dotazech.

Informace o prostředcích vidíte na řídicím panelu. Nicméně nejsou k dispozici žádné statistiky související s tokem. Data nemusí být k dispozici z důvodu nekomunikačních toků mezi prostředky. Počkejte na 60 minut a znovu ověřte stav. Pokud potíže potrvají a jste si jisti, že existují komunikační toky mezi prostředky, vyvolejte obavy ve [fóru hlasu uživatele](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Je možné nakonfigurovat analýzu provozu pomocí PowerShellu nebo šablony Azure Resource Manager nebo klienta?

Analýzu provozu můžete nakonfigurovat pomocí Windows PowerShellu z verze 6.2.1 a vyšší. Pokud chcete nakonfigurovat protokolování toku a analýzu provozu pro konkrétní NSG pomocí rutiny Set, přečtěte si téma [set-AzNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkwatcherconfigflowlog). Pokud chcete získat stav protokolování toku a analýzy provozu pro konkrétní NSG, přečtěte si téma [Get-AzNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkwatcherflowlogstatus).

V současné době nemůžete použít šablonu Azure Resource Manager ke konfiguraci analýzy provozu.

Pokud chcete nakonfigurovat analýzu provozu pomocí klienta Azure Resource Manager, přečtěte si následující příklady.

**Nastavit příklad rutiny:**
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


## <a name="how-is-traffic-analytics-priced"></a>Jak se Analýza provozu cena?

Měření Analýza provozu. Měření vychází ze zpracování dat protokolu toku službou a ukládá výsledné rozšířené protokoly do Log Analyticsho pracovního prostoru. 

Například podle [cenového plánu](https://azure.microsoft.com/pricing/details/network-watcher/), který zvažuje středozápadní USA oblast, pokud Flow protokoluje data uložená v účtu úložiště zpracovaném Analýza provozu je 10 GB a rozšířené protokoly ingestované v Log Analytics pracovní prostor je 1 GB, vztahují se na ně tyto poplatky: 10 x $2,3 + 1 x $2,76 = $25,76

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Jak často Analýza provozu data procesu?

Viz [část agregace dat](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema#data-aggregation) v dokumentu Analýza provozu schématu a agregace dat

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Jak se Analýza provozu rozhodnout, že je IP adresa škodlivá? 

Analýza provozu spoléhá na interní systémy Microsoft Threat Intelligence, které považují IP adresu za škodlivou. Tyto systémy využívají různé zdroje telemetrie, jako jsou produkty a služby společnosti Microsoft, Microsoft Digital Zločins (DCU), Microsoft Security Response Center (MSRC) a externí kanály a sestavování spousty informací nad ním. Některá z těchto dat jsou interní Microsoft. Pokud je známá IP adresa označená jako škodlivá, vydejte prosím lístek podpory, abyste věděli podrobnosti.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Jak můžu nastavit výstrahy pro Analýza provozu data?

Analýza provozu nemá integrovanou podporu pro výstrahy. Vzhledem k tomu, že Analýza provozu data jsou uložená v Log Analytics můžete psát vlastní dotazy a nastavovat upozornění. Uvedené
- Nabídne můžete použít pro Log Analytics v Analýza provozu. 
- Pro psaní dotazů použijte [zde dokumentované schéma](traffic-analytics-schema.md) . 
- Pokud chcete vytvořit upozornění, klikněte na nové pravidlo upozornění.
- Informace o vytvoření výstrahy najdete v [dokumentaci k protokolům výstrah](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) .

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Jak můžu přejít pomocí klávesnice v zobrazení geografického mapování?

Stránka geografického mapování obsahuje dvě hlavní části:
    
- **Banner**: Banner v horní části geografické mapy nabízí tlačítka pro výběr filtrů distribuce provozu (například nasazení, provoz ze zemí nebo oblastí a škodlivá). Když vyberete tlačítko, příslušný filtr se použije na mapě. Například pokud vyberete aktivní tlačítko, mapa zvýrazní aktivní datová centra v nasazení.
- **Mapa**: Pod hlavičkou se v části Mapa zobrazuje distribuce provozu mezi datacentry Azure a zeměmi nebo oblastmi.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navigace na klávesnici na banneru
    
- Ve výchozím nastavení je výběr na stránce geografického mapování pro hlavičku "filtr Azure DCss".
- Chcete-li přejít na jiný filtr, použijte `Tab` buď `Right arrow` klíč, nebo. Chcete-li přejít zpět, použijte `Shift+Tab` buď `Left arrow` klíč, nebo. Přední navigace je ponechána vpravo a následuje shora dolů.
- Pokud `Enter` chcete použít `Down` vybraný filtr, stiskněte klávesy nebo klávesu šipka. V závislosti na výběru a nasazení filtru se zvýrazní jeden nebo víc uzlů pod sekcí map.
- Chcete-li přepínat mezi hlavičkou `Ctrl+F6`a mapou, stiskněte klávesu.
        
### <a name="keyboard-navigation-on-the-map"></a>Navigace na klávesnici na mapě
    
- Po výběru jakéhokoli filtru v proužku a jeho stisknutí `Ctrl+F6`se fokus přesune na jeden ze zvýrazněných uzlů (**datacentrum Azure** nebo **země/oblast**) v zobrazení mapy.
- Chcete-li přejít na jiné zvýrazněné uzly v mapě, `Tab` použijte buď `Right arrow` klíč, nebo pro pohyb vpřed. Použijte `Shift+Tab` klíč`Left arrow` nebo pro zpětný pohyb.
- Pokud chcete vybrat libovolný zvýrazněný uzel na mapě, použijte `Enter` klávesu nebo `Down arrow` .
- Při výběru všech takových uzlů se fokus přesune do **pole informační nástroj** pro uzel. Ve výchozím nastavení se fokus přesune na tlačítko uzavřeno v **poli informační nástroj**. Chcete-li se dále pohybovat uvnitř zobrazení pole `Right arrow` , `Left arrow` použijte klávesy a a přejděte k části vpřed a zpět v uvedeném pořadí. Stisknutí `Enter` má stejný efekt jako v **poli informační nástroj**vyberte tlačítko s fokusem.
- Když stisknete klávesu `Tab` , když je fokus v **poli Information Tool**, přesune se fokus do koncových bodů stejného kontinentu jako vybraný uzel. K přesunu `Left arrow` těchto koncových bodů použijte klávesy a.`Right arrow`
- Chcete-li přejít na jiné koncové body toku nebo kontinenty, použijte `Tab` k přemístění vpřed a `Shift+Tab` k zpětnému přesunu.
- Pokud je fokus v clusterech s kontinentem, zvýrazněte `Down` koncové body v rámci clusteru s kontinentem nebo pomocí kláves se `Enter` šipkami. Chcete-li přejít mezi koncovými body a tlačítkem Zavřít v informačním poli clusteru kontinentů, použijte `Right arrow` buď `Left arrow` klávesu nebo, pro dopředu a zpětnou pohyb v uvedeném pořadí. U libovolného koncového bodu můžete použít `Shift+L` k přepnutí na linku připojení z vybraného uzlu do koncového bodu. Opětovným stisknutím `Shift+L` klávesy můžete přejít na vybraný koncový bod.
        
### <a name="keyboard-navigation-at-any-stage"></a>Navigace na klávesnici v libovolné fázi
    
- `Esc`sbalí rozbalený výběr.
- Klíč provádí stejnou akci jako `Esc`. `Up arrow` Klíč provádí stejnou akci jako `Enter`. `Down arrow`
- Slouží `Shift+Plus` k přiblížení a `Shift+Minus` oddálení.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Jak můžu přejít pomocí klávesnice v zobrazení topologie virtuální sítě?

Stránka topologie virtuálních sítí obsahuje dvě hlavní části:
    
- **Banner**: Banner v horní části topologie virtuálních sítí nabízí tlačítka pro výběr filtrů distribuce provozu (například připojené virtuální sítě, odpojené virtuální sítě a veřejné IP adresy). Když vyberete tlačítko, příslušný filtr se aplikuje na topologii. Pokud například vyberete tlačítko aktivní, topologie zvýrazní aktivní virtuální sítě ve vašem nasazení.
- **Topologie**: Pod hlavičkou se v části topologie zobrazuje distribuce provozu mezi virtuálními sítěmi.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navigace na klávesnici na banneru
    
- Ve výchozím nastavení je výběr na stránce topologie virtuální sítě pro tento proužek filtrem "připojené virtuální sítě".
- Pokud se chcete přesunout na jiný filtr, `Tab` použijte klávesu pro přesunutí vpřed. Chcete-li přejít zpět, `Shift+Tab` použijte klíč. Přední navigace je ponechána vpravo a následuje shora dolů.
- Kliknutím `Enter` použijete vybraný filtr. Na základě výběru a nasazení filtru se zvýrazní jeden nebo víc uzlů (virtuální síť) v části topologie.
- Chcete-li přepínat mezi hlavičkou a topologií `Ctrl+F6`, stiskněte klávesu.
        
### <a name="keyboard-navigation-on-the-topology"></a>Navigace na klávesnici v topologii
    
- Po výběru jakéhokoli filtru v proužku a jeho stisknutí `Ctrl+F6`se fokus přesune na jeden ze zvýrazněných uzlů (**VNet**) v zobrazení topologie.
- Chcete-li přejít na jiné zvýrazněné uzly v zobrazení topologie, `Shift+Right arrow` použijte klávesu pro dopředný pohyb. 
- Na zvýrazněných uzlech se fokus přesune do **pole informační nástroj** pro uzel. Ve výchozím nastavení se fokus přesune na tlačítko **Další podrobnosti** v **poli informační nástroj**. Chcete-li se dále pohybovat uvnitř zobrazení **pole** , `Right arrow` použijte `Left arrow` klávesy a k přesunutí vpřed a zpět v uvedeném pořadí. Stisknutí `Enter` má stejný efekt jako v **poli informační nástroj**vyberte tlačítko s fokusem.
- Po výběru všech takových uzlů můžete navštívit všechna jeho připojení, a to tak, že stisknete `Shift+Left arrow` klávesu. Fokus se přesune do **pole informační nástroj** daného připojení. V jakémkoli okamžiku se dá fokus přesunout zpátky na uzel stisknutím klávesy `Shift+Right arrow` znovu.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Jak můžu přejít pomocí klávesnice v zobrazení topologie podsítě?

Stránka topologie virtuální podsítě obsahuje dvě hlavní části:
    
- **Banner**: Banner v horní části topologie virtuální podsítě nabízí tlačítka pro výběr filtrů distribuce přenosů (například aktivních, středních a podsítí brány). Když vyberete tlačítko, příslušný filtr se aplikuje na topologii. Pokud například vyberete aktivní tlačítko, topologie zvýrazní aktivní virtuální síť v nasazení.
- **Topologie**: Pod hlavičkou se v části topologie zobrazuje distribuce přenosů mezi virtuálními podsítěmi.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navigace na klávesnici na banneru
    
- Ve výchozím nastavení je výběr na stránce topologie virtuálních podsítí pro tento proužek filtrem "podsítě".
- Pokud se chcete přesunout na jiný filtr, `Tab` použijte klávesu pro přesunutí vpřed. Chcete-li přejít zpět, `Shift+Tab` použijte klíč. Přední navigace je ponechána vpravo a následuje shora dolů.
- Kliknutím `Enter` použijete vybraný filtr. V závislosti na výběru a nasazení filtru se zvýrazní jeden nebo víc uzlů (podsíť) v části topologie.
- Chcete-li přepínat mezi hlavičkou a topologií `Ctrl+F6`, stiskněte klávesu.
        
### <a name="keyboard-navigation-on-the-topology"></a>Navigace na klávesnici v topologii
    
- Po výběru jakéhokoli filtru v proužku a jeho stisknutí `Ctrl+F6`se fokus přesune do jednoho ze zvýrazněných uzlů (**podsítě**) v zobrazení topologie.
- Chcete-li přejít na jiné zvýrazněné uzly v zobrazení topologie, `Shift+Right arrow` použijte klávesu pro dopředný pohyb. 
- Na zvýrazněných uzlech se fokus přesune do **pole informační nástroj** pro uzel. Ve výchozím nastavení se fokus přesune na tlačítko **Další podrobnosti** v **poli informační nástroj**. Chcete-li se dále pohybovat uvnitř zobrazení pole `Right arrow` , `Left arrow` použijte klávesy a a přejděte k části vpřed a zpět v uvedeném pořadí. Stisknutí `Enter` má stejný efekt jako v **poli informační nástroj**vyberte tlačítko s fokusem.
- Na výběr všech takových uzlů můžete všechny své připojení navštívit tak, že stisknete `Shift+Left arrow` klávesu. Fokus se přesune do **pole informační nástroj** daného připojení. V jakémkoli okamžiku se dá fokus přesunout zpátky na uzel stisknutím klávesy `Shift+Right arrow` znovu.    

