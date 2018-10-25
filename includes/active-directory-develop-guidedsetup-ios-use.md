---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 06da33b91ef9846204b33ba2cb3dea40c75d425d
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988244"
---
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Pomocí knihovny Microsoft Authentication Library (MSAL) k získání tokenu pro rozhraní Microsoft Graph API

Otevřít `ViewController.swift` a nahraďte kód:

```swift
import UIKit
import MSAL

// A View Controller that will respond to the events of the Storyboard.
class ViewController: UIViewController, UITextFieldDelegate, URLSessionDelegate {

    // Replace Your_Application_Id_Here with the client ID you received in the portal. The below is for running the demo only.
    let kClientID = "Your_Application_Id_Here"

    // These settings you don't need to edit unless you wish to attempt deeper scenarios with the app.
    let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
    let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
    let kAuthority = "https://login.microsoftonline.com/common"

    var accessToken = String()
    var applicationContext : MSALPublicClientApplication?

    @IBOutlet weak var loggingText: UITextView!
    @IBOutlet weak var signoutButton: UIButton!

    /**
        Setup public client application in viewDidLoad
    */

    override func viewDidLoad() {

        super.viewDidLoad()

        do {

            /**
             Initialize a MSALPublicClientApplication with a given clientID and authority
             - clientId:            The clientID of your application, you should get this from the app portal.
             - authority:           A URL indicating a directory that MSAL can use to obtain tokens. In Azure AD
                                    it is of the form https://<instance/<tenant>, where <instance> is the
                                    directory host (e.g. https://login.microsoftonline.com) and <tenant> is a
                                    identifier within the directory itself (e.g. a domain associated to the
                                    tenant, such as contoso.onmicrosoft.com, or the GUID representing the
                                    TenantID property of the directory)
             - error                The error that occurred creating the application object, if any, if you're
                                    not interested in the specific error pass in nil.
             */

            guard let authorityURL = URL(string: kAuthority) else {
                self.loggingText.text = "Unable to create authority URL"
                return
            }

            let authority = try MSALAuthority(url: authorityURL)
            self.applicationContext = try MSALPublicClientApplication(clientId: kClientID, authority: authority)

        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }

    override func viewWillAppear(_ animated: Bool) {

        super.viewWillAppear(animated)
        signoutButton.isEnabled = !self.accessToken.isEmpty
    }

    /**
     This button will invoke the authorization flow.
    */

    @IBAction func callGraphButton(_ sender: UIButton) {

        if self.currentAccount() == nil {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            self.acquireTokenInteractively()
        } else {
            self.acquireTokenSilently()
        }
    }

    func acquireTokenInteractively() {

        guard let applicationContext = self.applicationContext else { return }

        applicationContext.acquireToken(forScopes: kScopes) { (result, error) in

            if let error = error {

                self.updateLogging(text: "Could not acquire token: \(error)")
                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken!
            self.updateLogging(text: "Access token is \(self.accessToken)")
            self.updateSignoutButton(enabled: true)
            self.getContentWithToken()
        }
    }

    func acquireTokenSilently() {

        guard let applicationContext = self.applicationContext else { return }

        /**
         Acquire a token for an existing account silently
         - forScopes:           Permissions you want included in the access token received
                                in the result in the completionBlock. Not all scopes are
                                guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
                                authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
                                flow completes, or encounters an error.
         */

        applicationContext.acquireTokenSilent(forScopes: kScopes, account: self.currentAccount()) { (result, error) in

            if let error = error {

                let nsError = error as NSError

                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.
                if (nsError.domain == MSALErrorDomain
                    && nsError.code == MSALErrorCode.interactionRequired.rawValue) {

                    DispatchQueue.main.async {
                        self.acquireTokenInteractively()
                    }

                } else {
                    self.updateLogging(text: "Could not acquire token silently: \(error)")
                }

                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken!
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignoutButton(enabled: true)
            self.getContentWithToken()
        }
    }

    func currentAccount() -> MSALAccount? {

        guard let applicationContext = self.applicationContext else { return nil }

        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        do {

            let cachedAccounts = try applicationContext.allAccounts()

            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }

        } catch let error as NSError {

            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }

        return nil
    }
```

<!--start-collapse-->
### <a name="more-information"></a>Další informace

#### <a name="getting-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Volání `acquireToken` metoda výsledky v okně prohlížeče výzvy k přihlášení. Aplikace obvykle vyžadují uživateli umožní přihlásit interaktivně poprvé, které potřebují přístup k chráněnému prostředku, nebo při bezobslužném operaci získat token selže (třeba heslo uživatele s prošlou platností).

#### <a name="getting-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

`acquireTokenSilent` Obsluhovala token pořízení a obnovení bez nutnosti zásahu uživatele. Po `acquireToken` provádí poprvé, `acquireTokenSilent` je metoda běžně používá k získání tokenů použít pro přístup k chráněným prostředkům pro pozdější volání – jako volání na vyžádání nebo tokeny obnovení probíhají bezobslužně.

