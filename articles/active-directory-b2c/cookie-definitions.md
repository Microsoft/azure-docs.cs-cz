---
title: Definice souborů cookie – Azure Active Directory B2C | Microsoft Docs
description: Poskytuje definice pro soubory cookie používané v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 66de4559ed006735f53ff993cce29370428b9998
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930895"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Definice souborů cookie pro Azure Active Directory B2C

V následující tabulce jsou uvedeny soubory cookie používané v Azure Active Directory B2C.

| Name (Název) | Domain (Doména) | Konec platnosti | Účel |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | Konec [relace prohlížeče](session-behavior.md) | Uchovává data o členství uživatelů napříč klienty. Klienti, na kterých je uživatel členem, a úroveň členství (správce nebo uživatel). |
| x-ms-cpim-slice | login.microsoftonline.com, b2clogin.com, značka domény | Konec [relace prohlížeče](session-behavior.md) | Slouží ke směrování požadavků do příslušné provozní instance. |
| x-ms-cpim-trans | login.microsoftonline.com, b2clogin.com, značka domény | Konec [relace prohlížeče](session-behavior.md) | Používá se ke sledování transakcí (počet požadavků na ověření Azure AD B2C) a aktuální transakce. |
| x-ms-cpim-sso:{Id} | login.microsoftonline.com, b2clogin.com, značka domény | Konec [relace prohlížeče](session-behavior.md) | Používá se k údržbě relace jednotného přihlašování. |
| x-MS-cpim-cache: {ID} _n | login.microsoftonline.com, b2clogin.com, značka domény | Konec [relace prohlížeče](session-behavior.md), úspěšné ověření | Slouží k údržbě stavu požadavku. |
| x-ms-cpim-csrf | login.microsoftonline.com, b2clogin.com, značka domény | Konec [relace prohlížeče](session-behavior.md) | Token pro padělání žádostí mezi weby používaný pro CRSF ochranu. |
| x-ms-cpim-dc | login.microsoftonline.com, b2clogin.com, značka domény | Konec [relace prohlížeče](session-behavior.md) | Používá se pro Azure AD B2C síťové směrování. |
| x-ms-cpim-ctx | login.microsoftonline.com, b2clogin.com, značka domény | Konec [relace prohlížeče](session-behavior.md) | Kontext |
| x-ms-cpim-rp | login.microsoftonline.com, b2clogin.com, značka domény | Konec [relace prohlížeče](session-behavior.md) | Slouží k ukládání dat členství pro tenanta poskytovatele prostředků. |
| x-ms-cpim-rc | login.microsoftonline.com, b2clogin.com, značka domény | Konec [relace prohlížeče](session-behavior.md) | Slouží k uložení souboru cookie přenosu. |
