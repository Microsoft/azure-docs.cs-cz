---
title: Připojit data zabezpečení e-mailu Proofpoint na vyžádání do Azure Sentinel | Microsoft Docs
description: Naučte se používat datový konektor pro data zabezpečení e-mailů Proofpoint na vyžádání k vyžádání protokolů zabezpečení e-mailu do Azure Sentinel. Zobrazit POD e-mailem data zabezpečení v sešitech, vytvořit výstrahy a vylepšit šetření.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 73a9d9c7ab321aebd615922e5d4395c0318e809c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580448"
---
# <a name="connect-your-proofpoint-on-demand-email-security-pod-solution-to-azure-sentinel"></a>Připojte řešení Proofpoint na vyžádání e-mailu (POD) do Azure Sentinel

> [!IMPORTANT]
> Konektor zabezpečení e-mailu Proofpoint na vyžádání je momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Tento článek vysvětluje, jak připojit zařízení zabezpečení e-mailu Proofpoint na vyžádání ke službě Azure Sentinel. Konektory POD daty umožňují snadno připojit své protokoly POD s použitím funkce Azure Sentinel, abyste mohli zobrazit data v sešitech, používat je k vytváření vlastních výstrah a začlenit je ke zlepšení šetření.  Integrace mezi zabezpečením e-mailů Proofpoint na vyžádání a službou Azure Sentinel využívá rozhraní API WebSocket.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Požadavky

- V pracovním prostoru Azure Sentinel musíte mít oprávnění ke čtení a zápisu.

- Pro pracovní prostor musíte mít oprávnění ke čtení sdílených klíčů. [Přečtěte si další informace o klíčích pracovních prostorů](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Abyste mohli Azure Functions vytvořit Function App, musíte mít oprávnění ke čtení a zápisu. [Přečtěte si další informace o Azure Functions](../azure-functions/index.yml).

- Musíte mít následující přihlašovací údaje rozhraní API WebSocket: ProofpointClusterID, ProofpointToken. [Přečtěte si další informace o rozhraní API pro WebSocket](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API).

## <a name="configure-and-connect-proofpoint-on-demand-email-security"></a>Konfigurace a připojení e-mailu Proofpoint na vyžádání – zabezpečení

Zabezpečení e-mailu na vyžádání Proofpoint může integrovat a exportovat protokoly přímo do Azure Sentinel.

1. V nabídce navigace v Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte **Proofpoint na vyžádání zabezpečení e-mailu (Preview)** a pak **otevřete stránku konektor**.

1. Postupujte podle kroků popsaných v části **Konfigurace** stránky konektor.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části *vlastní protokoly* v následujících tabulkách:
- `ProofpointPOD_message_CL`
- `ProofpointPOD_maillog_CL`

V některých užitečných vzorových dotazech se můžete podívat na kartu **Další kroky** na stránce konektor.

## <a name="validate-connectivity"></a>Ověřit připojení

Může to trvat až 60 minut, než se protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak propojit zabezpečení e-mailu Proofpoint na vyžádání do Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.