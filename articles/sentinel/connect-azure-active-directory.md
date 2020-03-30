---
title: Připojení dat Azure AD k Azure Sentinelu | Dokumenty společnosti Microsoft
description: Přečtěte si, jak propojit data Azure Active Directory s Azure Sentinelem.
services: sentinel
documentationcenter: na
author: yelevin
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
ms.author: yelevin
ms.openlocfilehash: be9241a6156621d3f90dbab2da5bebeb463b4232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588616"
---
# <a name="connect-data-from-azure-active-directory"></a>Připojení dat z Azure Active Directory



Azure Sentinel umožňuje shromažďovat data z [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) a streamovat je do Azure Sentinelu. Můžete streamovat [protokoly přihlášení](../active-directory/reports-monitoring/concept-sign-ins.md) a [protokoly auditování](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Požadavky

- Pokud chcete exportovat přihlašovací data ze služby Active Directory, musíte mít licenci Azure AD P1 nebo P2.

- Uživatel s oprávněními globálního správce nebo správce zabezpečení v tenantovi, ze kterého chcete streamovat protokoly.

- Abyste mohli zobrazit stav připojení, musíte mít oprávnění k přístupu k diagnostickým protokolům Azure AD. 


## <a name="connect-to-azure-ad"></a>Připojení k Azure AD

1. V Azure Sentinelu vyberte **Datové konektory** a klikněte na dlaždici **Azure Active Directory.**

1. Vedle protokolů, které chcete streamovat do Azure Sentinelu, klikněte na **připojit**.

1. Můžete si vybrat, jestli chcete, aby výstrahy z Azure AD automaticky generovat incidenty v Azure Sentinelu. V části **Vytvořit incidenty** vyberte **Povolit,** chcete-li povolit výchozí analytické pravidlo, které automaticky vytváří incidenty z výstrah generovaných v připojené službě zabezpečení. Toto pravidlo pak můžete upravit v části **Analytics** a potom **v části Aktivní pravidla**.

1. Chcete-li použít příslušné schéma v Log Analytics pro výstrahy Azure AD, vyhledejte **SigninLogs** a **AuditLogs**.




## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit Azure AD k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
