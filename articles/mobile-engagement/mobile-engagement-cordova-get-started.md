---
title: Začínáme s Azure Mobile Engagementem pro Cordovu/Phonegap
description: Naučte se používat Azure Mobile Engagement s analýzou a nabízenými oznámením pro aplikace Cordova/Phonegap.
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 54fe9113-e239-4ed7-9fd1-a502d7ac7f47
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-phonegap
ms.devlang: js
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: abe0ad6117cc2feb72ebe788d07b50e8f145174c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Začínáme s Azure Mobile Engagementem pro Cordovu/Phonegap
> [!IMPORTANT]
> Azure Mobile Engagement se vyřadí z provozu 31. 3. 2018. Tato stránka bude odstraněna krátce potom.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

V tomto tématu si ukážeme, jak používat Azure Mobile Engagement k porozumění tomu, jak je vaše aplikace používána, a jak odesílat nabízená oznámení segmentovaným uživatelům mobilní aplikace vyvinuté pomocí Cordovy.

V tomto kurzu si pomocí Macu vytvoříme prázdnou aplikaci Cordova a pak do ní integrujeme sadu SDK Mobile Engagement. Ta shromažďuje základní analytická data a přijímá nabízená oznámení přes APNS ( Apple Push Notification System) pro iOS nebo GCM (Google Cloud Messaging) pro Android. Aplikaci pak nasadíme do zařízení IOS nebo Android pro testování. 

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).
> 
> 

V tomto kurzu budete potřebovat následující:

