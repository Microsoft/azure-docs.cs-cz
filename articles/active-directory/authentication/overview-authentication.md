---
title: Ověřování uživatelů pomocí Azure Active Directory
description: Jak jako správce Azure AD ochráním ověřování uživatelů a zároveň snížím dopad na koncové uživatele?
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: overview
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: 0b232ed8bacfeb896fd5ee6ff9e2a58b71dc1517
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162954"
---
# <a name="what-methods-are-available-for-authentication"></a>Které metody ověřování jsou dostupné?

Občas můžete slyšet ve zprávách, jak někdo ukradne hesla nebo dojde k ohrožení zabezpečení identit. Zabezpečení organizace můžete kromě běžného hesla okamžitě zvýšit vynucením dvoufaktorového ověřování. Microsoft Azure Active Directory (Azure AD) obsahuje funkce, jako je Azure Multi-Factor Authentication (Azure MFA) a samoobslužné resetování hesla Azure AD (SSPR), které s dalšími metodami ověřování pomáhají správcům chránit organizace a uživatele.

Když uživatelé potřebují přístup k citlivé aplikaci, resetovat heslo nebo povolit Windows Hello, může se jim zobrazit výzva s dodatečným ověřením, aby se ověřilo, že jsou to skutečně oni.

Dodatečné ověřování může mít podobu jedné z těchto metod ověřování:

* kód v e-mailu nebo zprávě SMS
* telefonní hovor
* oznámení nebo kód v telefonu
* odpovědi na bezpečnostní otázky

![Příklad přihlašovací stránky login.microsoftonline.com v prohlížeči Chrome](media/overview-authentication/overview-login.png)

Samoobslužné resetování hesla služby Azure MFA a Azure AD dává správcům kontrolu nad konfigurací, zásadami, monitorováním a vytvářením sestav ve službě Azure AD a na webu Azure Portal, aby mohli chránit své organizace.

## <a name="self-service-password-reset"></a>Samoobslužné resetování hesla

Samoobslužné resetování hesla dává uživatelům v případě potřeby možnost resetovat si heslo bez zásahu správce.

> [!VIDEO https://www.youtube.com/embed/hc97Yx5PJiM]

Samoobslužné resetování hesla zahrnuje:

* **Změnu hesla:** Znám svoje heslo, ale chci ho změnit na nové.
* **Resetování hesla:** Nemůžu se přihlásit a chci si heslo pomocí jedné ze schválených metod ověřování resetovat.
* **Odemknutí účtu:** Nemůžu se přihlásit ke svému účtu, protože je uzamčený a chci ho jednou ze schválených metod ověřování odemknout.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure Multi-Factor Authentication (MFA) je řešení dvoustupňového ověřování od Microsoftu. Použitím metod ověřování schválených správcem pomáhá Azure MFA chránit přístup k datům a aplikacím a současně splňuje požadavky na jednoduchý proces přihlašování.

## <a name="next-steps"></a>Další kroky

Dalším krokem je začít a nakonfigurovat samoobslužné resetování hesla a ověřování Azure Multi-Factor Authentication.

Jak začít používat samoobslužné resetování hesla najdete v článku [Rychlé nasazení samoobslužného resetování hesla Azure AD](quickstart-sspr.md).

Další informace o samoobslužném resetování hesla najdete v článku o [fungování samoobslužného resetování hesla Azure AD](concept-sspr-howitworks.md)

Více informací o ověřování Azure Multi-Factor Authentication najdete v článku o [fungování ověřování Azure Multi-Factor Authentication](concept-mfa-howitworks.md)