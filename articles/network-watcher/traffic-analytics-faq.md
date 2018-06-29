---
title: Nejčastější dotazy k Azure provoz analytics | Microsoft Docs
description: Získejte odpovědi na některé nejčastější dotazy o analýzy provozu.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: a4b87d92751c84d96bc70915d16adae7943c145e
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062873"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Analýza provozu nejčastější dotazy

Tento článek shromažďuje na jednom místě řadu nejčastější dotazy o analýzy provozu v sledovací proces sítě Azure.

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Jaké jsou požadavky pro použití Analýza provozu?

Analýza provozu vyžaduje splnění následujících předpokladů:

- Sledovací proces sítě povolené předplatné.
- Skupina zabezpečení sítě (NSG) toku protokoly povolené pro skupiny Nsg, kterou chcete sledovat.
- Účet úložiště Azure pro uložení nezpracovaná flog protokoly.
- K pracovnímu prostoru analýzy protokolů Azure s oprávnění ke čtení a zápisu.

Váš účet musí splňovat jednu z těchto povolit provoz analytics:

- Musí mít váš účet přiřazenou na jednu z následujících rolí na úrovni předplatného: správce účtu, služby správce nebo spolusprávce.
- Váš účet musí mít jednu z následujících rolí řízení (RBAC) přístupu podle rolí v oboru předplatné: vlastník, Přispěvatel, čtečky nebo Přispěvatel sítě.
- Pokud váš účet není přiřazen k jedné z výše uvedených rolí, musí být přiřazen vlastní roli, která je přiřazena následujících akcí na úrovni předplatného.
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
Kontrola role přiřazené uživateli pro předplatné:

1. Přihlaste se k Azure pomocí **Login-AzureRmAccount**. 

2. Vyberte požadované předplatné pomocí **Select-AzureRmSubscription**. 

3. K zobrazení seznamu všech rolí, které jsou přiřazeny pro zadaného uživatele, použijte **Get-AzureRmRoleAssignment - SignInName [e-mail uživatele] - IncludeClassicAdministrators**. 

Pokud nevidíte žádný výstup, obraťte se na správce příslušného předplatného a získat tak přístup ke spuštění příkazů. Další podrobnosti najdete v tématu [Správa řízení přístupu na základě rolí pomocí Azure PowerShell](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-powershell).


## <a name="in-which-azure-regions-are-traffic-analytics-available"></a>Ve které oblasti jsou přenosy analytics k dispozici?

Analýza provozu můžete použít pro skupiny Nsg v některém z následujících podporovaných oblastí: – Západ střední USA, Východ USA, Východ USA 2, – Sever střední USA, Jižní střední USA, střed USA, západ USA, západní USA 2, západní Evropa, Severní Evropa, Spojené království – Západ, Spojené království – Jih, Austrálie – východ, Austrálie – jihovýchod a Asie a Tichomoří – jihovýchod. Pracovní prostor analýzy protokolů musí existovat v – Západ střední USA, Východ USA, západní Evropa, Spojené království – Jih, Austrálie – jihovýchod nebo jihovýchodní Asie.

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Můžete povolit toku skupin Nsg protokoly pro nacházet v různých oblastech než pracovního prostoru?

Ano, tyto skupiny Nsg můžou být v různých oblastech než pracovní prostor analýzy protokolů.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Lze nakonfigurovat více skupin Nsg v rámci jednoho pracovního prostoru?

Ano.

## <a name="can-i-use-an-existing-workspace"></a>Můžete použít k existujícímu pracovnímu prostoru?

