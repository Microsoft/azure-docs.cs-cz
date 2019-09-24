---
title: Připojit data Office 365 k Azure Sentinel | Microsoft Docs
description: Naučte se připojit data Office 365 k Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 481acfdccb54077f18c1f4bf0375c0dc66e66007
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240664"
---
# <a name="connect-data-from-office-365-logs"></a>Připojení dat z protokolů Office 365



Protokoly auditu ze [sady Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) můžete streamovat do Azure Sentinel jediným kliknutím. Protokoly auditu z několika tenantů můžete streamovat do jednoho pracovního prostoru v Azure Sentinel. Konektor protokolu aktivit Office 365 nabízí přehled o probíhajících činnostech uživatelů. Získáte informace o různých akcích uživatelů, správců, systémových nastavení a událostech ze sady Office 365. Připojením sady Office 365 do Azure Sentinel můžete tato data použít k zobrazení řídicích panelů, vytváření vlastních výstrah a zlepšení procesu šetření.

> [!IMPORTANT]
> Pokud máte licenci E3, musíte předtím, než budete mít přístup k datům prostřednictvím rozhraní API aktivity správy sady Office 365, povolit jednotné protokolování auditu pro vaši organizaci sady Office 365. Provedete to tak, že zapnete protokol auditu Office 365. Pokyny najdete v tématu [Zapnutí nebo vypnutí prohledávání protokolu auditu systému Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Další informace najdete v referenčních informacích k [rozhraní API pro správu Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).

## <a name="prerequisites"></a>Požadavky

- Musíte být globálním správcem nebo správcem zabezpečení vašeho tenanta.
- V počítači, ze kterého jste se přihlásili ke službě Azure Sentinel pro vytvoření připojení, se ujistěte, že je port 4433 otevřený pro webový provoz. Po úspěšném připojení se tento port může zavřít znovu.
- Pokud váš tenant nemá licenci Office 365 E3 nebo Office 365 E5, musíte ve svém tenantovi povolit jednotné auditování pomocí jednoho z těchto procesů:
    - [Pomocí rutiny Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) a povolte parametr "UnifiedAuditLogIngestionEnabled").
    - [Nebo pomocí uživatelského rozhraní Centrum zabezpečení a dodržování předpisů](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Připojení k Office 365

1. V Azure Sentinel vyberte **datové konektory** a pak klikněte na dlaždici **Office 365** .

2. Pokud jste ho ještě nepovolili, v části **připojení** použijte tlačítko **Povolit** , aby bylo možné řešení Office 365 povolit. Pokud už je povolená, bude se identifikovat na obrazovce připojení, jak už je povolené.
1. Office 365 umožňuje streamovat data z několika tenantů do Azure Sentinel. Pro každého tenanta, ke kterému se chcete připojit, přidejte tenanta v části **připojit klienty k Azure Sentinel**. 
1. Otevře se obrazovka služby Active Directory. Zobrazí se výzva k ověření pomocí globálního správce pro každého tenanta, kterého se chcete připojit ke službě Azure Sentinel, a poskytněte oprávnění službě Azure Sentinel pro čtení protokolů. 
5. V části streamování protokolů aktivit Office 365 klikněte na **Vybrat** a vyberte typy protokolů, které chcete streamovat do Azure Sentinel. Služba Azure Sentinel v současné době podporuje Exchange a SharePoint.

4. Klikněte na **použít změny**.

3. Pokud chcete použít příslušné schéma v Log Analytics pro protokoly Office 365, vyhledejte **OfficeActivity**.


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Office 365 ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).

