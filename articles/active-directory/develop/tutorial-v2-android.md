---
title: Přihlášení uživatelů k & volání microsoft graphu (Android) – platforma identit Microsoftu | Azure
description: Získejte přístupový token a volejte Microsoft Graph nebo rozhraní API, které vyžadují přístupové tokeny z platformy identit microsoftu (Android)
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
ms.openlocfilehash: 5c8bd5accefceee042601c3cf7d71f5e9131e04e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880818"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-application"></a>Kurz: Přihlášení uživatelů a volání Microsoft Graphu z aplikace pro Android 

>[!NOTE]
>Tento kurz ukazuje zjednodušené příklady, jak pracovat s MSAL pro Android. Pro jednoduchost tento kurz používá pouze režim jednoho účtu. Můžete také zobrazit repo a klonovat [předkonfigurované ukázkové aplikace](https://github.com/Azure-Samples/ms-identity-android-java/) prozkoumat složitější scénáře. Další informace o ukázkové aplikaci, konfiguraci a registraci naléháte na úvodní [matné](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-android) úvodní obrazovce. 

V tomto kurzu se dozvíte, jak integrovat aplikaci pro Android s platformou identit microsoftu pomocí Knihovny ověřování Microsoft pro Android. Dozvíte se, jak se přihlásit a odhlásit uživatele, získat přístupový token pro volání rozhraní Microsoft Graph API a požádat o rozhraní Graph API. 

> [!div class="checklist"]
> * Integrace aplikace pro Android s platformou Microsoft Identity Platform 
> * Přihlášení uživatele 
> * Získání přístupového tokenu pro volání rozhraní Microsoft Graph API 
> * Volání rozhraní Microsoft Graph API 
> * Odhlášení uživatele 

Po dokončení tohoto kurzu bude vaše aplikace přijímat přihlášení k osobním účtům Microsoft (včetně outlook.com, live.com a dalších) a také pracovních nebo školních účtů od jakékoli společnosti nebo organizace, která používá Azure Active Directory.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="how-this-tutorial-works"></a>Jak tento výukový program funguje

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Aplikace v tomto kurzu se přihlásí uživatele a získat data jejich jménem. Tato data budou přístupná prostřednictvím chráněného rozhraní API (Microsoft Graph API), které vyžaduje autorizaci a je chráněno platformou identit microsoftu.

A konkrétně:

* Vaše aplikace se přihlásí uživatele buď prostřednictvím prohlížeče nebo Microsoft Authenticator a Intune Portál společnosti.
* Koncový uživatel přijme oprávnění, která vaše aplikace požadovala.
* Vaší aplikaci bude vydán přístupový token pro rozhraní Microsoft Graph API.
* Přístupový token bude součástí požadavku HTTP do webového rozhraní API.
* Zpracujte odpověď aplikace Microsoft Graph.

Tato ukázka používá knihovnu Microsoft Authentication pro Android (MSAL) k implementaci ověřování: [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 MSAL automaticky obnoví tokeny, doručuje jednotné přihlašování (SSO) mezi ostatními aplikacemi v zařízení a spravuje účet(y).

### <a name="prerequisites"></a>Požadavky

* Tento výukový program vyžaduje Android Studio verze 3.5+

## <a name="create-a-project"></a>Vytvoření projektu
Pokud ještě nemáte aplikaci pro Android, postupujte podle následujících kroků k nastavení nového projektu. 

1. Otevřete Android Studio a vyberte **Spustit nový projekt Android Studio**.
2. Vyberte **Základní aktivita** a vyberte **Další**.
3. Pojmenujte svoji aplikaci.
4. Uložte název balíčku. Později ji zadáte na portál Azure.
5. Změňte jazyk z **Kotlin** na **Java**.
6. Nastavte **minimální úroveň rozhraní API** na **rozhraní API 19** nebo vyšší a klepněte na tlačítko **Dokončit**.
7. V zobrazení projektu zvolte **Project** v rozevírací části, chcete-li zobrazit zdrojové a `28`nezdrojové soubory projektu, otevřít **aplikaci/build.gradle** a nastavit `targetSdkVersion` na .

## <a name="integrate-with-microsoft-authentication-library"></a>Integrace s knihovnou ověřování microsoftu 

### <a name="register-your-application"></a>Registrace vaší aplikace

1. Přejděte na [portál Azure](https://aka.ms/MobileAppReg).
2. Otevřete [okno Registrace aplikací](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) a klikněte na **+Nová registrace**.
3. Zadejte **název** aplikace a **bez** nastavení identifikátoru URI přesměrování klikněte na **Registrovat**.
4. V části **Spravovat** v podokně, které se zobrazí, vyberte **Authentication** > **+ Add a platform** > **Android**. (Možná budete muset vybrat "Přepnout na nové prostředí" v horní části okna, abyste viděli tuto část)
5. Zadejte název balíčku projektu. Pokud jste kód stáhli, `com.azuresamples.msalandroidapp`je tato hodnota .
6. V části **Hodnotit hodnotu hash podpisu** na stránce **Konfigurace aplikace pro Android** klikněte na Generování **hash podpisu vývoje.** a zkopírujte příkaz KeyTool pro vaši platformu.

   > [!Note]
   > KeyTool.exe je nainstalován jako součást Java Development Kit (JDK). Chcete-li spustit příkaz KeyTool, musíte také nainstalovat nástroj OpenSSL. Další informace naleznete v dokumentaci k [systému Android o generování klíče.](https://developer.android.com/studio/publish/app-signing#generate-key) 

7. Zadejte **hodnotu hash podpisu** vygenerovanou nástrojem KeyTool.
8. Klikněte `Configure` a uložte **konfiguraci MSAL,** která se zobrazí na **konfigurační** stránce Androidu, abyste ji mohli zadat při pozdější konfiguraci aplikace.  Klikněte na **Done** (Hotovo).

### <a name="configure-your-application"></a>Konfigurace aplikace 

1. V podokně projektů aplikace Android Studio přejděte na **app\src\main\res**.
2. Klepněte pravým tlačítkem myši na **položku Res** a zvolte **Nový** > **adresář**. Zadejte `raw` jako nový název adresáře a klepněte na tlačítko **OK**.
3. V **aplikaci** > **src** > **hlavní** > **res** > **raw**, `auth_configbn_single_account.json` vytvořte nový soubor JSON s názvem a vložte konfiguraci MSAL, kterou jste uložili dříve. 

    Pod identifikátor URI přesměrování vložte: 
    ```json
      "account_mode" : "SINGLE",
    ```
    Konfigurační soubor by měl vypadat takto: 
    ```json   
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
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
   >Tento kurz ukazuje pouze, jak nakonfigurovat aplikaci v režimu jednoho účtu. Další informace o [režimu jednoho vs. více účtu](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account) a [konfiguraci aplikace nawebují dokumentaci.](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration)
   
4. V **aplikaci** > **src** > **hlavní** > **AndroidManifest.xml**, přidejte aktivitu `BrowserTabActivity` níže do těla aplikace. Tato položka umožňuje společnosti Microsoft volat zpět do vaší aplikace po dokončení ověřování:

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

    `android:host=` Nahraďte hodnotu názvem balíčku, který jste zaregistrovali na portálu Azure.
    Nahraďte hodnotu hodnotou hodnotu `android:path=` hodnotou hodnotu hodnotou hodnotu hodnotou hodnoty pomocí hodnoty hodnoty hash klíče, který jste zaregistrovali na portálu Azure. Algoritmus hash podpisu by **neměl** být kódován adresou URL. Ujistěte se, `/` že je vedoucí na začátku vašeho podpisu hash. 
    >[!NOTE]
    >"Název balíčku", který `android:host` nahradíte hodnotou, by měl vypadat podobně jako: "com.azuresamples.msalandroidapp" "Signature Hash", který nahradíte, `android:path` by měl vypadat podobně jako: "/1wIqXSqBj7w+h11ZifsnqwgyKrY=" Tyto hodnoty budete moci také najít v okně Ověřování registrace aplikace. Všimněte si, že identifikátor URI přesměrování bude vypadat podobně jako: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". Zatímco hodnota hash podpisu je kódována na konci této hodnoty, hodnota hash `android:path` podpisu by **neměla** být kódována ve vaší hodnotě. 

## <a name="use-msal"></a>Použití funkce MSAL 

### <a name="add-msal-to-your-project"></a>Přidání msal do projektu

1. V okně projektu Android Studio přejděte na **aplikaci** > **src** > **build.gradle** a přidejte následující: 

    ```gradle
    repositories{
        jcenter()
    }  
    dependencies{
        implementation 'com.microsoft.identity.client:msal:1.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version") 
    }
    ```
    [Další informace o sadě Microsoft Graph SDK](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Požadované importy 

Přidejte následující do horní části **aplikace** > **src** > **hlavní**> **java** > **com.example(yourapp)** > **MainActivity.java** 

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

## <a name="instantiate-publicclientapplication"></a>Vytvořit instanci aplikace PublicClientApplication
#### <a name="initialize-variables"></a>Inicializovat proměnné 
```java
private final static String[] SCOPES = {"File.Read"};
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

### <a name="oncreate"></a>přiVytvořit
Uvnitř `MainActivity` třídy, naleznete následující onCreate() metoda k vytvoření instance `SingleAccountPublicClientApplication`MSAL pomocí .

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

### <a name="initializeui"></a>inicializovatUu
Poslouchejte tlačítka a metody volání nebo protokolovat chyby odpovídajícím způsobem. 
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
> Odhlášení pomocí služby MSAL odebere všechny známé informace o uživateli z aplikace, ale uživatel bude mít stále aktivní relaci na svém zařízení. Pokud se uživatel pokusí znovu přihlásit, může se mu zobrazit uživatelské rozhraní pro přihlášení, ale nemusí být nutné znovu zadat svá pověření, protože relace zařízení je stále aktivní. 

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

Následující kód ukazuje, jak volat GraphAPI pomocí sady SDK grafu. 

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

## <a name="add-ui"></a>Přidat ui
### <a name="activity"></a>Aktivita 
Pokud chcete modelovat vaše ui z tohoto kurzu, následující metody poskytují návod k aktualizaci textu a poslechu tlačítek.

#### <a name="updateui"></a>aktualizaceUI
Povolte nebo zakažte tlačítka na základě stavu přihlášení a nastavte text.  
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
#### <a name="displayerror"></a>chyba_
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
Metoda aktualizace textu v ui tak, aby odráželodhlásit. 

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Rozložení 

Ukázkový `activity_main.xml` soubor pro zobrazení tlačítek a textových polí. 

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

Vytvořte a nasaďte aplikaci do testovacího zařízení nebo emulátoru. Měli byste být schopni se přihlásit a získat tokeny pro Azure AD nebo osobní účty Microsoft.

Po přihlášení se v aplikaci zobrazí data vrácená z koncového bodu Microsoft Graphu. `/me`

### <a name="consent"></a>Souhlasu

Při prvním přihlášení uživatele do vaší aplikace bude identita Microsoftu vyzvána k souhlasu s požadovanými oprávněními. Někteří klienti Azure AD zakázali souhlas uživatelů, což vyžaduje, aby správci souhlasili jménem všech uživatelů. Chcete-li tento scénář podpořit, budete muset buď vytvořit vlastního tenanta nebo získat souhlas správce. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, odstraňte objekt aplikace, který jste vytvořili v kroku [Registrace aplikace.](#register-your-application)

## <a name="get-help"></a>Podpora

Pokud máte potíže s tímto kurzem nebo s platformou identit microsoftu, navštivte [nápovědu a podporu.](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)

Pomozte nám vylepšit platformu identit Microsoftu. Řekněte nám, co si myslíte, že dokončení krátké hod-průzkum u dvou otázek.

> [!div class="nextstepaction"]
> [Průzkum platformy identity Microsoftu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
