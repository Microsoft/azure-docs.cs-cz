---
title: Připojení dat Azure DDoS Protection k Azure Sentinel
description: Naučte se ingestovat Azure DDoS Protection data do Azure Sentinel, abyste je mohli zobrazit, analyzovat a prozkoumat.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/01/2020
ms.author: yelevin
ms.openlocfilehash: e8e44f69965af1987bd5f023644d966b3caf1c77
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505819"
---
# <a name="connect-data-from-azure-ddos-protection"></a>Připojení dat z Azure DDoS Protection

> [!IMPORTANT]
> Konektor dat Azure DDoS Protection v Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Služba DDoS (Distributed DOS) se pokouší vyčerpat prostředky aplikace, takže aplikace nebude k dispozici oprávněným uživatelům. Útoky DDoS můžou cílit na jakýkoli koncový bod, který je veřejně dostupný přes internet. [Ochrana Azure DDoS](../virtual-network/ddos-protection-overview.md)v kombinaci s osvědčenými postupy pro návrh aplikací poskytuje robustní ochranu před útoky DDoS. Protokoly Azure DDoS Protection můžete připojit ke službě Azure Sentinel a umožnit vám zobrazovat data protokolu v sešitech, použít je k vytváření vlastních výstrah a začlenit je k vylepšení šetření. 

## <a name="prerequisites"></a>Požadavky

- V pracovním prostoru Azure Sentinel musíte mít oprávnění ke čtení a zápisu.

- Musíte mít nakonfigurovaný plán služby [Azure DDoS Standard Protection](../virtual-network/manage-ddos-protection.md#create-a-ddos-protection-plan).

- Musíte mít nakonfigurovanou [virtuální síť s povolenou službou Azure DDoS Standard](../virtual-network/manage-ddos-protection.md#enable-ddos-for-a-new-virtual-network).

## <a name="connect-to-azure-ddos-protection"></a>Připojení k Azure DDoS Protection
    
1. V navigační nabídce Azure Sentinel vyberte **datové konektory**.

1. V galerii datových konektorů vyberte **Azure DDoS Protection** a potom v podokně náhledu vyberte **Stránka otevřít konektor** .

1. Povolte **diagnostické protokoly** na všech branách firewall, jejichž protokoly chcete připojit:

    1. Vyberte odkaz **Otevřít nastavení diagnostiky >** a v seznamu vyberte prostředek **veřejné IP adresy** .

    1. Vyberte **+ Přidat nastavení diagnostiky**.

    1. Na obrazovce **nastavení diagnostiky** :
       - Do pole  **název nastavení diagnostiky** zadejte název.

       - Zaškrtněte políčko **Odeslat do Log Analytics** . Pod ní se zobrazí dvě nová pole. Vyberte relevantní **předplatné** a **Log Analytics pracovní prostor** (kde se nachází Azure Sentinel).

       - Zaškrtněte políčka u typů pravidel, jejichž protokoly chcete ingestovat. Doporučujeme **DDoSProtectionNotifications**, **DDoSMitigationFlowLogs**a **DDoSMitigationReports**.

    1. V horní části obrazovky klikněte na **Uložit** . Tento postup opakujte pro všechny další brány firewall (veřejné IP adresy), pro které jste povolili DDoS Protection.

1. Pokud chcete použít příslušné schéma v Log Analytics pro Azure DDoS Protection výstrahy, vyhledejte **AzureDiagnostics**.

> [!NOTE]
>
> U tohoto konkrétního datového konektoru se indikátory stavu připojení (barevný pruh v galerii datových konektorů a ikony připojení vedle názvů datových typů) budou zobrazovat jako *připojené* (zeleně) pouze v případě, že data byla v určitém bodě za poslední dva týdny. Když dva týdny prošly bez příjmu dat, konektor se zobrazí jako odpojený. Okamžik, kdy se data dostanou, se vrátí do stavu *připojeno* .

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit protokoly Azure DDoS Protection ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
