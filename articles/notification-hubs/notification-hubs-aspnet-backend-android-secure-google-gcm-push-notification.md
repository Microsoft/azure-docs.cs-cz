---
title: Posílání zabezpečených nabízených oznámení pomocí Azure Notification Hubs
description: Naučte se odesílat zabezpečená nabízená oznámení do aplikace pro Android z Azure. Ukázky kódu napsané v jazycích Java a C#.
documentationcenter: android
keywords: nabízené oznámení, nabízená oznámení, nabízené zprávy, nabízená oznámení Android
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: f2d5d618fabbe7400ce825f984ace1622a524f05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88004016"
---
# <a name="send-secure-push-notifications-with-azure-notification-hubs"></a>Posílání zabezpečených nabízených oznámení pomocí Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Univerzální pro Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Přehled

> [!IMPORTANT]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Podpora nabízených oznámení v Microsoft Azure umožňuje přístup k infrastruktuře nabízených oznámení s více platformami a škálování na více platforem, která výrazně zjednodušuje implementaci nabízených oznámení pro příjemce i podnikové aplikace pro mobilní platformy.

V důsledku regulativních nebo bezpečnostních omezení někdy může aplikace chtít v oznámení zahrnout něco, co nelze přenést pomocí standardní infrastruktury nabízených oznámení. V tomto kurzu se dozvíte, jak dosáhnout stejného prostředí odesláním citlivých informací prostřednictvím zabezpečeného a ověřeného připojení mezi klientským zařízením s Androidem a back-endu aplikace.

Tok je na vysoké úrovni následující:

- Back-end aplikace:
  * Ukládá zabezpečenou datovou část do back-endové databáze.
  * Odešle ID tohoto oznámení na zařízení s Androidem (nejsou posílány žádné zabezpečené informace).
- Aplikace v zařízení při příjmu oznámení:
  * Zařízení s Androidem kontaktuje back-end požadující zabezpečenou datovou část.
  * Aplikace může datovou část zobrazit jako oznámení na zařízení.

Je důležité si uvědomit, že v předchozím toku (a v tomto kurzu) se předpokládá, že zařízení po přihlášení uživatele uloží ověřovací token do místního úložiště. Tento přístup zaručuje bezproblémové prostředí, protože zařízení může načíst zabezpečenou datovou část oznámení pomocí tohoto tokenu. Pokud vaše aplikace neukládá ověřovací tokeny na zařízení nebo pokud tyto tokeny můžou být prošlé, aplikace zařízení po přijetí nabízeného oznámení by měla zobrazit obecné oznámení s výzvou, aby uživatel spustil aplikaci. Aplikace pak uživatele ověří a zobrazí datovou část oznámení.

V tomto kurzu se dozvíte, jak odesílat zabezpečená nabízená oznámení. Vychází z kurzu [informování uživatelů](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) , takže byste nejdřív měli provést kroky v tomto kurzu.

> [!NOTE]
> V tomto kurzu se předpokládá, že jste vytvořili a nakonfigurovali centrum oznámení, jak je popsáno v tématu [Začínáme s Notification Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Úprava projektu pro Android

Teď, když jste změnili back-end aplikace, abyste poslali jenom ID nabízeného oznámení, musíte změnit aplikaci pro Android, aby zpracovala toto oznámení, a volat se zpátky do back-endu, aby se načetla zabezpečená zpráva, která se má zobrazit.
Aby bylo možné dosáhnout tohoto cíle, je nutné zajistit, aby vaše aplikace pro Android věděla, jak se k back-endu při přijímání nabízených oznámení ověřuje.

Teď upravte tok přihlášení, aby se uložila hodnota hlavičky ověřování ve sdílených preferencích vaší aplikace. Obdobná mechanismy se dají použít k uložení jakéhokoli ověřovacího tokenu (například tokeny OAuth), který musí aplikace používat, aniž by museli vyžadovat přihlašovací údaje uživatele.

1. V projektu aplikace pro Android přidejte do horní části třídy následující konstanty `MainActivity` :

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```

2. Stále ve `MainActivity` třídě aktualizujte `getAuthorizationHeader()` metodu tak, aby obsahovala následující kód:

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

3. `import`Na začátek souboru přidejte následující příkazy `MainActivity` :

    ```java
    import android.content.SharedPreferences;
    ```

Nyní Změňte obslužnou rutinu, která je volána při přijetí oznámení.

1. V `MyHandler` třídě změňte `OnReceive()` metodu tak, aby obsahovala:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```

2. Pak přidejte `retrieveNotification()` metodu a nahraďte zástupný symbol koncovým `{back-end endpoint}` bodem back-end získaným při nasazení back-endu:

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

Tato metoda volá back-end aplikace pro načtení obsahu oznámení pomocí přihlašovacích údajů uložených ve sdílených preferencích a zobrazí se jako normální oznámení. Oznámení vypadá uživateli aplikace přesně stejně jako jakékoli jiné nabízené oznámení.

Je vhodnější zpracovat případy chybějící vlastnosti záhlaví ověřování nebo zamítnutí back-endu. Konkrétní zpracování těchto případů většinou závisí na uživatelském prostředí. Jednou z možností je zobrazit oznámení s obecnou výzvou pro uživatele, aby se ověřilo, že se má načíst vlastní oznámení.

## <a name="run-the-application"></a>Spuštění aplikace

Chcete-li spustit aplikaci, proveďte následující akce:

1. Ujistěte se, že **projekt appbackend** je nasazený v Azure. Pokud používáte Visual Studio, spusťte aplikaci webového rozhraní API **projekt appbackend** . Zobrazí se webová stránka ASP.NET.
2. V části zatmění spusťte aplikaci na fyzickém zařízení s Androidem nebo v emulátoru.
3. V uživatelském rozhraní aplikace pro Android zadejte uživatelské jméno a heslo. Může se jednat o libovolný řetězec, ale musí se jednat o stejnou hodnotu.
4. V uživatelském rozhraní aplikace pro Android klikněte na **Přihlásit** se. Pak klikněte na **Odeslat nabízení**.
