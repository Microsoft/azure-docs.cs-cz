---
title: Odesílání zabezpečených nabízených oznámení pomocí center oznámení Azure
description: Přečtěte si, jak odesílat zabezpečená nabízená oznámení do aplikace pro Android z Azure. Ukázky kódu napsané v jazyce Java a C#.
documentationcenter: android
keywords: push oznámení, nabízená oznámení, push zprávy, android push oznámení
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 419a9f9b5ce698c7516edd55856cbea9891ba029
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212182"
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Odesílání zabezpečených nabízených oznámení pomocí center oznámení Azure

> [!div class="op_single_selector"]
> * [Univerzální pro Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Přehled

> [!IMPORTANT]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Podpora nabízených oznámení v Microsoft Azure umožňuje přístup k snadno použitelné infrastruktuře nabízených zpráv s více platformami a škálování, což značně zjednodušuje implementaci nabízených oznámení pro spotřebitelské i podnikové aplikace. mobilních platforem.

Z důvodu regulačních nebo bezpečnostních omezení může aplikace někdy chtít zahrnout něco do oznámení, které nelze přenášet prostřednictvím standardní infrastruktury nabízených oznámení. Tento kurz popisuje, jak dosáhnout stejného prostředí odesláním citlivých informací prostřednictvím zabezpečeného a ověřeného připojení mezi klientským zařízením Android a back-endem aplikace.

Na vysoké úrovni je tok následující:

1. Back-end aplikace:
   * Ukládá zabezpečené datové části v back-end databázi.
   * Odešle ID tohoto oznámení do zařízení Android (nejsou odesílány žádné zabezpečené informace).
2. Aplikace na zařízení při příjmu oznámení:
   * Zařízení Android kontaktuje back-end požadující zabezpečenou datovou část.
   * Aplikace může zobrazit datovou část jako oznámení na zařízení.

Je důležité si uvědomit, že v předchozím toku (a v tomto kurzu) se předpokládá, že zařízení ukládá ověřovací token v místním úložišti, po přihlášení uživatele. Tento přístup zaručuje bezproblémové prostředí, protože zařízení může načíst zabezpečenou datovou část oznámení pomocí tohoto tokenu. Pokud vaše aplikace neukládá ověřovací tokeny na zařízení nebo pokud tyto tokeny mohou vypršet, aplikace zařízení po obdržení nabízeného oznámení by měla zobrazit obecné oznámení s výzvou uživateli ke spuštění aplikace. Aplikace pak ověří uživatele a zobrazí datovou část oznámení.

Tento kurz ukazuje, jak odesílat zabezpečená nabízená oznámení. Vychází z kurzu [Upozornit uživatele,](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) takže byste měli nejprve provést kroky v tomto kurzu, pokud jste tak již neučinili.

> [!NOTE]
> Tento kurz předpokládá, že jste vytvořili a nakonfigurovali centrum oznámení, jak je popsáno v [začínáme s centra oznámení (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Úprava projektu Android

Teď, když jste upravili back-end aplikace tak, aby odeslal pouze *ID* nabízeného oznámení, musíte změnit aplikaci pro Android tak, aby toto oznámení zpracovávala a zavolala zpět back-end, abyste načetli zabezpečenou zprávu, která se má zobrazit.
K dosažení tohoto cíle, musíte zajistit, aby vaše aplikace pro Android ví, jak se ověřit pomocí back-endu, když obdrží nabízená oznámení.

Nyní upravte *tok přihlášení,* abyste uložili hodnotu ověřovací hlavičky ve sdílených předvolbách aplikace. Analogové mechanismy lze použít k uložení libovolného ověřovacího tokenu (například tokenů OAuth), které musí aplikace používat bez nutnosti pověření uživatele.

1. V projektu aplikace pro Android přidejte na začátek třídy následující konstanty: `MainActivity`

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```
2. Stále ve `MainActivity` třídě aktualizujte metodu `getAuthorizationHeader()` tak, aby obsahovala následující kód:

    ```java
    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

        SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

        return basicAuthHeader;
    }
    ```
3. V horní `import` části souboru `MainActivity` přidejte následující příkazy:

    ```java
    import android.content.SharedPreferences;
    ```

Nyní změňte obslužnou rutinu, která je volána při přijetí oznámení.

1. Ve `MyHandler` třídě změnit `OnReceive()` metodu obsahovat:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```
2. Pak přidejte metodu `retrieveNotification()` a `{back-end endpoint}` nahraďte zástupný symbol koncovým bodem back-end, který byl získán při nasazování back-endu:

    ```java
    private void retrieveNotification(final String secureMessageId) {
        SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                    request.addHeader("Authorization", "Basic "+authorizationHeader);
                    HttpResponse response = new DefaultHttpClient().execute(request);
                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                        throw new RuntimeException("Error retrieving secure notification");
                    }
                    String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                    JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                    sendNotification(secureNotification.getString("Payload"));
                } catch (Exception e) {
                    Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                    return e;
                }
                return null;
            }
        }.execute(null, null, null);
    }
    ```

Tato metoda volá back-end aplikace k načtení obsahu oznámení pomocí přihlašovacích údajů uložených ve sdílených předvolbách a zobrazí jej jako normální oznámení. Oznámení se dívá na uživatele aplikace stejně jako jakékoli jiné nabízené oznámení.

Je vhodnější zpracovat případy chybějící vlastnosti záhlaví ověřování nebo odmítnutí back-endem. Konkrétní zpracování těchto případů většinou závisí na cílové uživatelské zkušenosti. Jednou z možností je zobrazení oznámení s obecnou výzvou pro uživatele k ověření načíst skutečné oznámení.

## <a name="run-the-application"></a>Spuštění aplikace

Chcete-li aplikaci spustit, proveďte následující akce:

1. Ujistěte se, že **AppBackend** je nasazený v Azure. Pokud používáte Visual Studio, spusťte aplikaci **AppBackend** Web API. Zobrazí se ASP.NET webová stránka.
2. V Eclipse spusťte aplikaci na fyzickém zařízení Android nebo emulátoru.
3. V uživatelském rozhraní aplikace pro Android zadejte uživatelské jméno a heslo. Může se jednat o libovolný řetězec, ale musí mít stejnou hodnotu.
4. V unovém uj. **Log in** Potom klepněte na **tlačítko Odeslat nabízenou položku**.
