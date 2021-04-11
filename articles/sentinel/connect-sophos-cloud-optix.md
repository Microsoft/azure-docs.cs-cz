---
title: Připojení dat Sophos Cloud Optix do Azure Sentinel | Microsoft Docs
description: Naučte se používat konektor Sophos Cloud Optix k vyžádání <PRODUCT NAME> protokolů do Azure Sentinel. Zobrazení <PRODUCT NAME> dat v sešitech, vytváření výstrah a vylepšení šetření.
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
ms.date: 01/26/2021
ms.author: yelevin
ms.openlocfilehash: c66205ffd9bd5a742d645cbf2f9251a44329a16e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700821"
---
# <a name="connect-your-sophos-cloud-optix-to-azure-sentinel"></a>Připojte svůj Optix do cloudu Sophos do Azure Sentinel

> [!IMPORTANT]
> Konektor Sophos Cloud Optix je aktuálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Sophos Cloud Optix Connector vám umožní snadno propojit všechny protokoly řešení zabezpečení služby Sophos Cloud Optix s vaší službou Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření.  Tato funkce poskytuje lepší přehled o zabezpečení cloudu a stav dodržování předpisů a vylepšuje možnosti operací cloudového zabezpečení. Integrace mezi Sophos Cloud Optix a Azure Sentinel využívá REST API.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-sophos-cloud-optix"></a>Konfigurace a připojení Sophos Cloud Optix

Sophos Cloud Optix může integrovat a exportovat protokoly přímo do Azure Sentinel.

1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte **Sophos Cloud Optix (Preview)**.

1. Vyberte **stránku otevřít konektor**.

1. Zkopírujte a uložte **ID pracovního prostoru** a **primární klíč** ze stránky konektoru.

1. Postupujte podle pokynů v článku Sophos pro [integraci s Microsoft Azure Sentinel](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html) (od třetího kroku).

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v části **protokoly** v části **vlastní protokoly** v tabulce *SophosCloudOptix_CL* .

Pokud chcete zadat dotaz na Sophos Cloud Optix data, zadejte ho `SophosCloudOptix_CL` do okna dotazu. Další užitečné Ukázky dotazů najdete na kartě **Další kroky** na stránce konektoru a v dokumentaci k webu [Sophos](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html).

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Sophos Cloud Optix ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
