---
title: Připojení Squadra Technologies Squadra data do Azure Sentinel | Microsoft Docs
description: Naučte se připojit Squadra Technologies Squadra data do Azure Sentinel.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77588106"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Připojte své Squadra technologie Squadra data k Azure Sentinel 

> [!IMPORTANT]
> Konektor dat Squadra Technologies Security Removable Media Manager (Squadra) v Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Konektor Squadra pro Squadra Technologies umožňuje snadno připojit protokoly řešení zabezpečení Squadra pomocí Azure Sentinel. Umožňuje zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Tento konektor vám umožní získat přehled o událostech vyměnitelného úložiště USB. Integrace mezi Squadra technologiemi Squadra a službou Azure Sentinel využívá REST API.


> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Konfigurace a připojení Squadra Technologies Squadra 

Squadra Technologies Squadra mohou integrovat a exportovat protokoly přímo do Azure Sentinel.
1. Na portálu Sentinel Azure klikněte na datové konektory a vyberte Squadra Technologies Squadra a pak otevřete stránku konektor.

2. Postupujte podle kroků uvedených v [průvodci připojováním technologií Squadra pro Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) a získejte Squadra Squadra data v Azure Sentinel.   


## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v Log Analytics secRMM_CL CustomLogs.
Pokud chcete pro Squadra Technologies Squadra použít příslušné schéma v Log Analytics, vyhledejte secRMM_CL.

## <a name="validate-connectivity"></a>Ověřit připojení
Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Squadra Technologies Squadra ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.

