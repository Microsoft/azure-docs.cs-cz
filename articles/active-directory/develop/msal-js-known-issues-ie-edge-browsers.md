---
title: Problémy v Internet Exploreru & Microsoft Edge (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Seznamte se s informacemi o tom, jaké jsou problémy při používání knihovny Microsoft Authentication Library pro JavaScript (MSAL. js) s Internet Explorerem a prohlížeči Microsoft Edge.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5ae2dee68ec0da8e8a00d4f01583461462bc196c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76696091"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Známé problémy v prohlížečích Internet Explorer a Microsoft Edge (MSAL. js)

## <a name="issues-due-to-security-zones"></a>Problémy způsobené zónami zabezpečení
V IE a Microsoft Edge jsme měli několik sestav o problémech s ověřováním (od aktualizace *verze prohlížeče Microsoft Edge do 40.15063.0.0*). Sledujeme tyto informace a informovali o týmu Microsoft Edge. I když Microsoft Edge funguje na řešení, tady je popis často se vyskytujících potíží a možných alternativních řešení, která je možné implementovat.

### <a name="cause"></a>Příčina
Příčina většiny těchto problémů je následující. Úložiště relací a místní úložiště jsou rozdělené podle zón zabezpečení v prohlížeči Microsoft Edge. V této konkrétní verzi Microsoft Edge se při přesměrování aplikace mezi zónami vymažou úložiště relací a místní úložiště. Konkrétně je úložiště relací v normální navigaci v prohlížeči vymazáno a relace i místní úložiště jsou vymazány v režimu InPrivate prohlížeče. MSAL. js uloží určitý stav do úložiště relace a spoléhá na kontrolu tohoto stavu během toků ověřování. Při vymazání úložiště relace dojde ke ztrátě tohoto stavu, takže dojde k přerušení prostředí.

### <a name="issues"></a>Problémy

- **Nekonečná smyčka přesměrování a opakované načítání stránek během ověřování**. Když se uživatelé přihlásí k aplikaci v Microsoft Edge, budou přesměrováni zpátky z přihlašovací stránky AAD a zablokují se v nekonečné smyčce přesměrování, což má za následek opakované opětovné načtení stránky. Obvykle se doprovází `invalid_state` chyba v úložišti relace.

- **Nekonečná smyčka tokenů získání a AADSTS50058 chyba**. Když se aplikace běžící v Microsoft Edge pokusí získat token pro určitý prostředek, aplikace se může zablokovat v nekonečné smyčce volání metody Get tokenu spolu s následující chybou v protokolu AAD v trasování sítě:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Automaticky otevíraná okna se nezavřou nebo se zablokuje při ověřování pomocí přihlašovacích údajů prostřednictvím místní nabídky**. Při ověřování prostřednictvím překryvného okna v Microsoft Edge nebo IE (InPrivate) po zadání přihlašovacích údajů a přihlášení se v případě, že je v navigaci zapojeno více domén napříč zónami zabezpečení, se místní okno nezavře, protože MSAL. js ztratí popisovač do místního okna.  

### <a name="update-fix-available-in-msaljs-023"></a>Aktualizace: Oprava je k dispozici v MSAL. js 0.2.3
Opravy potíží s smyčkou přesměrování ověřování byly vydány v [MSAL. js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Pokud chcete tuto `storeAuthStateInCookie` opravu využít, povolte příznak v konfiguraci MSAL. js. Ve výchozím nastavení je tento příznak nastaven na hodnotu false.

Pokud je `storeAuthStateInCookie` příznak povolený, MSAL. js použije soubory cookie prohlížeče k uložení stavu požadavku potřebného k ověření toků ověřování.

> [!NOTE]
> Tato oprava ještě není k dispozici pro obálky msal-úhlů a msal-AngularJS. Tato oprava neřeší problém pomocí překryvných oken.

Použijte alternativní řešení níže.

#### <a name="other-workarounds"></a>Další alternativní řešení
Před přijetím těchto alternativních řešení se ujistěte, že se k vašemu problému dochází jenom v konkrétní verzi prohlížeče Microsoft Edge a funguje v ostatních prohlížečích.  
1. V prvním kroku, jak tyto problémy obejít, se ujistěte, že se doména aplikace, a všechny další lokality, které jsou zapojeny do přesměrování toku ověřování, přidaly jako důvěryhodné lokality v nastavení zabezpečení prohlížeče tak, aby patřily do stejné bezpečnostní zóny.
Postup je následující:
    - Otevřete **Internet Explorer** a klikněte na **Nastavení** (ikona ozubeného kolečka) v pravém horním rohu.
    - Vybrat **Možnosti Internetu**
    - Vyberte kartu **zabezpečení** .
    - V části **Důvěryhodné servery** klikněte na tlačítko **lokality** a v dialogovém okně, které se otevře, přidejte adresy URL.

2. Jak už bylo uvedeno dříve, protože během pravidelné navigace bylo vymazáno pouze úložiště relace, můžete MSAL. js nakonfigurovat tak, aby místo toho používal místní úložiště. To lze nastavit jako parametr `cacheLocation` konfigurace při inicializaci MSAL.

Upozorňujeme, že tento problém se nevyřeší při procházení InPrivate, protože se vymažou jak relace, tak místní úložiště.

## <a name="issues-due-to-popup-blockers"></a>Problémy kvůli blokování automaticky otevíraných oken

Existují případy, kdy jsou automaticky otevíraná okna blokována v IE nebo Microsoft Edge, například když během služby Multi-Factor Authentication dojde k druhému místnímu přihlášení. V prohlížeči se zobrazí upozornění, aby se místní nabídka povolila jednou nebo vždycky. Pokud se rozhodnete pro povolení, prohlížeč otevře automaticky otevírané okno a vrátí `null` popisovač pro něj. V důsledku toho knihovna nemá popisovač pro okno a neexistuje žádný způsob, jak zavřít překryvné okno. Ke stejnému problému nedochází v Chrome, když vás vyzve k povolení překryvných oken, protože neotevře automaticky otevírané okno.

**Alternativním řešením**je, že vývojáři budou muset v IE a Microsoft Edge povolená překryvná okna předtím, než začnou používat svoji aplikaci, aby se tomuto problému vyhnuli.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [použití MSAL. js v Internet Exploreru](msal-js-use-ie-browser.md).
