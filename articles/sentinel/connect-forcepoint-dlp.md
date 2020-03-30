---
title: Připojení programu Forcepoint DLP k Azure Sentinel| Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit forcepoint dlp k Azure Sentinelu.
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
ms.openlocfilehash: 3bdb9d2b23ce05929ba5612e0c6a03fe1aab05de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588242"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Připojení programu Forcepoint DLP k Azure Sentinelu

> [!IMPORTANT]
> Forcepoint Data Loss Prevention (DLP) datový konektor v Azure Sentinelu je aktuálně ve verzi Public Preview. Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Konektor Forcepoint DLP umožňuje automaticky exportovat data incidentů DLP do Azure Sentinelu. Můžete ji použít k získání přehledu o aktivitách uživatelů a incidentech se ztrátou dat. Umožňuje také korelace s daty z úloh Azure a dalších informačních kanálů a zlepšuje možnosti monitorování pomocí sešitů v Azure Sentinelu.

> [!NOTE]
> Data se budou ukládat v geografickém umístění pracovního prostoru, ve kterém používáte Azure Sentinel.

## <a name="configure-and-connect-forcepoint-dlp"></a>Konfigurace a připojení programu Forcepoint DLP

Nakonfigurujte forcepoint dlp pro předávání dat incidentů ve formátu JSON do pracovního prostoru Azure prostřednictvím rozhraní REST API, jak je vysvětleno v [Forcepoint DLP Integration Guide](https://frcpnt.com/dlp-sentinel).


## <a name="find-your-data"></a>Vyhledání dat

Po nastavení konektoru Forcepoint DLP se data zobrazí v Log Analytics v části CustomLogs **ForcepointDLPEvents_CL**.


Chcete-li použít příslušné schéma v Log Analytics pro Forcepoint DLP, vyhledejte **ForcepointDLPEvents_CL**.


## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat více než 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli, jak připojit Forcepoint DLP k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:

- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)