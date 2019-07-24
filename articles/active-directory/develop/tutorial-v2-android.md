---
title: Začínáme s Androidem – Microsoft Identity Platform | Azure
description: Jak aplikace pro Android získá přístupový token a zavolá Microsoft Graph rozhraní API nebo rozhraní API, které vyžadují přístupové tokeny od platformy Microsoft Identity Platform.
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
ms.openlocfilehash: 5bd19b6094d68277130916b5cda565ba9e633c59
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334120"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Přihlaste se uživatelům a zavolejte Microsoft Graph z aplikace pro Android.

V tomto kurzu se dozvíte, jak integrovat aplikaci pro Android s platformou Microsoft identity. Vaše aplikace se přihlásí k uživateli, získá přístupový token pro volání rozhraní API Microsoft Graph a vytvoří požadavek na rozhraní Microsoft Graph API.  

Po dokončení průvodce bude aplikace přijímat přihlašovacíky osobních účtů Microsoft (včetně outlook.com, live.com a dalších) a pracovních nebo školních účtů z jakékoli společnosti nebo organizace, která používá Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Jak tento kurz funguje

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Aplikace v tomto kurzu se bude přihlašovat uživatelům a získat data jménem.  Tato data budou k dispozici prostřednictvím chráněného rozhraní API (Microsoft Graph API), které vyžaduje autorizaci a jsou chráněny platformou Microsoft identity.

A konkrétně:

* Vaše aplikace se přihlásí k uživateli přes prohlížeč nebo Microsoft Authenticator a Portál společnosti Intune.
* Koncový uživatel bude akceptovat oprávnění, která vaše aplikace požadovala.
* Vaše aplikace se vydá přístupový token pro rozhraní Microsoft Graph API.
* Přístupový token bude součástí požadavku HTTP webovému rozhraní API.
* Zpracujte odpověď Microsoft Graph.

