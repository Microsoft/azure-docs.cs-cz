---
title: Připojit data programu Microsoft Defender pro Office 365 (dříve Office 365 ATP) do Azure Sentinel | Microsoft Docs
description: Ingestujte Microsoft Defender pro Office 365 data do Azure Sentinel, abyste získali přehled a vytvořili automatizované scénáře reakce.
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
ms.date: 09/06/2020
ms.author: yelevin
ms.openlocfilehash: 5ac72c9861e06773ffab87dcbf4e730562b798fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91342444"
---
# <a name="connect-alerts-from-microsoft-defender-for-office-365"></a>Připojit výstrahy z programu Microsoft Defender pro Office 365 

> [!IMPORTANT]
>
> - **Microsoft Defender pro Office 365** se dřív nazývala **Office 365 Advanced Threat Protection (ATP)**.
>
>     Starý název se může v produktu (včetně datového konektoru v Azure Sentinel) v časovém intervalu zobrazovat i nadále.
>
> - Ingestování výstrah programu Microsoft Defender pro Office 365 je momentálně ve verzi Public Preview. Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
[Microsoft Defender pro Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-advanced-threat-protection-service-description) chrání vaši organizaci proti nulovému a dalším pokročilým hrozbám, které představují neznámý malware v e-mailových zprávách, na škodlivých odkazech na adresy URL a v nástrojích pro spolupráci. Ingestování výstrah programu Microsoft Defender pro Office 365 do služby Azure Sentinel vám umožní využívat informace o hrozbách e-mailu, sdílení souborů a zabezpečení na základě adres URL v rámci bezpečnostních operací. Pak můžete lépe analyzovat události zabezpečení napříč vaší organizací a sestavovat playbooky pro efektivní a okamžitou reakci.

Konektor importuje následující výstrahy:

- Po kliknutí na potenciálně škodlivou adresu URL se zjistilo. 

- Po doručení se odebraly e-mailové zprávy obsahující malware

- E-mailové zprávy obsahující adresy URL podvodné pošty odebrané po doručení 

- E-mail hlášený uživatelem jako malware nebo phishing 

- Zjištěny podezřelé vzorce odesílání e-mailů 

- Uživatel omezil odesílání e-mailů. 

Tyto výstrahy můžou vidět zákazníci Office v **Centru zabezpečení a dodržování předpisů Office**.

## <a name="prerequisites"></a>Požadavky

- Když konektor povolíte, musíte mít oprávnění ke čtení a zápisu v pracovním prostoru Azure Sentinel.

- Musíte být globálním správcem nebo správcem zabezpečení v tenantovi pracovního prostoru Azure Sentinel.

- Musíte mít platnou licenci pro [office 365 ATP Plan 2](https://docs.microsoft.com/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2) (včetně licencí Office 365 E5, Office 365 A5 a Microsoft 365 E5 a k nákupu zvlášť). 

## <a name="connect-to-microsoft-defender-for-office-365"></a>Připojit se k programu Microsoft Defender pro Office 365

Pokud se nasadí Microsoft Defender pro Office 365 a nakonfigurují zásady, můžou se výstrahy snadno ingestovat do Azure Sentinel.

1. V Azure Sentinel vyberte **datové konektory** z navigační nabídky.

1. Vyberte **Microsoft Defender pro Office 365** (v galerii konektorů se může dál volat *Rozšířená ochrana před internetovými útoky Office 365*) a vyberte **otevřít stránku konektoru**.

1. V části **Konfigurace** klikněte na **připojit**. 

1. V části **vytvořit incidenty** klikněte na **Povolit**.

1. Pokud chcete použít příslušné schéma k dotazování na výstrahy ATP Office 365, vyhledejte **SecurityAlert** a zadejte **název zprostředkovatele** jako **OATP**.

1. Vyberte kartu **Další kroky** , chcete-li zobrazit a použít Ukázky dotazů a šablony pravidel analýz, které jsou součástí sady Microsoft Defender pro Office 365 Connector.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Microsoft Defender pro Office 365 ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).
