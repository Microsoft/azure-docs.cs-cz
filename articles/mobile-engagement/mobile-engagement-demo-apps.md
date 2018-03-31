---
title: Ukázkové aplikace Azure Mobile Engagement | Microsoft Docs
description: Popisuje, kde lze stáhnout, jak používat a výhody použití ukázkovou aplikaci Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: f624d5aa-254b-4ad0-96a3-f00e6c3a2c97
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2016
ms.author: piyushjo
ms.openlocfilehash: b74afcadc52973a4d824afddc0abff6ac9f71d17
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-demo-app"></a>Azure Mobile Engagement ukázkovou aplikaci
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Publikování ukázkové aplikace Azure Mobile Engagement pro **iOS**, **Android**, a **Windows** platformy můžete najít užitečné zdroje a další informace o Mobile Engagement.

Aplikace vám pomůže:

* Snadno najít užitečné odkazy na zdroje Mobile Engagement, jako jsou videa, dokumentace, fórum podpory a kde lze zvýšit žádosti o funkce.
* Zaznamenat ukázka oznámení, které podporuje Mobile Engagement, jak získat návrhy pro vaše vlastní mobilní aplikace.
* Použijte odkaz na implementaci pro zkoumání implementaci Mobile Engagementu do vlastní aplikace. Informace najdete na:
  
  * Shromažďování dat analytics.
  * Implementace pokročilé scénáře oznámení typů jako *přes celou obrazovku vkládaná* nebo *automaticky otevírané okno*.
  * Implementovat průzkumy a hlasování.
  * Implementujte scénáře tichou nabízené dat a posílejte nabízená oznámení.   

## <a name="app-installation"></a>Instalace aplikace
Tato aplikace je k dispozici v následujících obchody s aplikacemi:

