---
title: Připojení dat Azure Firewall k Azure Sentinel
description: Přečtěte si, jak připojit Azure Firewall dat ke službě Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 05a2869152def0fc9a44defaa21f0643d9fdbb08
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2020
ms.locfileid: "89504872"
---
# <a name="connect-data-from-azure-firewall"></a>Připojení dat z Azure Firewall

> [!IMPORTANT]
> Konektor dat Azure Firewall v Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Jedná se o plně stavovou bránu firewall jako službu s integrovanou vysokou dostupností a neomezenou škálovatelností cloudu. 

Protokoly Azure Firewall můžete připojit ke službě Azure Sentinel, což vám umožní zobrazit data protokolu v sešitech, použít je k vytvoření vlastních výstrah a začlenit je do lepšího šetření.

Přečtěte si další informace o [monitorování protokolů Azure firewall](https://docs.microsoft.com/azure/firewall/tutorial-diagnostics).

## <a name="prerequisites"></a>Požadavky

- V pracovním prostoru Azure Sentinel musíte mít oprávnění ke čtení a zápisu.

## <a name="connect-to-azure-firewall"></a>Připojení k Azure Firewall
    
1. V navigační nabídce Azure Sentinel vyberte **datové konektory**.

1. V galerii datových konektorů vyberte **Azure firewall** a potom v podokně náhledu vyberte **Stránka otevřít konektor**  .

1. Povolte **diagnostické protokoly** na všech branách firewall, jejichž protokoly chcete připojit:

    1. Vyberte odkaz **otevřít Azure firewall prostředků >** .

    1. Z navigační nabídky **bran firewall** vyberte **nastavení diagnostiky**.

    1. V dolní části seznamu vyberte **+ Přidat nastavení diagnostiky** .

    1. Na obrazovce **nastavení diagnostiky** zadejte název do pole  **název nastavení diagnostiky** .
    
    1. Zaškrtněte políčko **Odeslat do Log Analytics** . Pod ní se zobrazí dvě nová pole. Vyberte relevantní **předplatné** a **Log Analytics pracovní prostor** (kde se nachází Azure Sentinel).

    1. Zaškrtněte políčka u typů pravidel, jejichž protokoly chcete ingestovat. Doporučujeme **AzureFirewallApplicationRule** a **AzureFirewallNetworkRule**.

    1. V horní části obrazovky vyberte **Save (Uložit** ).

1. Pokud chcete použít příslušné schéma v Log Analytics pro Azure Firewall výstrahy, vyhledejte **AzureDiagnostics**.

> [!NOTE]
>
> U tohoto konkrétního datového konektoru se indikátory stavu připojení (barevný pruh v galerii datových konektorů a ikony připojení vedle názvů datových typů) budou zobrazovat jako *připojené* (zeleně) pouze v případě, že data byla v určitém bodě za poslední dva týdny. Když dva týdny prošly bez příjmu dat, konektor se zobrazí jako odpojený. Okamžik, kdy se data dostanou, se vrátí do stavu *připojeno* .

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit protokoly Azure Firewall ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
