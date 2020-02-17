---
title: Synchronizovat atributy s Azure AD pro mapování | Microsoft Docs
description: Naučte se synchronizovat atributy z vaší místní služby Active Directory do Azure AD. Při konfiguraci zřizování uživatelů pro aplikace SaaS použijte funkci rozšíření adresáře a přidejte zdrojové atributy, které se ve výchozím nastavení nesynchronizují.
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
ms.openlocfilehash: 12d8e60d3bfeb2e72d52b30dffcec4ad6e6c3f08
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368180"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Synchronizace atributu z místní služby Active Directory do Azure AD kvůli zřizování pro aplikaci

Při přizpůsobování mapování atributů pro zřizování uživatelů můžete zjistit, že atribut, který chcete mapovat, se nezobrazí v seznamu **zdrojového atributu** . V tomto článku se dozvíte, jak přidat chybějící atribut synchronizací z místní služby Active Directory (AD) do Azure Active Directory (Azure AD).

Azure AD musí obsahovat všechna data potřebná k vytvoření profilu uživatele při zřizování uživatelských účtů z Azure AD do aplikace SaaS. V některých případech je možné, že budete potřebovat synchronizovat atributy z místní služby AD do Azure AD. Azure AD Connect automaticky synchronizuje určité atributy do Azure AD, ale ne všechny atributy. Kromě toho je možné, že některé atributy (například SAMAccountName), které jsou synchronizovány ve výchozím nastavení, nemusí být vystaveny pomocí rozhraní Microsoft Graph API. V těchto případech můžete použít funkci rozšíření adresář Azure AD Connect k synchronizaci atributu do Azure AD. Tímto způsobem bude atribut viditelný pro rozhraní Microsoft Graph API a službu Azure AD Provisioning.

Pokud jsou data potřebná pro zřizování ve službě Active Directory, ale není k dispozici pro zřizování z důvodu výše popsaných, postupujte podle těchto kroků.
 
## <a name="sync-an-attribute"></a>Synchronizace atributu 

1. Otevřete Průvodce Azure AD Connect, zvolte úlohy a pak zvolte **přizpůsobit možnosti synchronizace**.

   ![Stránka další úlohy Průvodce Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Přihlaste se jako globální správce Azure AD. 

3. Na stránce **volitelné funkce** vyberte možnost **synchronizace atributů rozšíření adresáře**.
 
   ![Stránka volitelných funkcí Průvodce Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Vyberte atributy, které chcete pro Azure AD zvětšit.
   > [!NOTE]
   > Hledání v části **dostupné atributy** rozlišuje velká a malá písmena.

   ![Stránka pro výběr rozšíření adresáře průvodce Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Dokončete průvodce Azure AD Connect a umožněte spuštění úplný cyklus synchronizace. Po dokončení cyklu se schéma rozšíří a nové hodnoty se synchronizují mezi místními službami AD a Azure AD.
 
6. V Azure Portal při [úpravách mapování atributů uživatele](customize-application-attributes.md)nyní bude seznam **zdrojového atributu** obsahovat přidaný atribut ve formátu `<attributename> (extension_<appID>_<attributename>)`. Vyberte atribut a namapujte ho k cílové aplikaci pro zřizování.

   ![Stránka pro výběr rozšíření adresáře průvodce Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Možnost zřízení referenčních atributů z místní služby AD, jako je **ManagedBy** nebo **DN/rozlišující jméno**, není dnes podporována. Tuto funkci si můžete vyžádat na [uživatelském hlasu](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Další kroky

* [Definujte, kdo je v oboru pro zřizování.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
