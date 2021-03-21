---
title: Připojení dat firewallu webových aplikací Azure (WAF) do Azure Sentinel
description: Naučte se připojit data Azure WAF k Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 05/07/2020
ms.author: yelevin
ms.openlocfilehash: c554f3582e67622a5a1739c9e410328c902d491b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94655897"
---
# <a name="connect-data-from-azure-web-application-firewall-waf"></a>Připojení dat z firewallu webových aplikací Azure (WAF)

Webové aplikace jsou stále více zaměřené na škodlivé útoky, které využívají často známá ohrožení zabezpečení. Firewall webových aplikací Azure (WAF) zajišťuje centralizovanou ochranu webových aplikací před běžným zneužitím a hrozbami, jako je vkládání kódu a skriptování mezi weby. Azure WAF se dá nasadit do služby [azure Application Gateway](../web-application-firewall/ag/ag-overview.md) , služby front-in [Azure](../web-application-firewall/afds/afds-overview.md) a prostřednictvím zásad WAF pro [Azure Content Delivery Network (CDN)](../web-application-firewall/cdn/cdn-overview.md) (aktuálně ve verzi Public Preview).
Protokoly Azure WAF můžete připojit ke službě Azure Sentinel a umožnit vám zobrazovat data protokolu v sešitech, použít je k vytváření vlastních výstrah a začlenit je k vylepšení šetření.

## <a name="prerequisites"></a>Předpoklady

- V pracovním prostoru Azure Sentinel musíte mít oprávnění ke čtení a zápisu.

## <a name="connect-to-azure-waf"></a>Připojení k Azure WAF

### <a name="instructions-tab"></a>Karta pokyny

1. V navigační nabídce Azure Sentinel vyberte **datové konektory**.

1. Z Galerie datových konektorů vyberte **Firewall webových aplikací Azure (WAF)** a pak v podokně náhledu vyberte **stránku otevřít konektor** .

1. Vyberte odkaz pro typ prostředku WAF, jehož protokoly chcete připojit – **otevřete Application Gateway >prostředků**, **otevřete frontu prostředků >** nebo **otevřete Content Delivery Network (CDN) WAF zásadu >** – a jednou na obrazovce seznamu prostředků vyberte prostředek WAF ze seznamu.

    1. V navigační nabídce prostředku WAF vyberte **nastavení diagnostiky**.

    1. V dolní části seznamu vyberte **+ Přidat nastavení diagnostiky** .

    1. Na obrazovce **nastavení diagnostiky** zadejte název do pole **název nastavení diagnostiky** .

    1. Klikněte na zaškrtávací políčko **Odeslat do Log Analytics** . Pod ní se zobrazí dvě nová pole. Vyberte relevantní **předplatné** a **Log Analytics pracovní prostor** (kde se nachází Azure Sentinel).

    1. Zaškrtněte políčka u typů pravidel, jejichž protokoly chcete ingestovat. Naše doporučení pro každý typ prostředku:

        | Prostředek | Protokoly, které se mají vybrat pro ingestování |
        |----------|------------------------------|
        | Application Gateway | ApplicationGatewayAccessLog<br>ApplicationGatewayFirewallLog |
        | Front Door          | FrontdoorAccessLog<br>FrontdoorWebApplicationFirewallLog |
        | Zásady CDN WAF      | WebApplicationFirewallLogs |
        |

    1. Vyberte **Uložit**.

### <a name="next-steps-tab"></a>Karta Další kroky

- Podívejte se na Doporučené sešity, Ukázky dotazů a šablony analytických pravidel, které jsou součástí konektoru dat **brány firewall webových aplikací Azure** , abyste získali přehled o datech protokolu Azure WAF.

- K dotazování na data WAF Azure v **protokolech** zadejte **AzureDiagnostics** do okna dotazu.

> [!NOTE]
>
> U tohoto konkrétního datového konektoru se indikátory stavu připojení (barevný pruh v galerii datových konektorů a ikony připojení vedle názvů datových typů) budou zobrazovat jako *připojené* (zeleně) pouze v případě, že data byla v určitém bodě za poslední dva týdny. Když dva týdny prošly bez příjmu dat, konektor se zobrazí jako odpojený. Okamžik, kdy se data dostanou, se vrátí do stavu *připojeno* .

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit protokoly Azure WAF ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).