Nakonec `acquireTokenSilent` selže – například uživatel odhlásil nebo došlo ke změně hesla na jiném zařízení. Knihovna MSAL zjistí, že problém lze vyřešit tak, že vyžaduje interaktivní akci, vyvolá-li `MSALErrorCode.interactionRequired` výjimky. Vaše aplikace dokáže zpracovat tuto výjimku dvěma způsoby:

1. Volání proti `acquireToken` okamžitě, jehož výsledkem výzvy k přihlášení. Tento model se obvykle používá v online aplikace tam, kde není žádný offline obsah v aplikaci k dispozici pro uživatele. Tento instalační program s průvodcem vygenerovaná ukázková aplikace používá tento vzor: můžete pozorování v akci první čas spuštění aplikace. Vzhledem k tomu, že žádný uživatel nikdy nepoužil aplikace, `applicationContext.allAccounts().first` bude obsahovat hodnotu null a ` MSALErrorCode.interactionRequired ` , bude vyvolána výjimka. Kód v ukázce pak zpracovává výjimku při volání `acquireToken` výsledkem výzvy k přihlášení.

2. Aplikace lze také nastavit vizuální označení pro uživatele, který interaktivnímu přihlášení je nutné, takže uživatel může vybrat správný čas pro přihlášení, nebo aplikace může pokus zopakovat, `acquireTokenSilent` později. To se obvykle používá, když uživatel může používat další funkce aplikace bez narušení – například je offline obsah k dispozici v aplikaci. V takovém případě se uživatel může rozhodnout při chtějí přihlášení pro přístup k chráněnému prostředku nebo aktualizujte zastaralé informace, nebo aplikace se můžete rozhodnout používat opakovat `acquireTokenSilent` obnovení po síti po se dočasně není k dispozici.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Volání rozhraní Microsoft Graph API pomocí tokenu, který jste získali

Přidat novou metodu níže pro `ViewController.swift`. Tato metoda se používá, aby `GET` požadavku pomocí rozhraní Microsoft Graph API *hlavičku protokolu HTTP ověřování*:

```swift
 func getContentWithToken() {

        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)
    
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in

            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }

            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {

                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            self.updateLogging(text: "Result from Graph: \(result))")

        }.resume()
    }
```

<!--start-collapse-->
### <a name="making-a-rest-call-against-a-protected-api"></a>Volání REST chráněné rozhraní API

V této ukázkové aplikaci `getContentWithToken()` aby protokolu HTTP se používá metoda `GET` požadavku na chráněný prostředek, který se vyžaduje token a potom vrátí obsah volajícímu. Tato metoda přidá tokenu získaného v *hlavičku protokolu HTTP ověřování*. Pro tuto ukázku, prostředek je v rozhraní Microsoft Graph API *mě* koncový bod – zobrazí informace o profilu uživatele.
<!--end-collapse-->

## <a name="set-up-sign-out"></a>Nastavení odhlášení

Přidejte následující metodu do `ViewController.swift` odhlášení uživatele:

```swift 
 @IBAction func signoutButton(_ sender: UIButton) {

        guard let applicationContext = self.applicationContext else { return }

        guard let account = self.currentAccount() else { return }

        do {

            /**
             Removes all tokens from the cache for this application for the provided account
             - account:    The account to remove from the cache
             */

            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signoutButton.isEnabled = false

        } catch let error as NSError {

            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }

}
```

<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>Další informace o odhlášení

`signoutButton` Metoda odebere uživatele z mezipaměti uživatele MSAL – se efektivně tak dozví, MSAL tak budoucí požadavek na získání tokenu bude úspěšné pouze v případě, že je k interaktivní zapomenout aktuálního uživatele.

I když aplikace v této ukázce se podporuje jenom jednoho konkrétního uživatele, MSAL podporuje scénáře, ve kterém může být přihlášeni více účtů ve stejnou dobu – příkladem je e-mailové aplikace, kde má uživatel více účtů.
<!--end-collapse-->

## <a name="register-the-callback"></a>Registrovat zpětné volání

Jakmile se uživatel ověřuje, prohlížeč přesměruje uživatele zpět do aplikace. Postupujte podle pokynů dole zaregistrujte toto zpětné volání:

1. Otevřít `AppDelegate.swift` a importovat MSAL:

```swift
import MSAL
```

<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Přidejte následující metodu do vaší <code>AppDelegate</code> třídy pro zpracování zpětných volání:
</li>
</ol>

```swift
// @brief Handles inbound URLs. Checks if the URL matches the redirect URI for a pending AppAuth
// authorization request and if so, will look for the code in the response.

func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    
    print("Received callback!")
    
    MSALPublicClientApplication.handleMSALResponse(url)
    
    return true
}
```
