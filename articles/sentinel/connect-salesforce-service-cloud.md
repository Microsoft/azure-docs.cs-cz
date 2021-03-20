---
title: Připojení cloudových dat služby Salesforce ke službě Azure Sentinel | Microsoft Docs
description: Naučte se používat cloudový datový konektor služby Salesforce k vyžádání protokolů Salesforce do Azure Sentinel. Zobrazení dat Salesforce v sešitech, vytváření výstrah a vylepšení šetření.
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
ms.openlocfilehash: 1efd91d92bac1bc1f39d82aaa0cc71daa0275f8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570542"
---
# <a name="connect-your-salesforce-service-cloud-to-azure-sentinel"></a>Připojení cloudu služby Salesforce k Azure Sentinel

> [!IMPORTANT]
> Cloudový konektor služby Salesforce je momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Tento článek vysvětluje, jak připojit vaše cloudové řešení služby Salesforce ke službě Azure Sentinel. Cloudový datový konektor služby Salesforce umožňuje snadno připojit data Salesforce ke službě Azure Sentinel, takže ji můžete zobrazit v sešitech, použít ji k vytvoření vlastních výstrah a začlenit je k vylepšení šetření. Integrace mezi cloudem služby Salesforce a službou Azure Sentinel využívá REST API.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Předpoklady

- V pracovním prostoru Azure Sentinel musíte mít oprávnění ke čtení a zápisu.

- Pro pracovní prostor musíte mít oprávnění ke čtení sdílených klíčů. [Přečtěte si další informace o klíčích pracovních prostorů](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Abyste mohli Azure Functions vytvořit Function App, musíte mít oprávnění ke čtení a zápisu. [Přečtěte si další informace o Azure Functions](../azure-functions/index.yml).

- Musíte mít následující přihlašovací údaje pro Salesforce REST API: **uživatelské jméno SALESFORCE API**, **heslo Salesforce API**, **token zabezpečení Salesforce**, **klíč příjemce Salesforce**, **tajný klíč uživatele Salesforce**. [Přečtěte si další informace o REST API Salesforce](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm).

## <a name="configure-and-connect-salesforce-service-cloud"></a>Konfigurace a připojení cloudu služby Salesforce

Cloud služby Salesforce dokáže integrovat a exportovat protokoly přímo do Azure Sentinel.

1. V nabídce navigace v Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte **Salesforce Service Cloud (Preview)** a pak **otevřete stránku konektor**.

1. Postupujte podle kroků popsaných v části **Konfigurace** stránky konektor.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **CustomLogs** v `SalesforceServiceCloud_CL` tabulce.

V některých užitečných vzorových dotazech se můžete podívat na kartu **Další kroky** na stránce konektor.

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Cloud služby Salesforce ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.