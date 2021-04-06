---
title: Připojení protokolů Dynamics 365 ke službě Azure Sentinel | Microsoft Docs
description: Naučte se používat konektory aktivit Common Data Service Dynamics 365 (CD) k získání informací o probíhajících aktivitách správce, uživatelů a podpory.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: 018368b6284cf39edec01f0a9a943b8ea15c85d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98104173"
---
# <a name="connect-dynamics-365-activity-logs-to-azure-sentinel"></a>Připojení protokolů aktivit Dynamics 365 ke službě Azure Sentinel

Konektor pro aktivity Common Data Service [Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description) (CD) poskytuje přehled o aktivitách správce, uživatelů a podpory a také události protokolování služby Microsoft sociální Engagement. Připojením Dynamics 365 CRM do Azure Sentinel můžete tato data zobrazit v sešitech, použít je k vytváření vlastních výstrah a využít je k vylepšení procesu šetření. Tento nový konektor Azure Sentinel shromažďuje data o DISCích Dynamics z rozhraní API pro správu Office. Další informace o aktivitách disků Dynamics, které jsou auditovány v Power platformách, najdete v části [povolení a používání protokolování aktivit](/power-platform/admin/enable-use-comprehensive-auditing).

> [!IMPORTANT]
>
> Konektor pro aktivity Common Data Service Dynamics 365 (CD) je aktuálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

## <a name="prerequisites"></a>Požadavky

- Musíte mít oprávnění ke čtení a zápisu v pracovním prostoru Sentinel Azure.

- Musíte mít [produkční licenci pro Microsoft Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description). Tento konektor není pro prostředí izolovaného prostoru k dispozici.
    - K protokolování aktivit se vyžaduje předplatné Microsoft 365 Enterprise [E3 nebo E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) .

- Načtení dat z rozhraní API pro správu sady Office:
    - Musíte být globálním správcem vašeho tenanta.

    - V [Centru zabezpečení a dodržování předpisů pro Office](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance)musí být povolené [protokolování auditu Office](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) .

## <a name="enable-the-dynamics-365-activities-data-connector"></a>Povolit datový konektor aktivit Dynamics 365

1. V navigační nabídce Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte **Dynamics 365 (Preview)** a pak vyberte **otevřít stránku konektoru** v podokně náhledu.

1. Na kartě **pokyny** klikněte v části **Konfigurace** na **připojit**. 

    Po aktivaci konektoru bude trvat přibližně 30 minut, než bude možné zobrazit data přicházející do grafu. 

    Podle [protokolu auditu Office v centru dodržování předpisů](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#requirements-to-search-the-audit-log)může trvat až 30 minut nebo až 24 hodin, než dojde k události, aby se odpovídající záznam protokolu auditu vrátil do výsledků.

1. Protokoly auditu Microsoft Dynamics najdete v `Dynamics365Activity` tabulce. Podívejte se na [referenční informace o schématu](/azure/azure-monitor/reference/tables/dynamics365activity)tabulky.

## <a name="querying-the-data"></a>Dotazování na data

1. V nabídce navigace v Azure Sentinel vyberte **protokoly**.

1. Spuštěním následujícího dotazu ověřte, zda přicházejí protokoly:

    ```kusto
    Dynamics365Activity
    | take 10
    ```


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak propojit data o aktivitách Dynamics 365 s Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte s detekcí hrozeb pomocí služby Azure Sentinel a pomocí [předdefinovaných](tutorial-detect-threats-built-in.md) nebo [vlastních](tutorial-detect-threats-custom.md) pravidel.
