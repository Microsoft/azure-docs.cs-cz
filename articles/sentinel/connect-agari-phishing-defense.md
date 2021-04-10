---
title: Propojte svá řešení ochrany před podvodnými útoky a ochranou před hrozbami v Azure Sentinel | Microsoft Docs
description: Naučte se, jak pomocí konektoru pro ochranu před podvodnými útoky a ochranou ochranné známky z něj načíst své protokoly do Azure Sentinel. Zobrazení dat o Agarech v sešitech, vytváření výstrah a vylepšení šetření.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: a60a0291d6669b2a9115dffa8e0d4d63fae4a440
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724434"
---
# <a name="connect-your-agari-phishing-defense-and-brand-protection-solutions-to-azure-sentinel"></a>Propojte svá řešení ochrany před podvodnými útoky a ochranou před hrozbami v Azure Sentinel

> [!IMPORTANT]
> Konektor pro ochranu před útoky phishing a ochranou brandingu je v současnosti ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Konektor pro ochranu před útoky phishing a ochranou brandingu vám umožní snadno připojit protokoly k ochraně vašich značek a řešení ochrany před útoky phishing do Azure Sentinel, abyste mohli zobrazit data v sešitech, dotazovat se na ně a vytvořit vlastní výstrahy a začlenit je ke zlepšení šetření. Agary řešení se integrují s použitím služby Azure Sentinel pomocí Azure Functions a REST API.

Kromě toho můžou zákazníci s podporou ochrany před hrozbami využít výhod sdílení informací o hrozeb prostřednictvím Graph API zabezpečení.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Požadavky

Pro připojení ochrany před podvodnými útoky a ochranou proti útokům z agaru do Azure Sentinel se vyžadují následující:

- Oprávnění ke čtení a zápisu v pracovním prostoru Sentinel Azure.

- Oprávnění ke čtení sdílených klíčů pro pracovní prostor. [Přečtěte si další informace o klíčích pracovních prostorů](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Pro vytvoření Function App oprávnění ke čtení a zápisu na Azure Functions. [Přečtěte si další informace o Azure Functions](../azure-functions/index.yml).

- Ujistěte se, že máte ID a **tajné klíče** **klienta** v agaru (stejné ve všech řešeních agaru). Pokyny najdete na [webu o vývojářích agaru](https://developers.agari.com/agari-platform/docs/quick-start) .

## <a name="configure-and-connect-agari-solutions"></a>Konfigurace a připojení agarových řešení 

Řešení agaru mohou integrovat a exportovat protokoly přímo do Azure Sentinel pomocí Function App Azure.

> [!NOTE]
> Tento konektor používá Azure Functions k připojení k řešením agaru k vyžádání jejich protokolů do služby Azure Sentinel. To může vést k dodatečným nákladům příjmu dat. Podrobnosti najdete na stránce s [cenami Azure Functions](https://azure.microsoft.com/pricing/details/functions/) .

1. **Shromáždit přihlašovací údaje rozhraní API pro Agary:** 

    Ujistěte se, že máte ID a **tajné klíče** **klienta** v agaru. Pokyny najdete na [webu pro vývojáře na agarech](https://developers.agari.com/agari-platform/docs/quick-start#generate-api-credentials).

1. **Volitelné Povolit Graph API zabezpečení:** 

    Agars Function App umožňuje sdílet analýzy hrozeb pomocí služby Azure Sentinel prostřednictvím Graph API zabezpečení. Chcete-li použít tuto funkci, bude nutné povolit [konektor platformy Sentinel Threat Intelligence](connect-threat-intelligence.md) a také [zaregistrovat aplikaci](/graph/auth-register-app-v2) v Azure Active Directory.

    Tento proces vám poskytne tři informace pro použití při nasazování Function App níže: **ID tenanta grafu**, **ID klienta grafu** a **tajný kód klienta grafu**.

1. **Nasazení konektoru a přidruženého Function App Azure:** 

    1. Na portálu Sentinel Azure vyberte **datové konektory**. Vyberte **agarové ochrany proti útokům phishing a ochranu značkou (Preview)** a pak **otevřete stránku konektor**.

    1. V části **Konfigurace** zkopírujte **ID pracovního prostoru** Azure Sentinel a **primární klíč** a vložte je z libovolného.

    1. Vyberte **nasadit do Azure**. (Možná se budete muset posunout dolů a najít tlačítko.)

    1. Zobrazí se obrazovka **vlastní nasazení** .

        - Zadejte **ID klienta** a **tajný kód klienta** (tajné klíče).

        - Zadejte **ID pracovního prostoru** Azure Sentinel a **klíč pracovního prostoru** (primární klíč), které jste zkopírovali a umístili.

        - Pro řešení agaru, pro která máte aktivní předplatná, vyberte **true** nebo **false** .

        - Pokud jste vytvořili aplikaci Azure pro sdílení IoCs se službou Azure Sentinel pomocí Graph API zabezpečení, vyberte **hodnotu true** pro **možnost povolit sdílení grafu zabezpečení** a zadejte **ID tenanta grafu**, **ID klienta grafu** a **tajný klíč klienta grafu**.

    1. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření klikněte na **vytvořit**.

1. **Přiřaďte k vašemu Function App potřebná oprávnění:**

    Konektor agaru používá k ukládání časových razítek přístupu k protokolu proměnnou prostředí. Aby aplikace mohla zapisovat do této proměnné, musí být oprávnění přiřazena k identitě přiřazené systémem.

    1. V Azure Portal přejděte na **Function App**.

    1. V okně **Function App** vyberte ze seznamu Function App a **pak v navigační** nabídce Function App vyberte **Identita** .

    1. Na kartě **přiřazeno systému** nastavte **stav** na **zapnuto**. 

    1. Vyberte **Uložit** a zobrazí se tlačítko **přiřazení rolí Azure** . Klikněte na něj.

    1. Na obrazovce **přiřazení rolí Azure** vyberte **Přidat přiřazení role**. Nastavte **Rozsah** na **předplatné**, vyberte své předplatné z rozevíracího seznamu **předplatné** a nastavte **role** na **vlastníka dat konfigurace aplikace**. 

    1. Vyberte **Uložit**.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v části **protokoly** v *CustomLogs*, v následujících tabulkách: 

- `agari_apdtc_log_CL`
- `agari_apdpolicy_log_CL`
- `agari_bpalerts_log_CL`

Pokud chcete zadat dotaz na data řešení agaru, v okně dotazu zadejte jeden z výše uvedených názvů tabulek.

V některých užitečných vzorových dotazech se můžete podívat na kartu **Další kroky** na stránce konektor.

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli, jak připojit řešení pro ochranu před útoky phishing a ochranou ochranné známky do Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.