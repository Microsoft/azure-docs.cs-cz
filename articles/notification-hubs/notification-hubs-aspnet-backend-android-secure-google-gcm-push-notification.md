---
title: Odesílání zabezpečených nabízených oznámení pomocí Azure Notification Hubs
description: Zjistěte, jak odesílání zabezpečených nabízených oznámení do aplikace pro Android z Azure. Ukázky kódu napsané v jazyce Java a C#.
documentationcenter: android
keywords: nabízené oznámení, nabízená oznámení, nabízené zprávy, android nabízená oznámení
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38698024"
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Odesílání zabezpečených nabízených oznámení pomocí Azure Notification Hubs
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

Podpora nabízená oznámení v Microsoft Azure umožňuje získat přístup k předplatným snadným ovládáním, multiplatformní, horizontálně škálovanou infrastrukturu zpráva, což výrazně zjednodušuje provádění nabízená oznámení pro zákaznické a podnikové aplikace mobilní platformy.

Z důvodu dodržování legislativních nebo omezení zabezpečení, někdy aplikace může být vhodné zahrnout něco oznámení, který nemůže být přenesen prostřednictvím infrastrukturu standardní nabízených oznámení. Tento kurz popisuje, jak dosáhnout stejné prostředí tím, že odesílání citlivé informace přes zabezpečené ověřené připojení mezi zařízení s Androidem klienta a aplikačního back-endu.

Na vysoké úrovni tok je následujícím způsobem:

1. Back-end aplikace:
   * Úložiště zabezpečené datové části v back-end databáze.
   * ID tohoto oznámení se odešle do zařízení s Androidem (se neodesílají žádné informace o zabezpečení).
2. Aplikace na zařízení, když obdrží oznámení:
   * Zařízení s Androidem kontaktuje back endu, zabezpečené datové části požadavku.
   * Aplikace můžete zobrazit datovou část jako oznámení na zařízení.

Je důležité si uvědomit, že v předchozím toku (a v tomto kurzu), se předpokládá, že zařízení ukládá ověřovací token v místním úložišti, po přihlášení uživatele. Tento postup zaručuje bezproblémové prostředí, jak zařízení můžete načíst zabezpečené pomocí tohoto tokenu datovou část v oznámení. Pokud vaše aplikace neukládá ověřovacích tokenů na zařízení nebo pokud můžete platnost těchto tokenů, aplikace pro zařízení, při přijetí nabízené oznámení by měl zobrazit obecné oznámení výzvou, aby uživatel ke spuštění aplikace. Aplikace pak ověří uživatele a zobrazuje datová část oznámení.

Tento kurz ukazuje postupy při odesílání zabezpečených nabízených oznámení. Je nástavbou [oznamování uživatelům pomocí](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) kurz, takže byste měli dokončit kroky v tomto kurzu nejprve Pokud jste tak již neučinili.

> [!NOTE]
> Tento kurz předpokládá, že jste vytvořili a konfiguraci centra oznámení, jak je popsáno v [Začínáme se službou Notification Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Upravit projekt pro Android
Teď, když jste změnili back endu aplikace k odeslání jenom *ID* nabízeného oznámení, budete muset změnit své aplikace pro Android zpracovat oznámení a zpětné volání back endu k načtení zabezpečené zprávy, který se má zobrazit.
K dosažení tohoto cíle, budete muset Ujistěte se, že aplikace pro Android umí ke svému ověření s back endu, když obdrží nabízená oznámení.

Nyní, změnit *přihlášení* flow, aby bylo možné uložit hodnota hlavičky ověřování do sdílené předvolby vaší aplikace. Obdobná mechanismy je možné ukládat libovolný ověřovací token (například tokenů OAuth), který má aplikace používat bez nutnosti přihlašovací údaje uživatele.

1. V projektu aplikace pro Android, přidejte následující konstanty v horní části **MainActivity** třídy:
   
        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
2. Pořád ještě v **MainActivity** třídy, aktualizujte `getAuthorizationHeader()` metodu tak, aby obsahovala následující kód:
   
        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
   
            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();
   
            return basicAuthHeader;
        }
3. Přidejte následující `import` příkazů v horní části **MainActivity** souboru:
   
        import android.content.SharedPreferences;

Nyní změníte obslužnou rutinu, která je volána při přijetí oznámení.

1. V **MyHandler** třídy změnit `OnReceive()` metoda obsahuje:
   
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }
2. Pak přidejte `retrieveNotification()` metoda nahraďte zástupnou hodnotu `{back-end endpoint}` s koncovým bodem back-end získané při nasazování back endu:
   
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

Tato metoda volá back endu aplikace načíst obsah oznámení s použitím přihlašovací údaje uložené ve sdílené předvolby a zobrazí je jako normální oznámení. Bude vypadat oznámení pro uživatele aplikace úplně stejně jako jakékoli jiné nabízené oznámení.

Je vhodnější zpracovat případy chybí vlastnost záhlaví ověřování nebo zamítnutí back-end. Konkrétní zpracování těchto případech většinou závisí na cíl činnost koncového uživatele. Jednou z možností je zobrazení oznámení s výzvou obecný kvůli ověření uživatele k načtení skutečné oznámení.

## <a name="run-the-application"></a>Spuštění aplikace
Ke spuštění aplikace, proveďte následující akce:

1. Ujistěte se, že **AppBackend** je nasazené v Azure. Pokud používáte Visual Studio, spusťte **AppBackend** aplikace webového rozhraní API. Zobrazí se webová stránka ASP.NET.
2. V nástroji Eclipse spusťte aplikaci na fyzické zařízení s Androidem nebo emulátoru.
3. V aplikaci pro Android uživatelského rozhraní zadejte uživatelské jméno a heslo. Mohou to být libovolný řetězec, ale musí být stejnou hodnotu.
4. V aplikaci pro Android uživatelského rozhraní, klikněte na tlačítko **přihlášení**. Pak klikněte na tlačítko **odesílání nabízených oznámení**.

