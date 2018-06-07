---
title: 'Synchronizace Azure AD Connect: Správa účtu služby Azure AD | Microsoft Docs'
description: Toto téma popisuje postup obnovení účtu služby Azure AD.
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
ms.openlocfilehash: 8f201f2478e2883289a6cc4b435e2c3218950b1d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594036"
---
# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Synchronizace Azure AD Connect: Správa účtu služby Azure AD
Účet služby používaný nástrojem konektor služby Azure AD by měla být služba volné. Pokud budete potřebovat resetovat svoje přihlašovací údaje, pak toto téma je pro vás. Například pokud má globálního správce tak, že resetovat heslo u účtu služby pomocí prostředí PowerShell.

## <a name="reset-the-credentials"></a>Resetování přihlašovacích údajů
Pokud účet služby definované v Azure AD Connector nemůže kontaktovat kvůli potížím s ověřováním Azure AD, můžete resetovat heslo.

1. Přihlaste se k Azure AD Connect synchronizačního serveru a spusťte prostředí PowerShell.
2. Spusťte `Add-ADSyncAADServiceAccount`.  
   ![Addadsyncaadserviceaccount rutiny prostředí PowerShell](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Zadejte přihlašovací údaje Azure AD globálního správce.

Tato rutina resetuje heslo pro účet služby a aktualizovat ji ve službě Azure AD i v synchronizační modul.

## <a name="known-issues-these-steps-can-solve"></a>Známé problémy těchto kroků můžete vyřešit.
Tato část se seznam chyb ohlášených zákazníky, které byly odstraněny přihlašovací údaje resetování na účet služby Azure AD.

- - -
Událost 6900  
Serveru došlo k neočekávané chybě během zpracování oznámení o změně hesla:  
AADSTS70002: Chyba ověřuje přihlašovací údaje. AADSTS50054: Staré heslo se používá k ověřování.

- - -
Událost 659  
Chyba při načítání konfiguraci synchronizace hesel zásad. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Chyba ověřuje přihlašovací údaje. AADSTS50054: Staré heslo se používá k ověřování.

## <a name="next-steps"></a>Další postup
**Témata s přehledem**

* [Synchronizace Azure AD Connect: pochopení a přizpůsobení synchronizace](active-directory-aadconnectsync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)

