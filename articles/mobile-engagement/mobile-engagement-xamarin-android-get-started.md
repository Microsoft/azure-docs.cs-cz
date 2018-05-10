---
title: Začínáme s Azure Mobile Engagementem pro Xamarin.Android
description: Naučte se používat Azure Mobile Engagement s analytickými funkcemi a nabízenými oznámeními pro aplikace pro Xamarin.Android.
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: ''
ms.assetid: fb68cf98-08a2-41b5-8e59-757469de3fe7
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/16/2016
ms.author: piyushjo
ms.openlocfilehash: e8635cb057b579f10575cdaf846af9fa604b7993
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>Začínáme s Azure Mobile Engagementem pro aplikace pro Xamarin.Android
> [!IMPORTANT]
> Azure Mobile Engagement se vyřadí z provozu 31. 3. 2018. Tato stránka bude odstraněna krátce potom.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

V tomto tématu si ukážeme, jak používat Azure Mobile Engagement, jak porozumět používání aplikací a odesílat nabízená oznámení segmentovaným uživatelům aplikace pro Xamarin.Android.
Tento kurz představuje scénář jednoduchého vysílání přes Mobile Engagement. V něm můžete vytvořit prázdnou aplikaci pro Xamarin.Android, která sbírá základní data a dostává nabízená oznámení pomocí služby GCM (Google Cloud Messaging).

> [!NOTE]
> Službu Azure Mobile Engagement vyřadíme z provozu v březnu 2018. V současnosti je dostupná jenom pro stávající zákazníky. Další informace najdete v tématu [Mobile Engagement](https://azure.microsoft.com/services/mobile-engagement/).

V tomto kurzu budete potřebovat následující:

* [Xamarin Studio](http://xamarin.com/studio). Můžete také použít Visual Studio s Xamarinem, ale v tomto kurzu používáme Xamarin Studio. Instalační pokyny najdete v tématu o [nastavení a instalaci pro Visual Studio a Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
* [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).
> 
> 

## <a id="setup-azme"></a>Nastavení Mobile Engagementu pro vaši aplikaci pro Android
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Připojení aplikace k back-endu Mobile Engagementu
V tomto kurzu si představíme „základní integraci“, čili minimální sadu požadovanou pro shromažďování dat a odesílání nabízených oznámení. 

Pomocí Xamarin Studia si vytvoříme základní aplikaci, na které si tuto integraci předvedeme.

### <a name="create-a-new-xamarinandroid-project"></a>Vytvoření nového projektu Xamarin.Android
1. Spusťte **Xamarin Studio** a klikněte na položky **File** (Soubor)  -> **New** (Nové)  -> **Solution** (Řešení). 
   
    ![][1]
2. Vyberte **Android App** (Aplikace pro Android), zkontrolujte, zda je vybraný jazyk **C#** a klikněte na tlačítko **Next** (Další).
   
    ![][2]
3. Vyplňte pole **App Name** (Název aplikace) a **Organization Identifier** (Identifikátor organizace). Zaškrtněte **Google Play Services** (Služby Google Play) a klikněte na tlačítko **Next** (Další). 
   
    ![][3]
4. Podle potřeby aktualizujte pole **Project Name** (Název projektu), **Solution Name** (Název řešení) a **Location** (Umístění) a klikněte na **Create** (Vytvořit).
   
    ![][4]

Xamarin Studio vytvoří aplikaci, do které budeme integrovat Mobile Engagement. 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Připojení aplikace k back-endu Mobile Engagementu
1. Pravým tlačítkem myši klikněte v oknech řešení na složku **Packages** (Balíčky) a vyberte **Add Packages...** (Přidat balíčky...).
   
    ![][5]
2. Vyhledejte **Microsoft Azure Mobile Engagement Xamarin SDK** a přidejte jej do řešení.  
   
    ![][6]
3. Otevřete soubor **MainActivity.cs** a pomocí příkazů přidejte následující:
   
        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;
4. V metodě `OnCreate` přidejte následující a inicializujte připojení s back-endem Mobile Engagementu. Nezapomeňte přidat řetězec **ConnectionString**. 
   
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

### <a name="add-permissions-and-a-service-declaration"></a>Přidání oprávnění a deklarace služby
1. Ve složce Properties (Vlastnosti) otevřete soubor **Manifest.xml**. Vyberte kartu Source (Zdroj), na které můžete přímo aktualizovat zdroj dat XML.
2. Přidejte oprávnění do souboru Manifest.xml (který najdete ve složce **Properties** (Vlastnosti)) vašeho projektu těsně před značku `<application>` nebo za ni.
   
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
3. Pokud chcete deklarovat službu agenta, přidejte mezi značky `<application>` a `</application>` následující:
   
        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
4. U kódu, který jste vložili, nahraďte `"<Your application name>"` v atributu label. Tento text se bude zobrazovat v nabídce **Settings** (Nastavení), kde uživatelé vidí, jaké služby mají na zařízení zrovna spuštěné. Do tohoto popisku můžete například přidat slovo „Service“.

### <a name="send-a-screen-to-mobile-engagement"></a>Odeslání obrazovky do Mobile Engagementu
Pokud chcete začít odesílat data a zajistit, že uživatelé jsou aktivní, musíte odeslat alespoň jednu obrazovku na back-end Mobile Engagementu. Pokud to chcete provést, zajistěte, aby se metoda `MainActivity` převzala z `EngagementActivity` namísto `Activity`.

    public class MainActivity : EngagementActivity

Pokud `EngagementActivity` neumožňuje převzetí, musíte do `OnResume` a `OnPause` přidat metody `.StartActivity` a `.EndActivity`.  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }

            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

## <a id="monitor"></a>Připojení aplikace se sledováním v reálném čase
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Povolení nabízených oznámení a zasílání zpráv v aplikaci
Mobile Engagement vám umožňuje v rámci kampaní oslovit uživatele a komunikovat s nimi prostřednictvím nabízených oznámení a zpráv v aplikacích. Tento modul se na portálu Mobile Engagement nazývá REACH.
V následujících sekcích nastavíte aplikaci, aby tato nabízená oznámení a zprávy přijímala.

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png
