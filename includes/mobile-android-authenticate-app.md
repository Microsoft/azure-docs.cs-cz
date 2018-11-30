---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: eded2d6a9f2c270a2b3ccca296277b0a016733fd
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440078"
---
1. Otevřete projekt v nástroji Android Studio.

2. V **Project Exploreru** v nástroji Android Studio, otevřete `ToDoActivity.java` a přidejte následující příkazy pro import:

    ```java
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.atomic.AtomicBoolean;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
    ```

3. Přidejte následující metodu do **ToDoActivity** třídy:

    ```java
    // You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
    public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

    private void authenticate() {
        // Sign in using the Google provider.
        mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the login request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    createTable();
                } else {
                    // sign-in failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

    Tento kód vytvoří metodu ke zpracování procesu ověřování Google. Dialogové okno zobrazí ID ověřeného uživatele. Pouze můžete přejít na úspěšné ověřování.

    > [!NOTE]
    > Pokud používáte zprostředkovatelů identity než Google, změňte hodnotu předanou **přihlášení** metody do jednoho z následujících hodnot: _MicrosoftAccount_, _Facebook_, _Twitter_, nebo _windowsazureactivedirectory_.

4. V **onCreate** metodu, přidejte následující řádek kódu za kód, který vytvoří instanci `MobileServiceClient` objektu.

    ```java
    authenticate();
    ```

    Toto volání zahájí proces ověřování.

5. Přesunout zbývající kód po `authenticate();` v **onCreate** metoda do nového **createTable** metody:

    ```java
    private void createTable() {

        // Get the table instance to use.
        mToDoTable = mClient.getTable(ToDoItem.class);

        mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

        // Create an adapter to bind the items with the view.
        mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
        ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
        listViewToDo.setAdapter(mAdapter);

        // Load the items from Azure.
        refreshItemsFromTable();
    }
    ```

6. Chcete-li zajistit přesměrování funguje podle očekávání, přidejte následující fragment `RedirectUrlActivity` k `AndroidManifest.xml`:

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}"
                android:host="easyauth.callback"/>
        </intent-filter>
    </activity>
    ```

7. Přidat `redirectUriScheme` k `build.gradle` vaší aplikace pro Android.

    ```gradle
    android {
        buildTypes {
            release {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
            debug {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
        }
    }
    ```

8. Přidat `com.android.support:customtabs:23.0.1` závislostí ve vaší `build.gradle`:

    ```gradle
    dependencies {
        // ...
        compile 'com.android.support:customtabs:23.0.1'
    }
    ```

9. Z **spustit** nabídky, klikněte na tlačítko **spuštění aplikace** spusťte aplikaci a přihlaste se pomocí zprostředkovatele identity zvolená.

> [!WARNING]
> Schéma adresy URL uvedené rozlišuje velká a malá písmena. Ujistěte se, že všechny výskyty `{url_scheme_of_you_app}` velká a malá písmena.

Pokud jste úspěšně přihlášení, aplikace by měla běžet bez chyb a by měl mít provést aktualizace dat a dotazování na back-end službu.
