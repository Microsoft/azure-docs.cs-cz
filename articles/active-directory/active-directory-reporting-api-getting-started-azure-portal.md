---
title: Začínáme s Azure AD reporting rozhraní API | Microsoft Docs
description: Jak začít pracovat s Azure Active Directory, vytváření sestav rozhraní API
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: dhanyahk;rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: df0b672a07575a0d26fff89c90008043d02818dd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589096"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Začínáme s Azure Active Directory, vytváření sestav rozhraní API

Azure Active Directory poskytuje celou řadu [sestavy](active-directory-reporting-azure-portal.md). Data z těchto sestav můžou být velmi užitečná pro vaše aplikace, jako jsou systémy SIEM nebo nástroje pro auditování a business intelligence. 

Pomocí generování sestav rozhraní API Azure AD, můžete získat programový přístup k datům prostřednictvím sady založené na REST API. Tato rozhraní API můžete volat z nejrůznějších programovacích jazyků a nástrojů.

Tento článek poskytuje návod pro přístup k použití rozhraní API související data pro generování sestav.

Pokud narazíte na problémy, projděte si téma [jak získat podporu pro Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).


## <a name="prerequisites"></a>Požadavky

Chcete-li získat přístup k rozhraní API služby generování sestav, i když plánujete přístup k rozhraní API pomocí skriptu, je potřeba:

1. Přiřazení rolí (čtečky zabezpečení, správce zabezpečení, globální správce)
2. Registrace aplikace
3. Udělení oprávnění
4. Shromážděte nastavení konfigurace


 
Podrobné pokyny najdete v tématu [požadavky pro přístup k službě Azure Active Directory, vytváření sestav rozhraní API](active-directory-reporting-api-prerequisites-azure-portal.md).


## <a name="recommendation"></a>Doporučení 

Pokud plánujete načítání generování sestav dat bez zásahu uživatele, měli byste zvážit použití API generování sestav Azure AD s certifikáty.

Podrobné pokyny najdete v tématu [získat data pomocí rozhraní API Azure Reporting AD s certifikáty](active-directory-reporting-api-with-certificates.md).


## <a name="explore"></a>Prozkoumat

Získáte první dojem o rozhraní API pro vytváření sestav:
   
   - [Pomocí ukázky pro audit rozhraní API](active-directory-reporting-api-audit-samples.md) 
 
   - [Pomocí ukázky pro sestavu přihlašovací aktivita rozhraní API](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="customize"></a>Přizpůsobení  

Vytvořte vlastní řešení: 
   
   - [Pomocí auditu referenční dokumentace rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 

   - [Pomocí odkaz na sestavu API přihlašovací aktivita](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



