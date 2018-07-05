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
ms.openlocfilehash: 93532f4b0b2d527a4d5c79e2ee1b2810394b2f11
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442079"
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

Můžete použít [MSGraph explorer](https://developer.microsoft.com/en-us/graph/graph-explorer) ověřte vaše přihlášení a auditování dat rozhraní API. Ujistěte se, že pro přihlášení ke svému účtu pomocí tlačítek přihlášení v uživatelském rozhraní Graph Explorer a nastavte **Tasks.ReadWrite** a **Directory.ReadAll** oprávnění pro vašeho tenanta, jak je znázorněno.   

![Graph Exploreru](./media/active-directory-reporting-api-getting-started-azure-portal/graph-explorer.png)

![Upravit oprávnění uživatelského rozhraní](./media/active-directory-reporting-api-getting-started-azure-portal/modify-permissions.png)

## <a name="recommendation"></a>Doporučení 

Pokud plánujete načítání generování sestav dat bez zásahu uživatele, měli byste zvážit, pomocí API generování sestav Azure AD s certifikáty.

Podrobné pokyny najdete v tématu [získání dat pomocí API generování sestav Azure AD s certifikáty](active-directory-reporting-api-with-certificates.md).


## <a name="explore"></a>Prozkoumat

Získáte první dojem rozhraní API pro generování sestav:
   
   - [Použití ukázek pro audit rozhraní API](active-directory-reporting-api-audit-samples.md) 
 
   - [Použití ukázek pro sestavy aktivit přihlašování rozhraní API](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="customize"></a>Přizpůsobení  

Vytvořte svoje vlastní řešení: 
   
   - [Použití auditování reference k rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 

   - [Pomocí aktivit přihlašování odkaz na sestavu rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



