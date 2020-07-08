---
title: Připojení protokolů Office 365 ke službě Azure Sentinel | Microsoft Docs
description: Naučte se pomocí konektoru protokolu Office 365 získat informace o probíhajících činnostech uživatelů a správců v Exchange a SharePointu, včetně OneDrivu.
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
ms.date: 05/21/2020
ms.author: yelevin
ms.openlocfilehash: 180b25f80bd27caea20b1c17cd84fda38c172e0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559341"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Připojení protokolů Office 365 ke službě Azure Sentinel

Konektor protokolu [sady Office 365](https://docs.microsoft.com/office/) přináší informace o probíhajících uživatelských a administrativních činnostech v **systému Exchange** a **SharePoint** (včetně **OneDrivu**) do Azure Sentinel. Tyto informace obsahují podrobnosti o akcích, jako jsou například soubory ke stažení, odeslané požadavky na přístup, změny v událostech skupiny a operace poštovních schránek a také informace o uživateli, který akce provedl. Připojení protokolů Office 365 k Azure Sentinel umožňuje zobrazit a analyzovat tato data v sešitech, dotazovat se na ně a vytvořit vlastní výstrahy a začlenit je ke zvýšení vašeho procesu šetření, což vám poskytne lepší přehled o zabezpečení sady Office 365.

## <a name="prerequisites"></a>Požadavky

- Musíte mít oprávnění ke čtení a zápisu v pracovním prostoru Sentinel Azure.

- Musíte být globálním správcem nebo správcem zabezpečení vašeho tenanta.

- Vaše nasazení Office 365 musí být ve stejném tenantovi jako váš pracovní prostor Azure Sentinel.

> [!IMPORTANT]
> - Aby mohl konektor získat přístup k datům prostřednictvím rozhraní API aktivity správy Office 365, musíte mít v nasazení sady Office 365 zapnuté **jednotné protokolování auditu** . V závislosti na typu licence Office 365/Microsoft 365, kterou máte, může nebo nemusí být ve výchozím nastavení povolená. V [Centru zabezpečení a dodržování předpisů pro Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) zkontrolujte stav jednotného protokolování auditu podle typu vaší licence.
> - Můžete také ručně povolit, zakázat a kontrolovat aktuální stav protokolu Unified audit pro Office 365. Pokyny najdete v tématu [Zapnutí nebo vypnutí prohledávání protokolu auditu systému Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off).
> - Další informace najdete v referenčních informacích k [rozhraní API aktivity správy Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).


   > [!NOTE]
   > Jak je uvedeno výše, a jak vidíte na stránce konektor v části **datové typy**, konektor Azure Sentinel Office 365 aktuálně podporuje přijímání protokolů auditu jenom z Microsoft Exchange a SharePointu (včetně OneDrivu). Existuje však několik externích řešení, pokud se zajímáte o [přenos dat z týmů](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761) nebo [jiných dat Office](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) do služby Azure Sentinel. 

## <a name="enable-the-office-365-log-connector"></a>Povolení konektoru protokolu systému Office 365

1. V navigační nabídce Azure Sentinel vyberte **datové konektory**.

1. V seznamu **konektory dat** klikněte na možnost **Office 365**a pak na pravé straně tlačítko **otevřít stránku konektoru** .

1. V části s názvem **Konfigurace**zaškrtněte políčka protokoly aktivit Office 365, které chcete připojit ke službě Azure Sentinel, a klikněte na **použít změny**. 

   > [!NOTE]
   > Pokud jste dříve připojili více tenantů k Azure Sentinel pomocí starší verze konektoru sady Office 365, který to podporuje, budete moci zobrazit a upravit protokoly, které shromáždíte z každého tenanta. Nebudete moct přidávat další klienty, ale můžete odebrat dřív přidané klienty.

1. K dotazování na data protokolu Office 365 v Log Analytics zadejte `OfficeActivity` první řádek okna dotazu.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Office 365 ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte s detekcí hrozeb pomocí služby Azure Sentinel a pomocí [předdefinovaných](tutorial-detect-threats-built-in.md) nebo [vlastních](tutorial-detect-threats-custom.md) pravidel.

