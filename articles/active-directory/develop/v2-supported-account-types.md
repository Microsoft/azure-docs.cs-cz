---
title: Podporované typy účtů | Azure
titleSuffix: Microsoft identity platform
description: Koncepční dokumentace o cílových a podporovaných typech účtů v aplikacích
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: d6c184e2983a072dec4b3021a1b58a61cd206dba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755979"
---
# <a name="supported-account-types"></a>Podporované typy účtu

Tento článek vysvětluje, co jsou typy účtů (někdy označované jako *cílové skupiny*) podporované v aplikacích Microsoft Identity Platform.

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="account-types-in-the-public-cloud"></a>Typy účtů ve veřejném cloudu

Ve veřejném cloudu Microsoft Azure se může většina typů aplikací přihlašovat uživatelům pomocí libovolné cílové skupiny:

- Pokud píšete obchodní aplikaci (LOB), můžete se přihlásit uživatele ve vaší vlastní organizaci. Taková aplikace se někdy označuje jako *jeden tenant*.
- Pokud jste nezávislý výrobce softwaru, můžete napsat aplikaci, která se přihlásí uživatelům:

  - V jakékoli organizaci. Taková aplikace se nazývá *víceklientské* webové aplikace. Někdy si přečtete, že se přihlásí uživatelům pomocí svých pracovních nebo školních účtů.
  - Pomocí svého pracovního nebo školního nebo osobního účtu Microsoft.
  - Pouze osobní účty Microsoft.
    
- Pokud píšete aplikaci od firmy do spotřebitele, můžete se také přihlásit pomocí Azure Active Directory B2C (Azure AD B2C) a přihlašovat uživatele pomocí jejich sociálních identit.

## <a name="account-type-support-in-authentication-flows"></a>Podpora typů účtů v tocích ověřování

Některé typy účtů se nedají používat s určitými toky ověřování. Například v aplikacích Desktop, UWP nebo démon:

- Aplikace démona se dají používat jenom s organizacemi Azure AD. Nedává smysl použít aplikace démona k manipulaci s osobními účty Microsoft. Souhlas správce nebude nikdy udělen.
- Tok integrovaného ověřování systému Windows můžete použít jenom s pracovními nebo školními účty (ve vaší organizaci nebo v jakékoli organizaci). Integrované ověřování systému Windows funguje s doménovým účtem a vyžaduje, aby počítače byly připojené k doméně nebo aby bylo připojené k Azure AD. Tento tok nedává smysl pro osobní účty Microsoft.
- [Udělení přihlašovacích údajů pro heslo vlastníka prostředku](./v2-oauth-ropc.md) (uživatelské jméno a heslo) se nedá použít u osobních účtů Microsoft. Osobní účty Microsoft vyžadují, aby uživatel souhlasil s přístupem k osobním prostředkům v každé přihlašovací relaci. To je důvod, proč toto chování není kompatibilní s neinteraktivními toky.

## <a name="account-types-in-national-clouds"></a>Typy účtů v národních cloudech

Aplikace se můžou také přihlašovat uživatelům v [národních cloudech](authentication-national-cloud.md). Osobní účty Microsoftu se ale v těchto cloudech nepodporují. To je důvod, proč jsou podporované typy účtů pro tyto cloudy omezené na vaši organizaci (jeden tenant) nebo na jakékoli organizace (víceklientské aplikace).

## <a name="next-steps"></a>Další kroky

- Další informace o [architektuře v Azure Active Directory](./single-and-multi-tenant-apps.md).
- Přečtěte si další informace o [národních cloudech](./authentication-national-cloud.md).
