---
title: Připojení protokolů Office 365 ke službě Azure Sentinel | Microsoft Docs
description: Naučte se pomocí konektoru protokolu Office 365 získat informace o probíhajících činnostech uživatelů a správců v Exchange, týmech a SharePointu, včetně OneDrivu.
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
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: d6b59de048cdf00d352c4f488ecb51bfdf83640f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89178922"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Připojení protokolů Office 365 ke službě Azure Sentinel

Konektor protokolu [Office 365](https://docs.microsoft.com/office/) přináší do Azure Sentinel informace o probíhajících činnostech uživatelů a správců v **Exchange** a **SharePointu** (včetně **OneDrivu**) a teď i v **týmech** . Tyto informace zahrnují podrobnosti o akcích, jako jsou třeba soubory ke stažení, odeslané požadavky na přístup, změny v událostech skupiny, operace poštovní schránky, týmové události (například události chatu, týmu, člena a kanálu) a také podrobnosti uživatele, který akce provedl. Připojení protokolů Office 365 k Azure Sentinel umožňuje zobrazit a analyzovat tato data v sešitech, dotazovat se na ně a vytvořit vlastní výstrahy a začlenit je ke zvýšení vašeho procesu šetření, což vám poskytne lepší přehled o zabezpečení sady Office 365.

> [!IMPORTANT]
> Protokoly konektoru protokolu Office 365 **pro Microsoft Teams** Connector jsou momentálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

- Musíte mít oprávnění ke čtení a zápisu v pracovním prostoru Sentinel Azure.

- Musíte být globálním správcem nebo správcem zabezpečení vašeho tenanta.

- Vaše nasazení Office 365 musí být ve stejném tenantovi jako váš pracovní prostor Azure Sentinel.

> [!IMPORTANT]
> - Aby mohl konektor získat přístup k datům prostřednictvím rozhraní API aktivity správy Office 365, musíte mít v nasazení sady Office 365 zapnuté **jednotné protokolování auditu** . V závislosti na typu licence Office 365/Microsoft 365, kterou máte, může nebo nemusí být ve výchozím nastavení povolená. V [Centru zabezpečení a dodržování předpisů pro Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) zkontrolujte stav jednotného protokolování auditu podle typu vaší licence.
> - Můžete také ručně povolit, zakázat a kontrolovat aktuální stav protokolu Unified audit pro Office 365. Pokyny najdete v tématu [Zapnutí nebo vypnutí prohledávání protokolu auditu systému Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off).
> - Další informace najdete v referenčních informacích k [rozhraní API aktivity správy Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).


   > [!NOTE]
   > Jak je uvedeno výše, a jak vidíte na stránce konektor v části **datové typy**, konektor Azure Sentinel Office 365 aktuálně podporuje přijímání protokolů auditu jenom z Microsoft Exchange a SharePointu (včetně OneDrivu) **a teď i z týmů**. Existuje však několik externích řešení, pokud máte zájem o převedení [dalších dat Office](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) do služby Azure Sentinel. 

## <a name="enable-the-office-365-log-connector"></a>Povolení konektoru protokolu systému Office 365

### <a name="instructions-tab"></a>Karta pokyny

1. V navigační nabídce Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte možnost **Office 365**a potom vyberte možnost **otevřít stránku konektoru** v podokně náhledu.

1. V části s názvem **Konfigurace**zaškrtněte políčka protokoly aktivit Office 365, které chcete připojit ke službě Azure Sentinel, a klikněte na **použít změny**. 

   > [!NOTE]
   > Pokud jste dříve připojili více tenantů k Azure Sentinel pomocí starší verze konektoru sady Office 365, který to podporuje, budete moci zobrazit a upravit protokoly, které shromáždíte z každého tenanta. Nebudete moct přidávat další klienty, ale můžete odebrat dřív přidané klienty.

### <a name="next-steps-tab"></a>Karta Další kroky

- Podívejte se na Doporučené sešity, Ukázky dotazů a šablony analytických pravidel, které jsou součástí konektoru protokolu **Office 365** , abyste získali přehled o datech protokolů SharePoint, OneDrive, Exchange a Teams.

- Chcete-li ručně zadat dotaz na data protokolu Office 365 v části **protokoly**, zadejte `OfficeActivity` do prvního řádku okna dotazu.
   - Chcete-li filtrovat dotaz pro určitý typ protokolu, zadejte na `| where OfficeWorkload == "<logtype>"` druhý řádek dotazu, kde *\<logtype\>* je `SharePoint` , `OneDrive` , `Exchange` nebo `MicrosoftTeams` .

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Office 365 ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte s detekcí hrozeb pomocí služby Azure Sentinel a pomocí [předdefinovaných](tutorial-detect-threats-built-in.md) nebo [vlastních](tutorial-detect-threats-custom.md) pravidel.

