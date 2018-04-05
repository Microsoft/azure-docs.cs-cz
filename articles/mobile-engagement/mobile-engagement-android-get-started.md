---
title: Začínáme s Android Apps Azure Mobile Engagementem
description: Naučte se používat Azure Mobile Engagement s analýzou a nabízenými oznámeními pro aplikace pro Android.
services: mobile-engagement
documentationcenter: android
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3c286c6d-cfef-4e3e-9b2c-715429fe82db
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 4eda8129e03e4880e168f7c154c7236da028e81e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>Začínáme s Azure Mobile Engagementem pro aplikace pro Android
> [!IMPORTANT]
> Azure Mobile Engagement se vyřadí z provozu 31. 3. 2018. Tato stránka bude odstraněna krátce potom.
> 


[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

V tomto tématu si ukážeme, jak používat Azure Mobile Engagement, jak porozumět používání aplikací a odesílat nabízená oznámení segmentovaným uživatelům aplikace pro Android.
Tento kurz představuje scénář jednoduchého vysílání přes Mobile Engagement. V něm můžete vytvořit prázdnou aplikaci pro Android, která sbírá základní data a dostává nabízená oznámení pomocí služby GCM (Google Cloud Messaging).

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto kurzu budete potřebovat [vývojářské nástroje pro Android](https://developer.android.com/sdk/index.html), které zahrnují integrované vývojové prostředí Android Studio a nejnovější platformu Android.

Také budete potřebovat sadu [Mobile Engagement Android SDK](https://aka.ms/vq9mfn).

> [!IMPORTANT]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).
>
>

## <a name="set-up-mobile-engagement-for-your-android-app"></a>Nastavení Mobile Engagementu pro vaši aplikaci pro Android
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Připojení aplikace k back-endu Mobile Engagementu
V tomto kurzu si představíme „základní integraci“, čili minimální sadu požadovanou pro shromažďování dat a odesílání nabízených oznámení. Pomocí Android Studia vytvoříte základní aplikaci, na které si tuto integraci předvedeme.

Kompletní dokumentaci k integraci najdete v článku [Integrace sady Mobile Engagement Android SDK](mobile-engagement-android-sdk-overview.md).

### <a name="create-an-android-project"></a>Vytvoření projektu Android
1. Spusťte **Android Studio** a v místní nabídce vyberte **Start a new Android Studio project** (Začít nový projekt Android Studio).

    ![][1]
2. Zadejte název aplikace a doménu firmy. Poznamenejte si, co vyplňujete, protože budete tyto údaje potřebovat později. Klikněte na **Další**.

    ![][2]
3. Zvolte cílový typ zařízení a úroveň API a klikněte na**Next**.

   > [!NOTE]
   > Mobile Engagement vyžaduje API minimálně úrovně 10 (Android 2.3.3).
   >
   >

    ![][3]
4. Zde vyberte **Blank Activity** (Prázdná aktivita), která je jedinou obrazovkou pro tuto aplikaci, a klikněte na **Next**.

    ![][4]
5. Na závěr ponechte výchozí nastavení a klikněte na**Finish** (Dokončit).

    ![][5]

Android Studio nyní vytvoří ukázkovou aplikaci, do které integrujeme Mobile Engagement.

### <a name="include-the-sdk-library-in-your-project"></a>Zahrnutí knihovny sady SDK do projektu
1. Stáhněte sadu [Mobile Engagement Android SDK](https://aka.ms/vq9mfn).
2. Extrahujte soubor archivu do složky v počítači.
3. Identifikujte knihovnu .jar pro aktuální verzi této sady SDK a zkopírujte ji do schránky.

      ![][6]
4. Přejděte do sekce **Project** (Projekt) (1) a vložte .sloubor jar do složky libs (2).

      ![][7]
5. Pro načtení knihovny synchronizujte projekt.

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>Připojení aplikace k back-endu Mobile Engagementu pomocí připojovacího řetězce
1. Zkopírujte následující řádky kódu do vytváření aktivity (je třeba provést pouze na jednom místě aplikace, obvykle v hlavní aktivitě). V případě této ukázkové aplikace otevřete MainActivity pod složkou src -> main -> java a přidejte následující:

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);
2. Vyřešte reference stisknutím Alt+Enter nebo přidáním následujících příkazů pro import:

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;
3. Přejděte zpět na Azure Portal na stránce **Connection Info** (Informace o připojení) vaší aplikace a zkopírujte **připojovací řetězec**.

      ![](../../includes/media/mobile-engagement-create-app-in-portal-new/app-connection-info.png)

4. Vložte jej do parametru `setConnectionString` a nahraďte celý zobrazený řetězec následujícím kódem:

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>Přidání oprávnění a deklarace služby
1. Přidejte oprávnění do souboru Manifest.xml vašeho projektu těsně před značku `<application>` nebo za ni.

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
2. Chcete-li deklarovat službu agenta, přidejte tento kód mezi značky `<application>` a `</application>`:

        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
3. Ve vloženém kódu nahraďte `"<Your application name>"` v popisku, který se zobrazí v nabídce **Nastavení**, kde můžete zobrazit služeb spuštěné v zařízení. Do tohoto popisku můžete například přidat slovo „Service“.

### <a name="send-a-screen-to-mobile-engagement"></a>Odeslání obrazovky do Mobile Engagementu
Pokud chcete začít odesílat data a zajistit, že uživatelé jsou aktivní, musíte odeslat alespoň jednu obrazovku (aktivitu) na back-end Mobile Engagementu.

Přejděte na **MainActivity.java** a přidejte následující kód, kterým nahradíte základní třídu **MainActivity** třídou **EngagementActivity**:

    public class MainActivity extends EngagementActivity {

> [!NOTE]
> Pokud vaše základní třída není *Activity*, podívejte se do [Rozšířených možností hlášení pro Android](mobile-engagement-android-advanced-reporting.md) na postupy, jak dědit z různých tříd.
>
>

Okomentujte následující řádek pro tento jednoduchý vzorový scénář:

    // setSupportActionBar(toolbar);

Pokud chcete zachovat `ActionBar` ve vaší aplikaci, zobrazte si část [Rozšířená sestava Android](mobile-engagement-android-advanced-reporting.md).

## <a name="connect-app-with-real-time-monitoring"></a>Připojení aplikace se sledováním v reálném čase
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>Povolení nabízených oznámení a zasílání zpráv v aplikaci
Během kampaně vám Mobile Engagement umožňuje interagovat a KOMUNIKOVAT s uživateli pomocí nabízených oznámení a zpráv v aplikaci. Tento modul se na portálu Mobile Engagement nazývá REACH.
V následujících sekcích nastavíte aplikaci, aby tato nabízená oznámení a zprávy přijímala.

### <a name="copy-sdk-resources-in-your-project"></a>Kopírování prostředků sady SDK do projektu
1. Přejděte zpět na obsah stažené sady SDK a zkopírujte složku **res**.

    ![][10]
2. Přejděte zpět na Android Studio, vyberte adresář **main** projektu a vložte do něj zkopírovanou složku res, abyste prostředky přidali do projektu.

    ![][11]

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>Další kroky
Podrobné informace o integraci sady SDK najdete v článku o integraci sady [Android SDK](mobile-engagement-android-sdk-overview.md).

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png
