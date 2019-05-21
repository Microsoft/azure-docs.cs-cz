---
title: Začínáme s Androidem – platforma identit Microsoft | Azure
description: Aplikace pro Android můžete jak získat přístupový token a volat Microsoft Graph API nebo rozhraní API, která vyžadují přístupových tokenů z platforma identit Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6112facfc0c10d7a0a0495cd778fa6c3cb6130a7
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962149"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Přihlašování uživatelů a volání Microsoft Graphu z aplikace pro Android

V tomto kurzu se dozvíte, jak integrovat aplikace pro Android do Microsoft identity platform. Konkrétně aplikace bude přihlásit uživatele, získání přístupového tokenu pro volání rozhraní Microsoft Graph API a vytvořte žádost na rozhraní Microsoft Graph API.  

Po dokončení průvodce bude vaše aplikace akceptovat přihlášení osobní účty Microsoft (včetně outlook.com, live.com a další) a pracovní nebo školní účty z jakéhokoli společnosti nebo organizace, která používá Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Jak funguje v tomto kurzu

![Ukazuje, jak ukázková aplikace vygenerované v tomto kurzu funguje](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Aplikace v této ukázce se přihlásit uživatele a jejich jménem získat data.  Tato data budete přistupovat prostřednictvím chráněné rozhraní API (Microsoft Graph API v tomto případě), který vyžaduje ověření.

A konkrétně:

* Vaše aplikace se přihlásit uživatele buď prostřednictvím prohlížeče nebo Microsoft Authenticator a portál společnosti Intune.
* Koncový uživatel přijme oprávnění, která vyžaduje vaše aplikace. 
* Vaše aplikace budou vydány lístky přístupového tokenu pro rozhraní Microsoft Graph API.
* Požadavek HTTP do webového rozhraní API zahrne přístupový token.
* Zpracování odpovědi Microsoft Graphu.

Tato ukázka používá Microsoft Authentication library pro Android (MSAL) k implementaci ověřeného Knihovna MSAL bude automaticky obnovit tokeny, poskytovat jednotné přihlašování mezi jinými aplikacemi na zařízení a spravovat účty.

## <a name="prerequisites"></a>Požadavky

* Tento instalační program s asistencí používá Android Studio.
* Vyžaduje se Android 16 nebo novější (19 + je doporučeno).

## <a name="library"></a>Knihovna

Tato příručka používá následující knihovny pro ověřování:

|Knihovna|Popis|
|---|---|
|[com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|

## <a name="set-up-your-project"></a>Nastavení projektu

V tomto kurzu se vytvoří nový projekt. Pokud chcete stáhnout dokončený kurzu místo toho [stáhnout kód](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>Vytvořit nový projekt

1. Otevřete Android Studio a vyberte buď **spusťte nový projekt Android Studio**.
    - Pokud je už otevřená Android Studio, vyberte **souboru** > **nový** > **nový projekt**.
2. Ponechte **prázdná aktivita** jak vypadá, vyberte **Další**.
3. Pojmenujte svoji aplikaci, nastavte `Minimum API level` k **rozhraní API 19 nebo novější**, přístupů **Dokončit**.
5. Ve vaší `app/build.gradle`, nastavte `targetedSdkVersion` 27. 

## <a name="register-your-application"></a>Zaregistrujte svoji aplikaci.

Můžete zaregistrovat aplikaci v některém ze dvou způsobů, jak je popsáno v následujících dvou částech.

### <a name="register-your-app"></a>Zaregistrovat aplikaci

1. Přejděte [webu Azure portal](https://aka.ms/MobileAppReg) > vyberte `New registration`. 
2. Zadejte **název** pro vaši aplikaci > `Register`. **Není nastavena identifikátor URI pro přesměrování v této fázi**. 
3. V `Manage` části, přejděte na `Authentication` > `Add a platform` > `Android`
    - Zadejte název balíčku projektu. Pokud jste stáhli kód, tato hodnota je `com.azuresamples.msalandroidapp`. 
    - Zadejte vaše hodnoty hash podpisu ladění/vývoj. Pomocí příkazu KeyTool na portálu ke generování hodnoty Hash podpisu. 
4. Přístupů `Configure` a uložit ***MSAL konfigurace*** na později. 

## <a name="build-your-app"></a>Sestavení aplikace

### <a name="configure-your-android-app"></a>Konfigurovat aplikaci pro Android

1. Klikněte pravým tlačítkem myši **res** > **nový** > **složky** > **nezpracovaná složku prostředků**
2. V **aplikace** > **res** > **nezpracovaná**, vytvořte nový soubor JSON s názvem `auth_config.json` a vložte váš ***MSAL konfigurace***. Zobrazit [MSAL konfigurace pro další informace o](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
   <!-- Workaround for Docs conversion bug -->
3. V **aplikace** > **manifesty** > **AndroidManifest.xml**, přidejte `BrowserTabActivity` aktivity níže. Tato položka umožňuje společnosti Microsoft pro zpětné volání do aplikace po dokončení ověřování:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Mějte na paměti, hodnoty Hash podpisu používá by neměl být zakódován do adresy URL **AndroidManifest.xml**. 

4. Uvnitř **AndroidManifest.xml** a přímo nad `<application>` značky, přidejte následující oprávnění:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

5. V `BrowserTabActivity`, nahraďte ***název balíčku*** a ***hodnoty Hash podpisu*** hodnotami, které je zaregistrovaný na webu Azure Portal.

### <a name="create-the-apps-ui"></a>Vytvoření uživatelského rozhraní aplikace

1. Přejděte na **res** > **rozložení**a pak otevřete **activity_main.xml**.
2. Změna rozložení aktivitu z `android.support.constraint.ConstraintLayout` nebo jiné `LinearLayout`.
3. Přidat `android:orientation="vertical"` vlastnost `LinearLayout` uzlu.
4. Vložte následující kód do `LinearLayout` uzlu, nahraďte aktuálním obsahu:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```

### <a name="add-msal-to-your-project"></a>Do projektu přidejte MSAL

1. V nástroji Android Studio vyberte **skriptů Gradle** > **build.gradle (modul: aplikace)**.
2. V části **závislosti**, vložte následující kód:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>Použití MSAL 

Několik částí provádět změny v `MainAcitivty.java`. Jsme budete možné provést jednotlivé kroky potřebné k přidání a použití MSAL ve vaší aplikaci.

#### <a name="required-imports"></a>Požadované importy

Do projektu přidejte následující importy: 

```java
import android.app.Activity;
import android.content.Intent;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import com.android.volley.*;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.Volley;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

#### <a name="instantiating-msal"></a>Vytvoření instance MSAL 

Uvnitř `MainActivity` třídy, budeme muset vytvořit instanci MSAL spolu s několika konfiguracemi o tom, co jsou aplikace bude provádět, včetně oborů a webové rozhraní API, které chceme, aby pro přístup k. 

Zkopírujte následující proměnné uvnitř `MainActivity`:

```java
final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

/* UI & Debugging Variables */
private static final String TAG = MainActivity.class.getSimpleName();
Button callGraphButton;
Button signOutButton;

/* Azure AD Variables */
private PublicClientApplication sampleApp;
private IAuthenticationResult authResult;
```

Teď pro vytvoření instance MSAL, zkopírujte následující kód `onCreate(...)` metody:

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

callGraphButton = (Button) findViewById(R.id.callGraph);
signOutButton = (Button) findViewById(R.id.clearCache);

callGraphButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onCallGraphClicked();
    }
});

signOutButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onSignOutClicked();
    }
});

