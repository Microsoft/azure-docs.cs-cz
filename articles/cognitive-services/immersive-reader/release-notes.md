---
title: Poznámky k verzi pro moderní čtečku pro sadu SDK
titleSuffix: Azure Cognitive Services
description: Přečtěte si další informace o novinkách v sadě JavaScript SDK pro moderní čtečku.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 10/12/2020
ms.author: dylankil
ms.openlocfilehash: 6b041916c6fa446732e95d49391d9ead63eb1b17
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133530"
---
# <a name="immersive-reader-javascript-sdk-release-notes"></a>Poznámky k verzi sady JavaScript pro moderní čtečku

## <a name="version-110"></a>1.1.0 verze

Tato verze obsahuje nové funkce, opravy ohrožení zabezpečení, opravy chyb, aktualizace ukázek kódu a možnosti konfigurace.

#### <a name="new-features"></a>Nové funkce

* Povolit ukládání a načítání uživatelských předvoleb v různých prohlížečích a zařízeních
* Povolit konfiguraci výchozích možností zobrazení
* Přidejte možnost pro nastavení jazyka překladu, povolení překladu slov a povolení překladu dokumentů při spouštění moderního čtecího zařízení.
* Přidání podpory pro konfiguraci čtení nahlasu přes možnosti
* Umožňuje přidat možnost zakázat první spuštění prostředí.
* Přidat ImmersiveReaderView pro UWP

#### <a name="improvements"></a>Vylepšen

* Aktualizujte kód HTML ukázky kódu pro Android, aby fungoval s nejnovější sadou SDK.
* Odpověď na spuštění aktualizace pro vrácení počtu zpracovaných znaků
* Aktualizace ukázek kódu pro použití v 1.1.0
* Nepovolit volání launchAsync, když se už načítají
* Kontrolovat neplatný obsah ignorováním zpráv, kde data nejsou řetězcem
* Zabalení volání okna v klauzuli if pro kontrolu podpory Promise prohlížeče

#### <a name="fixes"></a>Opravy

* Opravit dependabot odebráním příze. Lock z gitignore
* Oprava ohrožení zabezpečení pomocí upgradu PUG na verzi v 3.0.0 v rychlém startu – ukázka kódu NodeJS
* Oprava několika chyb zabezpečení při upgradu balíčků jest a TypeScript
* Oprava bezpečnostní chyby zabezpečení upgradem Microsoft. IdentityModel. clients. Active na verzi v 5.2.0

<br>

## <a name="version-100"></a>1.0.0 verze

Tato verze obsahuje zásadní změny, nové funkce, vylepšení ukázek kódu a opravy chyb.

#### <a name="breaking-changes"></a>Zásadní změny

* Vyžaduje token Azure AD a subdoménu a zastaralá tokeny používané v předchozích verzích.
* Nastavte CookiePolicy na Disabled. Zachování uživatelských předvoleb je ve výchozím nastavení zakázáno. Čtecí modul se spustí s výchozím nastavením pokaždé, pokud není CookiePolicy nastavené na povoleno.

#### <a name="new-features"></a>Nové funkce

* Přidání podpory pro povolení nebo zakázání souborů cookie
* Ukázka kódu rychlého startu pro Android Kotlin
* Ukázka kódu pro rychlý Start pro Android Java
* Ukázka kódu pro rychlé zahájení přidání Node.js

#### <a name="improvements"></a>Vylepšen

* Aktualizace Node.js rozšířené README.md
* Změna ukázky kódu Pythonu z možnosti Upřesnit na rychlé spuštění
* Přesunout ukázku kódu SWIFT pro iOS do JS/ukázek
* Aktualizace ukázek kódu pro použití v 1.0.0

#### <a name="fixes"></a>Opravy

* Oprava pro Node.js pokročilý ukázkový kód
* Přidání chybějících souborů pro Advanced-CSharp-Multiple-Resources
* Odebrání odkazů en-US z

<br>

## <a name="version-003"></a>0.0.3 verze

