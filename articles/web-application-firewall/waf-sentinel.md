---
title: Použití funkce Sentinel Azure s bránou firewall webových aplikací Azure
description: V tomto článku se dozvíte, jak používat Azure Sentinel s bránou firewall webových aplikací Azure (WAF).
services: web-application-firewall
author: TreMansdoerfer
ms.service: web-application-firewall
ms.date: 10/12/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 3d905dd1e6acab8f9f6d3885c882dd9c32133cb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596430"
---
# <a name="using-azure-sentinel-with-azure-web-application-firewall"></a>Použití funkce Sentinel Azure s bránou firewall webových aplikací Azure

Firewall webových aplikací Azure (WAF) v kombinaci s Azure Sentinel může poskytovat správu událostí informací o zabezpečení pro prostředky WAF. Azure Sentinel poskytuje analýzy zabezpečení pomocí Log Analytics, což vám umožňuje snadno rozlomit a zobrazovat data WAF. Pomocí služby Azure Sentinel můžete získat přístup k předem vytvořeným sešitům a upravit je tak, aby vyhovovaly potřebám vaší organizace. Sešit může zobrazit analýzy pro WAF ve službě Azure Content Delivery Network (CDN), WAF na frontách Azure a WAF na Application Gateway napříč několika předplatnými a pracovními prostory.

## <a name="waf-log-analytics-categories"></a>Kategorie WAF Log Analytics

WAF Log Analytics je rozdělené do následujících kategorií:  

- Všechny WAF akce provedeny 
- Horní 40 zablokovaných adres URI žádosti 
- Horních triggerů událostí 50  
- Zprávy v průběhu času 
- Podrobnosti o úplné zprávě 
- Události útoku podle zpráv  
- Události útoku v průběhu času 
- Filtr ID sledování 
- Sledovací zprávy ID 
- Prvních 10 IP adres útoku 
- Zprávy o útokech na IP adresy 

## <a name="waf-workbook-examples"></a>Příklady sešitu WAF

Následující příklady WAF sešitu ukazují ukázková data:

:::image type="content" source="media//waf-sentinel/waf-actions-filter.png" alt-text="Filtr akcí WAF":::

:::image type="content" source="media//waf-sentinel/top-50-event-trigger.png" alt-text="Nejčastější události 50":::

:::image type="content" source="media//waf-sentinel/attack-events.png" alt-text="Události útoku":::

:::image type="content" source="media//waf-sentinel/top-10-attacking-ip-address.png" alt-text="Prvních 10 IP adres útoku":::

## <a name="launch-a-waf-workbook"></a>Spustit WAF sešit

WAF sešit funguje pro všechna přední dvířka Azure, Application Gateway a CDN WAF. Před připojením dat z těchto prostředků musí být v prostředku povolená analýza Log Analytics. 

Pokud chcete povolit Log Analytics pro každý prostředek, můžete přejít na konkrétní prostředek Azure front-dveří, Application Gateway nebo CDN:

1. Vyberte **nastavení diagnostiky**.
2. Vyberte **+ Přidat nastavení diagnostiky**. 
3. Na stránce nastavení diagnostiky:
   1. Zadejte název. 
   1. Vyberte **odeslat Log Analytics**. 
   1. Vyberte cílový pracovní prostor protokolu. 
   1. Vyberte typy protokolů, které chcete analyzovat:
      1. Application Gateway: ' ApplicationGatewayAccessLog ' a ' ApplicationGatewayFirewallLog '
      1. Přední dvířka Azure: ' FrontDoorAccessLog ' a ' FrontDoorFirewallLog '
      1. CDN: ' AzureCdnAccessLog '
   1. Vyberte **Uložit**.

   :::image type="content" source="media//waf-sentinel/diagnostics-setting.png" alt-text="Nastavení diagnostiky":::

4. Na domovské stránce Azure zadejte do vyhledávacího panelu **Azure Sentinel** a vyberte prostředek služby **Azure Sentinel** . 
2. Vyberte již aktivní pracovní prostor nebo vytvořte nový pracovní prostor. 
3. Na levé straně v části **Konfigurace** vyberte **datové konektory**.
4. Vyhledejte **Firewall webových aplikací společnosti Microsoft** a vyberte **Firewall webových aplikací (WAF) společnosti Microsoft**. V pravém dolním rohu vyberte stránku **otevřít konektor** .

   :::image type="content" source="media//waf-sentinel/data-connectors.png" alt-text="Datové konektory":::

8. Postupujte podle pokynů v části **Konfigurace** pro každý prostředek WAF, který chcete použít pro Log Analytics data pro případ, že jste to ještě neudělali předtím.
6. Po dokončení konfigurace jednotlivých prostředků WAF vyberte kartu **Další kroky** . Vyberte jeden z doporučených sešitů. Tento sešit bude používat všechna analytická data protokolu, která byla dříve povolena. Pracovní sešit WAF by teď měl existovat pro vaše prostředky WAF.

   :::image type="content" source="media//waf-sentinel/waf-workbooks.png" alt-text="WAF sešity":::


## <a name="next-steps"></a>Další kroky

- [Další informace o Sentinel Azure](../sentinel/overview.md)
- [Další informace o Azure Monitor sešitech](../azure-monitor/visualize/workbooks-overview.md)
