---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: eded2d6a9f2c270a2b3ccca296277b0a016733fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175694"
---
1. Otevřete projekt v Android Studiu.

2. V **Průzkumníkovi Projektu** v `ToDoActivity.java` Android Studiu otevřete soubor a přidejte následující příkazy importu:

    ```java
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.atomic.AtomicBoolean;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
    ```

3. Do třídy **ToDoActivity** přidejte následující metodu:

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

    Tento kód vytvoří metodu pro zpracování procesu ověřování Google. V dialogovém okně se zobrazí ID ověřeného uživatele. Můžete pokračovat pouze na úspěšné ověření.

    > [!NOTE]
    > Pokud používáte jiného poskytovatele identity než Google, změňte hodnotu předanou metodě **přihlášení** na jednu z následujících hodnot: _MicrosoftAccount_, _Facebook_, _Twitter_nebo _windowsazureactivedirectory_.

4. V **onCreate** metoda přidejte následující řádek kódu za kód, `MobileServiceClient` který konkretizovat objekt.

    ```java
    authenticate();
    ```

    Toto volání spustí proces ověřování.

5. Přesuňte zbývající kód `authenticate();` po v **onCreate** metoda na novou **metodu createTable:**

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

6. Chcete-li zajistit, aby přesměrování fungovalo podle `RedirectUrlActivity` očekávání, přidejte následující úryvek do `AndroidManifest.xml`:

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

7. `redirectUriScheme` Přidejte `build.gradle` do aplikace pro Android.

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

8. Přidat `com.android.support:customtabs:23.0.1` k závislostem `build.gradle`ve vašem :

    ```gradle
    dependencies {
        // ...
        compile 'com.android.support:customtabs:23.0.1'
    }
    ```

9. V nabídce **Spustit** klikněte na **Spustit aplikaci,** abyste aplikaci spustili a přihlásili se pomocí vybraného poskytovatele identity.

> [!WARNING]
> Uvedené schéma adres URL rozlišuje malá a velká písmena. Ujistěte se, `{url_scheme_of_you_app}` že všechny výskyty použití stejného případu.

Když jste úspěšně přihlášeni, aplikace by měla běžet bez chyb a měli byste být schopni dotazovat back-endové služby a provádět aktualizace dat.
