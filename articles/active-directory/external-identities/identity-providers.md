---
title: Zprostředkovatelé identity pro externí identity – Azure AD
description: Naučte se používat Azure AD jako výchozího zprostředkovatele identity pro sdílení s externími uživateli.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdef929b27c636b3908dd7a88eb93adc2382a53f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687735"
---
# <a name="identity-providers-for-external-identities"></a>Zprostředkovatelé identity pro externí identity

> [!NOTE]
> Některé z funkcí, které jsou uvedené v tomto článku, jsou funkce Azure Active Directory veřejné verze Preview. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

*Zprostředkovatel identity* vytváří, uchovává a spravuje informace o identitě při poskytování služeb ověřování aplikacím. Při sdílení aplikací a prostředků s externími uživateli je Azure AD výchozím zprostředkovatelem identity pro sdílení. To znamená, že když budete pozvat externí uživatele, kteří už mají službu Azure AD nebo účet Microsoft, můžou se automaticky přihlásit bez další konfigurace na vaší straně.

Kromě účtů Azure AD nabízí externí identity nejrůznější poskytovatele identity.

- **Účty Microsoft** (Preview): uživatelé typu Host můžou použít vlastní osobní účet Microsoft (MSA) a uplatnit pozvánky na spolupráci B2B. Při nastavování samoobslužného uživatelského toku registrace můžete přidat [účet Microsoft (Preview)](microsoft-account.md) jako jednoho z povolených zprostředkovatelů identity. K dispozici není žádná další konfigurace, aby bylo možné tohoto zprostředkovatele identity zpřístupnit uživatelským tokům.

- **Heslo pro jednorázové e-maily** (Preview): při uplatnění pozvánky nebo přístupu ke sdílenému prostředku může uživatel typu Host požádat o dočasný kód, který se pošle na svou e-mailovou adresu. Pak tento kód zadá, aby bylo možné pokračovat v přihlašování. Funkce jednorázového hesla pro e-mail ověřuje uživatele typu Host B2B, když je nepůjde ověřit jiným způsobem. Při nastavování uživatelského toku samoobslužné registrace můžete přidat **e-mailové One-Time heslo (Preview)** jako jeden z povolených zprostředkovatelů identity. Vyžaduje se některé nastavení. viz [e-mail ověřování jednorázového hesla](one-time-passcode.md).

- **Google**: Google Federation umožňuje externím uživatelům uplatnit pozvánky od vás tím, že se přihlásí k aplikacím pomocí vlastních účtů Gmail. Google Federation se dá použít taky v uživatelských tocích samoobslužné registrace. Podívejte se, jak [Přidat Google jako poskytovatele identity](google-federation.md).
   > [!IMPORTANT]
   > **Od 4. ledna 2021** je Google [zastaralá podpora přihlašování v nástroji WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Pokud používáte Google Federation nebo samoobslužnou registraci pomocí služby Gmail, měli byste [testovat kompatibilitu vašich obchodních nativních aplikací](google-federation.md#deprecation-of-webview-sign-in-support).

- **Facebook**: při sestavování aplikace můžete nakonfigurovat samoobslužné registrace a povolit facebookovou federaci, aby se uživatelé mohli k vaší aplikaci zaregistrovat pomocí vlastních účtů Facebook. Facebook se dá použít jenom pro uživatelské toky přihlašování k samoobslužným službám a není k dispozici jako možnost přihlášení, když uživatelé uplatní pozvánky od vás. Podívejte se [, jak přidat Facebook jako zprostředkovatele identity](facebook-federation.md).

- **Přímá federace**: můžete také nastavit přímou federaci s jakýmkoli externím zprostředkovatelem identity, který podporuje protokoly SAML nebo WS-Fed. Přímá federace umožňuje externím uživatelům uplatnit pozvánky od vás tím, že se přihlásí ke svým aplikacím pomocí stávajících sociálních nebo podnikových účtů. Podívejte se, jak [nastavit přímou federaci](direct-federation.md).
   > [!NOTE]
   > Přímé zprostředkovatele identity federace nelze použít v uživatelských tocích samoobslužné registrace.

## <a name="adding-social-identity-providers"></a>Přidávají se poskytovatelé sociálních identit.

Služba Azure AD je ve výchozím nastavení povolená pro samoobslužné registrace, takže uživatelé mají vždycky možnost se zaregistrovat pomocí účtu Azure AD. Můžete ale povolit jiné poskytovatele identity, včetně poskytovatelů sociálních identit, jako je Google nebo Facebook. Pokud chcete nastavit zprostředkovatele sociální identity v tenantovi Azure AD, vytvoříte aplikaci na zprostředkovateli identity a nakonfigurujete přihlašovací údaje. Získáte ID klienta nebo aplikace a tajný klíč klienta nebo aplikace, které pak můžete přidat do svého tenanta služby Azure AD.

Po přidání poskytovatele identity k vašemu tenantovi služby Azure AD:

- Když pozvání externího uživatele k aplikacím nebo prostředkům ve vaší organizaci budete pozvat, externí uživatel se může přihlásit pomocí vlastního účtu s tímto poskytovatelem identity.
- Když pro své aplikace povolíte [samoobslužnou registraci](self-service-sign-up-overview.md) , můžou si externí uživatelé zaregistrovat svoje aplikace pomocí vlastních účtů s poskytovateli identit, které jste přidali. Budou moct vybírat z možností poskytovatelů sociálních identit, které jste na stránce pro registraci zpřístupnili:

   ![Snímek obrazovky zobrazující přihlašovací obrazovku s možnostmi Google a Facebook](media/identity-providers/sign-in-with-social-identity.png)

Pro optimální prostředí přihlašování federovat se zprostředkovateli identity, kdykoli je to možné, abyste mohli pozvaní hostům umožnit bezproblémové přihlašování při přístupu k aplikacím.  

## <a name="next-steps"></a>Další kroky

Informace o tom, jak přidat zprostředkovatele identity pro přihlášení k vašim aplikacím, najdete v následujících článcích:

- [Přidání e-mailového ověřování jednorázového hesla](one-time-passcode.md)
- [Přidat Google](google-federation.md) jako povoleného poskytovatele sociálních identit
- [Přidat Facebook](facebook-federation.md) jako povoleného poskytovatele sociálních identit
- [Nastavte přímou federaci](direct-federation.md) s jakoukoli organizací, jejíž poskytovatel identity podporuje protokol SAML 2,0 nebo WS-Fed. Upozorňujeme, že přímá federace není možností pro uživatelské toky samoobslužné registrace.
