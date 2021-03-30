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
ms.topic: how-to
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: e4f31c560fe3dd91689b361ed520e466fd52da1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85360006"
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
Událost 6900 na serveru došlo k neočekávané chybě při zpracování oznámení o změně hesla: AADSTS70002: Chyba při ověřování přihlašovacích údajů. AADSTS50054: pro ověřování se používá staré heslo.

---
Při načítání konfigurace synchronizace zásad hesel došlo k chybě události 659. Microsoft. IdentityModel. clients. Active. AdalServiceException: AADSTS70002: Chyba při ověřování přihlašovacích údajů. AADSTS50054: pro ověřování se používá staré heslo.

## <a name="next-steps"></a>Další kroky
**Témata s přehledem**

* [Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
