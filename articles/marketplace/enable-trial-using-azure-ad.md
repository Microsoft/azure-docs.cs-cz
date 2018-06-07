---
title: Povolit zkušební verze pomocí služby Azure AD | Azure
description: Povolit typ výpis zkušební verze pomocí služby Azure Active Directory (Azure AD) v Azure Marketplace a AppSource pro vydavatele aplikace a služby
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/04/2018
ms.author: ellacroi
ms.openlocfilehash: 4140ba98c0c65c22674c61dc7266818af904e777
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825086"
---
# <a name="enable-trial-using-azure-ad"></a>Povolit zkušební verze pomocí služby Azure AD  
Azure Active Directory (Azure AD) je Cloudová služba identity, která umožňuje ověřování s Microsoft pracovní nebo školní účet s použitím rozhraní standardní: OAuth a OpenID Connect.  
*   Další informace o Azure AD, navštivte stránku nacházející se v Azure Active Directory [azure.microsoft.com/services/active-directory](https://azure.microsoft.com/services/active-directory).  

Vám a vašim zákazníkům se ověřují v marketplace pomocí služby Azure AD. Poté, co zákazník vybere vaše zkušební období výpis na marketplace, zákazník je přesměrován na zkušební prostředí.  V prostředí zkušební může nastavit zákazníkovi přímo bez nutnosti další kroky přihlášení. Aplikace nebo nabídka přijímá token z Azure AD při ověřování, který obsahuje informace o cenné uživatele pro vytvoření uživatelského účtu v aplikaci nebo nabídku. Můžete automatizovat vaše nastavení a zvýšit pravděpodobnost převod.  
*   Další informace o token odeslaný z Azure AD během ověřování, navštivte tokeny ukázka nacházející se na [docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)

Použijte k povolení ověřování jedním kliknutím v aplikaci nebo zkušební verze Azure AD.  
*   Zjednodušte zkušeností zákazníků z marketplace na zkušební verzi.  
*   Udržujte chování prostředí v produktu, i když se uživatel přesměruje z Marketplace pro vaše prostředí domény nebo zkušební verze.  
*   Sníží pravděpodobnost, že další na přesměrování, protože nejsou k dispozici žádné další kroky, přihlášení.  
*   Snižte překážek nasazení pro velké naplnění uživatele Azure AD.  

## <a name="verify-your-azure-ad-integration-on-the-marketplace-multitenant-apps"></a>Ověřte si Azure AD integrace na marketplace: víceklientské aplikace  
Podporují následující možnosti pro vaše řešení pomocí služby Azure AD.  
*   Svou aplikaci zaregistrujte v obchodní poutače na webu marketplace.  
*   Povolte funkci podpory víceklientské architektury v Azure AD získat zkušební prostředí jedním kliknutím.  
    *   Další informace o registraci aplikace, najdete v článku integrace aplikací s Azure Active Directory stránky nacházející se v [docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Pokud jste ještě na používání služby Azure AD federované jednotné přihlašování (SSO) a potom postupujte podle těchto kroků.  
1.  Registrace aplikace na webu marketplace. 
2.  Vytvořte jednotné přihlašování s Azure AD pomocí OAuth 2.0 nebo OpenID Connect.  
    *   Další informace o protokolu OAuth 2.0, najdete v článku OAuth 2.0 stránky nacházející se v [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).  
    *   Další informace o Open ID Connect najdete OpenID Connect stránky nacházející se v [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  
3.  Povolte funkci podpory víceklientské architektury v Azure AD a poskytuje prostředí zkušební jedním kliknutím.  
    *   Další informace o certifikační AppSource, navštivte AppSource stránka certifikační nacházející se v [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified). 

## <a name="verify-your-azure-ad-integration-on-the-marketplace-single-tenant-apps"></a>Ověřte si Azure AD integrace na marketplace: jedním-klientské aplikace  
Podporovat některý z následujících možností pro řešení jednoho klienta.  
*   Přidání uživatelů do adresáře jako uživatele typu Host pomocí Azure AD B2B.  
    *   Další informace o Azure AD s B2B najdete, co je Azure AD s B2B spolupráce stránky nacházející se v [docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Ručně nastavte zkušební verze pro zákazníky používající mě kontaktu.  
*   Vývoj testovací jednotku na zákazníka.  
*   Vytvoření ukázkové víceklientské ukázkovou aplikaci pomocí jednotného přihlašování.  

## <a name="next-steps"></a>Další postup
*   Přejděte [AppSource vydavatele průvodce a Azure Marketplace](./marketplace-publishers-guide.md) stránky.  
 
---  

