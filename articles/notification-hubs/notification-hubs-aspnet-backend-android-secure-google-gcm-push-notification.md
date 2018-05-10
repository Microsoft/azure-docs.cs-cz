---
title: Odesílání zabezpečené nabízených oznámení pomocí Azure Notification Hubs
description: Naučte se odesílání zabezpečené nabízených oznámení do aplikace pro Android z Azure. Ukázky kódu jsou vytvořeny v jazyce Java a C#.
documentationcenter: android
keywords: nabízená oznámení, nabízená oznámení, nabízené zprávy, android nabízená oznámení
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 04/25/2018
ms.author: dimazaid
ms.openlocfilehash: 58f6967c59a5060baa10ff83752b9c6ed08226cb
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Odesílání zabezpečené nabízených oznámení pomocí Azure Notification Hubs
> [!div class="op_single_selector"]
> * [Univerzální pro Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Přehled
> [!IMPORTANT]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).
> 
> 

Podpora nabízená oznámení v Microsoft Azure umožňuje získat přístup snadno použitelnou, multiplatformní a upraveným nabízená zpráva infrastruktury, což výrazně zjednodušuje implementaci nabízená oznámení spotřebních a podnikových aplikací pro mobilní platformy.

Kvůli zákonným omezení zabezpečení, někdy aplikace může chtít zahrnout něco v oznámení, kterou nelze přenést prostřednictvím infrastrukturu pro standardní nabízená oznámení. Tento kurz popisuje, jak zajistit stejné prostředí posíláním důvěrných informací o prostřednictvím zabezpečeného a ověřené připojení mezi klientské zařízení Android a back-end aplikace.

Na vysoké úrovni tok je následující:

1. Back-end aplikace:
   * Zabezpečení datové úložiště v databázi back-end.
   * ID tohoto oznámení se odešle do zařízení s Androidem (zabezpečené nebudou odeslány žádné informace).
2. Aplikace na zařízení, když obdrží oznámení:
   * Zařízení s Androidem kontaktuje back-end vyžaduje zabezpečené datové části.
   * Aplikace můžete zobrazit datové části jako upozornění na zařízení.

Je důležité si uvědomit, že v předchozím toku (a v tomto kurzu), se předpokládá, že zařízení ukládá ověřovací token do místního úložiště, po přihlášení uživatele. Tento postup zaručuje integrované prostředí jako zařízení můžete načíst pomocí tohoto tokenu zabezpečení datové na oznámení. Pokud vaše aplikace nejsou uložené tokeny ověřování v zařízení, nebo pokud tyto tokeny můžete vypršela platnost, by měla aplikace zařízení při přijetí nabízeného oznámení zobrazit obecné oznámení uživateli zobrazuje výzvu spusťte aplikaci. Aplikace pak ověřuje uživatele a ukazuje datová část oznámení.

Tento kurz ukazuje, jak odesílat zabezpečené nabízená oznámení. Vychází [upozornění uživatelů](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) kurzu, takže byste měli dokončit kroky v tomto kurzu nejprve Pokud jste tak ještě neučinili.

> [!NOTE]
> V tomto kurzu se předpokládá, že jste vytvořili a nakonfigurovali vaše Centrum oznámení, jak je popsáno v [Začínáme s Notification Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Upravit projektu pro Android
Teď, když změnit váš back-end aplikace k odesílání jen na *ID* nabízená oznámení, budete muset změnit svoji aplikaci pro Android ke zpracování tohoto oznámení a zpětné volání váš back-end pro načtení zabezpečenou zprávu, který se má zobrazit.
K dosažení tohoto cíle, budete muset Ujistěte se, že vaše aplikace pro Android umí ke svému ověření s back-end, když obdrží nabízená oznámení.

Nyní změnit *přihlášení* toku a uložte hodnota hlavičky ověřování v sdílet předvolby vaší aplikace. Podobá mechanismy slouží k uložení žádné ověřovací token (například tokenů OAuth), který má aplikace použít bez nutnosti přihlašovací údaje uživatele.

1. V projektu aplikace pro Android, přidejte následující konstanty v horní části **MainActivity** třídy:
   
        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
2. Pořád ještě v **MainActivity** třídy, aktualizaci `getAuthorizationHeader()` metoda obsahuje následující kód:
   
        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
   
            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();
   
            return basicAuthHeader;
        }
3. Přidejte následující `import` příkazy v horní části **MainActivity** souboru:
   
        import android.content.SharedPreferences;

Teď změňte obslužná rutina, která je volána, když bylo přijato oznámení.

1. V **MyHandler** třídy změnu `OnReceive()` metoda obsahuje:
   
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }
2. Pak přidejte `retrieveNotification()` metoda, nahraďte zástupný symbol `{back-end endpoint}` s koncovým bodem back-end získali při nasazování back-end:
   
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

Tato metoda volá váš back-end aplikace k získání obsahu oznámení pomocí pověření uložených v sdílet předvolby a zobrazí jako normální oznámení. Oznámení vypadá na uživatele aplikace úplně stejně jako ostatní nabízených oznámení.

Je vhodnější pro zpracování v případech chybějící vlastnost hlavičky ověřování nebo odmítání back-end. Konkrétní zpracování těchto případech se většinou závisí na cílové činnost koncového uživatele. Jednou z možností je zobrazit oznámení s výzvou obecný pro ověření uživatele pro načtení skutečné oznámení.

## <a name="run-the-application"></a>Spuštění aplikace
Ke spuštění aplikace, proveďte následující akce:

1. Zajistěte, aby **AppBackend** je nasazené v Azure. Pokud používáte Visual Studio, spusťte **AppBackend** aplikace webového rozhraní API. Zobrazí se webová stránka ASP.NET.
2. V nástroji Eclipse spusťte aplikaci v emulátoru nebo fyzické zařízení Android.
3. V systému Android aplikace uživatelského rozhraní zadejte uživatelské jméno a heslo. Mohou to být libovolný řetězec, ale musí být stejnou hodnotu.
4. V systému Android aplikace uživatelského rozhraní, klikněte na **přihlásit**. Pak klikněte na tlačítko **odeslat nabízené**.

