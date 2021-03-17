---
title: Připojení dat Azure Firewall k Azure Sentinel
description: Přečtěte si, jak připojit Azure Firewall dat ke službě Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: b21ce75bfb33b5a8869c63b7d3f71fb9f0c93768
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621291"
---
# <a name="connect-data-from-azure-firewall"></a>Připojení dat z Azure Firewall

Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Jedná se o plně stavovou bránu firewall jako službu s integrovanou vysokou dostupností a neomezenou škálovatelností cloudu. 

Protokoly Azure Firewall můžete připojit ke službě Azure Sentinel, což vám umožní zobrazit data protokolu v sešitech, použít je k vytvoření vlastních výstrah a začlenit je do lepšího šetření.

Přečtěte si další informace o [monitorování protokolů Azure firewall](../firewall/firewall-diagnostics.md).

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