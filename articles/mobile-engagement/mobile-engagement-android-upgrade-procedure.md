---
title: Integraci sady Azure Mobile Engagement Android SDK
description: Nejnovější aktualizace a postupy pro Android SDK pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 11618586-c709-49ca-bcd8-745323ff1af6
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 77047cb1dc39fa3c05f58550ceea74e78396157f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="upgrade-procedures"></a>Postupy upgradu
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Pokud již máte integrovanou starší verze naše sady SDK do své aplikace, je nutné zvážit následující body při upgradu sady SDK.

Možná budete muset několik postupy použijte, pokud provedena několik verzí sady SDK. Například pokud migrujete z 1.4.0 1.6.0 budete muset nejdřív postupujte podle pokynů "od 1.4.0 k 1.5.0" pak postupu "od 1.5.0 k 1.6.0".

Bez ohledu na verzi upgradujete, budete muset nahradit `mobile-engagement-VERSION.jar` tímto novým připojením.

## <a name="from-420-to-421"></a>Z 4.2.0 k 4.2.1
Tento krok lze provést ve skutečnosti na všechny verze sady SDK, je zvýšení zabezpečení při integraci Reach aktivity.

Nyní byste měli přidat `exported="false"` pro všechny aktivity Reach.

Reach aktivity by teď měl vypadat takto vaše `AndroidManifest.xml`:

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

## <a name="from-400-to-410"></a>Z 4.0.0 k 4.1.0
SDK nyní popisovač nové oprávnění modelu ze systému Android M.

