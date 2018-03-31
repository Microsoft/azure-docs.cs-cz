---
title: Integraci sady Azure Mobile Engagement Android SDK
description: Nejnovější aktualizace a postupy pro Android SDK pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d72b5014-a22b-4a7f-a470-d2b8145b5b86
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/10/2016
ms.author: piyushjo
ms.openlocfilehash: 21f9c1007c6b7dabc625555c66f267b2b7c579f8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-gcm-with-mobile-engagement"></a>Postup pro integraci služby GCM Mobile Engagement
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

> [!IMPORTANT]
> Postupujte podle integrace postup popsaný v tom, jak integrovat Engagement Android dokumentu před těchto pokynů.
> 
> Tento dokument je užitečný jenom v případě, že jste již integrované modul kampaně Reach a plán tak, aby nabízel zařízení Google Play. Chcete-li integrovat kampaně Reach ve vaší aplikaci, přečtěte si nejprve postupy integrovat Engagement Reach v systému Android.
> 
> 

## <a name="introduction"></a>Úvod
Integrace služby GCM umožňuje poslat vaší aplikace.

Datové části GCM nabídnutých do sady SDK vždy obsahovat `azme` klíče v datový objekt. Proto pokud používáte GCM pro jiný účel ve vaší aplikaci, můžete filtrovat podle klíči nabízených oznámení.

> [!IMPORTANT]
> Jenom zařízení se systémem Android 2.2 nebo vyšší, s Google Play nainstalovaná a má Google pozadí připojení povoleno mohou poslat pomocí služby GCM; Můžete však integrovat tento kód bezpečně na nepodporované zařízení (používá právě záměry).
> 
> 

## <a name="create-a-google-cloud-messaging-project-with-api-key"></a>Vytvoření projektu služby GCM (Google Cloud Messaging) s klíčem rozhraní API
[!INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

## <a name="sdk-integration"></a>Integrace sady SDK
### <a name="managing-device-registrations"></a>Správa registrace zařízení
Každé zařízení musí odeslat příkaz registrace na servery Googlu, jinak nelze získat přístup.

Zařízení můžete také zrušit registraci oznámení GCM (zařízení je automaticky registrace, pokud odinstalaci aplikace).

Pokud nepoužijete [Google Play SDK] nebo jste již Neodesílat záměr registrace sami, můžete provést Engagement registraci zařízení automaticky za vás.

Chcete-li povolit, přidejte následující příkaz pro vaše `AndroidManifest.xml` souboru uvnitř `<application/>` značky:

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Id registrace ke službě Engagement Push komunikovat a přijímat oznámení
Abyste mohli komunikovat id registrace zařízení ke službě Engagement Push a jeho oznámení dostávat, přidejte následující příkaz pro vaše `AndroidManifest.xml` souboru uvnitř `<application/>` značky (i v případě registrace zařízení spravujete sami):

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

Ujistěte se, máte následující oprávnění ve vaší `AndroidManifest.xml` (po `</application>` značka).

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

## <a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Udělení přístupu aplikaci Mobile Engagement k vašemu klíči rozhraní API GCM
Postupujte podle [Tato příručka](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key) k udělení přístupu Mobile Engagement k vašemu klíči rozhraní API GCM.

[Google Play SDK]:https://developers.google.com/cloud-messaging/android/start
