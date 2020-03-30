---
title: Připojení ochrany proti mobilním hrozbám Zimperium k Azure Sentinel| Dokumenty společnosti Microsoft
description: Přečtěte si, jak propojit zimperium mobile threat defense s Azure Sentinelem.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587936"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Připojení ochrany proti mobilním hrozbám Zimperium k Azure Sentinelu


> [!IMPORTANT]
> Datový konektor Zimperium Mobile Threat Defense v Azure Sentinelu je momentálně ve verzi Public Preview.
> Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Konektor Zimperium Mobile Threat Defense vám dává možnost připojit protokol hrozeb Zimperium s Azure Sentinelem a zobrazit řídicí panely, vytvořit vlastní výstrahy a zlepšit vyšetřování. Získáte tak lepší přehled o prostředí mobilních hrozeb ve vaší organizaci a vylepšuje tezi operací zabezpečení.

> [!NOTE]
> Data se budou ukládat v geografickém umístění pracovního prostoru, ve kterém používáte Azure Sentinel.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Konfigurace a připojení zimperium mobilní ochrany proti hrozbám

Zimperium Mobile Threat Defense může integrovat a exportovat protokoly přímo do **Azure Sentinelu**.

1. Na portálu Azure Sentinel klikněte na datové konektory a vyberte **Zimperium Mobile Threat Defense**.
2. Vyberte **Otevřít stránku konektoru**.
3. Postupujte podle pokynů na stránce **konektoru Zimperium Mobile Threat Defense,** která je shrnuta následujícím způsobem.
 1. V okně zConsole klikněte na navigačním panelu na **Spravovat.**
 2. Klikněte na kartu **Integrace.**
 3. Klikněte na tlačítko **Hlášení hrozeb** a potom na tlačítko **Přidat integrace.**
 4. Vytvořte integraci výběrem **Microsoft Azure Sentinel** z dostupných integrací a zadejte ID pracovního prostoru a primární klíč pro připojení k Azure Sentinelu.
 5. Možnost vybrat úroveň filtru pro data hrozeb, která mají být nabízena do Azure Sentinelu, je také k dispozici. 

4. Další informace naleznete na [portálu zákaznické podpory Zimperium](https://support.zimperium.com).


## <a name="find-your-data"></a>Vyhledání dat

Po navázání úspěšného připojení se data zobrazí v Log Analytics v části CustomLogs ZimperiumThreatLog_CL a ZimperiumMitigationLog_CL.

Chcete-li použít příslušné schéma v Log Analytics pro Zimperium Mobile Threat Defense, vyhledejte ZimperiumThreatLog_CL a ZimperiumMitigationLog_CL.


## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat více než 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli, jak připojit Zimperium Mobile Threat Defense k Azure Sentinel. Další informace o Azure Sentinelu najdete v následujících článcích:

- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).

- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).

- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)

Další informace o Zimperiu najdete v následujících tématech:

- [Zimperium](https://zimperium.com)

- [Zimperium Mobilní Bezpečnost Blog](https://blog.zimperium.com)

- [Zimperium Zákaznický portál podpory](https://support.zimperium.com)

