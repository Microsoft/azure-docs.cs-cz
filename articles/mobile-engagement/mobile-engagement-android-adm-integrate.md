---
title: Integraci sady Azure Mobile Engagement Android SDK
description: Nejnovější aktualizace a postupy pro Android SDK pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a7d719ec-67b3-4be3-9d7f-0b61a57fe978
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 4c3b8f60333e6096411aad8499bb4bfc36e53f3c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-adm-with-engagement"></a>Postup pro integraci ADM zapojení
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

> [!IMPORTANT]
> Postupujte podle integrace postup popsaný v tom, jak integrovat Engagement Android dokumentu před těchto pokynů.
> 
> Tento dokument je užitečný jenom v případě, že jste již integrované modul kampaně Reach a plán tak, aby nabízel Amazon zařízení. Chcete-li integrovat kampaně Reach ve vaší aplikaci, přečtěte si nejprve postupy integrovat Engagement Reach v systému Android.
> 
> 

## <a name="introduction"></a>Úvod
Integrace ADM umožňuje poslat při cílení na zařízení se systémem Android Amazon vaší aplikace.

Datové části ADM nabídnutých do sady SDK vždy obsahovat `azme` klíče v datový objekt. Proto pokud používáte ADM pro jiný účel ve vaší aplikaci, můžete filtrovat podle klíči nabízených oznámení.

> [!IMPORTANT]
> Pouze Amazon Kindle zařízení se systémem Android 4.0.3 nebo vyšší jsou podporovány Amazon Device Messaging; Můžete však integrovat tento kód bezpečně na jiné zařízení.
> 
> 

## <a name="sign-up-to-adm"></a>Zaregistrujte si ADM
Pokud dosud neučinili, musíte povolit ADM na vašem účtu Amazon.

Postup je podrobně popsán v: [ <https://developer.amazon.com/sdk/adm/credentials.html> ].

Po dokončení procesu, můžete získat:

* Přihlašovací údaje OAuth (ID klienta a tajný klíč klienta) pro zapojení moct push zařízení.
* Klíč rozhraní API, která musí být integrovaná do aplikace.

## <a name="sdk-integration"></a>Integrace sady SDK
### <a name="managing-device-registrations"></a>Správa registrace zařízení
Každé zařízení musí odeslat příkaz registrace k serverům ADM, jinak nelze získat přístup.

Pokud už používáte [klientské knihovny ADM]a už máte [integrované ADM] můžete přímo přejít na android-sdk-adm přijímat.

Pokud ještě nebyly spojili ADM, má Engagement jednodušší způsob, jak povolit ve vaší aplikaci:

Upravit vaše `AndroidManifest.xml` souboru:

* Přidat obor názvů Amazon soubor by měl začínat takto:
  
      <?xml version="1.0" encoding="utf-8"?>
      <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:amazon="http://schemas.amazon.com/apk/res/android"
* Uvnitř `<application/>` značky, přidejte v této části:
  
      <amazon:enable-feature
         android:name="com.amazon.device.messaging"
         android:required="false"/>
  
      <meta-data android:name="engagement:adm:register" android:value="true" />
* Po přidání značka amazon, může mít chyby sestavení, pokud je váš cíl sestavení projektu menší než Android 2.1. Budete muset použít **Android 2.1 +** sestavení cíl (nemusíte si dělat starosti, můžete mít dál `minSdkVersion` nastavena na 4).
* Integrovat klíč rozhraní API ADM jako prostředek podle [tento postup].

Potom postupujte podle pokynů v dalších oddílech.

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Id registrace ke službě Engagement Push komunikovat a přijímat oznámení
Abyste mohli komunikovat id registrace zařízení ke službě Engagement Push a jeho oznámení dostávat, přidejte následující příkaz pro vaše `AndroidManifest.xml` souboru uvnitř `<application/>` značky (i když používáte ADM bez zapojení):

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

Ujistěte se, máte následující oprávnění ve vaší `AndroidManifest.xml` (před `</application>` značka).

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

## <a name="grant-engagement-oauth-credentials"></a>Přihlašovací údaje OAuth grant zapojení
Odešlete vaše přihlašovací údaje OAuth (ID klienta a tajný klíč klienta) na portálu Engagement.

[<https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[klientské knihovny ADM]:https://developer.amazon.com/sdk/adm/setup.html
[integrované ADM]:https://developer.amazon.com/sdk/adm/integrating-app.html
[tento postup]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
