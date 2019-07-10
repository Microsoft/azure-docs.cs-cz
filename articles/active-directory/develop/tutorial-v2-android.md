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
ms.date: 07/09/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71c6b0d4cd664b12dbd0fbd4e9423240c8dbebb3
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723816"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Přihlašování uživatelů a volání Microsoft Graphu z aplikace pro Android

V tomto kurzu se dozvíte, jak integrovat aplikace pro Android s platformou identity Microsoft. Vaše aplikace se přihlásit uživatele, získání přístupového tokenu pro volání rozhraní Microsoft Graph API a vytvořte žádost na rozhraní Microsoft Graph API.  

Po dokončení průvodce bude vaše aplikace akceptovat přihlášení osobní účty Microsoft (včetně outlook.com, live.com a další) a pracovní nebo školní účty z jakéhokoli společnosti nebo organizace, která používá Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Jak funguje v tomto kurzu

![Ukazuje, jak ukázková aplikace vygenerované v tomto kurzu funguje](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Aplikace v této ukázce se přihlásit uživatele a jejich jménem získat data.  Tato data budete přistupovat prostřednictvím chráněné rozhraní API (Microsoft Graph API), který vyžaduje ověření.

A konkrétně:

* Vaše aplikace se přihlásit uživatele buď prostřednictvím prohlížeče nebo Microsoft Authenticator a portál společnosti Intune.
* Koncový uživatel přijme oprávnění, která vyžaduje vaše aplikace. 
* Vaše aplikace budou vydány lístky přístupového tokenu pro rozhraní Microsoft Graph API.
* Požadavek HTTP do webového rozhraní API zahrne přístupový token.
* Zpracování odpovědi Microsoft Graphu.

Tato ukázka používá Microsoft Authentication library pro Android (MSAL) pro implementaci ověřování. Knihovna MSAL bude automaticky obnovit tokeny, poskytovat jednotné přihlašování (SSO) mezi jinými aplikacemi na zařízení a spravovat účty.

## <a name="prerequisites"></a>Požadavky

* Tento instalační program s asistencí používá Android Studio.
* Vyžaduje se Android 16 nebo novější (19 + je doporučeno).

## <a name="library"></a>Knihovna

Tato příručka používá následující knihovny pro ověřování:

|Knihovna|Popis|
|---|---|
|[com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|

## <a name="create-a-project"></a>Vytvoření projektu

V tomto kurzu se vytvoří nový projekt. Pokud chcete stáhnout dokončený kurzu místo toho [stáhnout kód](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

1. Otevřete Android Studio a vyberte **spusťte nový projekt Android Studio**
2. Vyberte **základní aktivity** a klikněte na tlačítko **Další**.
3. Pojmenujte svoji aplikaci
4. Uložte název balíčku. Zadáte ho později na webu Azure portal. 
5. Nastavte **úroveň rozhraní API minimální** k **API 19** nebo vyšší a klikněte na tlačítko **Dokončit**.
6. V zobrazení projektu zvolte **projektu** v rozevírací nabídce pro zobrazení zdroje a projekt jinými než zdrojovými soubory, otevřete **app/build.gradle** a nastavte `targetSdkVersion` k `27`.

## <a name="register-your-application"></a>Registrace vaší aplikace

1. Přejděte na web [Azure Portal](https://aka.ms/MobileAppReg).
2. Otevřít [okně registrace aplikace](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) a klikněte na tlačítko **+ registrace nové**.
3. Zadejte **název** pro vaši aplikaci a pak, aniž byste museli nastavovat identifikátor URI přesměrování, klikněte na **zaregistrovat**.
4. V **spravovat** podokně, které se zobrazí, vyberte v části **ověřování** >  **+ přidat platformu** > **Android**.
5. Zadejte název balíčku projektu. Pokud jste stáhli kód, tato hodnota je `com.azuresamples.msalandroidapp`.
6. V **hodnoty hash podpisu** část **konfigurace aplikace pro Android** klikněte na **generování vývoj hodnoty Hash podpisu.** a zkopírujte tento příkaz KeyTool určený pro vaši platformu. Mějte na paměti, KeyTool.exe je nainstalován jako součást sady Java Development Kit (JDK) a musíte mít také nainstalovaný nástroj OpenSSL ke spuštění příkazu KeyTool.
7. Zadejte **hodnoty hash podpisu** generovaných KeyTool.
8. Klikněte na tlačítko `Configure` a uložit **MSAL konfigurace** , který se zobrazí v **konfigurace pro Android** stránce mohli zadat, při konfiguraci aplikace později.  Klikněte na **Done** (Hotovo).

## <a name="build-your-app"></a>Sestavení aplikace

### <a name="configure-your-android-app"></a>Konfigurace aplikace pro Android

1. V podokně projektu Android Studio, přejděte na **app\src\main\res**.
2. Klikněte pravým tlačítkem na **res** a zvolte **nový** > **Directory**. Zadejte `raw` jako nový název adresáře a klikněte na **OK**.
3. V **aplikace** > **src** > **res** > **nezpracovaná**, vytvoření nového souboru JSON s názvem `auth_config.json`a vložte do něj MSAL konfiguraci, který jste předtím uložili. Zobrazit [MSAL konfigurace pro další informace o](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
   <!-- Workaround for Docs conversion bug -->
4. V **aplikace** > **src** > **hlavní** > **AndroidManifest.xml**, přidejte `BrowserTabActivity`aktivity níže. Tato položka umožňuje společnosti Microsoft pro zpětné volání do aplikace po dokončení ověřování:

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

    Nahraďte název balíčku, který jste zaregistrovali na webu Azure Portal pro `android:host=` hodnotu.
    Hodnota hash klíče jste zaregistrovali na webu Azure Portal pro nahrazení `android:path=` hodnotu. Hodnota Hash podpisu by neměl mít kódování URL.

5. Uvnitř **AndroidManifest.xml**, hned nad `<application>` značky, přidejte následující oprávnění:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

### <a name="create-the-apps-ui"></a>Vytvoření uživatelského rozhraní aplikace

1. V okně projektu Android Studio, přejděte na **aplikace** > **src** > **hlavní** > **res**  >  **rozložení** a otevřete **activity_main.xml** a otevřete **Text** zobrazení.
2. Změna rozložení aktivity, například `<androidx.coordinatorlayout.widget.CoordinatorLayout` k `<androidx.coordinatorlayout.widget.LinearLayout`.
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

1. V okně projektu Android Studio, přejděte na **aplikace** > **src** > **build.gradle**.
2. V části **závislosti**, vložte následující údaje:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>Použití MSAL

Teď provádět změny v `MainActivity.java` přidat a používat knihovna MSAL ve vaší aplikaci.
V okně projektu Android Studio, přejděte na **aplikace** > **src** > **hlavní** > **java**  >  **com.example.msal**a otevřete `MainActivity.java`

#### <a name="required-imports"></a>Požadované importy

Přidejte následující importy v horní části `MainActivity.java`:

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

#### <a name="instantiate-msal"></a>Instantiate MSAL

Uvnitř `MainActivity` třídy, budeme muset vytvořit instanci MSAL spolu s několika konfiguracemi o tom, co jsou aplikace bude provádět, včetně oborů a webové rozhraní API, které chceme, aby pro přístup k.

Zkopírujte následující proměnné uvnitř `MainActivity` třídy:

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

Nahraďte obsah `onCreate()` pro vytvoření instance MSAL následujícím kódem:

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

Výše uvedený kód se pokusí přihlásit uživatele bez upozornění při otevření vaší aplikace prostřednictvím `getAccounts()` a v případě úspěšného ověření `acquireTokenSilentAsync()`.  V následujících částech Implementujeme obslužná metoda zpětného volání případě, že existuje se žádné účty přihlášení.

#### <a name="use-msal-to-get-tokens"></a>Použití MSAL k získání tokenů

Teď můžeme implementovat Uživatelském rozhraní aplikace logiky zpracování a jak získat tokeny interaktivně prostřednictvím MSAL.

Knihovna MSAL poskytuje dva primární metody pro získávání tokenů: `acquireTokenSilentAsync()` a `acquireToken()`.  

`acquireTokenSilentAsync()` přihlásí uživatele a získat tokeny bez nutnosti zásahu uživatele, pokud se účet nachází. Pokud je úspěšná, bude MSAL předání bude generovat tokeny do vaší aplikace, pokud se nezdaří `MsalUiRequiredException`.  Tato výjimka se vygeneruje, zda má uživatel pro interaktivní přihlášení mají v prostředí (přihlašovací údaje, vícefaktorové ověřování nebo jiné podmíněného přístupu zásad může nebo nemusí být požadovaný), a následné použití `acquireToken()`.  

`acquireToken()` Při pokusu o přihlášení uživatele a získat tokeny, zobrazí se uživatelské rozhraní. Může však použít soubory cookie relací v prohlížeči nebo v aplikaci Microsoft authenticator, účet poskytují interaktivní jednotného přihlašování.

Vytvořit v následujících třech metod uživatelského rozhraní `MainActivity` třídy:

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

Přidejte následující metody k získání aktuální aktivitu a zpracování zpětných volání silent & interaktivním okně:

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

Dále přidáte podporu pro odhlášení.

> [!Important]
> Odhlásit se MSAL odstraní všechny známé informace o uživateli z aplikace, ale uživatel bude mít stále aktivní relace na svém zařízení. Pokud se uživatel pokusí přihlásit znovu, může se zobrazit přihlašovací uživatelské rozhraní, ale možná muset znovu zadat své přihlašovací údaje, protože je stále aktivní relace zařízení.

Přidání odhlašování funkci, přidejte následující metodu uvnitř `MainActivity` třídy. Tato metoda projde všechny účty a odebere je:

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

Po obdržení tokenu, můžeme vytvořit žádost o [Microsoft Graph API](https://graph.microsoft.com) přístupový token se bude nacházet v `AuthenticationResult` uvnitř zpětného volání autorizace `onSuccess()` metody. Vytvořit požadavek na oprávnění, aplikaci muset přidat přístupového tokenu hlavičky protokolu HTTP:

| Klíč hlavičky    | value                 |
| ------------- | --------------------- |
| Autorizace | Bearer \<access-token> |

Přidejte následující dvě metody uvnitř `MainActivity` třídy volání grafu a aktualizaci uživatelského rozhraní:

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

#### <a name="multi-account-applications"></a>Více účet aplikace

Tato aplikace je vytvořená pro scénář jeden účet. Knihovna MSAL také podporuje scénáře více účtů, ale vyžaduje další úkony z aplikací. Je potřeba vytvořit uživatelské rozhraní umožňující uživateli na výběru účtu, který chce použít pro každou akci, která vyžaduje tokeny. Aplikace můžete alternativně implementovat heuristiku k výběru účtu, který se má použít prostřednictvím `getAccounts()` metody.

## <a name="test-your-app"></a>Testování aplikace

### <a name="run-locally"></a>Spuštění v místním prostředí

Sestavení a nasazení aplikace do testovacího zařízení nebo emulátoru. Byste měli být schopni se přihlásit a získat tokeny pro službu Azure AD nebo osobní účty Microsoft.

Po přihlášení, zobrazí data vrácená z Microsoft Graphu `/me` koncového bodu.

### <a name="consent"></a>Vyjádření souhlasu

Prvním libovolný uživatel přihlásí do vaší aplikace, uživatelé budou vyzváni k pomocí Microsoft identity souhlas oprávnění požadovaná.  Zatímco většina uživatelů jsou schopny vyjadřuje, zakázali několik tenantů Azure AD souhlas uživatele, který vyžaduje souhlas jménem všech uživatelů správce. Pro podporu tohoto scénáře, zaregistrujte obory vaší aplikace na webu Azure Portal.

## <a name="get-help"></a>Podpora

Navštivte [Nápověda a podpora](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) Pokud máte potíže s tímto kurzem, nebo s platformou identity Microsoft.
