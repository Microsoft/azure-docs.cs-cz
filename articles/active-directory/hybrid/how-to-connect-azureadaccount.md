---
title: 'Synchronizace Azure AD Connect: způsob správy účtu služby Azure AD | Dokumentace Microsoftu'
description: Toto téma popisuje postup při obnovení účtu služby Azure AD.
services: active-directory
keywords: AADSTS70002, AADSTS50054, jak resetovat heslo pro účet konektoru služby synchronizace Azure AD Connect
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 93dcbe5ceac87d84db9638de6d5fe120de8c2d36
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46313646"
---
# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Synchronizace Azure AD Connect: způsob správy účtu služby Azure AD
Účet služby používaný nástrojem konektor služby Azure AD by měla být služba zdarma. Pokud potřebujete resetovat svoje přihlašovací údaje, toto téma je za vás. Například pokud globální správce má omylem resetovat heslo k účtu služby pomocí prostředí PowerShell.

## <a name="reset-the-credentials"></a>Resetovat přihlašovací údaje
Pokud účet služby definované v Azure AD Connector nemůže kontaktovat kvůli potížím s ověřováním Azure AD, můžete resetovat heslo.

1. Přihlaste se k serveru synchronizace Azure AD Connect a spusťte prostředí PowerShell.
2. Spusťte `Add-ADSyncAADServiceAccount`.  
   ![Addadsyncaadserviceaccount rutiny prostředí PowerShell](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Zadejte přihlašovací údaje globálního správce Azure AD.

Tato rutina resetuje heslo pro účet služby a aktualizovat ho ve službě Azure AD i v modulu synchronizace.

## <a name="known-issues-these-steps-can-solve"></a>Známé problémy můžete vyřešit tyto kroky
Tato část se seznam chyb hlášených zákazníky, které byly opraveny pověření resetovat na účtu služby Azure AD.

- - -
Událost 6900  
Na serveru došlo k neočekávané chybě při zpracování oznámení o změně hesla:  
AADSTS70002: Chyba ověřování přihlašovacích údajů. AADSTS50054: Staré heslo se používá k ověřování.

- - -
Událost 659  
Chyba při načítání konfigurace synchronizace zásad hesel. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Chyba ověřování přihlašovacích údajů. AADSTS50054: Staré heslo se používá k ověřování.

## <a name="next-steps"></a>Další postup
**Témata s přehledem**

* [Synchronizace Azure AD Connect: Principy a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

