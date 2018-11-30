---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: deb94cab97bd9a402676cdc5c0239da8d07ed8b2
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440059"
---
Předchozí příklad ukázal standardní přihlášení, které vyžaduje klient kontaktovat zprostředkovatele identity a back-end služeb Azure při každém spuštění aplikace. Tato metoda je neefektivní a může mít související s problémy, pokud mnoho zákazníků, pokuste se spustit aplikaci současně. Lepším řešením je ukládat do mezipaměti autorizační token vrácený služby Azure a zkuste použít první před použitím u založené na zprostředkovatele přihlášení.

> [!NOTE]
> Můžete ukládat do mezipaměti u tokenu vydaného službou back-end služby Azure bez ohledu na to, jestli používáte ověřování klienta spravovat nebo spravované služby. Tento kurz používá spravovaný službou ověřování.
>
>

1. Otevřete souboru ToDoActivity.java a přidejte následující příkazy pro import:

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. Přidat následující členy do `ToDoActivity` třídy.

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. V souboru ToDoActivity.java přidejte následující definice `cacheUserToken` metody.

    ```java
    private void cacheUserToken(MobileServiceUser user)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        Editor editor = prefs.edit();
        editor.putString(USERIDPREF, user.getUserId());
        editor.putString(TOKENPREF, user.getAuthenticationToken());
        editor.commit();
    }
    ```

    Tato metoda ukládá ID uživatele a token v souboru předvoleb, který je označen jako privátní. To by měla chránit přístup k mezipaměti tak, aby ostatní aplikace na zařízení nebudou mít přístup k tokenu. Předvolba je pro aplikace v izolovaném prostoru. Pokud někdo získá přístup k zařízení, je však možné, že může získat přístup k mezipaměti tokenů jinými způsoby.

   > [!NOTE]
   > Token se šifrováním, jde dál chránit, pokud token přístupu k datům se považuje za vysoce citlivá a někdo může získat přístup k zařízení. Zcela zabezpečené řešení je nad rámec tohoto návodu, ale a závisí na vaše požadavky na zabezpečení.
   >
   >

4. V souboru ToDoActivity.java přidejte následující definice `loadUserTokenCache` metody.

    ```java
    private boolean loadUserTokenCache(MobileServiceClient client)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        String userId = prefs.getString(USERIDPREF, null);
        if (userId == null)
            return false;
        String token = prefs.getString(TOKENPREF, null);
        if (token == null)
            return false;

        MobileServiceUser user = new MobileServiceUser(userId);
        user.setAuthenticationToken(token);
        client.setCurrentUser(user);

        return true;
    }
    ```

5. V *ToDoActivity.java* souboru, nahradí `authenticate` a `onActivityResult` metody s následující dotazy, které používá mezipaměť tokenu. Změňte na zprostředkovatele přihlášení, pokud chcete použít jiný než Google.

    ```java
    private void authenticate() {
        // We first try to load a token cache if one exists.
        if (loadUserTokenCache(mClient))
        {
            createTable();
        }
        // If we failed to load a token cache, sign in and create a token cache
        else
        {
            // Sign in using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the sign-in request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    cacheUserToken(mClient.getCurrentUser());
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

6. Vytvářejte aplikace a testování ověřování pomocí platného účtu. Spusťte alespoň dvakrát. Při prvním spuštění mělo by se zobrazit výzva k přihlášení a vytvoření mezipamětí tokenů. Potom každé spuštění pokusí načíst mezipaměť tokenu pro ověření. By neměl muset přihlásit.
