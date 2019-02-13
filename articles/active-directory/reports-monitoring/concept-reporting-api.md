---
title: Začínáme s API pro generování sestav Azure AD | Dokumentace Microsoftu
description: Jak začít s Azure Active Directory API pro vytváření sestav
services: active-directory
documentationcenter: ''
author: priyamohanram
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
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0cab77617a72aeb43d7bb1786c7ccfad0382bf9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56166194"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Začínáme s Azure Active Directory API pro vytváření sestav

Azure Active Directory poskytuje širokou škálu [sestavy](overview-reports.md), který obsahuje užitečné informace pro aplikace, jako jsou systémy SIEM, audit a nástroje business intelligence. 

S použitím rozhraní Microsoft Graph API pro sestavy Azure AD, můžete získat programový přístup k datům prostřednictvím sady založené na protokolu REST API. Tato rozhraní API můžete volat z nejrůznějších programovacích jazyků a nástrojů.

Tento článek poskytuje přehled o vytváření sestav rozhraní API, včetně způsobů, jak k němu přístup.

Pokud narazíte na problémy, přečtěte si téma [jak získat podporu pro Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="prerequisites"></a>Požadavky

Přístup k vytváření sestav rozhraní API, s nebo bez zásahu uživatele, budete muset:

1. Přiřazení rolí (čtenář zabezpečení, správce zabezpečení, globální správce)
2. Registrace aplikace
3. Udělení oprávnění
4. Shromážděte nastavení konfigurace

Podrobné pokyny najdete v tématu [požadavky pro přístup k API pro vytváření sestav Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>Koncové body rozhraní API 

Koncový bod rozhraní Microsoft Graph API k protokolům auditu je `https://graph.microsoft.com/beta/auditLogs/directoryAudits` a koncový bod rozhraní Microsoft Graph API pro přihlášení je `https://graph.microsoft.com/beta/auditLogs/signIns`. Další informace najdete v tématu [referenční informace k rozhraní API auditu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) a [přihlášení reference k rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn).

Kromě toho můžete použít [události rizika Identity Protection API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) získat programový přístup k Microsoft Graphu pomocí detekce zabezpečení. Další informace najdete v tématu [Začínáme s Azure Active Directory Identity Protection a Microsoft Graph](../identity-protection/graph-get-started.md). 

> [!NOTE]
>  **Https:\/\/graph.windows.net\/\<název tenanta\>\/sestavy\/**  koncový bod je zastaralý. Pomocí nové koncové body rozhraní API k programovému přístupu ke sestavy aktivit a zabezpečení popsané výše.
  
## <a name="apis-with-graph-explorer"></a>Rozhraní API pomocí Graph Exploreru

Můžete použít [MSGraph explorer](https://developer.microsoft.com/graph/graph-explorer) ověřte vaše přihlášení a auditování dat rozhraní API. Ujistěte se, že pro přihlášení ke svému účtu pomocí tlačítek přihlášení v uživatelském rozhraní Graph Explorer a nastavte **AuditLog.Read.All** a **Directory.Read.All** oprávnění pro vašeho tenanta, jak je znázorněno.   

![Graph Exploreru](./media/concept-reporting-api/graph-explorer.png)

![Upravit oprávnění uživatelského rozhraní](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Použití certifikátů pro přístup k rozhraní API pro generování sestav Azure AD 

Použijte API generování sestav Azure AD s certifikáty, pokud budete chtít načíst data sestav bez zásahu uživatele.

Podrobné pokyny najdete v tématu [získání dat pomocí API generování sestav Azure AD s certifikáty](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Další postup

 * [Požadavky pro přístup k API pro vytváření sestav](howto-configure-prerequisites-for-reporting-api.md) 
 * [Získání dat pomocí API generování sestav Azure AD s certifikáty](tutorial-access-api-with-certificates.md)
 * [Řešení potíží s chybami v rozhraní API pro generování sestav Azure AD](troubleshoot-graph-api.md)


