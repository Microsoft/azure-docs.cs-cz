---
title: Změna hesla účtu konektoru služby Azure AD | Microsoft Docs
description: V tomto tématu najdete dokumentaci, jak obnovit účet konektoru služby Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ea151ee79fccd66f1d9422744d8f57829677ec0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "67204536"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Změna hesla účtu konektoru služby Azure AD
Účet konektoru Azure AD by měl být bezplatná služba. Pokud budete potřebovat resetovat své přihlašovací údaje, je toto téma pro vás. Například pokud má globální správce omylem resetování hesla k účtu pomocí PowerShellu.

## <a name="reset-the-credentials"></a>Resetovat přihlašovací údaje
Pokud účet služby Azure AD Connector nemůže kontaktovat Azure AD kvůli problémům s ověřováním, můžete heslo resetovat.

1. Přihlaste se k serveru Azure AD Connect Sync a spusťte PowerShell.
2. Spusťte `Add-ADSyncAADServiceAccount`.  
   ![Addadsyncaadserviceaccount rutiny PowerShellu](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Zadejte přihlašovací údaje globálního správce služby Azure AD.

Tato rutina obnoví heslo pro účet služby a aktualizuje ho v Azure AD i v synchronizačním modulu.

## <a name="known-issues-these-steps-can-solve"></a>Známé problémy, které tyto kroky můžou vyřešit
V této části je uveden seznam chyb hlášených zákazníky, které byly opraveny pomocí resetování přihlašovacích údajů na účtu konektoru služby Azure AD.

---
Událost 6900  
V serveru došlo k neočekávané chybě při zpracování oznámení o změně hesla:  
AADSTS70002: při ověřování přihlašovacích údajů došlo k chybě. AADSTS50054: pro ověřování se používá staré heslo.

---
Událost 659  
Při načítání konfigurace synchronizace zásad hesel došlo k chybě. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: při ověřování přihlašovacích údajů došlo k chybě. AADSTS50054: pro ověřování se používá staré heslo.

## <a name="next-steps"></a>Další kroky
**Témata s přehledem**

* [Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

