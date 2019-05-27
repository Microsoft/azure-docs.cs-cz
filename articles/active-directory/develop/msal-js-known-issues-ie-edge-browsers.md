---
title: Známé problémy v prohlížečích (knihovna Microsoft Authentication Library pro JavaScript) | Azure
description: Další informace o vědět problémy při použití knihovna Microsoft Authentication Library pro JavaScript (MSAL.js) s prohlížeči Internet Explorerem a Microsoft Edgem.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c57ed956ec50c8bac26720a27894c07353928336
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873897"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Známé problémy v prohlížeče Internet Explorer a Microsoft Edge s MSAL.js

## <a name="issues-due-to-security-zones"></a>Potíže způsobené zóny zabezpečení
Narazili jsme na více sestav problémy s ověřováním v IE a Microsoft Edge (od aktualizace *verze prohlížeče Microsoft Edge na 40.15063.0.0*). Toto sledování jsme vést informované týmu Microsoft Edge. Když Microsoft Edge pracuje na řešení, tady je popis nejčastěji se vyskytující problémy a možná alternativní řešení, které je možné implementovat.

### <a name="cause"></a>Příčina
Příčinou pro většinu těchto problémů je následujícím způsobem. Místní úložiště a úložiště relace dělí podle zóny zabezpečení v prohlížeči Microsoft Edge. V této konkrétní verzi sady Microsoft Edge když aplikace přesměruje napříč zónami, relace úložiště a místní úložiště jsou vymazány. Konkrétně relace úložiště je situace v pravidelných prohlížeče navigace a relace a místní úložiště v režimu InPrivate prohlížeče vymažou. MSAL.js uloží jejich určitého stavu v úložišti relace a spoléhá na tento stav kontroluje během ověřování toků. Pokud úložiště relace není zaškrtnuté, tento stav dojde ke ztrátě a proto za následek přerušené prostředí.

### <a name="issues"></a>Problémy

- **Přesměrování nekonečné smyčky a stránku znovu načte během ověřování**. Při přihlášení uživatele k aplikaci na Microsoft Edge přesměrováni zpět z AAD přihlašovací stránku a se zablokuje a v přesměrování nekonečné smyčce výsledkem zpracovávané opakované stránky. To je obvykle připojen `invalid_state` Chyba ve službě storage relace.

- **Nekonečné získat token smyčky a chyba AADSTS50058**. Když aplikaci běžící v Microsoft Edge se pokusí získat token pro určitý prostředek, aplikace můžou uváznout v nekonečné smyčce získání tokenu volání spolu s následující chybu ze služby AAD v trasování v síti:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Automaticky otevírané okno nezavírá nebo se zasekne při ověření pomocí přihlášení přes automaticky otevírané okno**. Při ověřování prostřednictvím automaticky otevíraném okně v Microsoft Edge nebo IE(InPrivate), po zadání přihlašovacích údajů a přihlašování, pokud na navigačním panelu se týká více domén napříč zónami zabezpečení automaticky otevíraném okně nezavírá protože MSAL.js ztratí úchytu automaticky otevíraném okně.  

    Tady jsou odkazy na tyto problémy v Microsoft Edge sledování problémů:  
    - [Chyba 13861050](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861050/)
    - [Chyba 13861663](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861663/)

### <a name="update-fix-available-in-msaljs-023"></a>Aktualizace: K dispozici v MSAL.js 0.2.3 oprava.
Opravy pro problémy s ověřováním přesměrování smyčky byly vydány v [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Povolit příznak `storeAuthStateInCookie` MSAL.js konfiguraci chcete využít výhod této opravy. Ve výchozím nastavení je tento příznak nastaven na hodnotu false.

Když `storeAuthStateInCookie` příznak povolený, MSAL.js použije k uložení stavu žádosti o požadované pro ověření toků ověřování souborů cookie prohlížeče.

> [!NOTE]
> Tato oprava není k dispozici pro obálky msal angular a službou msal angularjs. Tato oprava neřeší problém v systému windows automaticky otevíraného okna.

Použijte následující alternativní řešení.

#### <a name="other-workarounds"></a>Jiná řešení
Ujistěte se, že chcete otestovat, že je před přijetím těchto alternativních řešení problému dochází pouze na konkrétní verzi prohlížeče Microsoft Edge a funguje v ostatních prohlížečích.  
1. Jako první krok k vyřešení těchto problémů, ujistěte se, že doménu aplikace, a jakýchkoli jiných lokalit, které jsou součástí přesměrování tok ověřování jsou přidány jako důvěryhodných webů v nastavení zabezpečení prohlížeče, aby patří do stejné zóně zabezpečení.
Chcete-li to provést, postupujte takto:
    - Otevřít **aplikace Internet Explorer** a klikněte na **nastavení** (ikona ozubeného kolečka) v pravém horním rohu
    - Vyberte **Možnosti Internetu**
    - Vyberte **zabezpečení** kartu
    - V části **Důvěryhodné servery** možnost, klikněte na **lokality** tlačítko a přidejte adresy URL v dialogovém okně, které se otevře.

2. Jak už bylo zmíněno dříve, od jenom relace se vymaže úložiště během pravidelné navigace, můžete nakonfigurovat MSAL.js místo toho použít místní úložiště. To je možné nastavit jako `cacheLocation` konfigurační parametr při inicializaci MSAL.

Všimněte si, že se to sice vyřešit problém pro procházení InPrivate vzhledem k tomu, že jsou vymazány relace a místní úložiště.

## <a name="issues-due-to-popup-blockers"></a>Potíže způsobené automaticky otevíraných oken

Existují případy, kdy jsou zablokována vyskakovací okna v aplikaci Internet Explorer nebo Microsoft Edge, například když při ověřování službou Multi-Factor Authentication dojde k druhé automaticky otevíraného okna. Zobrazí se upozornění v prohlížeč tak, aby automaticky otevíraného okna, jednou nebo vždy. Pokud budete chtít povolit, automaticky se otevře automaticky otevíraném okně prohlížeče a vrátí `null` zpracování pro něj. V důsledku toho knihovny nemá popisovač okna a neexistuje žádný způsob, jak automaticky otevíraném okně zavřete. Stejný problém se neodehrává v prohlížeči Chrome, pokud budete vyzváni k povolit automaticky otevíraná okna, protože ji neotevře automaticky automaticky otevíraném okně.

Jako **řešení**, vývojáři budou muset povolit automaticky otevíraná okna v IE a Microsoft Edge, před zahájením používání jejich aplikace Pokud chcete vyhnout tomuto problému.

## <a name="next-steps"></a>Další postup
Další informace o [využitím MSAL.js v aplikaci Internet Explorer](msal-js-use-ie-browser.md).