Pokud používáte umístění funkcí nebo oznámení velký obrázek přečtěte [v této části](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

Kromě nový model oprávnění teď podporujeme konfiguraci funkce umístění za běhu.
Snažíme se stále kompatibilní s manifestu parametry pro umístění, ale je nyní zastaralý. Chcete-li použít konfigurace modulu runtime, odeberte v následujících částech z vaší ``AndroidManifest.xml``:

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

a číst [Tato aktualizuje postup](mobile-engagement-android-integrate-engagement.md#location-reporting) místo toho použít konfigurace modulu runtime.

## <a name="from-300-to-400"></a>Z 3.0.0 k 4.0.0
### <a name="native-push"></a>Nativního nabízení
Nativního nabízení (GCM/ADM) se teď také používá pro oznámení v aplikaci, musíte nakonfigurovat přihlašovací údaje nativního nabízení pro jakýkoli typ nabízené kampaně.

Není-li již postupujte [tento postup](mobile-engagement-android-integrate-engagement-reach.md#native-push).

### <a name="androidmanifestxml"></a>AndroidManifest.xml
Integrace reach byl změněn v ``AndroidManifest.xml``.

Nahraďte toto:

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

Od společnosti

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

Je to pravděpodobně načítání obrazovky teď kliknutím na oznámení (s text nebo webového obsahu) nebo hlasování.
Je nutné přidat tato funkce má u těchto kampaně pro práci v 4.0.0:

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### <a name="resources"></a>Zdroje a prostředky
Vložení nové `res/layout/engagement_loading.xml` souboru do projektu.

## <a name="from-240-to-300"></a>Z 2.4.0 k 3.0.0
Následující část popisuje postup migrace integraci sady SDK z Capptain služby, které do aplikace používá technologii Azure Mobile Engagement nabízí Capptain SAS. Pokud provádíte migraci ze starší verze, najdete na webu Capptain nejdřív přenést 2.4.0 a potom použijte následující postup.

> [!IMPORTANT]
> Capptain a Mobile Engagement nejsou stejné služby a postup níže uvedené jenom dozvíte, jak migrovat klientské aplikace. Migrace sady SDK v aplikaci není migrovat data ze serverů Capptain na servery Mobile Engagement.
> 
> 

### <a name="jar-file"></a>Soubor JAR
Nahraďte `capptain.jar` podle `mobile-engagement-VERSION.jar` ve vaší `libs` složky.

### <a name="resource-files"></a>Soubory prostředků
Každý soubor prostředků, který jsme zadali (s předponou podle `capptain_`) má nahradit za nové (s předponou `engagement_`).

Pokud jste si přizpůsobili tyto soubory, budete muset znovu použít vlastní u nových souborů **všechny identifikátory v souborech prostředků také přejmenovaná**.

### <a name="application-id"></a>ID aplikace
Zapojení teď používá připojovací řetězec konfigurace identifikátory SDK například identifikátor aplikace.

Budete muset použít `EngagementAgent.init` metoda aktivitou Spouštěče takto:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

Připojovací řetězec pro vaši aplikaci se zobrazí na portálu Azure.

Odeberte všechny volání `CapptainAgent.configure` jako `EngagementAgent.init` nahrazuje dané metody.

`appId` Již jde konfigurovat pomocí `AndroidManifest.xml`.

Odeberte z této části vaší `AndroidManifest.xml` pokud:

            <meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### <a name="java-api"></a>Java API
Každé volání jakákoli Třída Java naše SDK musí být přejmenována; například `CapptainAgent.getInstance(this)` musí být přejmenován `EngagementAgent.getInstance(this)`, `extends CapptainActivity` musí být přejmenován `extends EngagementActivity` atd...

Pokud byly integrovány s výchozí agenta předvoleb soubory, výchozí název souboru je nyní `engagement.agent` a zda je klíč `engagement:agent`.

Při vytváření webové oznámení, vazač Javascript je nyní `engagementReachContent`.

### <a name="androidmanifestxml"></a>AndroidManifest.xml
Mnoho změn došlo k dispozici, služba není sdíleny a spoustu příjemci už nejsou exportovatelný.

Deklarace služby je teď jednodušší; Odeberte záměrné filtru a všechny metadat je uvnitř a přidejte `exportable=false`.

Plus všechno, co je přejmenován na používat engagement.

Teď vypadá takto:

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

Pokud chcete povolit protokolů testování, meta-data nyní byl přesunut do aplikace značky a bylo přejmenováno:

            <application>

              <meta-data android:name="engagement:log:test" android:value="true" />

              <service/>

            </application>

Všechny ostatní metadata právě přejmenovaná, zde je úplný seznam (přejmenování kurzu jen ty, které používáte):

            <meta-data
              android:name="engagement:reportCrash"
              android:value="true"/>
            <meta-data
              android:name="engagement:sessionTimeout"
              android:value="10000"/>
            <meta-data
              android:name="engagement:burstThreshold"
              android:value="0"/>
            <meta-data
              android:name="engagement:connection:delay"
              android:value="0"/>
            <meta-data
              android:name="engagement:locationReport:lazyArea"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:background"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:fine"
              android:value="false"/>
            <meta-data
              android:name="engagement:agent:settings:name"
              android:value="engagement.agent"/>
            <meta-data
              android:name="engagement:agent:settings:mode"
              android:value="0"/>
            <meta-data
              android:name="engagement:gcm:sender"
              android:value="<YOUR_PROJECT_NUMBER>\n"/>
            <meta-data
              android:name="engagement:adm:register"
              android:value="true"/>
            <meta-data
              android:name="engagement:reach:notification:icon"
              android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>

            <activity android:name="SomeActivityWithoutReachOverlay">
              <meta-data
                android:name="engagement:notification:overlay"
                android:value="false"/>
            </activity>

Google Play a SmartAd sledování byl odebrán z SDK, musíte se odebrat toto bez nahrazení:

            <meta-data 
                android:name="capptain:track:installReferrerForwardList"
                android:value="com.class1,com.class2"/>
            <meta-data
                android:name="capptain:track:adservers"
                android:value="smartad" />

Reach aktivity jsou nyní deklarované takto:

            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/html"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

Pokud máte vlastní aktivity Reach, potřebujete jenom změnit záměrné akce, které odpovídají buď `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` nebo `com.microsoft.azure.engagement.reach.intent.action.POLL`.

Přejmenovaná všesměrového vysílání příjemci plus nyní přidáme `exported=false`. Tady je úplný seznam příjemců s novou specifikaci, (přejmenování kurzu jen ty, které používáte):

            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
              android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
              android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
              android:permission="com.amazon.device.messaging.permission.SEND">
              <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
                <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
              </intent-filter>
            </receiver>

Sledování příjemce byl odebrán, takže budete muset odebrat v této části:

          <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
            <intent-filter>
              <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
              <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
            </intent-filter>
          </receiver>

Všimněte si, že prohlášení o implementaci všesměrového vysílání příjemce **EngagementMessageReceiver** došlo ke změně v `AndroidManifest.xml`. Je to proto, že byly odstraněny rozhraní API k odeslání a odebrat libovolný protokolu XMPP zprávy z libovolné protokolu XMPP entit a rozhraní API pro odesílání a přijímání zpráv mezi zařízeními. Proto je třeba také odstranit následující zpětná volání z vaší **EngagementMessageReceiver** implementace:

            protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

a

            protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

potom odstraňte v žádném volání, **EngagementAgent** pro:

            sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

a

            sendXMPPMessage(android.os.Bundle msg)

### <a name="proguard"></a>Proguard
Proguard konfigurace může být ovlivněno rebranding, pravidla jsou nyní vyhledávání jako:

            -dontwarn android.**
            -keep class android.support.v4.** { *; }

            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
              <methods>;
            }

