---
title: Připojit data Office 365 k Azure Sentinel | Microsoft Docs
description: Naučte se připojit data Office 365 k Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2020
ms.author: yelevin
ms.openlocfilehash: 43eba727b1dc724aae6eea3ec77de1363c5db73f
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252517"
---
# <a name="connect-data-from-office-365-logs"></a>Připojení dat z protokolů Office 365



Protokoly auditu ze [sady Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) můžete streamovat do Azure Sentinel jediným kliknutím. Protokoly auditu ze sady Office 365 můžete streamovat do vašeho pracovního prostoru Sentinel Azure ve stejném tenantovi. Konektor protokolu aktivit Office 365 nabízí přehled o probíhajících činnostech uživatelů. Získáte informace o různých akcích uživatelů, správců, systémových nastavení a událostech ze sady Office 365. Připojením sady Office 365 do Azure Sentinel můžete tato data použít k zobrazení řídicích panelů, vytváření vlastních výstrah a zlepšení procesu šetření.

> [!IMPORTANT]
> Pokud máte licenci E3, musíte předtím, než budete mít přístup k datům prostřednictvím rozhraní API aktivity správy sady Office 365, povolit jednotné protokolování auditu pro vaši organizaci sady Office 365. Provedete to tak, že zapnete protokol auditu Office 365. Pokyny najdete v tématu [Zapnutí nebo vypnutí prohledávání protokolu auditu systému Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Další informace najdete v referenčních informacích k [rozhraní API pro správu Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).

## <a name="prerequisites"></a>Předpoklady

- Musíte být globálním správcem nebo správcem zabezpečení vašeho tenanta.
- Váš tenant musí mít povolený jednotný audit. Klienti s licencemi Office 365 E3 nebo E5 mají ve výchozím nastavení povolené jednotné auditování. <br>Pokud váš tenant nemá jednu z těchto licencí, je nutné ve svém tenantovi povolit jednotné auditování pomocí jedné z těchto metod:
    - [Pomocí rutiny Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) a povolte parametr "UnifiedAuditLogIngestionEnabled").
    - [Pomocí uživatelského rozhraní Centrum zabezpečení a dodržování předpisů](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Připojení k Office 365

1. V Azure Sentinel vyberte **datové konektory** a pak klikněte na dlaždici **Office 365** .

2. Pokud jste to ještě nepovolili, můžete to udělat tak, že v okně **datové konektory** vyberete konektor **Office 365** . Tady můžete kliknout na **stránku Open Connector** a v části konfigurační oddíl s názvem **Konfigurace** vybrat všechny protokoly aktivit Office 365, které chcete připojit k Azure Sentinel. 
   > [!NOTE]
   > Pokud jste už provedli připojení více tenantů v předchozí podporované verzi konektoru Office 365 ve službě Azure Sentinel, budete moct zobrazit a upravit protokoly, které shromáždíte od každého tenanta. Nebudete moct přidávat další klienty, ale můžete odebrat dřív přidané klienty.
3. Pokud chcete použít příslušné schéma v Log Analytics pro protokoly Office 365, vyhledejte **OfficeActivity**.


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Office 365 ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).