/* Configure your sample app and save state for this activity */
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);

/* Attempt to get a user and acquireTokenSilent */
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {
        if (!accounts.isEmpty()) {
            /* This sample doesn't support multi-account scenarios, use the first account */
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts */
        }
    }
});
```

Výše uvedené bloku kódu se pokusí přihlásit uživatele tiše při otevření vaší aplikace prostřednictvím `getAccounts(...)` a v případě úspěšného ověření `acquireTokenSilentAsync(...)`.  V následujících částech Implementujeme obslužná metoda zpětného volání případě, že existuje se žádné účty přihlášení. 

#### <a name="use-msal-to-get-tokens"></a>Použití MSAL k získání tokenů

Teď můžeme implementovat Uživatelském rozhraní aplikace logiky zpracování a jak získat tokeny interaktivně prostřednictvím MSAL. 

Knihovna MSAL poskytuje dva primární metody pro získávání tokenů: `acquireTokenSilentAsync` a `acquireToken`.  

`acquireTokenSilentAsync` přihlásí uživatele a získat tokeny bez nutnosti zásahu uživatele, pokud se účet nachází. Pokud je úspěšná, bude MSAL předání bude generovat tokeny do vaší aplikace, pokud se nezdaří `MsalUiRequiredException`.  Pokud se vygeneruje tuto výjimku nebo chcete, aby váš uživatel pro interaktivní přihlášení v prostředí (přihlašovací údaje, vícefaktorové ověřování nebo jiné podmíněného přístupu zásad může nebo nemusí být povinné), pak můžete použít `acquireToken`.  

`acquireToken` vždy zobrazit uživatelské rozhraní, při pokusu o přihlášení uživatele a získat tokeny; může však použít soubory cookie relací v prohlížeči nebo účet v aplikaci Microsoft authenticator poskytnout interaktivní Jednotným přihlašováním. 

Pokud chcete začít, vytvořte následující tři metody uživatelského rozhraní uvnitř `MainActivity` třídy:

```java
/* Set the UI for successful token acquisition data */
private void updateSuccessUI() {
    callGraphButton.setVisibility(View.INVISIBLE);
    signOutButton.setVisibility(View.VISIBLE);
    findViewById(R.id.welcome).setVisibility(View.VISIBLE);
    ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
            authResult.getAccount().getUsername());
    findViewById(R.id.graphData).setVisibility(View.VISIBLE);
}

