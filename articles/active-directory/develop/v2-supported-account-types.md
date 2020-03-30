---
title: Podporované typy účtů – platforma identit Microsoftu | Azure
description: Koncepční dokumentace o cílových skupinách a podporovaných typech účtů v aplikacích
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: de90c81f56b6017b2d53ecbfb2c400a4c9f05d81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262279"
---
# <a name="supported-account-types"></a>Podporované typy účtu

Tento článek vysvětluje, jaké typy účtů (někdy pojmenované cílové skupiny) jsou podporovány v aplikacích.

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Podporované typy účtů v aplikacích platformy Microsoft Identity

Ve veřejném cloudu Microsoft Azure může většina typů aplikací přihlašovat uživatele pomocí libovolné cílové skupiny:

- Pokud píšete aplikaci Line of Business (LOB), můžete přihlásit uživatele ve vlastní organizaci. Taková aplikace je někdy **pojmenována jeden tenant**.
- Pokud jste isv, můžete napsat aplikaci, která přihlášení-in uživatelů:

  - V každé organizaci. Taková aplikace se nazývá **víceklientská** webová aplikace. Někdy si přečtete, že se uživatelé připisují svým pracovním nebo školním účtům.
  - S jejich pracovní nebo školní nebo osobní účet Microsoft.
  - Pouze s osobním účtem Microsoft.
    > [!NOTE]
    > V současné době platforma identit Microsoft podporuje osobní účty Microsoft pouze registrací aplikace pro **pracovní nebo školní nebo osobní účty Microsoft**a pak omezit přihlášení v kódu `https://login.microsoftonline.com/consumers`aplikace zadáním autority Azure AD při vytváření aplikace, například .

- Pokud píšete obchodní aplikace pro spotřebitele, můžete také přihlásit uživatele s jejich sociální identity, pomocí Azure AD B2C.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Některé toky ověřování nepodporují všechny typy účtů

Některé typy účtů nelze použít s určitými toky ověřování. Například v aplikacích pro stolní počítače, UPW nebo v aplikacích daemonu:

- Aplikace Daemon lze použít pouze s organizacemi Azure Active Directory. Nemá smysl pokoušet se používat aplikace pro daemon k manipulaci s osobními účty Microsoft (souhlas správce nebude nikdy udělen).  
- Integrovaný tok ověřování systému Windows můžete používat pouze s pracovními nebo školními účty (ve vaší organizaci nebo v jakékoli organizaci). Integrované ověřování windows funguje s účty domény a vyžaduje, aby se počítače připojily k doméně nebo k připojily Azure AD. Tento tok nemá smysl pro osobní účty Microsoft.
- [Udělení hesla vlastníka prostředku](./v2-oauth-ropc.md) (uživatelské jméno/heslo) nelze použít s osobními účty Microsoft. Osobní účty Microsoft ve skutečnosti vyžadují, aby uživatel souhlasil s přístupem k osobním prostředkům při každé relaci přihlášení. To je důvod, proč toto chování není kompatibilní s neinteraktivní toky.
- Tok kódu zařízení ještě nefunguje s osobními účty Microsoft.

## <a name="supported-account-types-in-national-clouds"></a>Podporované typy účtů v národních cloudech

 Aplikace se můžou přihlašovat i k uživatelům v [národních cloudech](authentication-national-cloud.md). Osobní účty Microsoft však nejsou podporovány v těchto cloudech (podle definice těchto cloudů). To je důvod, proč podporované typy účtů jsou sníženy, pro tyto cloudy, pro vaši organizaci (jeden tenant) nebo všechny organizace (víceklientské aplikace).

## <a name="next-steps"></a>Další kroky

- Další informace o [nájmu ve službě Azure Active Directory](./single-and-multi-tenant-apps.md)
- Další informace o [Národních cloudech](./authentication-national-cloud.md)
