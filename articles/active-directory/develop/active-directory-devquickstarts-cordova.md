---
title: Začínáme se službou Azure AD Cordova | Microsoft Docs
description: Jak sestavit aplikaci Cordova, který se integruje s Azure AD pro přihlášení a zavolá rozhraní API Azure AD chráněné pomocí OAuth.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: b1a8d7bd-7ad6-44d5-8ccb-5255bb623345
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 6d6d514875aa675bf160ee08a3e94b58944020ee
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156727"
---
# <a name="azure-ad-cordova-getting-started"></a>Začínáme se službou Azure AD Cordova
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

K vývoji aplikací HTML5/JavaScript, které můžou běžet na mobilních zařízeních jako plně kvalifikované nativních aplikací můžete použít Apache Cordova. S Azure Active Directory (Azure AD) můžete přidat možnosti ověřování podnikové úrovni pro vaše aplikace Cordova.

Modulu plug-in Cordova zabalí Azure AD nativních sad SDK pro iOS, Android, Windows Store a Windows Phone. Pomocí, modul plug-in, můžete vylepšit vaší aplikace pro podporu přihlašování pomocí účtů služby Active Directory pro Windows Server vaši uživatelé získat přístup k Office 365 a rozhraní API služby Azure a i pomoc při ochraně volání vlastní vlastní webové rozhraní API.

V tomto kurzu používáme Apache Cordova, modul plug-in pro Active Directory Authentication Library (ADAL) ke zlepšování jednoduchou aplikaci přidáním následující funkce:

* Pomocí několika řádků kódu ověření uživatele a získat token.
* Tento token používaná k volání rozhraní Graph API pro dotazování adresáře a zobrazit výsledky. 
* Chcete-li minimalizovat ověřování výzvy pro uživatele pomocí ADAL mezipamětí tokenů.

Chcete-li tato vylepšení, budete muset:

1. Zaregistrovat aplikaci s Azure AD.
2. Přidávání kódu do vaší aplikace požadovat tokeny.
3. Přidejte kód k použití tokenu k dotazování na rozhraní Graph API a zobrazit výsledky.
4. Vytvoření projektu nasazení Cordova s všechny platformy, kterou chcete zacílit, přidat Cordova ADAL modulu plug-in a testování řešení v emulátorů.

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu potřebujete:

* Klient služby Azure AD, kde máte účet s právy pro vývoj aplikací.
* Vývojové prostředí, který je nakonfigurován pro použití Apache Cordova. 

Jak už máte-li nastavit, přímo přejít na krok 1.

Pokud nemáte klient služby Azure AD, použijte [pokyny o tom, jak získat](active-directory-howto-tenant.md).

Pokud nemáte nastavení na počítači pro Apache Cordova, nainstalujte následující:

