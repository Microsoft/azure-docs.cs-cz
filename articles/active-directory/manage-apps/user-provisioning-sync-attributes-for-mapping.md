---
title: Synchronizace atributů do služby Azure AD pro mapování | Dokumentace Microsoftu
description: Zjistěte, jak synchronizovat atributy z vaší místní Active Directory do služby Azure AD. Při konfiguraci zřizování uživatelů pro aplikace SaaS, pomocí funkce rozšíření adresáře přidat atributy zdroje, které nejsou ve výchozím nastavení synchronizují.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9460bc924ea662646360d1a3f5087949a39a03f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806113"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Synchronizovat atribut z vaší místní Active Directory do služby Azure AD pro zřizování pro aplikaci

Při přizpůsobování mapování atributů pro zřizování uživatelů, můžete zjistit, že atribut, který chcete propojit se nezobrazí v **zdrojový atribut** seznamu. Tento článek popisuje, jak přidat chybí atribut tím, že synchronizace z místní služby Active Directory (AD) do Azure Active Directory (Azure AD).

Azure AD může obsahovat všechna data požadovaná k vytvoření uživatelského profilu při zřizování uživatelských účtů ze služby Azure AD pro aplikaci SaaS. V některých případech může zpřístupnit data může potřebovat synchronizaci atributů z místní AD do služby Azure AD. Azure AD Connect synchronizuje automaticky určité atributy do služby Azure AD, ale ne všechny atributy. Kromě toho nemusí být vystaveny některých atributů (jako je například SAMAccountName), které jsou ve výchozím nastavení synchronizují přes Azure AD Graph API. V těchto případech můžete použít funkce rozšíření adresáře Azure AD Connect pro synchronizaci atributů do služby Azure AD. Tímto způsobem atribut se nebude zobrazovat na Azure AD Graph API a služba zřizování Azure AD.

Pokud data, která budete potřebovat pro zřizování je ve službě Active Directory, ale není k dispozici pro zřizování z důvodů popsaných výše, postupujte podle těchto kroků.
 
## <a name="sync-an-attribute"></a>Synchronizovat atribut 

1. Spustit Průvodce účtem služby Azure AD Connect, zvolte úlohy a klikněte na tlačítko **přizpůsobit možnosti synchronizace**.

   ![Azure Active Directory Connect stránka průvodce další úkoly](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Přihlaste se jako globální správce Azure AD. 

3. Na **volitelné funkce** stránce **synchronizace atributů rozšíření adresáře**.
 
   ![Azure Active Directory Connect stránka průvodce volitelné funkce](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Vyberte atributy, které chcete rozšířit do Azure AD.
   > [!NOTE]
   > Hledání v rámci **dostupné atributy** je velká a malá písmena.

   ![Azure Active Directory Connect průvodce adresář rozšíření stránka pro výběr](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Dokončení Průvodce službou Azure AD Connect a umožňují Úplné synchronizační cyklus pro spuštění. Po dokončení cyklu, aby bylo schéma rozšířeno a nové hodnoty jsou synchronizovány mezi místním AD a Azure AD.
 
6. Na webu Azure Portal, když jste [úprava mapování atributů uživatele](customize-application-attributes.md), **zdrojový atribut** seznam bude teď obsahovat atribut přidané ve formátu `<attributename> (extension_<appID>_<attributename>)`. Vybrat atribut a jejich mapování na cílové aplikace pro zřizování.

   ![Azure Active Directory Connect průvodce adresář rozšíření stránka pro výběr](media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Možnost pro zřízení atributy typu odkaz z místní služby AD, jako například **managedby** nebo **rozlišující název/DistinguishedName**, nepodporuje ještě dnes. Můžete požádat o tuto funkci na [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Další postup

* [Definovat, kdo je v oboru pro zřizování](define-conditional-rules-for-provisioning-user-accounts.md)
