---
title: Změna hesla účtu konektoru Azure AD | Dokumenty společnosti Microsoft
description: Toto téma dokumentuje, jak obnovit účet Konektor Azure AD.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67204536"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Změna hesla účtu konektoru služby Azure AD
Účet Azure AD Connector má být bez služby. Pokud potřebujete obnovit jeho pověření, pak je toto téma pro vás. Například pokud globální správce omylem obnovit heslo k účtu pomocí prostředí PowerShell.

## <a name="reset-the-credentials"></a>Obnovení přihlašovacích údajů
Pokud účet Konektor Azure AD nemůže kontaktovat Azure AD z důvodu problémů s ověřováním, heslo lze obnovit.

1. Přihlaste se k synchronizačnímu serveru Azure AD Connect a spusťte PowerShell.
2. Spusťte `Add-ADSyncAADServiceAccount`.  
   ![Rutina prostředí PowerShell addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Poskytněte přihlašovací údaje správce Azure AD Global.

Tato rutina resetuje heslo pro účet služby a aktualizovat jej ve službě Azure AD a v synchronizačním modulu.

## <a name="known-issues-these-steps-can-solve"></a>Známé problémy, které tyto kroky mohou vyřešit
Tato část je seznam chyb hlášených zákazníky, které byly opraveny obnovení přihlašovacích údajů na účtu Azure AD Connector.

---
Událost 6900  
Server zjistil neočekávanou chybu při zpracování oznámení o změně hesla:  
AADSTS70002: Chyba ověřování přihlašovacích údajů. AADSTS50054: Staré heslo se používá pro ověřování.

---
Událost 659  
Při načítání konfigurace synchronizace zásad hesla došlo k chybě. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Chyba ověřování přihlašovacích údajů. AADSTS50054: Staré heslo se používá pro ověřování.

## <a name="next-steps"></a>Další kroky
**Přehledná témata**

* [Synchronizace služby Azure AD Connect: Principy a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

