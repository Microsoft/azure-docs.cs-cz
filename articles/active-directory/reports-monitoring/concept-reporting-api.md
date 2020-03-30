---
title: Začínáme s rozhraním API pro vytváření sestav Azure AD | Dokumenty společnosti Microsoft
description: Jak začít s rozhraním API pro vytváření sestav služby Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56f240a5191dd483f89889f3ffe13b1819ca1e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399328"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Začínáme s rozhraním API pro vytváření sestav služby Azure Active Directory

Služba Azure Active Directory poskytuje různé [sestavy](overview-reports.md)obsahující užitečné informace pro aplikace, jako jsou systémy SIEM, nástroje auditu a business intelligence. 

Pomocí rozhraní MICROSOFT Graph API pro sestavy Azure AD můžete získat programový přístup k datům prostřednictvím sady rozhraní API založených na rest. Tato rozhraní API můžete volat z nejrůznějších programovacích jazyků a nástrojů.

Tento článek obsahuje přehled rozhraní API pro vytváření sestav, včetně způsobů, jak k němu získat přístup.

Pokud narazíte na problémy, podívejte [se, jak získat podporu pro Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="prerequisites"></a>Požadavky

Chcete-li získat přístup k rozhraní API pro vytváření sestav s zásahem uživatele nebo bez něj, je třeba:

1. Přiřazení rolí (čtečka zabezpečení, správce zabezpečení, globální správce)
2. Registrace aplikace
3. Udělení oprávnění
4. Shromáždit nastavení konfigurace

Podrobné pokyny najdete v [tématu požadavky pro přístup k rozhraní Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>Koncové body rozhraní API 

Koncový bod rozhraní API rozhraní Microsoft `https://graph.microsoft.com/beta/auditLogs/directoryAudits` Graph pro protokoly auditu je `https://graph.microsoft.com/beta/auditLogs/signIns`a koncový bod rozhraní API rozhraní Microsoft Graph pro přihlášení je . Další informace naleznete v [odkazu rozhraní API auditu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) a [v odkazu na přihlašovací rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn).

Kromě toho můžete pomocí [rozhraní API detekce rizik ochrany identity](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) získat programový přístup k detekci zabezpečení pomocí aplikace Microsoft Graph. Další informace najdete [v tématu Začínáme s Azure Active Directory Identity Protection a Microsoft Graph](../identity-protection/graph-get-started.md). 
  
## <a name="apis-with-microsoft-graph-explorer"></a>Rozhraní API s průzkumníkem Microsoft Graphexplorer

K ověření dat rozhraní API pro přihlášení a auditování můžete použít [aplikaci Microsoft Graph Explorer.](https://developer.microsoft.com/graph/graph-explorer) Nezapomeňte se přihlásit ke svému účtu pomocí obou přihlašovacích tlačítek v ui aplikace Graph Explorer a nastavte oprávnění **AuditLog.Read.All** a **Directory.Read.All** pro vašeho tenanta, jak je znázorněno.   

![Graph Explorer](./media/concept-reporting-api/graph-explorer.png)

![Upravit ui oprávnění](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Použití certifikátů pro přístup k rozhraní API pro vytváření sestav Azure AD 

Pokud plánujete načítat data sestav bez zásahu uživatele, použijte rozhraní API pro vytváření sestav Azure AD s certifikáty.

Podrobné pokyny najdete v [tématu Získání dat pomocí rozhraní API pro vytváření sestav Azure AD s certifikáty](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Další kroky

 * [Požadavky na přístup k rozhraní API pro vytváření sestav](howto-configure-prerequisites-for-reporting-api.md) 
 * [Získání dat pomocí rozhraní API pro generování sestav Azure AD s certifikáty](tutorial-access-api-with-certificates.md)
 * [Poradce při potížích v rozhraní API pro vytváření sestav Azure AD](troubleshoot-graph-api.md)


