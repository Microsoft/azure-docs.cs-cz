---
title: Připojit okta data o jednom Sign-On do Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak propojit okta data s jedním Sign-On do Azure Sentinel.
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
ms.openlocfilehash: 05a9b8009d896a2ee87df3e1c4493d249a887566
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083918"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>Připojení okta jednoho Sign-On ke službě Azure Sentinel pomocí funkce Azure Functions

> [!IMPORTANT]
> Okta data Connector s jedním Sign-On v Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Konektor okta Single Sign-On (SSO) umožňuje snadno propojit všechny protokoly řešení zabezpečení [okta single Sign-On (SSO)](https://www.okta.com/products/single-sign-on/) s Azure Sentinel, zobrazovat řídicí panely, vytvářet vlastní výstrahy a vylepšit šetření. Integrace mezi okta jedním Sign-On a službou Azure Sentinel využívá Azure Functions k vyžádání dat protokolu pomocí REST API.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-okta-single-sign-on"></a>Konfigurace a připojení okta Single Sign-On

Azure Functions mohou integrovat a přijímat události a protokoly přímo z okta jednoho Sign-On a předávají je do Azure Sentinel.

1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte **okta konektor pro jednotné přihlašování** .

1. Vyberte **stránku otevřít konektor**.

1. Postupujte podle pokynů na stránce **jednotného přihlašování okta** .

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v Log Analytics pod tabulkou **Okta_CL** .

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit okta jeden Sign-On ke službě Azure Sentinel pomocí aplikací Azure Function App. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.

