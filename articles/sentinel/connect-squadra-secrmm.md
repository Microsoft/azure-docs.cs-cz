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
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: a00b4b1e81c0d644cf1475aa46dda3848fda1365
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632897"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Připojte své Squadra technologie Squadra data k Azure Sentinel 

Konektor Squadra pro Squadra Technologies umožňuje snadno připojit protokoly řešení zabezpečení Squadra pomocí Azure Sentinel. Umožňuje zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Tento konektor vám umožní získat přehled o událostech vyměnitelného úložiště USB. Integrace mezi Squadra technologiemi Squadra a službou Azure Sentinel využívá REST API.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Konfigurace a připojení Squadra Technologies Squadra 

Squadra Technologies Squadra mohou integrovat a exportovat protokoly přímo do Azure Sentinel.
1. Na portálu Sentinel Azure klikněte na datové konektory a vyberte Squadra Technologies Squadra a pak otevřete stránku konektor.

2. Postupujte podle kroků uvedených v [průvodci připojováním technologií Squadra pro Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) a získejte Squadra Squadra data v Azure Sentinel.   

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **CustomLogs** v `secRMM_CL` tabulce.

Pokud chcete zadat dotaz na protokoly Squadra Technologies Squadra, zadejte název tabulky v horní části okna dotazu.

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Squadra Technologies Squadra ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
