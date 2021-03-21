---
title: Připojení dat Azure DDoS Protection k Azure Sentinel
description: Naučte se ingestovat Azure DDoS Protection data do Azure Sentinel, abyste je mohli zobrazit, analyzovat a prozkoumat.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 8089b1e74e88db81c1c15ad2cbf2072abcfff241
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98621341"
---
# <a name="connect-data-from-azure-ddos-protection"></a>Připojení dat z Azure DDoS Protection

Služba DDoS (Distributed DOS) se pokouší vyčerpat prostředky aplikace, takže aplikace nebude k dispozici oprávněným uživatelům. Útoky DDoS můžou cílit na jakýkoli koncový bod, který je veřejně dostupný přes internet. [Ochrana Azure DDoS](../ddos-protection/ddos-protection-overview.md)v kombinaci s osvědčenými postupy pro návrh aplikací poskytuje robustní ochranu před útoky DDoS. Protokoly Azure DDoS Protection můžete připojit ke službě Azure Sentinel a umožnit vám zobrazovat data protokolu v sešitech, použít je k vytváření vlastních výstrah a začlenit je k vylepšení šetření. 

## <a name="prerequisites"></a>Předpoklady

- V pracovním prostoru Azure Sentinel musíte mít oprávnění ke čtení a zápisu.

- Musíte mít nakonfigurovaný plán služby [Azure DDoS Standard Protection](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan).

- Musíte mít nakonfigurovanou [virtuální síť s povolenou službou Azure DDoS Standard](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network).

## <a name="connect-to-azure-ddos-protection"></a>Připojení k Azure DDoS Protection
    
1. V navigační nabídce Azure Sentinel vyberte **datové konektory**.

1. V galerii datových konektorů vyberte **Azure DDoS Protection** a potom v podokně náhledu vyberte **Stránka otevřít konektor** .

1. Povolte **diagnostické protokoly** na všech veřejných IP adresách, jejichž protokoly chcete připojit:

    1. Vyberte odkaz **Otevřít nastavení diagnostiky >** a v seznamu vyberte prostředek **veřejné IP adresy** .

    1. Vyberte **+ Přidat nastavení diagnostiky**.

    1. Na obrazovce **nastavení diagnostiky** :
       - Do pole  **název nastavení diagnostiky** zadejte název.

       - Zaškrtněte políčko **Odeslat do Log Analytics** . Pod ní se zobrazí dvě nová pole. Vyberte relevantní **předplatné** a **Log Analytics pracovní prostor** (kde se nachází Azure Sentinel).

       - Zaškrtněte políčka u typů pravidel, jejichž protokoly chcete ingestovat. Doporučujeme **DDoSProtectionNotifications**, **DDoSMitigationFlowLogs** a **DDoSMitigationReports**.

    1. V horní části obrazovky klikněte na **Uložit** . Tento postup opakujte pro všechny další brány firewall (veřejné IP adresy), pro které jste povolili DDoS Protection.

1. Pokud chcete použít příslušné schéma v Log Analytics pro Azure DDoS Protection výstrahy, vyhledejte **AzureDiagnostics**.

> [!NOTE]
>
> U tohoto konkrétního datového konektoru se indikátory stavu připojení (barevný pruh v galerii datových konektorů a ikony připojení vedle názvů datových typů) budou zobrazovat jako *připojené* (zeleně) pouze v případě, že data byla v určitém bodě za poslední dva týdny. Když dva týdny prošly bez příjmu dat, konektor se zobrazí jako odpojený. Okamžik, kdy se data dostanou, se vrátí do stavu *připojeno* .

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit protokoly Azure DDoS Protection ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