* **Univerzální pro Windows ukázkovou aplikaci**:
  
  * Stáhnout aplikaci v [aplikace pro Windows store](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2).
  * Aplikace byla vyvinutá jako Windows 10 univerzální aplikace. Zdrojový kód je k dispozici na [Githubu](https://github.com/Azure/azure-mobile-engagement-app-windows).
* **iOS ukázku aplikace**:
  
  * Stáhnout aplikaci v [Apple storu](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090).
  * Aplikace byla vyvinutá v iOS Swift. Zdrojový kód je k dispozici na [Githubu](https://github.com/Azure/azure-mobile-engagement-app-ios).
* **Android ukázkovou aplikaci**:
  
  * Stáhnout aplikaci v [obchodu Google Play](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement).
  * Zdrojový kód je k dispozici na [Githubu](https://github.com/Azure/azure-mobile-engagement-app-android).

![Univerzální pro Windows ukázkovou aplikaci][1]

![Ukázka aplikace iOS][2]
![Android ukázkovou aplikaci][3]

## <a name="usage"></a>Využití
Tuto aplikaci můžete použít následujícími způsoby:

**Stažení aplikace na zařízení z aplikace ukládání odkazů (dříve uvedených):**

> [!IMPORTANT]
> Nepotřebujete účtu Azure nebo nutnosti připojení aplikace k back-end. Aplikace funguje nezávisle.
> 
> 

* Když máte aplikaci na vašem zařízení, potom můžete přejít prostřednictvím odkazů v levé nabídce najít užitečné zdroje informací o Mobile Engagement.
* Přidali jsme [informačního kanálu RSS služby](https://aka.ms/azmerssfeed) do této aplikace tak, aby se vždy aktualizované o nejnovější aktualizace produktu.
* Můžete také přejít pomocí ukázkových scénářů oznámení do prostředí typ oznámení, které jsou podporovány Mobile Engagementu pro každou platformu. Tato oznámení mohou být došlo místně – tzn., můžete kliknout na tlačítka na obrazovkách tak, aby zobrazovalo prostředí oznámení, který je stejný jako při odesílání oznámení z platformy Mobile Engagement.

![Nabídky aplikace pro Windows][4]

![Nabídky aplikace pro iOS][5]
![nabídky aplikace pro Android][6]

**Zdrojový kód stáhněte z Githubu odkazů (dříve uvedených):**

* Po stažení zdrojový kód, otevřete ho v příslušných vývojového prostředí – XCode pro iOS, Android Studio pro Android a Visual Studio pro Windows.
* Potom postupujte podle našich [základní kroky pro integraci sady SDK](mobile-engagement-windows-store-dotnet-get-started.md) tak, že jste tuto aplikaci připojit k vlastní instanci back-end Mobile Engagement.
  * Budete muset nakonfigurovat připojovací řetězec v aplikaci.
  * Musíte také nakonfigurovat nabízená oznámení pro vaši aplikaci.
* Můžete si všimnout, že aplikace je instrumentovány s Mobile Engagement. Proto jako otevřete aplikaci po připojení k back-end, budete moci zobrazit uživatelské relace, aktivity, události a tak dále, na **monitorování** kartě.
* Také budete moci odesílat oznámení do této aplikace z vlastního instance Mobile Engagement, místo použití místního oznámení.
  
  * Sem můžete přidat zařízení jako testovací zařízení pomocí **získat ID zařízení** položky nabídky v aplikaci. To vám dává ID zařízení, která pak zaregistrovat jako testovací zařízení s vaší instancí back-end platformy.
    
    ![ID zařízení v systému Windows][7]
    
    ![ID zařízení v systému iOS][8]
    ![ID zařízení v systému Android][9]

## <a name="key-features-of-the-demo-app"></a>Klíčové funkce ukázkovou aplikaci
* Jak už bylo zmíněno dříve, s touto aplikací mít ve vaší ruky všechny klíče prostředky pro Mobile Engagement. Můžete přejít prostřednictvím odkazů v levé nabídce.
* Oznámení se na aplikace pro každou platformu můžete zaznamenat. Tato oznámení mohou být zajišťovány jako **pouze oznámení**, kde kliknutím na oznámení jednoduše otevře nativní obrazovky aplikace (pomocí **přímé propojení**)--nebo jako **sdělení webovém**, kde může poskytovat další obsah HTML z části Mobile Engagement back end, který se má zobrazit při kliknutí na oznámení.
  
    ![Oznámení se na aplikace][29]
* V systému iOS budete muset zavřete aplikaci najdete v části nabízená oznámení se na aplikace nebo systému. Můžete se podívat na implementaci zde pro přidání **tlačítka akce**, jako jsou ty, které jsou přidány do tohoto oznámení se na aplikace pro *zpětné vazby* a *sdílenou složku* (tak, aby uživatel může trvat akce přímo z vlastního oznámení).
  
    ![Oznámení se na aplikace v systému iOS][11] ![Zobrazení oznámení se na aplikace v systému iOS][14]
* Možnosti, které jsou podporovány v systému Android, přidáváte víceřádkový text (**dlouhý Text**) nebo bitovou kopii oznámení (**velký obrázek**) na oznámení, spolu s **tlačítka akce** (jak podporuje iOS).
  
    ![Oznámení se na aplikace v systému Android][12] ![Zobrazení oznámení se na aplikace v systému Android][15]
* Ve Windows 10 uvidíte, jak oznámení vypadá na počítač. Toto oznámení se také objeví ve Windows 10 **centru oznámení**. Neexistuje žádná podpora pro přidání **tlačítka akce** v tuto chvíli v sadě Windows SDK.
  
    ![Oznámení se na aplikace v systému Windows][10] ![Zobrazení se na aplikace v systému Windows][13]
* Oznámení "v aplikaci" výchozí pro každou platformu můžete zaznamenat. Toto je prostředí dvoustupňové kde **oznámení** okna je zobrazen jako první. Když kliknete na tlačítko ji, otevře se nahoru na celé obrazovce **oznámení**, jak je uvedeno v následujícím snímku obrazovky. Obsah tohoto oznámení pochází z vaší instance back-end Mobile Engagement. Sada SDK obsahuje šablony pro oznámení a oznámení. Můžete snadno přizpůsobit, jak je znázorněno v této ukázkové aplikaci a uveďte naše logo a barevné zvýrazňování.  
  
    ![Oznámení v aplikaci v systému Windows][16]
  
    ![Oznámení v aplikaci v iOS][17]  ![Oznámení v aplikaci pro Android][18]
  
    **iOS**, **Android**
* Můžete také **kategorie** funkce Mobile Engagement k přizpůsobení toto výchozí chování. V ukázkovou aplikaci jsme jste ukázán dvě běžné způsoby, jak změnit možnosti oznámení. Všimněte si, že funkce kategorie není dosud podporována ve Windows SDK.
  
    **Přes celou obrazovku vkládaná:**
  
    ![Oznámení v aplikaci - vkládaná kategorie][30]
  
    ![Vkládaná kategorie v systému iOS][21]     ![Vkládaná kategorie v systému Android][22]
  
    **Automaticky otevírané okno oznámení:**
  
    ![Oznámení v aplikaci - automaticky otevírané okno kategorie][31]
  
    ![Automaticky otevírané okno oznámení v iOS][19]    ![Automaticky otevírané okno oznámení v systému Android][20]

**iOS**, **Android**

* Mobile Engagement také podporuje speciálním typem oznámení v aplikaci s názvem **hlasování**. Můžete k odeslání rychlé průzkumy uživatelům segmentovaným aplikace. Můžete přidat otázky a možnosti pro každou otázku stejně jako na následujícím snímku obrazovky. To pak nezobrazí jako oznámení v aplikaci pro uživatele aplikace.   
  
    ![Dotazování oznámení][32]
  
    ![Průzkum v systému Windows][26]
  
    ![Nástroj na navrhování průzkumů v systému iOS][27]   ![Průzkum v systému Android][28]

**iOS**, **Android**

* Mobile Engagement také podporuje odesílání tichou **Data Push** oznámení. Pomocí těchto oznámení může odesílat data ze služby (např. data JSON v následujícím příkladu), které lze zpracovat ve vaší aplikaci a určitou akci. Příkladem je, jak jsme se mění ceny položky selektivně pomocí dat nabízená oznámení.
  
    ![Data nabízená oznámení][33]
  
    ![Data nabízených oznámení v systému Windows][23]
  
    ![Data nabízených oznámení v iOS][24]  ![Data nabízených oznámení v systému Android][25]

**iOS**, **Android**

> [!NOTE]
> Podrobné pokyny pro některý z těchto oznámení můžete zobrazit kliknutím **klepněte sem a pokyny, jak chcete tahle oznámení odeslat z platformy Mobile Engagement** na jakékoli obrazovce ukázka oznámení.
> 
> 

[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
