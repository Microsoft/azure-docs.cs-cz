---
title: Připojení dat secRMM společnosti Squadra Technologies k Azure Sentinel| Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit data secRMM společnosti Squadra Technologies k Azure Sentinelu.
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
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588106"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Připojení dat secRMM společnosti Squadra Technologies k Azure Sentinelu 

> [!IMPORTANT]
> Datový konektor Squadra Technologies Security Removable Media Manager (secRMM) v Azure Sentinelu je aktuálně ve verzi Public Preview.
> Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Konektor secRMM společnosti Squadra Technologies umožňuje snadné připojení protokolů zabezpečení Squadra Technologies secRMM s Azure Sentinel. Umožňuje zobrazit řídicí panely, vytvářet vlastní výstrahy a zlepšit vyšetřování. Tento konektor poskytuje přehled o událostech vyměnitelného úložiště USB. Integrace mezi Squadra Technologies secRMM a Azure Sentinel využívá rozhraní REST API.


> [!NOTE]
> Data se budou ukládat v geografickém umístění pracovního prostoru, ve kterém používáte Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Konfigurace a připojení technologie Squadra secRMM 

Squadra Technologies secRMM můžete integrovat a exportovat protokoly přímo do Azure Sentinelu.
1. Na portálu Azure Sentinel klikněte na Datové konektory a vyberte Squadra Technologies secRMM a pak otevřít stránku konektoru.

2. Postupujte podle pokynů uvedených v [squadra technologie onboarding průvodce pro Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) získat Squadra secRMM data v Azure Sentinelu.   


## <a name="find-your-data"></a>Vyhledání dat

Po navázání úspěšného připojení se data zobrazí v Log Analytics v části CustomLogs secRMM_CL.
Chcete-li použít příslušné schéma v Log Analytics pro secRMM Squadra Technologies, vyhledejte secRMM_CL.

## <a name="validate-connectivity"></a>Ověřit připojení
Může trvat více než 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit Squadra Technologies secRMM k Azure Sentinel. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)

