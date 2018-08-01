---
title: Začínáme s API pro generování sestav Azure AD | Dokumentace Microsoftu
description: Jak začít s Azure Active Directory API pro vytváření sestav
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 89562e6a2bfb977585cec1925a5f306c69c3d1e8
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390666"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Začínáme s Azure Active Directory API pro vytváření sestav

Azure Active Directory poskytuje širokou škálu [sestavy](active-directory-reporting-azure-portal.md). Data z těchto sestav můžou být velmi užitečná pro vaše aplikace, jako jsou systémy SIEM nebo nástroje pro auditování a business intelligence. 

S použitím rozhraní API pro generování sestav Azure AD, můžete získat programový přístup k datům prostřednictvím sady založené na protokolu REST API. Tato rozhraní API můžete volat z nejrůznějších programovacích jazyků a nástrojů.

Tento článek poskytuje návod pro přístup k datům sestav pomocí rozhraní API související.

Pokud narazíte na problémy, přečtěte si téma [jak získat podporu pro Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).


## <a name="prerequisites"></a>Požadavky

Pro přístup k rozhraní API pro generování sestav, i v případě, že plánujete přístup k rozhraní API pomocí skriptu, budete muset:

1. Přiřazení rolí (čtenář zabezpečení, správce zabezpečení, globální správce)
2. Registrace aplikace
3. Udělení oprávnění
4. Shromážděte nastavení konfigurace

Podrobné pokyny najdete v tématu [požadavky pro přístup k API pro vytváření sestav Azure Active Directory](active-directory-reporting-api-prerequisites-azure-portal.md).

## <a name="apis-with-graph-explorer"></a>Rozhraní API pomocí Graph Exploreru

Můžete použít [MSGraph explorer](https://developer.microsoft.com/en-us/graph/graph-explorer) ověřte vaše přihlášení a auditování dat rozhraní API. Ujistěte se, že pro přihlášení ke svému účtu pomocí tlačítek přihlášení v uživatelském rozhraní Graph Explorer a nastavte **AuditLog.Read.All** a **Directory.Read.All** oprávnění pro vašeho tenanta, jak je znázorněno.   

![Graph Exploreru](./media/active-directory-reporting-api-getting-started-azure-portal/graph-explorer.png)

![Upravit oprávnění uživatelského rozhraní](./media/active-directory-reporting-api-getting-started-azure-portal/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Použití certifikátů pro přístup k rozhraní API pro generování sestav Azure AD 

Zvažte použití API generování sestav Azure AD s certifikáty, pokud budete chtít načíst data sestav bez zásahu uživatele.

Podrobné pokyny najdete v tématu [získání dat pomocí API generování sestav Azure AD s certifikáty](active-directory-reporting-api-with-certificates.md).


## <a name="explore"></a>Prozkoumat

Získáte první dojem rozhraní API pro generování sestav:
   
   - [Použití ukázek pro audit rozhraní API](active-directory-reporting-api-audit-samples.md) 
   - [Použití ukázek pro sestavy aktivit přihlašování rozhraní API](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="next-steps"></a>Další postup

 * [Referenční informace k rozhraní API auditu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
 * [Sestavy aktivit přihlašování reference k rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
 * [Řešení potíží s chybami v rozhraní API pro generování sestav Azure AD](active-directory-reporting-troubleshoot-graph-api.md)


