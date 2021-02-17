---
title: Řešení Azure Networking Analytics v Azure Monitor | Microsoft Docs
description: Řešení Azure Networking Analytics v Azure Monitor můžete použít ke kontrole protokolů skupin zabezpečení sítě Azure a protokolů Azure Application Gateway.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2018
ms.openlocfilehash: cdb43bd8b91881905b1734d0c0b36c33fb27d232
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577506"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Řešení monitorování sítě Azure v Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor nabízí následující řešení pro monitorování sítí:
* Network Performance Monitor (NPM) do
    * Monitorování stavu sítě
* Přehled služby Azure Application Gateway Analytics
    * Protokoly Application Gateway Azure
    * Metriky Azure Application Gateway
* Řešení pro monitorování a audit síťové aktivity v cloudové síti
    * [Analýza provozu](../../networking/network-monitoring-overview.md#traffic-analytics) 
    * Azure Network Security Group Analytics

## <a name="network-performance-monitor-npm"></a>Network Performance Monitor (NPM)

Řešení pro správu [Network Performance Monitor](../../networking/network-monitoring-overview.md) je řešení pro monitorování sítě, které monitoruje stav, dostupnost a dostupnost sítí.  Slouží k monitorování připojení mezi:

* Veřejný cloud a místní
* Datová centra a umístění uživatelů (pobočky)
* Podsítě hostující různé úrovně vícevrstvé aplikace.

Další informace najdete v tématu [Network Performance Monitor](../../networking/network-monitoring-overview.md).

## <a name="network-security-group-analytics"></a>Analýzy skupin zabezpečení sítě

1. Přidejte řešení pro správu do Azure Monitor a
2. Povolte diagnostiku pro přesměrování diagnostiky do pracovního prostoru Log Analytics v Azure Monitor. Není nutné zapisovat protokoly do úložiště objektů BLOB v Azure.

Pokud nejsou protokoly diagnostiky povolené, ovládací panel pro tento prostředek je prázdný a zobrazí se chybová zpráva.

## <a name="azure-application-gateway-analytics"></a>Analýza Azure Application Gateway

1. Povolte diagnostiku pro přesměrování diagnostiky do pracovního prostoru Log Analytics v Azure Monitor.
2. Využijte podrobný souhrn pro váš prostředek pomocí šablony sešitu pro Application Gateway.

Pokud nejsou pro Application Gateway povolené diagnostické protokoly, do sešitu se naplní jenom výchozí data metriky.


> [!NOTE]
> V lednu 2017 se změnil podporovaný způsob odesílání protokolů z aplikačních bran a skupin zabezpečení sítě do pracovního prostoru Log Analytics. Pokud se zobrazí řešení **Azure Networking Analytics (nepoužívané)** , přečtěte si téma [migrace z původního řešení pro analýzu sítě](#migrating-from-the-old-networking-analytics-solution) , kde najdete kroky, které je potřeba provést.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Kontrola informací o kolekci síťových dat Azure
Řešení Azure Application Gateway Analytics a Správa analýzy skupin zabezpečení sítě shromažďují protokoly diagnostiky přímo ze skupin Azure Application Gateway a zabezpečení sítě. Není nutné zapisovat protokoly do úložiště objektů BLOB v Azure a pro shromažďování dat není nutné žádného agenta.

V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o tom, jak se shromažďují data pro Azure Application Gateway analýzy a analýzy skupin zabezpečení sítě.

| Platforma | Přímý Agent | Agent Operations Manager pro Systems Center | Azure | Operations Manager požadováno? | Data agenta Operations Manager odeslána prostřednictvím skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |při přihlášení |


### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Povolení diagnostiky Azure Application Gateway na portálu

1. V Azure Portal přejděte na prostředek Application Gateway a sledujte ho.
2. Vyberte *nastavení diagnostiky* a otevřete následující stránku.

   ![Snímek obrazovky s konfigurací nastavení diagnostiky pro prostředek Application Gateway](media/azure-networking-analytics/diagnostic-settings-1.png)

   [![Snímek obrazovky stránky pro konfiguraci nastavení diagnostiky](media/azure-networking-analytics/diagnostic-settings-2.png)](media/azure-networking-analytics/application-gateway-diagnostics-2.png#lightbox)

5. Kliknutím na zaškrtávací políčko pro *odeslání Log Analytics*.
6. Vyberte existující pracovní prostor Log Analytics nebo vytvořte pracovní prostor.
7. Kliknutím zaškrtněte políčko **protokol** pro každý typ protokolu, který chcete shromáždit.
8. Kliknutím na *Uložit* Povolte protokolování diagnostiky do Azure monitor.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Povolení diagnostiky sítě Azure pomocí PowerShellu

Následující skript PowerShellu poskytuje příklad, jak povolit protokolování prostředků pro aplikační brány.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

#### <a name="accessing-azure-application-gateway-analytics-via-azure-monitor-network-insights"></a>Přístup ke službě Azure Application Gateway Analytics prostřednictvím Azure Monitor Network Insights

K Application Insights se dá získat pøístup prostřednictvím karty Insights v rámci prostředku Application Gateway.

![Snímek obrazovky Application Gateway Insights ](media/azure-networking-analytics/azure-appgw-insights.png
)

Na kartě Zobrazit podrobné metriky se otevře předem vyplněný sešit shrnující data z vašeho Application Gateway.

[![Snímek obrazovky Application Gatewayového sešitu](media/azure-networking-analytics/azure-appgw-workbook.png)](media/azure-networking-analytics/application-gateway-workbook.png#lightbox)

### <a name="new-capabilities-with-azure-monitor-network-insights-workbook"></a>Nové funkce se sešitem Azure Monitor Network Insights

> [!NOTE]
> K sešitu Azure Monitor Insights nejsou přidružené žádné další náklady. Pracovní prostor Log Analytics bude nadále účtován jako na základě využití.

Sešit Network Insights vám umožní využít výhod nejnovějších funkcí Azure Monitor a Log Analytics, včetně těchto:

* Centralizovaná Konzola pro monitorování a řešení potíží s daty [metriky](../insights/network-insights-overview.md#resource-health-and-metrics) a protokolů.

* Flexibilní plátno pro podporu vytváření vlastních propracovaných [vizualizací](../visualize/workbooks-overview.md#visualizations).

* Možnost využívat a [sdílet šablony sešitů](../visualize/workbooks-overview.md#workbooks-versus-workbook-templates) s širší komunitou

Další informace o možnostech nového sešitu řešení pro rezervaci [sešitů – přehled](../visualize/workbooks-overview.md)

## <a name="migrating-from-azure-gateway-analytics-solution-to-azure-monitor-workbooks"></a>Migrace z řešení Azure Gateway Analytics do sešitů Azure Monitor

> [!NOTE]
> Pro přístup k metrikám a Log Analytics pro vaše prostředky Application Gateway doporučujeme sešit Azure Monitor Network Insights.

1. Zajistěte, aby [nastavení diagnostiky byla povolená](#enable-azure-application-gateway-diagnostics-in-the-portal) pro ukládání protokolů do pracovního prostoru Log Analytics. Pokud je už nakonfigurovaná, Azure Monitor sešit Network Insights bude moct využívat data ze stejného umístění a žádné další změny se nevyžadují.

> [!NOTE]
> Všechna minulá data jsou již k dispozici v rámci sešitu z nastavení diagnostiky bodu byla původně povolena. Není vyžadován žádný přenos dat.

2. Přístup k [výchozímu sešitu Insights](#accessing-azure-application-gateway-analytics-via-azure-monitor-network-insights) pro váš Application Gateway prostředek. Všechny existující přehledy podporované řešením Application Gateway Analytics už v sešitu existují. To můžete roztáhnout tak, že přidáte vlastní [vizualizace](../visualize/workbooks-overview.md#visualizations) na základě metriky & data protokolu.

3. Až budete moct zobrazit veškerou metriku a log Insights, můžete řešení Azure Gateway Analytics odstranit z pracovního prostoru pomocí stránky řešení.

[![Snímek obrazovky s možností odstranění pro řešení Azure Application Gateway Analytics](media/azure-networking-analytics/azure-appgw-analytics-delete.png)](media/azure-networking-analytics/application-gateway-analytics-delete.png#lightbox)

## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Řešení analýzy skupin zabezpečení sítě Azure v Azure Monitor

![Symbol Azure Network Security Group Analytics](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> Řešení analýzy skupin zabezpečení sítě se přesouvá na podporu komunity, protože její funkce byla nahrazena [Analýza provozu](../../network-watcher/traffic-analytics.md).
> - Řešení je teď dostupné v [šablonách pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) a už brzy nebude dostupné v Azure Marketplace.
> - Stávajícím zákazníkům, kteří už toto řešení přidali do svého pracovního prostoru, bude i nadále fungovat bez jakýchkoli změn.
> - Microsoft bude dál podporovat odesílání protokolů prostředků NSG do vašeho pracovního prostoru pomocí nastavení diagnostiky.

Pro skupiny zabezpečení sítě jsou podporovány následující protokoly:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení Azure Networking Analytics použijte následující pokyny:

1. Pomocí postupu popsaného v tématu [přidání Azure monitor řešení z galerie řešení](./solutions.md)povolte řešení Azure Network Security Group Analytics.
2. Povolte protokolování diagnostiky pro prostředky [skupiny zabezpečení sítě](../../virtual-network/virtual-network-nsg-manage-log.md) , které chcete monitorovat.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Povolení diagnostiky skupiny zabezpečení sítě Azure na portálu

1. V Azure Portal přejděte na prostředek skupiny zabezpečení sítě, který se bude monitorovat.
2. Výběrem *diagnostické protokoly* otevřete následující stránku

   ![Snímek obrazovky se stránkou diagnostické protokoly pro prostředek skupiny zabezpečení sítě, který ukazuje možnost zapnout diagnostiku.](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. Kliknutím na *zapnout diagnostiku* otevřete následující stránku.

   ![Snímek obrazovky stránky pro konfiguraci nastavení diagnostiky Stav je nastaven na zapnuto, je vybrána možnost Odeslat do Log Analytics a jsou vybrány dva typy protokolů.](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Pokud chcete zapnout diagnostiku, *klikněte na* pod položkou *stav* .
5. Kliknutím na zaškrtávací políčko pro *odeslání Log Analytics*
6. Vyberte existující pracovní prostor Log Analytics nebo vytvořte pracovní prostor.
7. Zaškrtněte políčko **protokol** pro každý typ protokolu, který se má shromažďovat.
8. Kliknutím na *Uložit* Povolte protokolování diagnostiky na Log Analytics

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Povolení diagnostiky sítě Azure pomocí PowerShellu

Následující skript PowerShellu poskytuje příklad, jak povolit protokolování prostředků pro skupiny zabezpečení sítě.
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Použití analýzy skupin zabezpečení sítě Azure
Po kliknutí na dlaždici **Azure Network Security Group Analytics** na přehledu můžete zobrazit souhrny protokolů a pak přejít k podrobnostem v následujících kategoriích:

* Blokované toky ve skupině zabezpečení sítě
  * Pravidla skupiny zabezpečení sítě s blokovanými toky
  * Adresy MAC s blokovanými toky
* Povolené toky ve skupině zabezpečení sítě
  * Pravidla skupiny zabezpečení sítě s povolenými toky
  * Adresy MAC s povolenými toky

![Snímek obrazovky s daty pro skupinu zabezpečení sítě: blokované toky, včetně pravidel s blokovanými toky a adresou MAC s blokovanými toky.](media/azure-networking-analytics/log-analytics-nsg01.png)

![Snímek obrazovky dlaždic s daty pro skupinu zabezpečení sítě: povolené toky, včetně pravidel s povolenými toky a adresami MAC s povolenými toky.](media/azure-networking-analytics/log-analytics-nsg02.png)

Na řídicím panelu **Analytics skupiny zabezpečení sítě Azure** zkontrolujte souhrnné informace v jednom z oken a potom kliknutím na jednu Zobrazte podrobné informace na stránce prohledávání protokolu.

Na kterékoli stránce pro prohledávání protokolu můžete zobrazit výsledky podle času, podrobných výsledků a historie prohledávání protokolu. K zúžení výsledků můžete také filtrovat podle omezujících vlastností.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrace ze starého řešení pro analýzu sítě
V lednu 2017 se v pracovním prostoru Log Analytics změnil podporovaný způsob odesílání protokolů ze skupin Azure Application Gateway a skupin zabezpečení sítě Azure. Tyto změny poskytují následující výhody:
+ Protokoly se zapisují přímo do Azure Monitor bez nutnosti používat účet úložiště.
+ Menší latence od času, kdy jsou do nich generovány protokoly, které jsou k dispozici v Azure Monitor
+ Méně kroků konfigurace
+ Společný formát pro všechny typy diagnostiky Azure

Použití aktualizovaných řešení:

1. [Konfigurace diagnostiky, která se pošle přímo do Azure Monitor z Azure Application Gateway](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Konfigurace diagnostiky, která se má odeslat přímo Azure Monitor ze skupin zabezpečení sítě Azure](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Povolte *Azure Application Gateway Analytics* a řešení *Azure Network Security Group Analytics* pomocí procesu popsaného v tématu [Přidání Azure monitor řešení z galerie řešení](solutions.md)
3. Aktualizovat všechny uložené dotazy, řídicí panely nebo výstrahy, aby používaly nový datový typ
   + Typ je AzureDiagnostics. K filtrování síťových protokolů Azure můžete použít ResourceType.

     | Namísto: | Použije |
     | --- | --- |
     | NetworkApplicationgateways &#124;, kde OperationName = = "ApplicationGatewayAccess" | AzureDiagnostics &#124; kde ResourceType = = "APPLICATIONGATEWAYS" a NázevOperace = = "ApplicationGatewayAccess" |
     | NetworkApplicationgateways &#124;, kde OperationName = = "ApplicationGatewayPerformance" | AzureDiagnostics &#124; kde ResourceType = = "APPLICATIONGATEWAYS" a NázevOperace = = "ApplicationGatewayPerformance" |
     | NetworkSecuritygroups | AzureDiagnostics &#124; kde ResourceType = = "NETWORKSECURITYGROUPS" |

   + Pro každé pole, které má \_ v názvu příponu s, \_ d nebo \_ g, změňte první znak na malá písmena.
   + Pro každé pole, které má příponu \_ o v názvu, jsou data rozdělena do jednotlivých polí na základě názvů vnořených polí.
4. Odeberte řešení *Azure Networking Analytics (zastaralé)* .
   + Pokud používáte PowerShell, použijte `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Data shromážděná před změnou nejsou v novém řešení viditelná. Můžete pokračovat v dotazování na tato data pomocí starého názvu typu a pole.

## <a name="troubleshooting"></a>Řešení potíží
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Další kroky
* K zobrazení podrobných dat diagnostiky Azure použijte [dotazy protokolu v Azure monitor](../logs/log-query-overview.md) .

