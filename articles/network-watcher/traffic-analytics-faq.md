---
title: Analýza provozu Azure – nejčastější dotazy | Dokumentace Microsoftu
description: Získejte odpovědi na některé nejčastější dotazy o analýze provozu.
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
ms.openlocfilehash: debefb329304235818b7b441122b90ae9a2834a9
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578306"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Analýza provozu – nejčastější dotazy

Tento článek shromažďuje na jednom místě řadu nejčastější dotazy o analýze provozu ve službě Azure Network Watcher.

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Jaké jsou požadavky na použití analýzy provozu?

Analýza provozu vyžaduje následující požadavky:

- Sledovací proces sítě povolené předplatné.
- Protokoly toku skupina zabezpečení sítě (NSG) povolena pro skupiny zabezpečení sítě, kterou chcete monitorovat.
- Účet služby Azure Storage k ukládání nezpracovaná flog protokoly.
- Pracovní prostor Azure Log Analytics pomocí oprávnění ke čtení a zápisu.

Váš účet musí splňovat jeden z následujících akcí k povolení analýzy provozu:

- Váš účet musí mít přiřazenou k jednomu z následujících rolí na úrovni předplatného: účet správce, Správce služeb nebo spolupracující správce.
- Váš účet musí mít některou z následujících rolí řízení přístupu na základě role v oboru předplatného: vlastník, Přispěvatel, Čtenář nebo Přispěvatel sítě.
- Pokud váš účet nemá přiřazenou do jedné z výše uvedených rolí, musíte být přiřazeni vlastní roli, která je přiřazena následující akce na úrovni předplatného.
            
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

1. Přihlaste se k Azure s použitím **Login-AzureRmAccount**. 

2. Vyberte požadované předplatné s použitím **Select-AzureRmSubscription**. 

3. K zobrazení seznamu všech rolí, které jsou přiřazeny k zadané uživatele, použijte **Get-AzureRmRoleAssignment - SignInName [e-mail uživatele] - IncludeClassicAdministrators**. 

