---
title: Připojit Azure Active Directory dat ke službě Azure Sentinel | Microsoft Docs
description: Naučte se shromažďovat data z Azure Active Directory a streamovat protokoly pro přihlášení, audit a zřizování Azure AD do Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: d2cfc2d592c24cf5d15a489ea4bde36ea3c2f863
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872366"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>Připojení dat Azure Active Directory (Azure AD) do Azure Sentinel

K shromažďování dat z [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) a jejich streamování do Azure Sentinel můžete použít integrovaný konektor Azure Sentinel. Konektor umožňuje streamovat následující typy protokolů:

- [**Protokoly přihlášení**](../active-directory/reports-monitoring/concept-all-sign-ins.md), které obsahují informace o [interaktivních přihlášeních uživatelů](../active-directory/reports-monitoring/concept-all-sign-ins.md#user-sign-ins) , kde uživatel poskytuje ověřovací faktor.

    Konektor Azure AD teď obsahuje tři další kategorie přihlašovacích protokolů, které jsou aktuálně ve **verzi Preview**:
    
    - [**Neinteraktivní protokoly přihlášení uživatele**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins), které obsahují informace o přihlášeních prováděných klientem jménem uživatele bez jakýchkoli interakcí nebo ověřovacího faktoru od uživatele.
    
    - [**Přihlašovací protokoly instančního objektu**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins), které obsahují informace o přihlášení aplikací a instančních objektů, které nezahrnují žádného uživatele. V těchto přihlášeních aplikace nebo služba poskytují svým jménem vlastní přihlašovací údaje k ověřování nebo přístupu k prostředkům.
    
    - [**Protokoly přihlašování spravované identity**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins), které obsahují informace o přihlášení pomocí prostředků Azure, které mají tajné klíče spravované v Azure Další informace najdete v tématu [co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md)

- [**Protokoly auditu**](../active-directory/reports-monitoring/concept-audit-logs.md), které obsahují informace o aktivitě systému související se správou uživatelů a skupin, spravovaných aplikací a aktivit adresáře.

- [**Zřizování protokolů**](../active-directory/reports-monitoring/concept-provisioning-logs.md) (také ve **verzi Preview**), které obsahují informace o aktivitách uživatelů, skupin a rolí ZŘÍZENých službou Azure AD Provisioning. 

> [!IMPORTANT]
> Jak je uvedeno výše, některé z dostupných typů protokolů jsou momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.
## <a name="prerequisites"></a>Požadavky

- K ingestování přihlašovacích protokolů do Azure Sentinel se vyžaduje licence Azure Active Directory P1 nebo P2. Jakákoli licence Azure AD (Free/O365/P1/P2) je dostačující k ingestování dalších typů protokolů. Pro Azure Monitor (Log Analytics) a Sentinel Azure můžete platit další poplatky za GB.

- Uživateli musí být přiřazená role přispěvatele Sentinel Azure v pracovním prostoru.

- Uživateli musí být přiřazeni role Globální správce nebo správce zabezpečení v tenantovi, ze kterého chcete protokoly zasílat.

- Aby bylo možné zobrazit stav připojení, musí mít váš uživatel oprávnění ke čtení a zápisu pro nastavení diagnostiky Azure AD. 

## <a name="connect-to-azure-active-directory"></a>Připojení k Azure Active Directory

1. V Azure Sentinel vyberte **datové konektory** z navigační nabídky.

1. Z Galerie datových konektorů vyberte **Azure Active Directory** a pak vyberte **Stránka otevřít konektor**.

1. Zaškrtněte políčka vedle typů protokolů, které chcete streamovat do Azure Sentinel (viz výše), a klikněte na **připojit**.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **LogManagement** v následujících tabulkách:

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Pokud chcete zadat dotaz na protokoly služby Azure AD, zadejte odpovídající název tabulky v horní části okna dotazu.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Azure Active Directory ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
