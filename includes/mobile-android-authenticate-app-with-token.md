---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: deb94cab97bd9a402676cdc5c0239da8d07ed8b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175695"
---
V předchozím příkladu se ukázalo standardní přihlášení, které vyžaduje, aby klient kontaktovat zprostředkovatele identity a back-end služby Azure při každém spuštění aplikace. Tato metoda je neefektivní a můžete mít problémy související s využitím, pokud mnoho zákazníků se pokusí spustit aplikaci současně. Lepším přístupem je ukládat autorizační token vrácený službou Azure do mezipaměti a zkuste to použít nejprve před použitím přihlášení na základě zprostředkovatele.

> [!NOTE]
> Token vydaný back-endovou službou Azure můžete ukládat do mezipaměti bez ohledu na to, jestli používáte ověřování spravované klientem nebo službou. Tento kurz používá ověřování spravované službou.
>
>

1. Otevřete soubor ToDoActivity.java a přidejte následující příkazy importu:

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. Přidejte do třídy `ToDoActivity` následující členy.

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. Do souboru ToDoActivity.java přidejte následující `cacheUserToken` definici metody.

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

    Tato metoda ukládá ID uživatele a token v souboru předvoleb, který je označen jako soukromý. To by mělo chránit přístup ke mezipaměti tak, aby ostatní aplikace v zařízení nemají přístup k tokenu. Předvolba je pro aplikaci v izolovaném prostoru. Pokud však někdo získá přístup k zařízení, je možné, že může získat přístup k mezipaměti tokenů jinými prostředky.

   > [!NOTE]
   > Token můžete dále chránit šifrováním, pokud je přístup k vašim datům považován za vysoce citlivý a někdo může získat přístup k zařízení. Zcela zabezpečené řešení je však nad rámec tohoto kurzu a závisí na vašich požadavcích na zabezpečení.
   >
   >

4. Do souboru ToDoActivity.java přidejte následující `loadUserTokenCache` definici metody.

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

5. V souboru *ToDoActivity.java* `authenticate` nahraďte metody a `onActivityResult` následujícími metodami, které používají mezipaměť tokenů. Pokud chcete použít jiný účet než Google, změňte poskytovatele přihlášení.

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

6. Vytvořte aplikaci a otestujte ověřování pomocí platného účtu. Projej to alespoň dvakrát. Během prvního spuštění byste měli obdržet výzvu k přihlášení a vytvoření mezipaměti tokenů. Poté se každé spuštění pokusí načíst mezipaměť tokenů pro ověřování. Neměli byste se přihlašovat.
