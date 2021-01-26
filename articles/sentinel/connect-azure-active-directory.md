---
title: Připojit Azure Active Directory dat ke službě Azure Sentinel | Microsoft Docs
description: Naučte se shromažďovat data z Azure Active Directory a streamovat protokoly přihlášení do Azure AD a auditovat protokoly do Azure Sentinel.
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
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: eb89d2a4e719e34ad5ea31656dc9e3c02472b07d
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98802260"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Připojení dat z Azure Active Directory (Azure AD)

K shromažďování dat z [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) a jejich streamování do Azure Sentinel můžete použít integrovaný konektor Azure Sentinel. Konektor umožňuje streamovat [protokoly přihlášení](../active-directory/reports-monitoring/concept-sign-ins.md) a [protokoly auditu](../active-directory/reports-monitoring/concept-audit-logs.md).

## <a name="prerequisites"></a>Požadavky

- K ingestování přihlašovacích protokolů do Azure Sentinel musíte mít předplatné [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) . Pro Azure Monitor (Log Analytics) a Sentinel Azure můžete platit další poplatky za GB.

- Uživateli musí být přiřazená role přispěvatele Sentinel Azure v pracovním prostoru.

- Uživateli musí být přiřazeni role Globální správce nebo správce zabezpečení v tenantovi, ze kterého chcete protokoly zasílat.

- Aby bylo možné zobrazit stav připojení, musí mít váš uživatel oprávnění ke čtení a zápisu pro nastavení diagnostiky Azure AD. 

## <a name="connect-to-azure-active-directory"></a>Připojení k Azure Active Directory

1. V Azure Sentinel vyberte **datové konektory** z navigační nabídky.

1. Z Galerie datových konektorů vyberte **Azure Active Directory** a pak vyberte **Stránka otevřít konektor**.

1. Zaškrtněte políčka vedle typů protokolů, které chcete streamovat do Azure Sentinel, a klikněte na **připojit**. Typy protokolů, ze kterých si můžete vybrat:

    - **Protokoly přihlášení**: informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
    - **Protokoly auditu**: informace o činnosti systému týkající se správy uživatelů a skupin, spravovaných aplikací a aktivit adresáře.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **LogManagement** v následujících tabulkách:

- `SigninLogs`
- `AuditLogs`

Pokud chcete zadat dotaz na protokoly služby Azure AD, zadejte odpovídající název tabulky v horní části okna dotazu.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Azure Active Directory ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