Tato verze obsahuje nové funkce, vylepšení ukázek kódu, opravy ohrožení zabezpečení a opravy chyb.

#### <a name="new-features"></a>Nové funkce

* Přidání ukázky kódu SWIFT pro iOS
* Přidat rozšířený ukázkový kód v jazyce C# demonstrující použití více prostředků 
* Přidání podpory pro vypnutí funkce přepínání zobrazení na celé obrazovce
* Přidání podpory pro skrytí tlačítka pro opuštění aplikace s moderní čtečkou
* Přidejte funkci zpětného volání, která může být používána hostitelskou aplikací při ukončení moderního čtecího zařízení.
* Aktualizace ukázek kódu pro použití Azure Active Directory ověřování

#### <a name="improvements"></a>Vylepšen

* Aktualizovat rozšířený ukázkový kód pro C# pro zahrnutí wordového dokumentu
* Aktualizace ukázek kódu pro použití v 0.0.3

#### <a name="fixes"></a>Opravy

* Pokud chcete opravit chybu zabezpečení, upgradujte lodash na verzi 4.17.14.
* Aktualizace knihovny C# MSAL pro opravu ohrožení zabezpečení
* Pokud chcete opravit zranitelnost zabezpečení, upgradujte Mixin-hluboko na verzi 1.3.2.
* Upgradujte jest, Webpack a Webpack-CLI, které používaly ohrožené verze Set-Value a Mixin-hluboko k opravě ohrožení zabezpečení.

<br>

## <a name="version-002"></a>0.0.2 verze

Tato verze obsahuje nové funkce, vylepšení ukázek kódu, opravy ohrožení zabezpečení a opravy chyb.

#### <a name="new-features"></a>Nové funkce

* Přidat ukázku kódu pro Python Advanced
* Přidat ukázku kódu pro rychlý Start Java
* Přidat ukázku jednoduchého kódu

#### <a name="improvements"></a>Vylepšen

* Přejmenovat resourceName na cogSvcsSubdomain
* Přesunutí tajných kódů z kódu a používání proměnných prostředí
* Aktualizace ukázek kódu pro použití v 0.0.2

#### <a name="fixes"></a>Opravy

* Oprava chyb usnadnění – tlačítko pro moderní čtečku
* Opravit přerušené posouvání
* Pokud chcete opravit chybu zabezpečení, upgradujte balíček handlebars na verzi 4.1.2.
* Opravuje chyby v testech jednotek sady SDK.
* Opraví chyby kompatibility v JavaScriptu pro Internet Explorer 11
* Aktualizuje adresy URL sady SDK.

<br>

## <a name="version-001"></a>0.0.1 verze

Počáteční verze sady moderní čtečky JavaScript SDK.

* Přidat sadu JavaScript SDK pro moderní čtečku
* Přidání podpory pro určení jazyka uživatelského rozhraní
* Přidejte časový limit, který určí, kdy se má funkce launchAsync zdařit s chybou časového limitu.
* Přidání podpory pro určení indexu z-index prvku IFRAME pro moderní čtečku
* Přidání podpory pro použití značky WebView namísto prvku IFRAME pro zajištění kompatibility s aplikacemi pro Chrome
* Přidat testy jednotek sady SDK
* Přidat Node.js ukázka rozšířeného kódu
* Přidat rozšířený ukázku kódu pro C#
* Ukázka kódu pro rychlý Start v C#
* Přidat konfiguraci balíčku, přízi a jiné soubory sestavení
* Přidat konfigurační soubory Git
* Přidání souborů README.md do ukázek kódu a sady SDK
* Přidat licenci MIT
* Přidat pokyny přispěvatele
* Přidat prostředky SVG tlačítka pro statické ikony

## <a name="next-steps"></a>Další kroky

Začínáme s Asistivní čtečkou:

* Přečtěte si [odkaz na klientskou knihovnu pro moderní čtečku](./reference.md)
* Prozkoumat [klientskou knihovnu pro moderní čtenáře na GitHubu](https://github.com/microsoft/immersive-reader-sdk)