Pokud nevidíte žádný výstup, obraťte se na příslušného předplatného správce získat přístup ke spuštění příkazů. Další podrobnosti najdete v tématu [spravovat řízení přístupu podle role pomocí prostředí Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="in-which-azure-regions-are-traffic-analytics-available"></a>Ve které oblasti jsou analýzu provozu dostupné?

Analýza provozu můžete použít pro skupiny zabezpečení sítě v některém z následujících podporovaných oblastí: střed USA – Západ, USA – východ, USA – východ 2, střed USA – sever, střed USA – Jih, střed USA, USA – Západ, USA – západ 2, západní Evropa, Severní Evropa, Velká Británie – Západ, Velká Británie – Jih, Austrálie – východ, Austrálie – jihovýchod a Jihovýchodní Asie. Pracovní prostor Log Analytics musí existovat v na západní USA, střed USA, východní USA, západní Evropa, Velká Británie – Jih, Austrálie – jihovýchod nebo jihovýchodní Asie.

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Můžete povolit tok skupiny zabezpečení sítě se protokoly pro v různých oblastech, než pracovní prostor?

Ano, tyto skupiny Nsg můžou být v různých oblastech než pracovního prostoru Log Analytics.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Lze nakonfigurovat více skupin zabezpečení sítě v rámci jednoho pracovního prostoru?

Ano.

## <a name="can-i-use-an-existing-workspace"></a>Můžete použít stávající pracovní prostor?

Ano. Pokud vyberete existující pracovní prostor, ujistěte se, že byla migrována na nový dotazovací jazyk. Pokud nechcete upgradovat pracovní prostor, musíte vytvořit nový. Další informace o nový dotazovací jazyk, v tématu [Azure Log Analytics upgradovat na nové prohledávání protokolů](../log-analytics/log-analytics-log-search-upgrade.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-operations-management-suite-workspace-be-in-a-different-subscription"></a>Můžete svůj účet úložiště Azure se v jednom předplatném a pracovní prostor Operations Management Suite se v jiném předplatném?

Ano, váš účet úložiště Azure může být v jednom předplatném, a váš pracovní prostor Operations Management Suite může být v jiném předplatném.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>V jiném předplatném, ukládání nezpracovaných protokolů?

Ne. Libovolný účet úložiště, kde je povolena skupinu zabezpečení sítě pro protokoly toků můžete ukládat nezpracovaných protokolů. Účet úložiště a nezpracovaných protokolů musí však být ve stejném předplatném a oblasti.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Co když nelze nakonfigurovat skupinu NSG pro analýzu provozu z důvodu chyby "Nenalezeno"?

Vyberte podporovanou oblast. Pokud vyberete oblast není podporováno, se zobrazí chyba "Nebyl nalezen". Podporované oblasti jsou uvedeny dříve v tomto článku.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Co když se zobrazuje stav, "se nepodařilo načíst," v části stránky protokolů toku NSG?

Tok protokolování fungovalo správně, musí mít zaregistrovaný poskytovatel Microsoft.Insights. Pokud si nejste jisti, zda je pro vaše předplatné zaregistrovaný poskytovatel Microsoft.Insights, nahraďte *xxxxx-xxxxx-xxxxxx-xxxx* v následující příkaz a spusťte následující příkazy z prostředí PowerShell:

```powershell-interactive
**Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Konfiguroval jsem řešení. Proč mi nezobrazují nic na řídicím panelu?

Řídicím panelu může trvat až 30 minut se zobrazí při prvním. Řešení musí nejprve shromažďovat dostatek dat, aby se odvodit smysluplné přehledy. Potom generuje sestavy. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Co když se zobrazí tato zpráva: "jsme nenašli žádná data z tohoto pracovního prostoru pro vybraný časový interval. Zkuste změnit časový interval, nebo vyberte jiný pracovní prostor. "?

Vyzkoušejte následující možnosti:
- Změňte časový interval, v horním panelu.
- Vyberte jiný pracovní prostor Log Analytics v horním panelu.
- Zkuste se analýza provozu za 30 minut, pokud byla nedávno povolena.
    
Pokud potíže potrvají, vyvolat připomínky můžete vystavit ve [fóru User voice](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Co když se zobrazí tato zpráva: "analýza skupina NSG flow protokoly poprvé. Tento proces může trvat 20 – 30 minut. Kontrola znovu za chvíli. (2) Pokud váš pracovní prostor je pod SKU zdarma předchozí krok nefunguje, zkontrolujte ověření přes kvótu využití pracovního prostoru, jinak postupujte při nejčastějších dotazech týkajících se žádostí o další informace. "?

Vzhledem k tomu, může se zobrazit tato zpráva:
- Analýza provozu nedávno povolilo řešení a nemusí mít ještě agregovat dostatek dat, aby se odvodit smysluplné přehledy.
- Používáte bezplatnou verzi pracovního prostoru Operations Management Suite a překročení limitů kvóty. Můžete potřebovat použít pracovní prostor s větší kapacitou.
    
Pokud potíže potrvají, vyvolat připomínky můžete vystavit ve [fóru User voice](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Co když se zobrazí tato zpráva: "vypadá to, máme data prostředků (topologii) a informace o žádné toky. Mezitím můžete kliknout sem pro zobrazení zdroje dat a další informace naleznete na nejčastější dotazy. "?

Informace o zdroji se zobrazuje na řídicím panelu; Nicméně jsou k dispozici žádné statistiky týkající se flow. Data, nemusí být k dispozici z důvodu žádné toky komunikaci mezi prostředky. Počkejte po dobu 60 minut a spusťte opětovnou kontrolu stavu. Pokud se problém nevyřeší, a jste si jisti, že existují toky komunikaci mezi prostředky, vyvolat připomínky můžete vystavit ve [fóru User voice](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Můžete nakonfigurovat analýzu provozu pomocí Powershellu nebo šablony Azure Resource Manageru nebo klienta?

Analýza provozu můžete nakonfigurovat pomocí Windows Powershellu z verze 6.2.1 a vyšší. Konfigurace protokolování toků a analýzu provozu pro konkrétní skupiny zabezpečení sítě pomocí rutiny Set, naleznete v tématu [Set-AzureRmNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermnetworkwatcherconfigflowlog?view=azurermps-6.3.0). Protokolování toků a stav analýzy provozu pro konkrétní skupiny zabezpečení sítě, získáte [Get-AzureRmNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkwatcherflowlogstatus?view=azurermps-6.3.0).

V současné době nelze použít šablonu Azure Resource Manageru nakonfigurovat analýzu provozu.

Konfigurace analýzy provozu pomocí klienta Azure Resource Manageru, viz následující příklady.

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
**Získejte příkladu rutiny:**
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



## <a name="how-is-traffic-analytics-priced"></a>Jaké je analýza provozu ceny?

Analýza provozu se měří. Měření je založen na zpracování dat protokolů toku službou a ukládání výsledný rozšířené protokoly v pracovním prostoru Log Analytics. 

Příklad, jak je uvedeno [cenový plán](https://azure.microsoft.com/pricing/details/network-watcher/), vzhledem k tomu oblasti střed USA – Západ, pokud protokolů toku dat uložených v účtu úložiště, zpracování analýzy provozu je 10 GB a vylepšené protokoly v pracovním prostoru Log Analytics je 1 GB pak bude příslušné poplatky jsou: 10 x 2.3$ + 1 × 2.76$ = 25.76$

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Jak můžete Navigovat pomocí klávesnice v zobrazení geografické mapy?

Na stránce geografické mapy obsahuje dvě hlavní části:
    
- **Banner**: banner v horní části geografické mapy obsahuje tlačítka a vyberte filtry distribuce přenosů (například nasazení, provoz ze zemí a škodlivé). Když vyberete tlačítko, příslušný filtr platí na mapě. Například pokud vyberete tlačítko aktivní, zvýrazní mapy aktivní datových center ve vašem nasazení.
- **Mapa**: následující hlavičky, části Mapa zobrazuje distribuce provozu mezi datacentry Azure a zemí.
    
### <a name="keyboard-navigation-on-the-banner"></a>Procházení pomocí klávesnice na informační zprávě
    
- Ve výchozím nastavení je výběr na stránce geografické mapy pro hlavičky filtr "Azure řadiče domény".
- Chcete-li přesunout na jiný filtr, použijte buď `Tab` nebo `Right arrow` klíč. Pro přechod dozadu, použijte buď `Shift+Tab` nebo `Left arrow` klíč. Navigace směrem vpřed je zleva doprava, za nímž následuje nahoru, dolů.
- Stisknutím klávesy `Enter` nebo `Down` šipka použít vybraný filtr. Na základě výběru filtru a nasazení, jsou zvýrazněny jeden nebo více uzlů v mapě oddílu.
- Přepnout mezi banner a mapy, stiskněte klávesu `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-map"></a>Procházení pomocí klávesnice na mapě
    
- Po stisknutí a vybrali libovolný filtr na informační zprávě `Ctrl+F6`, fokus přesunete na některý zvýrazněné uzly (**datové centrum Azure** nebo **země/oblast**) v zobrazení mapy.
- Chcete-li přesunout do jiných zvýrazněné uzly na mapě, použijte buď `Tab` nebo `Right arrow` klíče pro přesun vpřed. Použití `Shift+Tab` nebo `Left arrow` klíče pro přesun zpětně.
- Chcete-li vybrat všechny zvýrazněný uzel na mapě, použijte `Enter` nebo `Down arrow` klíč.
- Na výběr z těchto uzlů, přesune fokus **informační okno nástroje** pro uzel. Ve výchozím nastavení, se aktivuje tlačítko uzavřeno **informační okno nástroje**. Další přesunout uvnitř **pole** použijte `Right arrow` a `Left arrow` klíče pro přesun vpřed a zpět v uvedeném pořadí. Stisknutím klávesy `Enter` má stejný účinek jako cílené tlačítka v **informační okno nástroje**.
- Když stisknete klávesu `Tab` při, zaměřuje se na **informační okno nástroje**, fokus se přesune do koncových bodů v být stejný kontinent jako vybraný uzel. Použití `Right arrow` a `Left arrow` kláves přes tyto koncové body.
- Chcete-li přesunout do jiné koncové body toku nebo obsah přenášel clustery, použijte `Tab` pro přesun vpřed a `Shift+Tab` pro přesun zpětně.
- Když je fokus na **kontinent clusterů**, použijte `Enter` nebo `Down` šipkami, abyste měli na očích koncové body uvnitř kontinentů clusteru. Přechod k koncové body a na tlačítko Zavřít informační okno kontinentů clusteru, použijte buď `Right arrow` nebo `Left arrow` klíč pro přesun vpřed a zpět, v uvedeném pořadí. Na libovolný koncový bod, můžete použít `Shift+L` přepnout na řádku připojení ze zvoleného uzlu ke koncovému bodu. Stisknutím klávesy `Shift+L` znovu přejděte na vybraný koncový bod.
        
### <a name="keyboard-navigation-at-any-stage"></a>Procházení pomocí klávesnice v jakékoli fázi
    
- `Esc` Sbalí rozbalené výběr.
- `Up arrow` Klíč provede stejnou akci jako `Esc`. `Down arrow` Klíč provede stejnou akci jako `Enter`.
- Použití `Shift+Plus` přiblížit, a `Shift+Minus` zmenšíte.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Jak můžete Navigovat pomocí klávesnice v zobrazení topologie virtuální sítě?

Na stránce topologie virtuální sítě obsahuje dvě hlavní části:
    
- **Banner**: banner v horní části topologie virtuální sítě poskytuje tlačítka a vyberte filtry distribuce přenosů (například propojenými virtuálními sítěmi, odpojené virtuální sítě a veřejné IP adresy). Když vyberete tlačítko, příslušný filtr platí na topologii. Například pokud vyberete tlačítko aktivní, zvýrazní topologie active virtuální sítě ve vašem nasazení.
- **Topologie**: následující hlavičky, zobrazuje v části topologie distribuce provozu mezi virtuálními sítěmi.
    
### <a name="keyboard-navigation-on-the-banner"></a>Procházení pomocí klávesnice na informační zprávě
    
- Ve výchozím nastavení je výběr na stránce topologie virtuální sítě pro hlavičky filtr "Připojené virtuální sítě".
- Chcete-li přesunout na jiný filtr, použijte `Tab` klíč posunout vpřed. Pro přechod dozadu, použijte `Shift+Tab` klíč. Navigace směrem vpřed je zleva doprava, za nímž následuje nahoru, dolů.
- Stisknutím klávesy `Enter` použít vybraný filtr. Na základě výběru filtru a nasazení, jsou zvýrazněny jeden nebo více uzlů (virtuální sítě) v části topologie.
- Přepnout mezi hlavičky a topologii, stiskněte klávesu `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Procházení pomocí klávesnice na topologii
    
- Po stisknutí a vybrali libovolný filtr na informační zprávě `Ctrl+F6`, fokus přesunete na některý zvýrazněné uzly (**VNet**) v zobrazení topologie.
- Chcete-li přesunout do jiných zvýrazněné uzly v zobrazení topologie, použijte `Shift+Right arrow` klíče pro přesun vpřed. 
- Na zvýrazněné uzly, přesune fokus **informační okno nástroje** pro uzel. Ve výchozím nastavení, přesune fokus **další podrobnosti** tlačítko **informační okno nástroje**. Další přesunout uvnitř **pole** zobrazit, použijte `Right arrow` a `Left arrow` klíče pro přesun vpřed a zpět v uvedeném pořadí. Stisknutím klávesy `Enter` má stejný účinek jako cílené tlačítka v **informační okno nástroje**.
- Na výběr tyto uzly, můžete navštívit jeho připojení jeden po druhém, stisknutím klávesy `Shift+Left arrow` klíč. Přesune fokus **informační okno nástroje** připojení. V kterékoli fázi můžete fokus posune zpět do uzlu stisknutím kombinace kláves `Shift+Right arrow` znovu.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Jak můžete Navigovat pomocí klávesnice v zobrazení topologie podsítě?

Na stránce topologie virtuální podsítě obsahuje dvě hlavní části:
    
- **Banner**: banner v horní části topologie virtuální podsítě obsahuje tlačítka a vyberte filtry distribuce přenosů (například aktivní, střední a brány podsítě). Když vyberete tlačítko, příslušný filtr platí na topologii. Například pokud vyberete tlačítko aktivní, zvýrazní topologie active virtuální podsítě ve vašem nasazení.
- **Topologie**: následující hlavičky, zobrazuje v části topologie distribuce provozu mezi virtuální podsítě.
    
### <a name="keyboard-navigation-on-the-banner"></a>Procházení pomocí klávesnice na informační zprávě
    
- Ve výchozím nastavení je výběr na stránce topologie virtuální podsítě pro hlavičky filtr "Podsítě".
- Chcete-li přesunout na jiný filtr, použijte `Tab` klíč posunout vpřed. Pro přechod dozadu, použijte `Shift+Tab` klíč. Navigace směrem vpřed je zleva doprava, za nímž následuje nahoru, dolů.
- Stisknutím klávesy `Enter` použít vybraný filtr. Na základě výběru filtru a nasazení, jsou zvýrazněny jeden nebo více uzlů (podsítě) v části topologie.
- Přepnout mezi hlavičky a topologii, stiskněte klávesu `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Procházení pomocí klávesnice na topologii
    
- Po stisknutí a vybrali libovolný filtr na informační zprávě `Ctrl+F6`, fokus přesunete na některý zvýrazněné uzly (**podsítě**) v zobrazení topologie.
- Chcete-li přesunout do jiných zvýrazněné uzly v zobrazení topologie, použijte `Shift+Right arrow` klíče pro přesun vpřed. 
- Na zvýrazněné uzly, přesune fokus **informační okno nástroje** pro uzel. Ve výchozím nastavení, přesune fokus **další podrobnosti** tlačítko **informační okno nástroje**. Další přesunout uvnitř **pole** použijte `Right arrow` a `Left arrow` klíče pro přesun vpřed a zpět v uvedeném pořadí. Stisknutím klávesy `Enter` má stejný účinek jako cílené tlačítka v **informační okno nástroje**.
- Na výběr tyto uzly, můžete navštívit jeho připojení jeden po druhém, stisknutím klávesy `Shift+Left arrow` klíč. Přesune fokus **informační okno nástroje** připojení. V kterékoli fázi můžete fokus posune zpět do uzlu stisknutím kombinace kláves `Shift+Right arrow` znovu.    