* XCode, který si můžete nainstalovat z Mac App Storu (pro nasazení na iOS)
* [Sadu SDK a emulátor systému Android](http://developer.android.com/sdk/installing/index.html) (pro nasazení na Android)
* Certifikát nabízených oznámení (.p12), který můžete získat ve vývojářském centru Apple, pro APNS
* Číslo GCM projektu, které můžete získat z Vývojářské konzole Google, pro GCM
* [Plugin Mobile Engagement pro Cordovu](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [!NOTE]
> Zdrojový kód a soubor ReadMe pluginu pro Cordovu najdete na [GitHubu](https://github.com/Azure/azure-mobile-engagement-cordova)
> 
> 

## <a id="setup-azme"></a>Nastavení Mobile Engagementu pro aplikaci Cordova
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Připojení aplikace k back-endu Mobile Engagementu
V tomto kurzu si představíme „základní integraci“, čili minimální sadu, která je zapotřebí pro shromažďování dat a odesílání nabízených oznámení. 

Pomocí Cordovy si vytvoříme základní aplikaci, na které si tuto integraci předvedeme.

### <a name="create-a-new-cordova-project"></a>Vytvoření nového projektu Cordova
1. Na počítači Mac spusťte *terminálové* okno a zadejte následující příkazy, které vytvoří nový projekt Cordova na základě výchozí šablony. Zkontrolujte, zda profil publikování, který nakonec použijete k nasazení své aplikace pro iOS, používá jako ID aplikace "com.mycompany.myapp". 
   
        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova
2. Spuštěním následujících příkazů nakonfigurujete svůj projekt pro **iOS** a spustíte ho v simulátoru iOS:
   
        $ cordova platform add ios 
        $ cordova run ios
3. Spuštěním následujících příkazů nakonfigurujete svůj projekt pro **Android** a spustíte ho v emulátoru Android. Ověřte si, zda má nastavení emulátoru sady SDK pro Android nastaven cíl jako rozhraní Google API (Google Inc.) s CPU/ABI jako Google APIs ARM.  
   
        $ cordova platform add android
        $ cordova run android
4. Přidejte plugin Cordova Console. 

    ```
    $ cordova plugin add cordova-plugin-console
    ``` 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Připojení aplikace k back-endu Mobile Engagementu
1. Nainstalujte plugin Azure Mobile Engagement pro Cordovu a přitom zadejte hodnoty proměnných pro jeho konfiguraci.
   
        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
             --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Ikona Reach pro Android*: Musí být název prostředku bez žádné přípony ani předpony (např. mynotificationicon). Soubor ikony musí být zkopírován do vašeho projektu Android (platforms/android/res/drawable)

*Ikona Reach pro iOS*: Musí být název prostředku včetně přípony (např. mojeikonanotifikace.png). Soubor ikony musí být přidán do projektu iOS s XCode (přes nabídku pro přidání souborů).

## <a id="monitor"></a>Povolení sledování v reálném čase
1. V projektu Cordova upravte soubor  **www/js/index.js** a přidejte volání Mobile Engagementu, aby byla deklarována nová aktivita při přijetí události *deviceReady*.
   
         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }
2. Spusťte aplikaci:
   
   * **Pro iOS**
     
       V okně `Terminal` spusťte aplikaci v nové instanci simulátoru spuštěním následujícího příkazu:
     
           cordova run ios
   * **Pro Android**
     
       V okně `Terminal` spusťte aplikaci v nové instanci emulátoru spuštěním následujícího příkazu:
     
           cordova run android
3. V protokolech konzoly uvidíte toto:
   
        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

## <a id="monitor"></a>Připojení aplikace se sledováním v reálném čase
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Povolení nabízených oznámení a zasílání zpráv v aplikaci
Mobile Engagement vám umožňuje v rámci kampaní komunikovat s uživateli přes nabízená oznámení a zprávy v aplikacích. Tento modul se na portálu Mobile Engagement nazývá REACH.
V následujících sekcích nastavíte aplikaci, aby tato nabízená oznámení a zprávy přijímala.

### <a name="configure-push-credentials-for-mobile-engagement"></a>Konfigurace přihlašovacích údajů nabízených oznámení pro Mobile Engagement
Pokud chcete povolit Mobile Engagementu odesílat vaším jménem nabízená oznámení, musíte mu udělit přístup k certifikátu iOS(Apple) nebo klíči rozhraní API serveru GCM. 

1. Přejděte na portál Mobile Engagement. Zkontrolujte, zda pracujete v aplikaci určené pro tento projekt, a potom klikněte na tlačítko **Zpřístupnit**, které najdete dole:
   
    ![][1]
2. Tím přejdete na stránku s nastavením portálu Engagement. Zde klikněte na sekci **Nativní oznámení**:
   
    ![][2]
3. Konfigurace certifikátu iOS / klíče rozhraní API serveru GCM
   
    **[iOS]**
   
    a. Vyberte svůj soubor .p12, nahrajte ho a zadejte heslo:
   
    ![][3]
   
    **[Android]**
   
    a. V části Nastavení GCM klikněte na ikonu úprav **klíče rozhraní API**. Zobrazí se automaticky otevírané okno. Do toho vložte klíč serveru GCM a klikněte na **OK**. 
   
    ![][4]

### <a name="enable-push-notifications-in-the-cordova-app"></a>Povolení nabízených oznámení v aplikaci Cordova
Upravte soubor **www/js/index.js** a přidejte volání Mobile Engagement, které žádá o nabízená oznámení a deklaruje obslužnou rutinu.

     onDeviceReady: function() {
           Engagement.initializeReach(  
                 // on OpenUrl  
                 function(_url) {   
                 alert(_url);   
                 });  
            Engagement.startActivity("myPage",{});  
        }

### <a name="run-the-app"></a>Spuštění aplikace
**[iOS]**

1. Použijeme XCode a vytvoříme a nasadíme aplikaci na zařízení, abychom si otestovali nabízená oznámení, protože iOS povoluje nabízená oznámení pouze pro skutečné zařízení. Přejděte do umístění, kde máte vytvořený projekt Cordova, a pokračujte na **...\platforms\ios**. V XCode otevřete nativní soubor .xcodeproj. 
2. Vytvořte a nasaďte aplikaci Cordova na zařízení iOS. Použijte k tomu účet obsahující profil pro zřizování s certifikátem, který jste právě nahráli na portál Mobile Engagement, a identifikátor aplikace odpovídající tomu, který jste zadali při vytváření aplikace Cordova. Pokud potřebujete identifikátory sjednotit, zkontrolujte *Bundle identifier* v souboru **Resources\*-info.plist** v XCode. 
3. Na vašem zařízení se zobrazí standardní místní nabídka iOS se zprávou, že aplikace vyžaduje povolení k odesílání upozornění. Udělte oprávnění. 

**[Android]**

Aplikaci pro Android můžete jednoduše spustit přes emulátor, protože emulátor Android podporuje oznámení GCM. 

    cordova run android

## <a id="send"></a>Odeslání oznámení do vaší aplikace
Nyní vytvoříme jednoduchou kampaň nabízených oznámení. Ta bude odesílat oznámení do vaší aplikace, která je na zařízení právě spuštěna:

1. Na portálu Mobile Engagement přejděte na kartu **Reach**
2. Kliknutím na **Nové oznámení** vytvořte kampaň nabízených oznámení
   
    ![][6]
3. Zadejte vstupy pro vytvoření kampaně **[Android]**
   
   * Zadejte **Název** kampaně. 
   * Vyberte **Typ doručení** –nastavte *Oznámení systému* *Jednoduché*
   * Vyberte **Čas doručení** – nastavte na *„Kdykoliv“*
   * Zadejte **Nadpis** oznámení, který se bude nacházet v prvním řádku nabízeného oznámení.
   * Vyplňte pole **Zpráva** pro oznámení (hlavní text zprávy). 
     
     ![][11]
4. Zadejte vstupy pro vytvoření kampaně **[iOS]**
   
   * Zadejte **Název** kampaně. 
   * Vyberte **Čas doručení** – nastavte *„Pouze mimo aplikaci“*
   * Zadejte **Nadpis** oznámení, který se bude nacházet v prvním řádku nabízeného oznámení.
   * Vyplňte pole **Zpráva** pro oznámení (hlavní text zprávy). 
     
     ![][12]
5. Přejděte dolů a v části obsahu vyberte **Pouze oznámení**
   
    ![][8]
6. [Nepovinné] Můžete také zadat adresu URL akce. Ujistěte se, že používá stejné schéma URL, jaké jste zadali při konfiguraci proměnné **AZME\_REDIRECT\_URL** pluginu, např *myapp://test*.  
7. Tím jste dokončili nastavení nejzákladnější možné kampaně. Nyní znovu přejděte dolů a kliknutím na tlačítko **Vytvořit** kampaň uložte.
8. Nakonec svou kampaň můžete **Aktivovat**
   
    ![][10]
9. Nyní byste měli na svém zařízení nebo emulátoru vidět nabízené oznámení z této kampaně. 

## <a id="next-steps"></a>Další kroky
[Přehled všech metod, které jsou k dispozici pro sadu Cordova Mobile Engagement SDK](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

