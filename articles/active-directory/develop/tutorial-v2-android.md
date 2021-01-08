---
title: 'Kurz: Vytvoření aplikace pro Android, která používá Microsoft Identity Platform pro ověřování | Azure'
titleSuffix: Microsoft identity platform
description: V tomto kurzu vytvoříte aplikaci pro Android, která pomocí platformy Microsoft Identity přihlašuje uživatele a získá přístupový token pro volání rozhraní API Microsoft Graph jménem.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 746d4ac2d346d67b5ca796c19c799f654715acc4
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013579"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-application"></a>Kurz: přihlášení uživatelů a volání rozhraní Microsoft Graph API z aplikace pro Android

V tomto kurzu vytvoříte aplikaci pro Android, která se integruje s platformou Microsoft identity k přihlašování uživatelů a získá přístupový token pro volání rozhraní API Microsoft Graph.

Po dokončení tohoto kurzu bude vaše aplikace přijímat přihlašování osobních účtů Microsoft (včetně outlook.com, live.com a dalších) a také pracovních nebo školních účtů z jakékoli společnosti nebo organizace, která používá Azure Active Directory.

V tomto kurzu: 

> [!div class="checklist"]
> * Vytvoření projektu aplikace pro Android v *Android Studio*
> * Registrace aplikace v Azure Portal
> * Přidat kód pro podporu přihlášení a odhlášení uživatele
> * Přidat kód pro volání rozhraní Microsoft Graph API
> * Otestování aplikace

## <a name="prerequisites"></a>Požadavky

* Android Studio 3.5 +

## <a name="how-this-tutorial-works"></a>Jak tento kurz funguje

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Aplikace v tomto kurzu se bude přihlašovat uživatelům a získat data jménem. Tato data budou k dispozici prostřednictvím chráněného rozhraní API (Microsoft Graph API), které vyžaduje autorizaci a jsou chráněny platformou Microsoft identity.

A konkrétně:

* Vaše aplikace se přihlásí k uživateli přes prohlížeč nebo Microsoft Authenticator a Portál společnosti Intune.
* Koncový uživatel bude akceptovat oprávnění, která vaše aplikace požadovala.
* Vaše aplikace se vydá přístupový token pro rozhraní Microsoft Graph API.
* Přístupový token bude součástí požadavku HTTP webovému rozhraní API.
* Zpracujte odpověď Microsoft Graph.

