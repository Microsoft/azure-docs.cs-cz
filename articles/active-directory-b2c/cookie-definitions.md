---
title: Soubor cookie definice – Azure Active Directory B2C | Dokumentace Microsoftu
description: Obsahuje definice pro soubory cookie použít v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7864320b71416d1b06661b8ae96c6113962250dd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703980"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Soubory cookie definice pro Azure Active Directory B2C

V následující tabulce jsou uvedeny soubory cookie se používají v Azure Active Directory B2C.

| Název | Domain (Doména) | Konec platnosti | Účel |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | Konec [relaci prohlížeče](active-directory-b2c-token-session-sso.md) | Obsahuje dat o členství uživatele mezi tenanty. Tenanti uživatel je členem sady a úroveň členství (správce nebo uživatel). |
| x-ms-cpim-slice | Login.microsoftonline.com, b2clogin.com, značkové domény | Konec [relaci prohlížeče](active-directory-b2c-token-session-sso.md) | Používá pro směrování požadavků na vhodné produkční instance. |
| x-ms-cpim-trans | Login.microsoftonline.com, b2clogin.com, značkové domény | Konec [relaci prohlížeče](active-directory-b2c-token-session-sso.md) | Používá se pro sledování transakce (počet požadavků na ověření na Azure AD B2C) a aktuální transakce. |
| x-ms-cpim-sso:{Id} | Login.microsoftonline.com, b2clogin.com, značkové domény | Konec [relaci prohlížeče](active-directory-b2c-token-session-sso.md) | Slouží ke správě relace jednotného přihlašování. |
| x-ms-cpim-cache:{id}_n | Login.microsoftonline.com, b2clogin.com, značkové domény | Konec [relaci prohlížeče](active-directory-b2c-token-session-sso.md), úspěšné ověření | Slouží ke správě stav žádosti. |
| x-ms-cpim-csrf | Login.microsoftonline.com, b2clogin.com, značkové domény | Konec [relaci prohlížeče](active-directory-b2c-token-session-sso.md) | Token proti padělání žádosti více webů pro CRSF ochrany. |
| x-ms-cpim-dc | Login.microsoftonline.com, b2clogin.com, značkové domény | Konec [relaci prohlížeče](active-directory-b2c-token-session-sso.md) | Používá pro směrování sítě k Azure AD B2C. |
| x-ms-cpim-ctx | Login.microsoftonline.com, b2clogin.com, značkové domény | Konec [relaci prohlížeče](active-directory-b2c-token-session-sso.md) | Kontext |
| x-ms-cpim-rp | Login.microsoftonline.com, b2clogin.com, značkové domény | Konec [relaci prohlížeče](active-directory-b2c-token-session-sso.md) | Se používá k ukládání dat o členství pro tenanta poskytovatele prostředků. |
| x-ms-cpim-rc | Login.microsoftonline.com, b2clogin.com, značkové domény | Konec [relaci prohlížeče](active-directory-b2c-token-session-sso.md) | Se používá k ukládání souborů cookie relay. |

