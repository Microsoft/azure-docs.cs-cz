---
title: Řešení chyb v rozhraní API pro vytváření sestav Azure Active Directory | Microsoft Docs
description: Poskytuje řešení chyb při volání rozhraní API pro vytváření sestav Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d1fb4f49e4f9ad41f971d869873200e6180b5cd
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399285"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Řešení chyb v rozhraní API pro vytváření sestav Azure Active Directory

V tomto článku jsou uvedené běžné chybové zprávy, se kterými se můžete setkat při přístupu k sestavám aktivit pomocí rozhraní Microsoft Graph API a postupu pro jejich řešení.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 interní chyba serveru HTTP při přístupu ke koncovému bodu Microsoft Graph v2

Momentálně nepodporujeme koncový bod Microsoft Graph v2 – zajistěte přístup k protokolům aktivit pomocí koncového bodu Microsoft Graph v1.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Chyba: tenant není B2C nebo tenant nemá licenci Premium.

Přístup k sestavám přihlášení vyžaduje licenci Azure Active Directory Premium 1 (P1). Pokud se zobrazí tato chybová zpráva při přístupu k přihlašování, ujistěte se, že je váš tenant licencován pomocí licence Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Chyba: uživatel není v povolených rolích. 

Pokud se zobrazí tato chybová zpráva při pokusu o přístup k protokolům auditu nebo k přihlášení pomocí rozhraní API, ujistěte se, že je váš účet součástí role **Čtenář zabezpečení** nebo **Čtenář sestav** ve vašem tenantovi Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Chyba: aplikace postrádá oprávnění pro čtení dat adresáře služby AAD 

Postupujte prosím podle kroků v části [požadavky pro přístup k rozhraní API pro vytváření sestav Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) , abyste zajistili, že vaše aplikace běží se správnou sadou oprávnění. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Chyba: v aplikaci chybí oprávnění Microsoft Graph rozhraní API pro čtení všech dat protokolu auditu.

Postupujte prosím podle kroků v části [požadavky pro přístup k rozhraní API pro vytváření sestav Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) , abyste zajistili, že vaše aplikace běží se správnou sadou oprávnění. 

## <a name="next-steps"></a>Další kroky

[Použijte referenční informace k rozhraní API pro audit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
[použijte referenční informace k rozhraní API pro sestavování aktivit přihlašování](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin) .
