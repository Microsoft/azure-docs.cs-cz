---
title: Změnit heslo účtu konektoru Azure AD | Dokumentace Microsoftu
description: Toto téma popisuje postup při obnovení účtu Azure AD Connector.
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
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204536"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Změna hesla účtu konektoru služby Azure AD
Účet Azure AD Connector by měla být služba zdarma. Pokud potřebujete resetovat svoje přihlašovací údaje, toto téma je za vás. Například pokud je globální správce omylem resetovat heslo k účtu pomocí Powershellu.

## <a name="reset-the-credentials"></a>Resetovat přihlašovací údaje
Pokud účet Azure AD Connector nemůže kontaktovat kvůli potížím s ověřováním Azure AD, můžete resetovat heslo.

1. Přihlaste se k serveru synchronizace Azure AD Connect a spusťte prostředí PowerShell.
2. Spusťte `Add-ADSyncAADServiceAccount`.  
   ![Addadsyncaadserviceaccount rutiny prostředí PowerShell](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Zadejte přihlašovací údaje globálního správce Azure AD.

Tato rutina resetuje heslo pro účet služby a aktualizovat ho ve službě Azure AD i v modulu synchronizace.

## <a name="known-issues-these-steps-can-solve"></a>Známé problémy můžete vyřešit tyto kroky
Tato část se seznam chyb hlášených zákazníky, které byly odstraněny pověření obnovit do účtu Azure AD Connector.

---
Událost 6900  
Na serveru došlo k neočekávané chybě při zpracování oznámení o změně hesla:  
AADSTS70002: Chyba ověřování přihlašovacích údajů. AADSTS50054: K ověřování se používá staré heslo.

---
Událost 659  
Chyba při načítání konfigurace synchronizace zásad hesel. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Chyba ověřování přihlašovacích údajů. AADSTS50054: K ověřování se používá staré heslo.

## <a name="next-steps"></a>Další postup
**Témata s přehledem**

* [Synchronizace Azure AD Connect: Pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

