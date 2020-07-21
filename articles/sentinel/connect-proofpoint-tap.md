---
title: Připojte data Proofpoint cílené ochrany (klepnutím) do Azure Sentinel | Microsoft Docs
description: Naučte se připojit data Proofpoint cílené ochrany (klepnutím) do Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: e4185de879fa6136531e6d4c64908befa1d3bc45
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531182"
---
# <a name="connect-your-proofpoint-tap-to-azure-sentinel-with-azure-function"></a>Připojení Proofpoint klepnutím do Azure Sentinel pomocí funkce Azure Functions

Konektor pro Proofpoint cíleného útoku (klepnutím) umožňuje snadno připojit všechny vaše [Proofpointy klepnutím](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection) na protokoly řešení zabezpečení pomocí služby Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Integrace mezi Proofpoint klepnutím a službou Azure Sentinel využívá Azure Functions k vyžádání dat protokolu pomocí REST API.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-proofpoint-tap"></a>Konfigurace a připojení Proofpoint klepnutím

Azure Functions mohou integrovat a přijímat události a protokoly přímo z Proofpoint klepnutím a přesílat je do Azure Sentinel.

1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte **Proofpoint klepněte na** konektor.

1. Vyberte **stránku otevřít konektor**.

1. Postupujte podle pokynů na stránce **PROOFPOINT klepnutí** .

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v Log Analytics v tabulkách **ProofpointTAPMessagesBlocked_CL**, **ProofpointTAPMessagesDelivered_CL**, **ProofpointTAPClicksPermitted_CL** a **ProofpointTAPClicksBlocked_CL** .

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli, jak připojit Proofpoint klepnutím ke službě Azure Sentinel pomocí aplikací Azure Function App. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
