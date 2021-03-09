---
title: Připojit protokoly diagnostiky Azure Key Vault ke službě Azure Sentinel
description: Naučte se používat Azure Policy k připojení Azure Key Vault diagnostických protokolů ke službě Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: 00e634ec62d1fda4b1f2763a8c69b8e950d54beb
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505184"
---
# <a name="connect-azure-key-vault-diagnostics-logs"></a>Připojit protokoly diagnostiky Azure Key Vault

Azure Key Vault je cloudová služba pro bezpečné ukládání a přístup k tajným klíčům. Tajný kód je cokoli, ke kterému chcete přísně řídit přístup, jako jsou klíče rozhraní API, hesla, certifikáty nebo kryptografické klíče.

Tento konektor vám umožní streamovat protokoly Azure Key Vault Diagnostics do Azure Sentinel, což vám umožní průběžně monitorovat aktivity ve všech vašich instancích.

Přečtěte si další informace o [monitorování Azure Key Vault](../azure-monitor/insights/key-vault-insights-overview.md) a o [Azure Key Vault telemetrie diagnostiky](../key-vault/general/logging.md).

## <a name="prerequisites"></a>Požadavky

Postup ingestování protokolů Azure Key Vault do Azure Sentinel:

- V pracovním prostoru Azure Sentinel musíte mít oprávnění ke čtení a zápisu.

- Pokud chcete použít Azure Policy k použití zásad streamování protokolů pro Azure Key Vault prostředků, musíte mít přiřazenou roli vlastníka pro obor přiřazení zásady.

## <a name="connect-to-azure-key-vault"></a>Připojení k Azure Key Vault

Tento konektor používá Azure Policy k aplikování jedné konfigurace streamování protokolu Azure Key Vault do kolekce instancí definované jako obor. Typy protokolů ingestované z Azure Key Vault můžete zobrazit na levé straně stránky konektoru v části **datové typy**.

1. V navigační nabídce Azure Sentinel vyberte **datové konektory**.

1. V galerii datových konektorů vyberte **Azure Key Vault** a potom v podokně náhledu vyberte **Stránka otevřít konektor** .

1. V části **Konfigurace** stránky konektoru rozbalte **možnost povolit protokoly diagnostiky na Azure Key Vault**.

1. Vyberte tlačítko **Průvodce spustit Azure Policy přiřazení** .

    Otevře se Průvodce přiřazením zásad, který je připravený k vytvoření nové zásady nazvané **Deploy – konfigurace nastavení diagnostiky pro Azure Key Vault Log Analytics pracovním prostoru**.

    1. Na kartě **základy** kliknutím na tlačítko se třemi tečkami v části **obor** vyberte vaše předplatné (případně skupinu prostředků). Můžete také přidat popis.

    1. Na kartě **parametry** vyberte pracovní prostor Azure Sentinel v rozevíracím seznamu **Log Analytics pracovní prostor** . Zbývající rozevírací pole reprezentují dostupné typy diagnostických protokolů. Ponechte označené jako "true" všechny typy protokolů, které chcete ingestovat.

    1. Pokud chcete zásady použít u stávajících prostředků, vyberte kartu **náprava** a označte zaškrtávací políčko **vytvořit úlohu nápravy** .

    1. Na kartě **Revize + vytvořit** klikněte na **vytvořit**. Vaše zásada je teď přiřazená k oboru, který jste zvolili.

> [!NOTE]
>
> U tohoto konkrétního datového konektoru se indikátory stavu připojení (barevný pruh v galerii datových konektorů a ikony připojení vedle názvů datových typů) budou zobrazovat jako *připojené* (zeleně) pouze v případě, že data byla v určitém bodě za poslední dva týdny. Když dva týdny prošly bez příjmu dat, konektor se zobrazí jako odpojený. Okamžik, kdy se data dostanou, se vrátí do stavu *připojeno* .

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Azure Key Vault ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
