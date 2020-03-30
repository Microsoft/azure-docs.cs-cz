---
title: Připojení dat Office 365 k Azure Sentinelu| Dokumenty společnosti Microsoft
description: Přečtěte si, jak propojit data Office 365 s Azure Sentinelem.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252517"
---
# <a name="connect-data-from-office-365-logs"></a>Připojení dat z protokolů Office 365



Jedním kliknutím můžete streamovat protokoly auditu z [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) do Azure Sentinelu. Protokoly auditu můžete streamovat z Office 365 do pracovního prostoru Azure Sentinelu ve stejném tenantovi. Konektor protokolu aktivit Office 365 poskytuje přehled o probíhajících aktivitách uživatelů. Získáte informace o různých akcích a událostech uživatelů, správců, systémů a zásad z Office 365. Připojením protokolů Office 365 do Azure Sentinelu můžete tato data použít k zobrazení řídicích panelů, vytvoření vlastních výstrah a zlepšení procesu vyšetřování.

> [!IMPORTANT]
> Pokud máte licenci E3, musíte před přístupem k datům prostřednictvím rozhraní Office 365 Management Activity API povolit jednotné protokolování auditu pro organizaci Office 365. To můžete provést zapnutím protokolu auditu Office 365. Pokyny najdete v [tématu Zapnutí nebo vypnutí nebo vypnutí hledání protokolu auditu Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Další informace najdete [v tématu Office 365 management Activity API reference](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).

## <a name="prerequisites"></a>Požadavky

- V tenantovi musíte být globálním správcem nebo správcem zabezpečení.
- Váš klient musí mít povoleno jednotné auditování. Klienti s licencemi Office 365 E3 nebo E5 mají ve výchozím nastavení povoleno jednotné auditování. <br>Pokud váš klient nemá jednu z těchto licencí, musíte povolit jednotné auditování na vašem tenantovi pomocí jedné z těchto metod:
    - [Pomocí rutiny Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) a povolte parametr "UnifiedAuditLogIngestionEnabled").
    - [Použití ui. centra dodržování předpisů zabezpečení &](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Připojení k Office 365

1. V Azure Sentinelu vyberte **Datové konektory** a klikněte na dlaždici **Office 365.**

2. Pokud jste ji ještě nepovolili, můžete tak učinit tak, že přejdete do okna **Datové konektory** a vyberete konektor **Office 365.** Tady klikněte na **stránku Otevřít konektor** a v části konfigurace s popiskem **Konfigurace** vyberte všechny protokoly aktivit Office 365, které chcete připojit k Azure Sentinelu. 
   > [!NOTE]
   > Pokud jste už připojili více klientů v dříve podporované verzi konektoru Office 365 v Azure Sentinelu, budete moct zobrazit a upravit protokoly, které shromažďujete od každého klienta. Nebudete moci přidat další klienty, ale můžete odebrat dříve přidané klienty.
3. Chcete-li použít příslušné schéma v Log Analytics pro protokoly Office 365, vyhledejte **OfficeActivity**.


## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit Office 365 k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).

