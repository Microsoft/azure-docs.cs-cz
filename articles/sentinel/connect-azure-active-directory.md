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
ms.date: 08/11/2020
ms.author: yelevin
ms.openlocfilehash: 279f54c3de964580cc37d1288a6e1b7726348e10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88208614"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Připojení dat z Azure Active Directory (Azure AD)



K shromažďování dat z [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) a jejich streamování do Azure Sentinel můžete použít integrovaný konektor Azure Sentinel. Konektor umožňuje streamovat [protokoly přihlášení](../active-directory/reports-monitoring/concept-sign-ins.md) a [protokoly auditu](../active-directory/reports-monitoring/concept-audit-logs.md).

## <a name="prerequisites"></a>Požadavky


- Jakákoli licence Azure AD (Free/O365/P1/P2) je dostatečná pro ingestování přihlašovacích protokolů do služby Azure Sentinel. Pro Azure Monitor (Log Analytics) a Sentinel Azure můžete platit další poplatky za GB.

- Uživateli musí být přiřazená role přispěvatele Sentinel Azure v pracovním prostoru.

- Uživateli musí být přiřazeni role Globální správce nebo správce zabezpečení v tenantovi, ze kterého chcete protokoly zasílat.

- Aby bylo možné zobrazit stav připojení, musí mít váš uživatel oprávnění ke čtení a zápisu pro nastavení diagnostiky Azure AD. 


## <a name="connect-to-azure-active-directory"></a>Připojení k Azure Active Directory

1. V Azure Sentinel vyberte **datové konektory** z navigační nabídky.

1. Z Galerie datových konektorů vyberte **Azure Active Directory** a pak vyberte **Stránka otevřít konektor**.

1. Zaškrtněte políčka vedle protokolů, které chcete streamovat do Azure Sentinel, a klikněte na **připojit**.

1. Můžete vybrat, jestli chcete, aby upozornění z Azure AD automaticky generovala incidenty ve službě Azure Sentinel. V části **vytvořit incidenty** vyberte **Povolit** můžete povolit výchozí pravidlo analýzy, které automaticky vytvoří incidenty z výstrah vygenerovaných v připojené službě zabezpečení. Toto pravidlo pak můžete upravit v části **Analýza** a pak na **aktivní pravidla**.

1. Pokud chcete použít příslušné schéma v Log Analytics pro dotazování na výstrahy služby Azure AD, zadejte `SigninLogs` nebo `AuditLogs` v okně dotazu.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Azure Active Directory ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
