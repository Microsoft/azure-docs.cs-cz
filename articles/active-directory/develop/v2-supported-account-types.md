---
title: Podporovaných účtů v aplikacích (cílová skupina) - platforma identit Microsoft
description: Rámcové dokumentaci o cílovým skupinám na místě a typy podporovaných účtů v aplikacích
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 908ba764dfee7c164c3e6f0ff24d2bbf6a0df287
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544848"
---
# <a name="supported-account-types"></a>Podporované typy účtu

Tento článek vysvětluje, jaké typy účtů (někdy označuje jako cílové skupiny) jsou podporovány v aplikacích

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Typy podporovaných účtů v aplikacích Microsoft Identity platform

Ve veřejném cloudu Microsoft Azure můžete s libovolným příjemcům většinu typů aplikací pro přihlašování uživatelů:

- Pokud píšete aplikaci řádku obchodní (LOB), můžete přihlásit uživatele ve vaší vlastní organizaci. Takové aplikace se někdy nazývá **jednoho tenanta**.
- Pokud jste nezávislý dodavatel softwaru, můžete psát aplikace přihlásí uživatelů:

  - V jakékoli organizaci. Takové aplikace jmenuje **víceklientské** webovou aplikaci. Někdy se věnovat čtení se uživatelé přihlásí pomocí svých pracovních nebo školních účtů.
  - S jejich pracovní nebo školní nebo osobní účet Microsoft.
  - S pouze osobního účtu Microsoft.
    > [!NOTE]
    > Aktuálně platforma identit Microsoft podporuje osobní účty Microsoft, jen když si zaregistrujete aplikaci pro **pracovní nebo školní účet nebo osobní účty Microsoft**a potom, omezíte přihlášení v kódu aplikace tak, že zadáte autoritu Azure AD, při vytváření aplikace, jako například `https://login.onmicrosoftonline.com/consumers`.

- Pokud píšete obchodní uživatelé aplikace, můžete taky přihlásit uživatele pomocí jejich sociálních identit pomocí Azure AD B2C.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Některé toky ověřování nepodporují všechny typy účtů

Některé typy účtů nemohou být součástí některé toky ověřování. Například v desktopu, aplikace UPW nebo proces démon aplikace:

- Démon procesu aplikace jde použít jenom s Azure Active Directory organizace. Nemá smysl pokus o použití aplikace démon k manipulaci s osobní účty Microsoft (souhlas správce nikdy udělí).  
- Můžete použít pouze flow integrované ověřování Windows pomocí pracovních nebo školních účtů (ve vaší organizaci nebo v jakékoli organizaci). Ve skutečnosti integrované ověřování Windows spolupracuje s účty domény a vyžaduje počítače být připojené k doméně nebo připojená k Azure AD. Tento tok nemá smysl pro osobní Accounts Microsoft.
- [Udělení heslo vlastníka prostředku](./v2-oauth-ropc.md) (uživatelského jména a hesla), nelze použít s osobní účty Microsoft. Ve skutečnosti vyžadují osobní účty Microsoft, že uživatel vyjádří souhlas přístup k prostředkům osobní v každé relaci přihlášení. To je důvod, proč, toto chování není kompatibilní s neinteraktivním přístupu toky.
- Tok kódu při zařízení ještě nefunguje s osobní účty Microsoft.

## <a name="supported-account-types-in-national-clouds"></a>Typy podporovaných účtů v národních cloudech

 Aplikace může také přihlašování uživatelů v [národních cloudů](authentication-national-cloud.md). Však nepodporují osobní účty Microsoft v těchto cloudech (podle definice tyto cloudy). To je důvod, proč je snížení typy podporovaných účtů pro tyto cloudy, vaše organizace (jeden tenant) nebo jakákoli organizace (aplikace s více tenanty).

## <a name="next-steps"></a>Další postup

- Další informace o [Tenantů ve službě Azure Active Directory](./single-and-multi-tenant-apps.md)
- Další informace o [národních Cloudů](./authentication-national-cloud.md)