---
title: Podporované účty v aplikacích (cílová skupina) – Microsoft Identity Platform
description: Koncepční dokumentace o cílových a podporovaných typech účtů v aplikacích
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad3cd612200dc33fe37036146b00f52073403ecc
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852042"
---
# <a name="supported-account-types"></a>Podporované typy účtu

Tento článek vysvětluje, jaké typy účtů (někdy označované jako cílové skupiny) jsou podporované v aplikacích.

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Podporované typy účtů v aplikacích Microsoft Identity Platform

Ve veřejném cloudu Microsoft Azure se může většina typů aplikací přihlašovat uživatelům pomocí libovolné cílové skupiny:

- Pokud píšete obchodní aplikaci (LOB), můžete se přihlásit uživatele ve vaší vlastní organizaci. Taková aplikace se někdy nazývá **jeden tenant**.
- Pokud jste nezávislý výrobce softwaru, můžete napsat aplikaci, která přihlašuje uživatele:

  - V jakékoli organizaci. Taková aplikace se nazývá **víceklientské** webové aplikace. Někdy si přečtete, že se přihlásí uživatelé pomocí svých pracovních nebo školních účtů.
  - S jejich pracovními nebo školními nebo osobními účet Microsoft.
  - Pouze osobní účet Microsoft.
    > [!NOTE]
    > Platforma Microsoft Identity Platform v současné době podporuje osobní účty Microsoft jenom tak, že si zaregistrujete aplikaci pro **pracovní nebo školní nebo osobní účty Microsoftu**a pak omezíte přihlášení v kódu pro aplikaci zadáním autority Azure AD. Při sestavování aplikace, jako `https://login.onmicrosoftonline.com/consumers`je například.

- Pokud píšete firmu pro příjemce aplikace, můžete se také přihlašovat pomocí Azure AD B2C uživatelů pomocí svých sociálních identit.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Některé toky ověřování nepodporují všechny typy účtů.

Některé typy účtů se nedají používat s určitými toky ověřování. Například na desktopu, v aplikacích pro UWP nebo v aplikacích démona:

- Aplikace démona se dají používat jenom s Azure Active Directory organizacemi. Nedoporučuje se pokusit používat aplikace démona k manipulaci s osobními účty Microsoft (souhlas správce nebude nikdy udělen).  
- Tok integrovaného ověřování systému Windows můžete použít jenom s pracovními nebo školními účty (ve vaší organizaci nebo v jakékoli organizaci). Integrované ověřování systému Windows funguje s doménovým účtem a vyžaduje, aby počítače byly připojené k doméně nebo aby bylo připojené k Azure AD. Tento tok nedává smysl pro osobní účty Microsoft.
- [Udělení hesla vlastníka prostředku](./v2-oauth-ropc.md) (uživatelské jméno/heslo) se nedá použít u osobních účtů Microsoft. Osobní účty Microsoft totiž vyžadují, aby se uživatel přihlásil k osobním prostředkům v každé přihlašovací relaci. To je důvod, proč toto chování není kompatibilní s neinteraktivními toky.
- Tok kódu zařízení ještě nepracuje s osobními účty Microsoft.

## <a name="supported-account-types-in-national-clouds"></a>Podporované typy účtů v národních cloudech

 Aplikace se můžou také přihlašovat uživatelům v [národních cloudech](authentication-national-cloud.md). Osobní účty Microsoft se ale v těchto cloudech nepodporují (podle definice těchto cloudů). To je důvod, proč jsou podporované typy účtů pro tyto cloudy omezené na vaši organizaci (jeden tenant) nebo na jakékoli organizace (víceklientské aplikace).

## <a name="next-steps"></a>Další postup

- Další informace o [architektuře v Azure Active Directory](./single-and-multi-tenant-apps.md)
- Další informace o [národních cloudech](./authentication-national-cloud.md)