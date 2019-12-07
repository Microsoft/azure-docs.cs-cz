---
title: Připojení dat Azure AD ke službě Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak připojit Azure Active Directory dat ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: f08cd731e40b204d042e5df418b03626b9082c3b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894661"
---
# <a name="connect-data-from-azure-active-directory"></a>Připojení dat z Azure Active Directory



Možnost Azure Sentinel umožňuje shromažďovat data z [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) a streamovat je do Azure Sentinel. Můžete zvolit streamování [protokolů přihlášení](../active-directory/reports-monitoring/concept-sign-ins.md) a [auditu](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Předpoklady

- Pokud chcete exportovat přihlašovací data ze služby Active Directory, musíte mít licenci Azure AD P1 nebo P2.

- Uživatel s oprávněními globálního správce nebo správce zabezpečení u tenanta, ze kterého chcete protokoly streamovat.

- Aby bylo možné zobrazit stav připojení, musíte mít oprávnění pro přístup k diagnostickým protokolům Azure AD. 


## <a name="connect-to-azure-ad"></a>Připojení k Azure AD

1. V Azure Sentinel vyberte **datové konektory** a pak klikněte na dlaždici **Azure Active Directory** .

1. Vedle protokolů, které chcete streamovat do služby Azure Sentinel, klikněte na **připojit**.

1. Můžete vybrat, jestli chcete, aby upozornění z Azure AD automaticky generovala incidenty ve službě Azure Sentinel. V části **vytvořit incidenty** vyberte **Povolit** , pokud chcete povolit výchozí analytické pravidlo, které automaticky vytvoří incidenty z výstrah vygenerovaných v připojené službě zabezpečení. Toto pravidlo pak můžete upravit v části **Analýza** a pak na **aktivní pravidla**.

1. Pokud chcete použít příslušné schéma v Log Analytics pro výstrahy Azure AD, vyhledejte **SigninLogs** a **AuditLogs**.




## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Azure AD ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
