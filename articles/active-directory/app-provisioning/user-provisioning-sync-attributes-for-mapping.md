---
title: Synchronizace atributů do služby Azure AD pro mapování | Dokumenty společnosti Microsoft
description: Přečtěte si, jak synchronizovat atributy z místní služby Active Directory do služby Azure AD. Při konfiguraci zřizování uživatelů do aplikací SaaS přidejte pomocí funkce rozšíření adresáře zdrojové atributy, které nejsou ve výchozím nastavení synchronizovány.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09d1efaf54bee65bd3274987e68e643f887baade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522267"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Synchronizace atributu z místní služby Active Directory do služby Azure AD pro zřizování do aplikace

Při přizpůsobení mapování atributů pro zřizování uživatelů můžete zjistit, že atribut, který chcete mapovat, se nezobrazuje v seznamu **atributů Zdroj.** Tento článek ukazuje, jak přidat chybějící atribut synchronizací z místní služby Active Directory (AD) do služby Azure Active Directory (Azure AD).

Azure AD musí obsahovat všechna data potřebná k vytvoření profilu uživatele při zřizování uživatelských účtů z Azure AD do aplikace SaaS. V některých případech, aby byla data k dispozici, budete potřebovat synchronizovat atributy z místního ad do Azure AD. Azure AD Connect automaticky synchronizuje určité atributy do Azure AD, ale ne všechny atributy. Kromě toho některé atributy (například SAMAccountName), které jsou synchronizovány ve výchozím nastavení nemusí být vystaveny pomocí rozhraní Microsoft Graph API. V těchto případech můžete použít funkci rozšíření adresáře Azure AD Connect k synchronizaci atributu do Služby Azure AD. Tímto způsobem atribut bude viditelné rozhraní API Microsoft Graph a služby zřizování Azure AD.

Pokud jsou data, která potřebujete pro zřizování, ve službě Active Directory, ale nejsou k dispozici pro zřizování z důvodu popsaného výše, postupujte takto.
 
## <a name="sync-an-attribute"></a>Synchronizace atributu 

1. Otevřete Průvodce připojením Azure AD, zvolte Úkoly a pak zvolte **Přizpůsobit možnosti synchronizace**.

   ![Stránka Průvodce připojením služby Azure Active Directory Stránka další úkoly](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Přihlaste se jako globální správce Azure AD. 

3. Na stránce **Volitelné funkce** vyberte **synchronizaci atributů rozšíření adresáře**.
 
   ![Stránka Průvodce připojením Služby Azure AD : Volitelné funkce](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Vyberte atributy, které chcete rozšířit do Azure AD.
   > [!NOTE]
   > Hledání v části **Dostupné atributy rozlišuje** malá a velká písmena.

   ![Stránka výběru rozšíření adresáře průvodce Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Dokončete průvodce Azure AD Connect a povolte spuštění úplného cyklu synchronizace. Po dokončení cyklu se schéma rozšíří a nové hodnoty se synchronizují mezi místním službou AD a službou Azure AD.
 
6. Na webu Azure Portal bude při [úpravách mapování uživatelských atributů](customize-application-attributes.md)nyní seznam **atributů Zdroj** obsahovat přidaný atribut ve formátu `<attributename> (extension_<appID>_<attributename>)`. Vyberte atribut a namapujte ho na cílovou aplikaci pro zřizování.

   ![Stránka výběru rozšíření adresáře průvodce Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Možnost zřídit referenční atributy z místní služby AD, například **managedby** nebo **DN/DistinguishedName**, není dnes podporována. Tuto funkci můžete požádat v [uživatelském hlasu](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Další kroky

* [Definujte, kdo má obor pro zřizování](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
