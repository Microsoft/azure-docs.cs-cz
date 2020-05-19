---
title: Zprostředkovatelé identity pro externí identity – Azure AD
description: Azure Active Directory spolupráce B2B podporuje vícefaktorové ověřování (MFA) pro selektivní přístup k podnikovým aplikacím.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 395473e70137ead2b7185d54d165f82e9b3f1f04
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598147"
---
# <a name="identity-providers-for-external-identities"></a>Zprostředkovatelé identity pro externí identity

*Zprostředkovatel identity* vytváří, uchovává a spravuje informace o identitě při poskytování služeb ověřování aplikacím. Při sdílení aplikací a prostředků s externími uživateli je Azure AD výchozím zprostředkovatelem identity pro sdílení. To znamená, že když budete pozvat externí uživatele, kteří už mají službu Azure AD nebo účet Microsoft, můžou se automaticky přihlásit bez další konfigurace na vaší straně.

Můžete ale uživatelům povolit, aby se přihlásili pomocí různých poskytovatelů identity. Můžete například nastavit federaci se zprostředkovateli sociálních identit, které podporuje Azure AD, včetně Google a Facebooku. Můžete se také federovat s jakýmkoli externím zprostředkovatelem identity, který podporuje protokoly SAML nebo WS. S externím federačním zprostředkovatelem identity můžete nabídnout externím uživatelům možnost přihlásit se k aplikacím pomocí stávajících účtů sociálních nebo podnikových účtů.

## <a name="how-it-works"></a>Jak to funguje

Služba Azure AD External identity je předem nakonfigurovaná pro federaci s Google a Facebookem. Pokud chcete nastavit tyto zprostředkovatele identity v tenantovi Azure AD, vytvoříte aplikaci pro každého zprostředkovatele identity a nakonfigurujete přihlašovací údaje. Získáte ID klienta nebo aplikace a tajný klíč klienta nebo aplikace, které pak můžete přidat do svého tenanta služby Azure AD.

Po přidání poskytovatele identity k vašemu tenantovi služby Azure AD:

- Když pozvání externího uživatele k aplikacím nebo prostředkům ve vaší organizaci budete pozvat, externí uživatel se může přihlásit pomocí vlastního účtu s tímto poskytovatelem identity.
- Když pro své aplikace povolíte [samoobslužnou registraci](self-service-sign-up-overview.md) , můžou si externí uživatelé zaregistrovat svoje aplikace pomocí vlastních účtů s poskytovateli identit, které jste přidali. 

Při uplatnění pozvánky nebo registraci k vaší aplikaci má externí uživatel možnost přihlásit se a ověřit se zprostředkovatelem sociální identity:

![Snímek obrazovky zobrazující přihlašovací obrazovku s možnostmi Google a Facebook](media/identity-providers/sign-in-with-social-identity.png)

Pro optimální prostředí přihlašování federovat se zprostředkovateli identity, kdykoli je to možné, abyste mohli pozvaní hostům umožnit bezproblémové přihlašování při přístupu k aplikacím.  

## <a name="next-steps"></a>Další kroky

Informace o tom, jak přidat zprostředkovatele identity pro přihlášení k vašim aplikacím, najdete v následujících článcích:

- [Přidat Google](google-federation.md) do seznamu poskytovatelů sociálních identit
- [Přidat Facebook](facebook-federation.md) do seznamu poskytovatelů sociálních identit
- [Nastavte přímou federaci](direct-federation.md) s jakoukoli organizací, jejíž poskytovatel identity podporuje protokol SAML 2,0 nebo WS-dodávání. Upozorňujeme, že přímá federace není možností pro uživatelské toky samoobslužné registrace.
