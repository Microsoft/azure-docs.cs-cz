---
title: Poradce při potížích s chybami v rozhraní API pro vytváření sestav služby Azure Active Directory | Dokumenty společnosti Microsoft
description: Poskytuje řešení chyb při volání rozhraní API pro vytváření sestav služby Azure Active Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399285"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Poradce při potížích v rozhraní API pro vytváření sestav služby Azure Active Directory

Tento článek uvádí běžné chybové zprávy, které můžete spustit při přístupu k sestavce aktivit pomocí rozhraní MICROSOFT Graph API a kroky pro jejich řešení.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Chyba interního serveru 500 HTTP při přístupu ke koncovému bodu Microsoft Graph V2

Momentálně nepodporujeme koncový bod Microsoft Graph u2 – ujistěte se, že máte přístup k protokolům aktivit pomocí koncového bodu Microsoft Graph v1.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Chyba: Ani klient není B2C nebo tenant nemá prémiovou licenci

Přístup k přihlašovacím sestavům vyžaduje licenci Služby Azure Active Directory premium 1 (P1). Pokud se při přístupu k přihlášení zobrazí tato chybová zpráva, ujistěte se, že váš tenant má licenci s licencí Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Chyba: Uživatel není v povolených rolích. 

Pokud se zobrazí tato chybová zpráva při pokusu o přístup k protokolům auditu nebo přihlášení pomocí rozhraní API, ujistěte se, že váš účet je součástí **role Čtečka zabezpečení** nebo **Čtečka sestav** v tenantovi služby Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Chyba: V aplikaci chybí oprávnění AAD číst data adresáře. 

Postupujte podle pokynů v [požadavcích pro přístup k rozhraní API pro vytváření sestav služby Azure Active Directory,](howto-configure-prerequisites-for-reporting-api.md) abyste zajistili, že vaše aplikace běží se správnou sadou oprávnění. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Chyba: Aplikace chybí Microsoft Graph API 'Číst všechna data protokolu auditu' oprávnění

Postupujte podle pokynů v [požadavcích pro přístup k rozhraní API pro vytváření sestav služby Azure Active Directory,](howto-configure-prerequisites-for-reporting-api.md) abyste zajistili, že vaše aplikace běží se správnou sadou oprávnění. 

## <a name="next-steps"></a>Další kroky

[Použití odkazu rozhraní](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
API pro audit[Použití odkazu rozhraní API pro přihlášení o aktivity](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