Ano. Pokud vyberete existujícímu pracovnímu prostoru, ujistěte se, že byla migrována do nového dotazu jazyka. Pokud nechcete upgradovat pracovním prostoru, budete muset vytvořit nový. Další informace o nový jazyk dotazů najdete v tématu [Azure Log Analytics upgradovat na nové hledání protokolu](../log-analytics/log-analytics-log-search-upgrade.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-operations-management-suite-workspace-be-in-a-different-subscription"></a>Můžete být svůj účet úložiště Azure v rámci jednoho předplatného a pracovní prostor služby Operations Management Suite se v jiném předplatném?

Ano, váš účet úložiště Azure může být v rámci jednoho předplatného a pracovní prostor služby Operations Management Suite může být v jiném předplatném.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Můžete uložit nezpracovaná protokoly v jiném předplatném.

Ne. Nezpracovaná protokoly můžete uložit ve libovolný účet úložiště podporou skupina NSG pro tok protokoly. Ale účet úložiště a raw protokoly musí být ve stejném předplatném, oblasti.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Co když nelze nakonfigurovat skupinu NSG pro analýzu provoz z důvodu chyby "Nebyl nalezen"?

Vyberte podporované oblasti. Pokud vyberete oblast není podporován, obdržíte chybu "Nebyl nalezen". Podporované oblasti jsou uvedeny výše v tomto článku.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Co v případě zobrazuje stav, "Nepodařilo se načíst," v části stránce NSG toku protokoly?

Zprostředkovatel Microsoft.Insights musí být zaregistrován pro tok protokolování správně fungovat. Pokud si nejste jisti, zda je poskytovatel Microsoft.Insights registrovaný pro vaše předplatné, nahraďte *xxxxx-xxxxx-xxxxxx-xxxx* na následující příkaz a spusťte následující příkazy z prostředí PowerShell:

```powershell-interactive
**Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Nakonfigurovali I řešení. Proč nejsou zobrazeny nic na řídicím panelu?

Řídicí panel může trvat až 30 minut, než se objeví při prvním. Řešení musí nejprve agregace dostatek dat pro její odvodit smysluplné přehledy. Poté generuje sestavy. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Co dělat, když se tato zpráva: "jsme nenašli žádná data v tomto pracovním prostoru pro vybraný časový interval. Zkuste změnit časový interval nebo vyberte jiný pracovní prostor. "?

Vyzkoušejte následující možnosti:
- Časový interval, v horním panelu změňte.
- Vyberte jiný pracovní prostor analýzy protokolů v horním panelu.
- Zkuste přístup k provozu analytics po 30 minutách, pokud bylo nedávno povolené.
    
Pokud potíže potrvají, vyšší riziko z hlediska v [fórum hlas uživatele](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Co dělat, když se tato zpráva: "analýza vaše skupina NSG toku protokoly poprvé. Tento proces může trvat 20-30 minut. Zkontrolujte zpět po určité době. 2) Pokud předchozí krok nefunguje a pracovní prostor je pod volné SKU, zkontrolujte vašeho pracovního prostoru využití k ověření přes kvótu, jinak postupujte nejčastější dotazy o další informace. "?

Tato zpráva se může zobrazit, protože:
- Analýza provozu byla nedávno povolená a nemusí být ještě agregovat dostatek dat pro její odvodit smysluplné přehledy.
- Používáte bezplatnou verzi pracovního prostoru služby Operations Management Suite a ta překročila maximální kvóty. Možná budete muset použít pracovní prostor s větší kapacitou.
    
Pokud potíže potrvají, vyšší riziko z hlediska v [fórum hlas uživatele](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Co dělat, když se tato zpráva: "vypadá máme zdroje dat (topologie) a žádné informace toky. Mezitím, klikněte sem můžete zobrazit zdroje dat a další informace naleznete na nejčastější dotazy. "?

Informace o zdroji se zobrazuje na řídicím panelu; však nejsou žádné související toku statistiky. Data nemusí být dispozici z důvodu toky žádná komunikace mezi prostředky. Počkejte po dobu 60 minut a znovu zkontrolovat stav. Pokud potíže potrvají, a jste si jisti, že existují toky komunikaci mezi prostředky, vyšší riziko z hlediska v [fórum hlas uživatele](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Můžete nakonfigurovat pomocí prostředí PowerShell Analýza provozu nebo šablony Azure Resource Manageru nebo klienta?

Analýza provozu můžete nakonfigurovat pomocí prostředí Windows PowerShell z verze 6.2.1 a vyšší. Konfigurace toku protokolování a analýza provozu pro konkrétní skupinu NSG pomocí rutiny Set najdete v tématu [Set-AzureRmNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermnetworkwatcherconfigflowlog?view=azurermps-6.3.0). Protokolování toku a stav Analýza provozu pro konkrétní skupinu NSG, získáte [Get-AzureRmNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkwatcherflowlogstatus?view=azurermps-6.3.0).

V současné době nelze použít šablonu Azure Resource Manager ke konfiguraci Analýza provozu.

Konfigurace analýzy provozu pomocí Azure Resource Manager klienta, viz následující příklady.

**Nastavte příkladu rutiny:**
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
**Získáte příkladu rutiny:**
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



## <a name="how-is-traffic-analytics-priced"></a>Jak je cenově Analýza provozu?

Analýza provozu účtované podle objemu. Měření je založený na zpracování toku dat protokolu službou a ukládání výsledná rozšířeného protokolů v pracovním prostoru analýzy protokolů. Další podrobnosti najdete v tématu [ceny plán](https://azure.microsoft.com/en-us/pricing/details/network-watcher/). 

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Jak můžete procházet pomocí klávesnice v zobrazení mapy geograficky?

Tato stránka geografické mapy obsahuje dvě hlavní části:
    
- **Hlavička**: informační zpráva v horní části geografické mapy obsahuje tlačítka Vybrat distribuční filtry přenosů (například nasazení, provoz z jiných zemí a škodlivé). Vyberete-li na tlačítko, příslušný filtr je použita na mapě. Pokud vyberete tlačítko aktivní, například mapy označuje active datových center ve vašem nasazení.
- **Mapa**: následující Banner informující o části Mapa zobrazuje distribuce přenosů mezi datovými centry Azure a zemích.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navigace klávesnice na informační zprávě
    
- Ve výchozím nastavení je výběr na stránce geografické mapy Banner informující o filtr "Azure řadiče domény".
- Chcete-li přesunout do jiné filtru, použijte buď `Tab` nebo `Right arrow` klíč. Chcete-li přesunout zpátky, použijte buď `Shift+Tab` nebo `Left arrow` klíč. Předat dál navigační zleva doprava, následuje horní dolů.
- Stiskněte klávesu `Enter` nebo `Down` klávesy ŠIPKA použít vybraný filtr. Na základě výběru filtru a nasazení, jsou vyznačené jeden nebo více uzlů části mapy.
- Chcete-li přepnout mezi banner a mapy, stiskněte `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-map"></a>Navigace na mapě pomocí klávesnice
    
- Po vybraný libovolný filtr na informační zprávě a stisknutí `Ctrl+F6`, se aktivuje jeden zvýrazněný uzel (**datové centrum Azure** nebo **země nebo oblast**) v zobrazení mapy.
- Chcete-li přesunout do jiných uzlů zvýrazněných v mapě, použijte buď `Tab` nebo `Right arrow` klíče pro posunování vpřed. Použití `Shift+Tab` nebo `Left arrow` klíče pro zpětné pohyb.
- Chcete-li vybrat všechny zvýrazněný uzel v mapě, použijte `Enter` nebo `Down arrow` klíč.
- Na výběr takové uzlů se aktivuje **informace nástroj pole** pro uzel. Ve výchozím nastavení, se aktivuje tlačítko uzavřené **informace nástroj pole**. Další přesunout uvnitř **pole** zobrazit, použijte `Right arrow` a `Left arrow` klíče přesunout vpřed a zpět, v uvedeném pořadí. Stisknutím `Enter` má stejný účinek jako výběr cílených tlačítka na **informace nástroj pole**.
- Po stisknutí klávesy `Tab` při zaměřuje se na **informace nástroj pole**, aktivuje koncové body na stejné kontinentě jako vybraný uzel. Použití `Right arrow` a `Left arrow` kláves se prostřednictvím těchto koncových bodů.
- Chcete-li přesunout do jiné toku koncových bodů nebo obsah přenášel clustery, použijte `Tab` pro posunování vpřed a `Shift+Tab` pro zpětné pohyb.
- Když je aktivní **clusterů kontinentě**, použijte `Enter` nebo `Down` klávesy se šipkami zvýrazněte koncové body uvnitř obsah přenášel clusteru. Pokud chcete přesunout prostřednictvím koncových bodů a tlačítko Zavřít na pole informace obsah přenášel clusteru, použijte buď `Right arrow` nebo `Left arrow` klíč pro přesun vpřed a zpět, v uvedeném pořadí. Na libovolný koncový bod, můžete použít `Shift+L` přepnutí na řádek připojení z vybraného uzlu ke koncovému bodu. Stisknutím klávesy `Shift+L` znovu pro přesun do vybraný koncový bod.
        
### <a name="keyboard-navigation-at-any-stage"></a>Navigace v jakékoli fázi pomocí klávesnice
    
- `Esc` Sbalí rozšířený výběr.
- `Up arrow` Klíč provede stejnou akci, jako `Esc`. `Down arrow` Klíč provede stejnou akci, jako `Enter`.
- Použití `Shift+Plus` v, a `Shift+Minus` zvětšit.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Jak můžete procházet pomocí klávesnice v zobrazení topologie virtuální sítě?

Tato stránka topologie virtuální sítě obsahuje dvě hlavní části:
    
- **Hlavička**: informační zpráva v horní části topologii virtuální sítě obsahuje tlačítka Vybrat distribuční filtry přenosů (například připojené virtuální sítě, odpojené virtuální sítě a veřejné IP adresy). Při výběru tlačítka příslušný filtr se použije na topologii. Pokud vyberete tlačítko aktivní, například topologii označuje active virtuální sítě ve vašem nasazení.
- **Topologie**: následující Banner informující o část topologie zobrazuje distribuce přenosů mezi virtuálními sítěmi.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navigace klávesnice na informační zprávě
    
- Ve výchozím nastavení je výběr na stránce topologie virtuální sítě na hlavičku filtr "Připojené virtuální sítě".
- Chcete-li přesunout do jiné filtru, použijte `Tab` k přesunu dál. Chcete-li přesunout zpátky, použijte `Shift+Tab` klíč. Předat dál navigační zleva doprava, následuje horní dolů.
- Stiskněte klávesu `Enter` použít vybraný filtr. Na základě výběru filtru a nasazení, jsou vyznačené jeden nebo více uzlů (virtuální sítě) v části topologie.
- Chcete-li přepnout mezi hlavičkou a topologii, stiskněte `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Navigace na topologii pomocí klávesnice
    
- Po vybraný libovolný filtr na informační zprávě a stisknutí `Ctrl+F6`, se aktivuje jeden zvýrazněný uzel (**VNet**) v zobrazení topologie.
- Chcete-li přesunout do jiné zvýrazněné uzly v zobrazení topologie, použijte `Shift+Right arrow` klíče pro posunování vpřed. 
- Na zvýrazněných uzly, se aktivuje **informace nástroj pole** pro uzel. Ve výchozím nastavení, se aktivuje **další podrobnosti** tlačítko **informace nástroj pole**. Další přesunout uvnitř **pole** zobrazit, použijte `Right arrow` a `Left arrow` klíče přesunout vpřed a zpět, v uvedeném pořadí. Stisknutím `Enter` má stejný účinek jako výběr cílených tlačítka na **informace nástroj pole**.
- Na výběr všechny tyto uzly, můžete navštívit jeho připojení jeden po druhém, stisknutím klávesy `Shift+Left arrow` klíč. Se aktivuje **informace nástroj pole** daného připojení. V libovolném bodě můžete fokus zapuštěno zpět do uzlu stisknutím `Shift+Right arrow` znovu.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Jak můžete procházet pomocí klávesnice v zobrazení topologie podsíť?

Tato stránka virtuální podsítě topologie obsahuje dvě hlavní části:
    
- **Hlavička**: informační zpráva v horní části virtuální podsítě topologie obsahuje tlačítka Vybrat distribuční filtry přenosů (například aktivní, střední a brány podsítě). Při výběru tlačítka příslušný filtr se použije na topologii. Pokud vyberete tlačítko aktivní, například topologii označuje active virtuálních podsítí ve vašem nasazení.
- **Topologie**: následující Banner informující o část topologie zobrazuje distribuce přenosů mezi virtuální podsítě.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navigace klávesnice na informační zprávě
    
- Ve výchozím nastavení je výběr na stránce topologie virtuální podsítě na nápis "Podsítě" filtr.
- Chcete-li přesunout do jiné filtru, použijte `Tab` k přesunu dál. Chcete-li přesunout zpátky, použijte `Shift+Tab` klíč. Předat dál navigační zleva doprava, následuje horní dolů.
- Stiskněte klávesu `Enter` použít vybraný filtr. Na základě výběru filtru a nasazení, jsou vyznačené jeden nebo více uzlů (podsítě) v části topologie.
- Chcete-li přepnout mezi hlavičkou a topologii, stiskněte `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Navigace na topologii pomocí klávesnice
    
- Po vybraný libovolný filtr na informační zprávě a stisknutí `Ctrl+F6`, se aktivuje jeden zvýrazněný uzel (**podsíť**) v zobrazení topologie.
- Chcete-li přesunout do jiné zvýrazněné uzly v zobrazení topologie, použijte `Shift+Right arrow` klíče pro posunování vpřed. 
- Na zvýrazněných uzly, se aktivuje **informace nástroj pole** pro uzel. Ve výchozím nastavení, se aktivuje **další podrobnosti** tlačítko **informace nástroj pole**. Další přesunout uvnitř **pole** zobrazit, použijte `Right arrow` a `Left arrow` klíče přesunout vpřed a zpět, v uvedeném pořadí. Stisknutím `Enter` má stejný účinek jako výběr cílených tlačítka na **informace nástroj pole**.
- Na výběr všechny tyto uzly, můžete navštívit jeho připojení jeden po druhém, stisknutím klávesy `Shift+Left arrow` klíč. Se aktivuje **informace nástroj pole** daného připojení. V libovolném bodě můžete fokus zapuštěno zpět do uzlu stisknutím `Shift+Right arrow` znovu.    

