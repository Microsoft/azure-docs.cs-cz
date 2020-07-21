---
title: Připojit data jednotného přihlašování okta do Azure Sentinel | Microsoft Docs
description: Naučte se připojit data jednotného přihlašování okta k Sentinel Azure.
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
ms.openlocfilehash: 37ade037b7f3c88f5ff33d7fc4640b19f366fe7a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531207"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>Připojení okta jednotného přihlašování k Azure Sentinel pomocí funkce Azure Functions

Konektor okta jednotného přihlašování (SSO) umožňuje snadno připojit všechny protokoly řešení zabezpečení [jednotného přihlašování okta (SSO)](https://www.okta.com/products/single-sign-on/) k Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Integrace mezi okta jednotným přihlašováním a protokolem Azure Sentinel využívá Azure Functions k vyžádání dat protokolu pomocí REST API.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-okta-single-sign-on"></a>Konfigurace a připojení jednotného přihlašování okta

Azure Functions může integrovat a přijímat události a protokoly přímo z jednotného přihlašování okta a předává je do Azure Sentinel.

1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte **okta konektor pro jednotné přihlašování** .

1. Vyberte **stránku otevřít konektor**.

1. Postupujte podle pokynů na stránce **jednotného přihlašování okta** .

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v Log Analytics pod tabulkou **Okta_CL** .

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak propojit okta jednotné přihlašování ke službě Azure Sentinel pomocí aplikací Azure Function App. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.