Tato ukázka používá knihovnu Microsoft Authentication Library pro Android (MSAL) k implementaci ověřování: [com. Microsoft. identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 MSAL bude automaticky obnovovat tokeny, poskytovat jednotné přihlašování (SSO) mezi ostatními aplikacemi na zařízení a spravovat účty.

## <a name="prerequisites"></a>Požadavky

* Tento kurz vyžaduje Android Studio verzi 16 nebo novější (doporučuje se 19 +).

## <a name="create-a-project"></a>Vytvoření projektu

V tomto kurzu se vytvoří nový projekt. Pokud chcete stáhnout dokončený kurz místo toho, [Stáhněte si kód](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

1. Otevřete Android Studio a vyberte **Spustit nový Android Studio projekt**.
2. Vyberte možnost **základní aktivita** a vyberte možnost **Další**.
3. Pojmenujte svoji aplikaci.
4. Uložte název balíčku. Později ji zadáte do Azure Portal.
5. Nastavte **minimální úroveň rozhraní API** na **rozhraní API 19** nebo vyšší a klikněte na **Dokončit**.
6. V zobrazení projektu vyberte v rozevíracím seznamu **projekt** a zobrazte zdrojové a nezdrojové soubory projektu, otevřete **App/Build. Gradle** a nastavte `targetSdkVersion` na `27`.

## <a name="register-your-application"></a>Registrace vaší aplikace

1. Přejděte na [Azure Portal](https://aka.ms/MobileAppReg).
2. Otevřete okno [Registrace aplikací](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) a klikněte na **+ Nová registrace**.
3. Zadejte **název** vaší aplikace a pak bez nastavení identifikátoru URI přesměrování klikněte na **zaregistrovat**.
4. V části **Spravovat** v podokně, které se zobrazí, vyberte **ověřování** >  **+ Přidat platformu** > **Android**.
5. Zadejte název balíčku vašeho projektu. Pokud jste kód stáhli, je `com.azuresamples.msalandroidapp`tato hodnota.
6. V části **Signature hash** na stránce **Konfigurace aplikace pro Android** klikněte na vygenerovat **hodnotu hash signatury pro vývoj.** a zkopírujte příkaz nástroje nástroje, který se má použít pro vaši platformu.

   > [!Note]
   > Nástroj Tool. exe je nainstalován jako součást sady Java Development Kit (JDK). Je také nutné nainstalovat nástroj OpenSSL pro spuštění příkazu nástroje.

7. Zadejte **hodnotu hash podpisu** generovanou nástrojem.
8. Klikněte `Configure` na a uložte **konfiguraci MSAL** , která se zobrazí na stránce **Konfigurace Androidu** , abyste ji mohli zadat při pozdější konfiguraci aplikace.  Klikněte na **Done** (Hotovo).

## <a name="build-your-app"></a>Sestavení aplikace

### <a name="add-your-app-registration"></a>Přidání registrace aplikace

1. V podokně projektu Android Studio přejděte na **app\src\main\res**.
2. Klikněte pravým tlačítkem na položku **res** a vyberte možnost **Nový** > **adresář**. Jako `raw` název nového adresáře zadejte a klikněte na **OK**.
3. Včásti**zdrojový zdroj**  >  >  `auth_config.json`aplikaceRAW vytvořte nový soubor JSON s názvem a vložte konfiguraci MSAL, kterou jste předtím uložili. >  Další informace najdete v tématu [Konfigurace MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
4. V **části app** > **Src** > **Main** **souboru AndroidManifest.** XML přidejte`BrowserTabActivity`aktivituníže.  >  Tato položka umožňuje, aby Microsoft po dokončení ověřování vrátil zpět do vaší aplikace:

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

    Pro tuto `android:host=` hodnotu nahraďte název balíčku, který jste zaregistrovali v Azure Portal.
    `android:path=` Hodnotu hash klíče, kterou jste zaregistrovali v Azure Portal, nahraďte hodnotou. Hodnota hash podpisu by neměla být kódovaná v adrese URL.

5. V **souboru souboru AndroidManifest. XML**těsně nad `<application>` značkou přidejte následující oprávnění:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

### <a name="create-the-apps-ui"></a>Vytvoření uživatelského rozhraní aplikace

1. V okně Android Studio projektu přejděte do **App** > **Src** > **Main** > **res** > **layout** a otevřete **activity_main. XML** a otevřete **text.** zobrazit.
2. Změňte rozložení aktivity, například: `<androidx.coordinatorlayout.widget.CoordinatorLayout` na. `<androidx.coordinatorlayout.widget.LinearLayout`
3. `android:orientation="vertical"` Přidejte vlastnost`LinearLayout` do uzlu.
4. Vložte následující kód do `LinearLayout` uzlu a nahraďte aktuální obsah:

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

### <a name="add-msal-to-your-project"></a>Přidání MSAL do projektu

1. V okně Android Studio projektu přejděte do **App** > **Src** > **Build. Gradle**.
2. V části **závislosti**vložte následující položky:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>Použití MSAL

Nyní proveďte změny v `MainActivity.java` rámci přidávání a používání MSAL ve vaší aplikaci.
V okně Android Studio projektu přejděte do **App** > **Src** > **Main** >  `MainActivity.java`**Java** > **com. example. msal**a otevřete.

#### <a name="required-imports"></a>Požadované importy

Přidejte následující importy poblíž horního okraje `MainActivity.java`:

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

#### <a name="instantiate-msal"></a>Vytvoření instance MSAL

V rámci `MainActivity` třídy budeme muset vytvořit instanci MSAL spolu s několika konfiguracemi o tom, co aplikace probíhají, včetně oborů a webového rozhraní API, které chceme mít přístup.

Zkopírujte do `MainActivity` třídy následující proměnné:

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

Chcete-li vytvořit `onCreate()` instanci MSAL, nahraďte obsah následujícím kódem:

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

Výše uvedený kód se pokusí uživatele v tichém režimu přihlašovat při otevření vaší aplikace `getAccounts()` prostřednictvím a, pokud jsou `acquireTokenSilentAsync()`úspěšné.  V následujících několika částech implementujeme obslužnou rutinu zpětného volání pro případ, že nejsou k dispozici žádné přihlášené účty.

#### <a name="use-msal-to-get-tokens"></a>Získání tokenů pomocí MSAL

Nyní můžeme implementovat logiku zpracování uživatelského rozhraní aplikace a interaktivně získat tokeny prostřednictvím MSAL.

MSAL zpřístupňuje dvě primární metody získání tokenů `acquireTokenSilentAsync()` : `acquireToken()`a.  

`acquireTokenSilentAsync()`přihlásí uživatele a získá tokeny bez zásahu uživatele, pokud je přítomen účet. Pokud to bude úspěšné, MSAL přepošle tokeny do vaší aplikace, pokud selže, vygeneruje `MsalUiRequiredException`se.  Pokud je tato výjimka vygenerována nebo chcete, aby uživatel měl interaktivní přihlašovací prostředí (přihlašovací údaje, MFA nebo jiné zásady podmíněného přístupu), pak použijte `acquireToken()`.  

`acquireToken()`zobrazí uživatelské rozhraní při pokusu o přihlášení uživatele a získání tokenů. K zajištění interaktivního přihlášení k jednotnému přihlašování ale může používat soubory cookie relace v prohlížeči nebo účet v Microsoft Authenticator.

V rámci `MainActivity` třídy vytvořte následující tři metody uživatelského rozhraní:

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

Přidejte následující metody pro získání aktuální aktivity a procesu tichého zpětného volání & interaktivní:

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

Dále přidejte podporu pro odhlášení.

> [!Important]
> Odhlášení pomocí MSAL odebere všechny známé informace o uživateli z aplikace, ale uživatel bude na svém zařízení mít stále aktivní relaci. Pokud se uživatel pokusí přihlásit znovu, může se jim zobrazit přihlašovací uživatelské rozhraní, ale nemusí znovu zadávat svoje přihlašovací údaje, protože relace zařízení je stále aktivní.

Chcete-li přidat možnost odhlášení, přidejte do `MainActivity` třídy následující metodu. Tato metoda cyklicky projde všechny účty a odebere je:

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

#### <a name="call-the-microsoft-graph-api"></a>Volání rozhraní API pro Microsoft Graph

Jakmile obdržíte token, můžeme na [rozhraní Microsoft Graph API](https://graph.microsoft.com) vytvořit žádost, že přístupový token bude uvnitř `AuthenticationResult` `onSuccess()` metody zpětného volání ověřování. Abyste mohli vytvořit autorizovaný požadavek, vaše aplikace bude muset přidat přístupový token do hlavičky HTTP:

| klíč záhlaví    | value                 |
| ------------- | --------------------- |
| Authorization | \<Přístup k > nosných tokenů |

Přidejte následující dvě metody uvnitř `MainActivity` třídy pro volání grafu a aktualizujte uživatelské rozhraní:

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

#### <a name="multi-account-applications"></a>Aplikace s více účty

Tato aplikace je vytvořená pro scénář s jedním účtem. MSAL podporuje také scénáře s více účty, ale vyžaduje další práci z aplikací. Budete muset vytvořit uživatelské rozhraní, které pomůže uživateli vybrat, který účet mají použít pro každou akci, která vyžaduje tokeny. Alternativně může vaše aplikace implementovat heuristiku pro výběr účtu, který se má použít `getAccounts()` prostřednictvím metody.

## <a name="test-your-app"></a>Testování aplikace

### <a name="run-locally"></a>Spuštění v místním prostředí

Sestavte a nasaďte aplikaci do testovacího zařízení nebo emulátoru. Měli byste být schopni se přihlásit a získat tokeny pro účty Azure AD nebo osobní účty Microsoft.

Po přihlášení aplikace zobrazí data vrácená z Microsoft Graphho `/me` koncového bodu.

### <a name="consent"></a>Souhlas

Když se uživatel poprvé přihlásí do vaší aplikace, zobrazí se mu výzva společnosti Microsoft pro vyjádření souhlasu s požadovanými oprávněními.  I když se většina uživatelů může zasílat, někteří klienti Azure AD mají zakázaný souhlas s uživatelem, který vyžaduje, aby správci souhlasí jménem všech uživatelů. Pro podporu tohoto scénáře Zaregistrujte obory aplikace v Azure Portal.

## <a name="get-help"></a>Podpora

Pokud máte potíže s tímto kurzem nebo s platformou Microsoft identity, přejděte na [pomoc a podpora](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) .

Pomůžeme nám vylepšit platformu Microsoft identity. Řekněte nám, co si myslíte, díky krátkému průzkumu dvou dotazů.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform Survey](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)