---
title: Soubor cookie definice – Azure Active Directory B2C | Dokumentace Microsoftu
description: Obsahuje definice pro soubory cookie použít v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ac422a00a919903063c96ac096882036b99a63e3
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887197"
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

