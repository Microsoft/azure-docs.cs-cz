---
title: Připojení ochrany před mobilními hrozbami Zimperium do Azure Sentinel | Microsoft Docs
description: Naučte se připojit ochranu před mobilními hrozbami Zimperium do Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 86854fa22a49f09e5d3d2fc5fdb53c245850fbac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77587936"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Připojení ochrany před mobilními hrozbami Zimperium do Azure Sentinel


> [!IMPORTANT]
> Datový konektor ochrany před mobilními hrozbami Zimperium ve službě Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Konektor ochrany před mobilními hrozbami Zimperium vám dává možnost připojit protokol hrozeb Zimperium ke službě Azure Sentinel a zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Díky tomu získáte lepší přehled o mobilních hrozbách vaší organizace a zlepšuje možnosti vaší operace zabezpečení.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Konfigurace a připojení ochrany před mobilními hrozbami Zimperium

Ochranu před mobilními hrozbami Zimperium můžete integrovat a exportovat protokoly přímo do **Azure Sentinel**.

1. Na portálu Sentinel Azure klikněte na datové konektory a vyberte **Zimperium ochrana před mobilními hrozbami**.
2. Vyberte **stránku otevřít konektor**.
3. Postupujte podle pokynů na stránce konektoru **ochrany před mobilními hrozbami Zimperium** , která je shrnuta následujícím způsobem.
 1. V zConsole klikněte na navigačním panelu na možnost **Spravovat** .
 2. Klikněte na kartu **integrace** .
 3. Klikněte na tlačítko **vytváření sestav hrozeb** a pak na tlačítko **Přidat Integration** .
 4. Vytvořte integraci výběrem možnosti **Microsoft Azure Sentinel** z dostupných integrací a zadáním ID a primárního klíče pracovního prostoru se připojte k Azure Sentinel.
 5. K dispozici je taky možnost vybrat úroveň filtru pro data hrozby, která se mají vložit do Azure Sentinel. 

4. Další informace najdete na [portálu zákaznických služeb Zimperium](https://support.zimperium.com).


## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v Log Analytics ZimperiumThreatLog_CL CustomLogs a ZimperiumMitigationLog_CL.

Pokud chcete použít příslušné schéma v Log Analytics pro ochranu před mobilními hrozbami Zimperium, vyhledejte ZimperiumThreatLog_CL a ZimperiumMitigationLog_CL.


## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit ochranu před mobilními hrozbami Zimperium do Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).

- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.

Další informace o Zimperium najdete v následujících tématech:

- [Zimperium](https://zimperium.com)

- [Blog o Zimperium Mobile Security](https://blog.zimperium.com)

- [Portál zákaznické podpory Zimperium](https://support.zimperium.com)

