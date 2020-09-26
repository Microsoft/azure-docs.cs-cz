---
title: Zprostředkovatelé identity pro externí identity – Azure AD
description: Naučte se používat Azure AD jako výchozího zprostředkovatele identity pro sdílení s externími uživateli.
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
ms.openlocfilehash: a61eca3340985886ef782b28a59a1283c5f473ce
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91265707"
---
# <a name="identity-providers-for-external-identities"></a>Zprostředkovatelé identity pro externí identity

*Zprostředkovatel identity* vytváří, uchovává a spravuje informace o identitě při poskytování služeb ověřování aplikacím. Při sdílení aplikací a prostředků s externími uživateli je Azure AD výchozím zprostředkovatelem identity pro sdílení. To znamená, že když budete pozvat externí uživatele, kteří už mají službu Azure AD nebo účet Microsoft, můžou se automaticky přihlásit bez další konfigurace na vaší straně.

Můžete ale uživatelům povolit, aby se přihlásili pomocí různých poskytovatelů identity.

- **Google**: Google Federation umožňuje externím uživatelům uplatnit pozvánky od vás tím, že se přihlásí k aplikacím pomocí vlastních účtů Gmail. Google Federation se dá použít taky v uživatelských tocích samoobslužné registrace.
   > [!NOTE]
   > Pokud je v aktuálním předplatném samoobslužné přihlášení přidaný tok uživatele k aplikaci a když uživateli odešlete pozvánku k této aplikaci, uživatel nebude moct použít účet Gmail k uplatnění pozvánky. Alternativním řešením je, že uživatel může projít samoobslužným procesem registrace. Nebo můžou uplatnit pozvánku pomocí přístupu k jiné aplikaci nebo pomocí portálu moje aplikace na https://myapps.microsoft.com .

- **Facebook**: při sestavování aplikace můžete nakonfigurovat samoobslužné registrace a povolit facebookovou federaci, aby se uživatelé mohli k vaší aplikaci zaregistrovat pomocí vlastních účtů Facebook. Facebook se dá použít jenom pro uživatelské toky přihlašování k samoobslužným službám a není k dispozici jako možnost přihlášení, když uživatelé uplatní pozvánky od vás.

- **Přímá federace**: můžete také nastavit přímou federaci s jakýmkoli externím zprostředkovatelem identity, který podporuje protokoly SAML nebo WS. Přímá federace umožňuje externím uživatelům uplatnit pozvánky od vás tím, že se přihlásí ke svým aplikacím pomocí stávajících sociálních nebo podnikových účtů. 
   > [!NOTE]
   > Přímé zprostředkovatele identity federace nelze použít v uživatelských tocích samoobslužné registrace.


## <a name="how-it-works"></a>Jak to funguje

Funkce samoobslužného registrace externích identit Azure AD umožňuje uživatelům zaregistrovat se pomocí účtu Azure AD, Google nebo Facebook. Pokud chcete v tenantovi Azure AD nastavit poskytovatele sociálních identit, vytvoříte pro každého zprostředkovatele identity aplikaci a nakonfigurujete přihlašovací údaje. Získáte ID klienta nebo aplikace a tajný klíč klienta nebo aplikace, které pak můžete přidat do svého tenanta služby Azure AD.

Po přidání poskytovatele identity k vašemu tenantovi služby Azure AD:

- Když pozvání externího uživatele k aplikacím nebo prostředkům ve vaší organizaci budete pozvat, externí uživatel se může přihlásit pomocí vlastního účtu s tímto poskytovatelem identity.
- Když pro své aplikace povolíte [samoobslužnou registraci](self-service-sign-up-overview.md) , můžou si externí uživatelé zaregistrovat svoje aplikace pomocí vlastních účtů s poskytovateli identit, které jste přidali.

> [!NOTE]
> Služba Azure AD je ve výchozím nastavení povolená pro samoobslužné registrace, takže uživatelé mají vždycky možnost se zaregistrovat pomocí účtu Azure AD.

Při uplatnění pozvánky nebo registraci k vaší aplikaci má externí uživatel možnost přihlásit se a ověřit se zprostředkovatelem sociální identity:

![Snímek obrazovky zobrazující přihlašovací obrazovku s možnostmi Google a Facebook](media/identity-providers/sign-in-with-social-identity.png)

Pro optimální prostředí přihlašování federovat se zprostředkovateli identity, kdykoli je to možné, abyste mohli pozvaní hostům umožnit bezproblémové přihlašování při přístupu k aplikacím.  

## <a name="next-steps"></a>Další kroky

Informace o tom, jak přidat zprostředkovatele identity pro přihlášení k vašim aplikacím, najdete v následujících článcích:

- [Přidat Google](google-federation.md) do seznamu poskytovatelů sociálních identit
- [Přidat Facebook](facebook-federation.md) do seznamu poskytovatelů sociálních identit
- [Nastavte přímou federaci](direct-federation.md) s jakoukoli organizací, jejíž poskytovatel identity podporuje protokol SAML 2,0 nebo WS-dodávání. Upozorňujeme, že přímá federace není možností pro uživatelské toky samoobslužné registrace.
