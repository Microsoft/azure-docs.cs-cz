---
title: Ověřování a zabezpečení uživatelů – Azure Active Directory
description: Jak jako správce Azure AD ochráním ověřování uživatelů a zároveň snížím dopad na koncové uživatele?
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccbb754f03f77ce0571912a5443dc9b18c66b463
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381109"
---
# <a name="what-methods-are-available-for-authentication"></a>Které metody ověřování jsou dostupné?

Občas můžete slyšet ve zprávách, jak někdo ukradne hesla nebo dojde k ohrožení zabezpečení identit. Zabezpečení organizace můžete kromě běžného hesla okamžitě zvýšit vynucením dvoufaktorového ověřování. Microsoft Azure Active Directory (Azure AD) obsahuje funkce, jako je Azure Multi-Factor Authentication (Azure MFA) a samoobslužné resetování hesla Azure AD (SSPR), které s dalšími metodami ověřování pomáhají správcům chránit organizace a uživatele.

Existuje mnoho scénářů, které zahrnují: přihlášení do aplikace, resetování hesla, povolení Windows Hello a dalších uživatelů. vaši uživatelé mohou být požádáni o poskytnutí dodatečného ověření, které jim říkají.

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

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Další kroky

Dalším krokem je začít a nakonfigurovat samoobslužné resetování hesla a ověřování Azure Multi-Factor Authentication.

Jak začít používat samoobslužné resetování hesla najdete v článku [Rychlé nasazení samoobslužného resetování hesla Azure AD](quickstart-sspr.md).

Další informace o samoobslužném resetování hesla najdete v článku o [fungování samoobslužného resetování hesla Azure AD](concept-sspr-howitworks.md)

Více informací o ověřování Azure Multi-Factor Authentication najdete v článku o [fungování ověřování Azure Multi-Factor Authentication](concept-mfa-howitworks.md)