/* Set the UI for signed out account */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");

    Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
            .show();
}

/* Use MSAL to acquireToken for the end-user
 * Callback will call Graph api w/ access token & update UI
 */
private void onCallGraphClicked() {
    sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
}
```

V dalším kroku přidejte metodu k získání aktuální aktivitu a zpracování zpětných volání silent & interaktivním okně:

```java
public Activity getActivity() {
    return this;
}

/* Callback used in for silent acquireToken calls.
 * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
 * else errors that we need to do an interactive request.
 */
private AuthenticationCallback getAuthSilentCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");

            /* Store the authResult */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}

/* Callback used for interactive request.  If succeeds we use the access
 * token to call the Microsoft Graph. Does not check cache
 */
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");
            Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

            /* Store the auth result */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

#### <a name="use-msal-for-sign-out"></a>Použití MSAL pro odhlášení

Dále, až budete přidáváme podporu pro odhlašování do vaší aplikace. 

Je důležité si uvědomit, odhlašování pomocí MSAL odebere všechny známé informace o uživateli z této aplikace, ale uživatel bude mít stále aktivní relace na svém zařízení. Pokud se uživatel pokusí přihlásit znovu, může se zobrazit interakce, ale možná bude nutné znovu zadat své přihlašovací údaje z důvodu se aktivní relace zařízení. 

Přidání odhlašování, zkopírujte následující metodu do vaší aplikace, která projde všechny účty a odebere je:

```java
/* Clears an account's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 * User will get interactive SSO if trying to sign back-in.
 */
private void onSignOutClicked() {
    /* Attempt to get a user and acquireTokenSilent
     * If this fails we do an interactive request
     */
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (accounts.isEmpty()) {
                /* No accounts to remove */

            } else {
                for (final IAccount account : accounts) {
                    sampleApp.removeAccount(
                            account,
                            new PublicClientApplication.AccountsRemovedCallback() {
                        @Override
                        public void onAccountsRemoved(Boolean isSuccess) {
                            if (isSuccess) {
                                /* successfully removed account */
                            } else {
                                /* failed to remove account */
                            }
                        }
                    });
                }
            }

            updateSignedOutUI();
        }
    });
}
```

#### <a name="call-the-microsoft-graph-api"></a>Volání rozhraní Microsoft Graph API

Jakmile úspěšně jsme jste získali token, jsme vytvořte žádost na rozhraní Microsoft Graph API. Přístupový token se bude nacházet v `AuthenticationResult` uvnitř zpětného volání autorizace `onSuccess(...)` metody. Vytvořit požadavek na oprávnění, aplikaci muset přidat přístupového tokenu hlavičky protokolu HTTP:

| Klíč hlavičky    | value                 |
| ------------- | --------------------- |
| Autorizace | Nosiče < přístupový token > |

K tomu v kódu, přidejte do své aplikace a graf volání a aktualizaci uživatelského rozhraní těchto dvou metod: 

```java
    /* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

Další informace o [Microsoft Graph API](https://graph.microsoft.com)!

#### <a name="multi-account-applications"></a>Více účet aplikace

Tato aplikace je vytvořená pro scénář jeden účet. Knihovna MSAL podporuje i scénáře více účtů, ale vyžaduje další úkony z aplikací. Je potřeba vytvořit uživatelské rozhraní umožňující uživateli na výběru účtu, který chce použít pro každou akci, která vyžaduje tokeny. Aplikace můžete alternativně implementovat heuristiku k výběru účtu, který se má použít prostřednictvím `getAccounts(...)` metody. 

## <a name="test-your-app"></a>Testování aplikace

### <a name="run-locally"></a>Spuštění v místním prostředí

Pokud jste postupovali podle výše uvedený kód, zkuste k vytvoření a nasazení aplikace do testovacího zařízení nebo emulátoru. Byste měli být schopni se přihlásit a získat tokeny pro službu Azure AD nebo osobní účty Microsoft! Po přihlášení uživatele, tato aplikace se zobrazí data vrácená z Microsoft Graphu `/me` koncového bodu. 

Pokud máte nějaké problémy, neváhejte otevřete problém v tomto dokumentu nebo knihovna MSAL a dejte nám vědět. 

### <a name="consent-to-your-app"></a>Souhlas s vaší aplikace

Prvním libovolný uživatel přihlásí do vaší aplikace, uživatelé budou vyzváni k pomocí Microsoft identity souhlas oprávnění požadovaná.  Zatímco většina uživatelů jsou schopny vyjadřuje, zakázali několik tenantů Azure AD souhlasu uživatele - vyžadující souhlas jménem všech uživatelů správce.  Pro podporu tohoto scénáře, je nutné zaregistrovat obory vaší aplikace na webu Azure Portal.

## <a name="help-and-support"></a>Nápověda a podpora

Měli jakékoli potíže, v tomto kurzu nebo s platformou identity Microsoft? Zobrazit [Nápověda a podpora](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)
