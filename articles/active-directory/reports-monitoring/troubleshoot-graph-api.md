---
title: Řešení chyb v rozhraní API pro vytváření sestav Azure Active Directory | Microsoft Docs
description: Poskytuje řešení chyb při volání rozhraní API pro vytváření sestav Azure Active Directory.
services: active-directory
documentationcenter: ''
author: cawrites
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
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6f1f34dcece9acb20d0db091152b24b26cb9fa2
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989538"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Řešení chyb v rozhraní API pro vytváření sestav Azure Active Directory

V tomto článku jsou uvedené běžné chybové zprávy, se kterými se můžete setkat při přístupu k sestavám aktivit pomocí MS Graph API a kroků pro jejich řešení.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 interní chyba serveru HTTP při přístupu ke koncovému bodu Microsoft Graph v2

Momentálně nepodporujeme koncový bod Microsoft Graph v2 – zajistěte přístup k protokolům aktivit pomocí koncového bodu Microsoft Graph v1.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Chyba: Nepovedlo se získat role uživatele z AD graphu.

Tato chybová zpráva se může zobrazit při pokusu o přístup k přihlašování pomocí Graph Exploreru. Ujistěte se, že jste přihlášeni ke svému účtu pomocí obou přihlašovacích tlačítek v uživatelském rozhraní Průzkumníka graphu, jak je znázorněno na následujícím obrázku. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Chyba: Nepovedlo se provést kontrolu licence Premium ze služby AD Graph. 

Pokud při pokusu o přístup k přihlašování pomocí Graph Exploreru spustíte tuto chybovou zprávu, zvolte možnost **změnit oprávnění** pod vaším účtem na levém navigačním panelu a vyberte **Tasks.** pročíst a **adresář. Read. All**. 

![Upravit uživatelské rozhraní oprávnění](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Chyba: Žádný tenant není B2C nebo tenant nemá licenci Premium.

Přístup k sestavám přihlášení vyžaduje licenci Azure Active Directory Premium 1 (P1). Pokud se zobrazí tato chybová zpráva při přístupu k přihlašování, ujistěte se, že je váš tenant licencován pomocí licence Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Chyba: Uživatel není v povolených rolích. 

Pokud se zobrazí tato chybová zpráva při pokusu o přístup k protokolům auditu nebo k přihlášení pomocí rozhraní API, ujistěte se, že je váš účet součástí role **Čtenář zabezpečení** nebo **Čtenář sestav** ve vašem tenantovi Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Chyba: Aplikace nemá oprávnění pro čtení dat adresáře AAD. 

Postupujte prosím podle kroků v části [požadavky pro přístup k rozhraní API pro vytváření sestav Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) , abyste zajistili, že vaše aplikace běží se správnou sadou oprávnění. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Chyba: V aplikaci chybí oprávnění pro čtení všech dat protokolu MSGraph API.

Postupujte prosím podle kroků v části [požadavky pro přístup k rozhraní API pro vytváření sestav Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) , abyste zajistili, že vaše aplikace běží se správnou sadou oprávnění. 

## <a name="next-steps"></a>Další kroky

[Použijte referenční informace](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
k rozhraní API pro audit,[použijte referenční informace k rozhraní API sestav aktivit přihlašování](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin) .