* [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Node.js](https://nodejs.org/download/)
* [Rozhraní příkazového řádku Cordova](https://cordova.apache.org/) (se dá snadno nainstalovat prostřednictvím Správce balíčku NPM: `npm install -g cordova`)

Předchozí instalace by měly fungovat na počítači PC i na Mac.

Každý Cílová platforma má jiné předpoklady:

* Sestavení a spuštění aplikace pro Windows Tabletu nebo Windows Phone:
  * Nainstalujte [Visual Studio 2013 pro Windows s aktualizací 2 nebo novější](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express nebo jinou verzi) nebo [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs#d-community).

* Sestavení a spuštění aplikace pro iOS:

  * Instalaci Xcode 6.x nebo novější. Stažení z [vývojáře Apple lokality](http://developer.apple.com/downloads) nebo [Mac App Storu](http://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  * Nainstalujte [ios-sim](https://www.npmjs.org/package/ios-sim). Můžete ji spustit aplikací pro iOS v simulátoru iOS z příkazového řádku. (Můžete ho snadno nainstalovat prostřednictvím terminálu: `npm install -g ios-sim`.)
* Sestavení a spuštění aplikace pro Android:

  * Nainstalujte [Java Development Kit (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) nebo novější. Zajistěte, aby `JAVA_HOME` (proměnnou prostředí) je správně nastavena podle JDK instalační cestu (například C:\Program Files\Java\jdk1.7.0_75).
  * Nainstalujte [sady SDK pro Android](http://developer.android.com/sdk/installing/index.html?pkg=tools) a přidejte `<android-sdk-location>\tools` umístění (například C:\tools\Android\android-sdk\tools) pro vaše `PATH` proměnné prostředí.
  * Otevřete Android SDK Manageru (například prostřednictvím terminálu: `android`) a nainstalujte:
    * *Android – 5.0.1 (rozhraní API 21)* SDK platformy
    * *Nástroje sestavení Android SDK* verze 19.1.0 nebo novější
    * *Podpora pro Android úložiště* (funkce)

  Sadu Android SDK neposkytuje žádné výchozí emulátor instance. Vytvořit spuštěním `android avd` z terminálu a potom vyberete **vytvořit**, pokud chcete spustit aplikaci pro Android na emulátor. Doporučujeme, abyste API úrovně 19 nebo vyšší. Další informace o možnosti Android emulátoru a vytvoření najdete v tématu [správce AVD](http://developer.android.com/tools/help/avd-manager.html) na webu Android.

## <a name="step-1-register-an-application-with-azure-ad"></a>Krok 1: Zaregistrujte aplikaci s Azure AD
Tento krok je volitelný. Tento kurz obsahuje předem zřízená hodnoty, které můžete použít k viz ukázka v akci bez provádění zřizování v vlastního klienta. Ale doporučujeme provést tento krok a seznámit se s proces, protože se bude vyžadovat, při vytváření vlastních aplikací.

Azure AD vydá tokeny pouze známé aplikací. Než z vaší aplikace můžete použít Azure AD, budete muset vytvořit položku pro něj ve vašem klientovi. Zaregistrujte novou aplikaci v klientovi:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na horním panelu klikněte na váš účet. V **Directory** vyberte klienta Azure AD, kam chcete registrace vaší aplikace.
3. Klikněte na tlačítko **všechny služby** v levém podokně a potom vyberte **Azure Active Directory**.
4. Klikněte na tlačítko **registrace aplikace**a potom vyberte **přidat**.
5. Postupujte podle výzev a vytvořte **nativní klientská aplikace**. (I když jsou aplikace Cordova na základě HTML, vytváříme nativní klientskou aplikaci. **Nativní klientská aplikace** musí být vybraná volba nebo aplikace nebude fungovat.)
  * **Název** popisuje vaší aplikace pro uživatele.
  * **Identifikátor URI pro přesměrování** je identifikátor URI, který se používá k vrácení tokeny do vaší aplikace. Zadejte **http://MyDirectorySearcherApp**.

Po dokončení registrace Azure AD jedinečný Identifikátor aplikace přiřadí vaší aplikace. Budete potřebovat tuto hodnotu v následujících částech. Můžete ji najít na kartě aplikace nově vytvořené aplikace.

Ke spuštění `DirSearchClient Sample`, udělení oprávnění nově vytvořené aplikace zpracovat dotaz rozhraní Azure AD Graph API:

1. Z **nastavení** vyberte **požadovaných oprávnění**a potom vyberte **přidat**. 
2. Pro aplikaci Azure Active Directory vyberte **Microsoft Graph** jako rozhraní API a přidejte **přístup k adresáři jako přihlášeného uživatele** oprávnění v rámci **delegovaná oprávnění**. To umožňuje vaše aplikace a dotaz rozhraní Graph API pro uživatele.

## <a name="step-2-clone-the-sample-app-repository"></a>Krok 2: Klonovat úložiště ukázkové aplikace
Z vašeho prostředí nebo na příkazovém řádku zadejte následující příkaz:

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="step-3-create-the-cordova-app"></a>Krok 3: Vytvoření aplikace Cordova
K vytvoření aplikace Cordova několika způsoby. V tomto kurzu použijeme Cordova rozhraní příkazového řádku (CLI).

1. Z vašeho prostředí nebo na příkazovém řádku zadejte následující příkaz:

        cordova create DirSearchClient

   Tento příkaz vytvoří strukturu složek a generování uživatelského rozhraní pro projekt Cordova.

2. Přesunout do nové složky DirSearchClient:

        cd .\DirSearchClient

3. Kopírovat obsah projektu starter v podsložce www pomocí Správce souborového nebo následující příkaz ve vašem prostředí:

  * Windows: `xcopy ..\NativeClient-MultiTarget-Cordova\DirSearchClient www /E /Y`
  * Mac: `cp -r  ../NativeClient-MultiTarget-Cordova/DirSearchClient/* www`

4. Přidáte modul plug-in seznamu povolených IP adres. To je nezbytné pro volání rozhraní Graph API.

        cordova plugin add cordova-plugin-whitelist

5. Přidejte všechny platformy, které chcete podporovat. Pokud chcete, aby pracovní vzorek, budete muset provést alespoň jeden z následujících příkazů. Všimněte si, že nebudete moci emulovat iOS v systému Windows nebo emulovat Windows v počítačích Mac.

        cordova platform add android
        cordova platform add ios
        cordova platform add windows

6. Do projektu přidejte knihovny ADAL pro modul plug-in Cordova:

        cordova plugin add cordova-plugin-ms-adal

## <a name="step-4-add-code-to-authenticate-users-and-obtain-tokens-from-azure-ad"></a>Krok 4: Přidejte kód, který ověřuje uživatele a získat tokeny z Azure AD
Aplikace, které vyvíjíte v tomto kurzu vám poskytne jednoduchý directory funkce vyhledávání. Uživatele můžete zadejte alias, všechny uživatele v adresáři a vizualizovat některé základní atributy. Projekt starter obsahuje definici základní uživatelské rozhraní aplikace (ve www/index.html) a generování uživatelského rozhraní, která sváže základní aplikaci událostí cyklů vazby uživatelské rozhraní a výsledky zobrazení logiku (v www/js/index.js). Úlohu pouze left můžete je přidat logiku, která implementuje identity úlohy.

První věcí, kterou je třeba provést v kódu je zavést protokol hodnoty, které používá Azure AD pro identifikaci vaší aplikace a prostředky, že cíl je. Tyto hodnoty se použije k vytvoření žádosti o tokeny později. V horní části souboru index.js vložte následující fragment kódu:

```javascript
var authority = "https://login.microsoftonline.com/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

`redirectUri` a `clientId` hodnoty by měla shodují s hodnotami, které popisují aplikace ve službě Azure AD. Můžete najít ty z **konfigurace** kartě na portálu Azure, jak je popsáno v kroku 1 v tomto kurzu.

> [!NOTE]
> Pokud jste se rozhodli pro novou aplikaci není registraci v vlastního klienta, můžete jednoduše vložit předkonfigurované hodnoty, protože je. Ukázka spuštěn, můžete se podívat, když měli vždycky vytvořit vlastní položku pro aplikace, které jsou určené pro produkční prostředí.

Dál přidejte kód žádosti o token. Vložte následující fragment kódu mezi `search` a `renderData` definice:

```javascript
    // Shows the user authentication dialog box if required
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Podle jeho rozdělení na dvě hlavní části Podívejme se na této funkce.
Tato ukázka je navržen pro práci s žádným klientem, a není svázán s některá. Použije "/ běžné" koncový bod, který umožňuje uživateli zadat libovolný účet během ověřování a přesměruje požadavek na klienta, kam patří:

Tato první část metoda zkontroluje ADAL mezipaměti zobrazíte, pokud je již uložen token. Pokud ano, používá metodu klienty odkud tokenu pochází pro provést novou inicializaci ADAL. To je nezbytné další výzev, protože použití z "/ běžné" vždy výsledkem s dotazem, aby uživatel zadal nový účet.

```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
Druhá část metoda provádí správné žádosti o token. `acquireTokenSilentAsync` ADAL se vraťte zpět token pro zadaného prostředku bez žádné UX zobrazující požádá – metoda Může dojít, pokud mezipaměť již obsahuje vhodný přístupový token, uložené, nebo pokud obnovovací token slouží k získání tokenu pro přístup k nové bez zobrazení všech řádku. Pokud se tento pokus selže, jsme vrátit zpět `acquireTokenAsync`– viditelně, který vyzve uživatele k ověření.

```javascript
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Teď, když máme token, jsme nakonec volání rozhraní Graph API a provádění vyhledávací dotaz, který má být. Vložte následující fragment kódu níže `authenticate` definice:

```javascript
// Makes an API call to receive the user list
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
Soubory od bodu zadat jednoduchý UX pro zadání uživatele alias v textovém poli. Tato metoda používá tuto hodnotu sestavte dotaz, ho spojovat se přístupový token, poslat Microsoft Graph a analyzovat výsledky. `renderData` Metoda, již existuje v souboru od bodu, má na starosti vizualizace výsledků.

## <a name="step-5-run-the-app"></a>Krok 5: Spuštění aplikace
Aplikace je nakonec připraven ke spuštění. Jeho provoz je jednoduchý: při spuštění aplikace, zadejte alias uživatele, kterou chcete vyhledat a potom klikněte na tlačítko. Se zobrazí výzva k ověření. Po úspěšném ověření a hledání úspěšné zobrazí se atributy vyhledávaná uživatele.

Při dalším spuštění provede vyhledávání bez zobrazení všech řádku díky přítomnosti dříve získal token v mezipaměti.

Konkrétní kroky pro spuštění aplikace se liší podle platformy.

### <a name="windows-10"></a>Windows 10
   Tabletu: `cordova run windows --archs=x64 -- --appx=uap`

   Mobilní zařízení (vyžaduje připojení k počítači zařízení Windows 10 Mobile): `cordova run windows --archs=arm -- --appx=uap --phone`

   > [!NOTE]
   > Při prvním spuštění může vyzváni k přihlášení pro vývojáře licenci. Další informace najdete v tématu [vývojáře licence](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-81-tabletpc"></a>Windows 8.1 Tabletu
   `cordova run windows`

   > [!NOTE]
   > Při prvním spuštění může vyzváni k přihlášení pro vývojáře licenci. Další informace najdete v tématu [vývojáře licence](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-phone-81"></a>Windows Phone 8.1
   Ke spuštění na připojeném zařízení: `cordova run windows --device -- --phone`

   Ke spuštění na výchozí emulátor: `cordova emulate windows -- --phone`

   Použití `cordova run windows --list -- --phone` zobrazíte všechny dostupné cíle a `cordova run windows --target=<target_name> -- --phone` ke spuštění aplikace na určité zařízení nebo emulátoru (například `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

### <a name="android"></a>Android
   Ke spuštění na připojeném zařízení: `cordova run android --device`

   Ke spuštění na výchozí emulátor: `cordova emulate android`

   Ujistěte se, že jste vytvořili instanci emulátoru pomocí Správce AVD, jak je popsáno výše v části "Požadavky".

   Použití `cordova run android --list` zobrazíte všechny dostupné cíle a `cordova run android --target=<target_name>` ke spuštění aplikace na určité zařízení nebo emulátoru (například `cordova run android --target="Nexus4_emulator"`).

### <a name="ios"></a>iOS
   Ke spuštění na připojeném zařízení: `cordova run ios --device`

   Ke spuštění na výchozí emulátor: `cordova emulate ios`

   > [!NOTE]
   > Zajistěte, aby byla `ios-sim` nainstalována pro spouštění v emulátoru balíčku. Další informace najdete v části "Požadavky".

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run the application on specific device or emulator (for example, `cordova run android --target="iPhone-6"`).

    Use `cordova run --help` to see additional build and run options.

## <a name="next-steps"></a>Další postup
Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) k dispozici v [Githubu](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient).

Nyní se můžete přesunout pokročilejší (a zajímavějšího) scénářů. Můžete chtít zkuste: [zabezpečit webové rozhraní Node.js API s Azure AD](active-directory-devquickstarts-webapi-nodejs.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