Tato ukázka používá knihovnu Microsoft Authentication Library pro Android (MSAL) k implementaci ověřování: [com. Microsoft. identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

MSAL bude automaticky obnovovat tokeny, poskytovat jednotné přihlašování (SSO) mezi ostatními aplikacemi na zařízení a spravovat účty.

> [!NOTE]
> Tento kurz ukazuje zjednodušené příklady práce s MSAL pro Android. Pro zjednodušení používá jenom režim jednoho účtu. Další informace o složitějších scénářích najdete na webu GitHub v ukázce dokončeného [funkčního kódu](https://github.com/Azure-Samples/ms-identity-android-java/) .

## <a name="create-a-project"></a>Vytvoření projektu
Pokud ještě nemáte aplikaci pro Android, postupujte podle těchto kroků a nastavte nový projekt.

1. Otevřete Android Studio a vyberte **Spustit nový Android Studio projekt**.
2. Vyberte možnost **základní aktivita** a vyberte možnost **Další**.
3. Pojmenujte svoji aplikaci.
4. Uložte název balíčku. Později ji zadáte do Azure Portal.
5. Změňte jazyk z **Kotlin** na **Java**.
6. Nastavte **minimální úroveň rozhraní API** na **rozhraní API 19** nebo vyšší a klikněte na **Dokončit**.
7. V zobrazení projektu vyberte v rozevíracím seznamu **projekt** a zobrazte zdrojové a nezdrojové soubory projektu, otevřete **App/Build. Gradle** a nastavte `targetSdkVersion` na `28` .

## <a name="integrate-with-microsoft-authentication-library"></a>Integrace s Microsoft Authentication Library

### <a name="register-your-application"></a>Registrace aplikace

1. Přihlaste se <a href="https://portal.azure.com/" target="_blank">k <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a>.
1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
1. Zadejte **název** vaší aplikace. Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
1. Vyberte **Zaregistrovat**.
1. V části **Spravovat** vyberte **ověřování**  >  **Přidat platformu**  >  **Android**.
1. Zadejte název balíčku vašeho projektu. Pokud jste kód stáhli, je tato hodnota `com.azuresamples.msalandroidapp` .
1. V části **hodnota hash podpisu** na stránce **Konfigurace aplikace pro Android** vyberte možnost **vygenerovat hodnotu hash signatury pro vývoj.** a zkopírujte příkaz nástroje nástroje, který se má použít pro vaši platformu.

   > [!Note]
   > KeyTool.exe je nainstalován jako součást sady Java Development Kit (JDK). Je také nutné nainstalovat nástroj OpenSSL pro spuštění příkazu nástroje. Další informace najdete v [dokumentaci k Androidu o generování klíče](https://developer.android.com/studio/publish/app-signing#generate-key) .

1. Zadejte **hodnotu hash podpisu** generovanou nástrojem.
1. Vyberte **Konfigurovat** a uložte **konfiguraci MSAL** , která se zobrazí na stránce **Konfigurace Androidu** , abyste ji mohli zadat při pozdější konfiguraci aplikace.  
1. Vyberte **Hotovo**.

### <a name="configure-your-application"></a>Konfigurace aplikace

1. V podokně projektu Android Studio přejděte na **app\src\main\res**.
1. Klikněte pravým tlačítkem na položku **res** a vyberte možnost **Nový**  >  **adresář**. `raw`Jako název nového adresáře zadejte a klikněte na **OK**.
1. V části **App**  >  **Src**  >  **Main**  >    >  **raw** vytvořte nový soubor JSON s názvem `auth_config_single_account.json` a vložte konfiguraci MSAL, kterou jste předtím uložili.

    Pod identifikátorem URI přesměrování vložte:
    ```json
      "account_mode" : "SINGLE",
    ```
    Konfigurační soubor by měl vypadat podobně jako v tomto příkladu:
    ```json
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "broker_redirect_uri_registered" : true,
      "account_mode" : "SINGLE",
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
   ```

   >[!NOTE]
   >Tento kurz ukazuje, jak nakonfigurovat aplikaci v režimu jednoho účtu. Podívejte se na dokumentaci, kde najdete další informace o [jednom nebo víc režimech účtů](./single-multi-account.md) a [konfiguraci aplikace](./msal-configuration.md) .

4. Do části **App**  >  **Src**  >  **Main**  >  **AndroidManifest.xml** přidejte `BrowserTabActivity` níže uvedenou aktivitu do těla aplikace. Tato položka umožňuje, aby Microsoft po dokončení ověřování vrátil zpět do vaší aplikace:

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

    Pro tuto hodnotu nahraďte název balíčku, který jste zaregistrovali v Azure Portal `android:host=` .
    Hodnotu hash klíče, kterou jste zaregistrovali v Azure Portal, nahraďte `android:path=` hodnotou. Hodnota hash **podpisu by neměla být kódovaná** v adrese URL. Zajistěte, aby na `/` začátku hodnoty hash podpisu existovala úvodní hodnota.
    >[!NOTE]
    >Název balíčku nahradíte hodnotou, kterou `android:host` by měl vypadat nějak takto: "com. azuresamples. msalandroidapp" signatura "hash", nahradíte `android:path` hodnotu parametr by měl vypadat nějak takto: "/1WIqXSqBj7w + h11ZifsnqwgyKrY =" tyto hodnoty bude možné najít v okně ověřování vaší registrace aplikace. Všimněte si, že váš identifikátor URI pro přesměrování bude vypadat nějak takto: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". I když je hodnota hash podpisu na konci této hodnoty zakódovaná, hodnota hash signatury by **neměla mít v** hodnotě kódování URL `android:path` .

## <a name="use-msal"></a>Použití MSAL

### <a name="add-msal-to-your-project"></a>Přidání MSAL do projektu

1. V okně Android Studio projektu přejděte do **App**  >  **Src**  >  **Build. Gradle** a přidejte následující:

    ```gradle
    repositories{
        jcenter()
    }
    dependencies{
        implementation 'com.microsoft.identity.client:msal:2.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version")
    }
    ```
    [Další informace o sadě Microsoft Graph SDK](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Požadované importy

Přidejte následující na začátek **aplikace**  >  **Src**  >  **hlavní** >  **Java**  >  **com. Příklad (yourapp)**  >  **MainActivity. Java**

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>Vytvoření instance PublicClientApplication
#### <a name="initialize-variables"></a>Inicializovat proměnné
```java
private final static String[] SCOPES = {"Files.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
V rámci `MainActivity` třídy, přečtěte si následující metodu Create () pro vytvoření instance MSAL pomocí `SingleAccountPublicClientApplication` .

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount

```java
//When app comes to the foreground, load existing account to determine if user is signed in
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
}
```

### <a name="initializeui"></a>initializeUI
Naslouchat tlačítkům a volat metody nebo protokolovat chyby.
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);

        //Sign in user
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });

        //Interactive
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> Odhlášení pomocí MSAL odebere všechny známé informace o uživateli z aplikace, ale uživatel bude na svém zařízení mít stále aktivní relaci. Pokud se uživatel pokusí přihlásit znovu, může se jim zobrazit přihlašovací uživatelské rozhraní, ale nemusí znovu zadávat svoje přihlašovací údaje, protože relace zařízení je stále aktivní.

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
Zpětné volání používané pro interaktivní požadavky.

```java
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
Zpětné volání používané pro tiché požadavky
```java
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Volání rozhraní API Microsoft Graphu

Následující kód ukazuje, jak volat GraphAPI pomocí sady Graph SDK.

### <a name="callgraphapi"></a>callGraphAPI

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>Přidat uživatelské rozhraní
### <a name="activity"></a>Aktivita
Pokud chcete vytvořit model uživatelského rozhraní mimo tento kurz, následující metody poskytují návod k aktualizaci textu a naslouchání tlačítkům.

#### <a name="updateui"></a>updateUI
Povolí nebo zakáže tlačítka na základě stavu přihlášení a nastavení textu.
```java
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
Metoda, která aktualizuje text v uživatelském rozhraní, aby odrážel odhlášení

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Rozložení

Vzorový `activity_main.xml` soubor pro zobrazení tlačítek a textových polí

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>Testování aplikace

### <a name="run-locally"></a>Spuštění v místním prostředí

Sestavte a nasaďte aplikaci do testovacího zařízení nebo emulátoru. Měli byste být schopni se přihlásit a získat tokeny pro účty Azure AD nebo osobní účty Microsoft.

Po přihlášení aplikace zobrazí data vrácená z Microsoft Graphho `/me` koncového bodu.
PR 4
### <a name="consent"></a>Souhlas

Když se uživatel poprvé přihlásí do vaší aplikace, zobrazí se mu výzva společnosti Microsoft pro vyjádření souhlasu s požadovanými oprávněními. Někteří klienti Azure AD mají zakázaný souhlas s uživatelem, který vyžaduje, aby správci měli souhlas jménem všech uživatelů. Pro podporu tohoto scénáře musíte buď vytvořit vlastního tenanta, nebo přijmout souhlas správce.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte objekt aplikace, který jste vytvořili v kroku [Registrace aplikace](#register-your-application) .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o vytváření mobilních aplikací, které volají chráněná webová rozhraní API v naší řadě scénářů s více částmi.

> [!div class="nextstepaction"]
> [Scénář: mobilní aplikace, která volá webová rozhraní API](scenario-mobile-overview.md)
