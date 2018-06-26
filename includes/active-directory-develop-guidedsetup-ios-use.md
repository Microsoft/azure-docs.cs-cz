## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Použití knihovny ověřování společnosti Microsoft (MSAL) k získání tokenu pro rozhraní Microsoft Graph API

Otevřete `ViewController.swift` a nahraďte kód:

```swift
import UIKit
import MSAL

class ViewController: UIViewController, UITextFieldDelegate, URLSessionDelegate {
    
    let kClientID = "Your_Application_Id_Here"
    let kAuthority = "https://login.microsoftonline.com/common/v2.0"

    let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
    let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
    
    var accessToken = String()
    var applicationContext = MSALPublicClientApplication.init()

    @IBOutlet weak var loggingText: UITextView!
    @IBOutlet weak var signoutButton: UIButton!

     // This button will invoke the call to the Microsoft Graph API. It uses the
     // built in Swift libraries to create a connection.
    
    @IBAction func callGraphButton(_ sender: UIButton) {
        
        
        do {
            
            // We check to see if we have a current signed-in user. If we don't, then we need to sign someone in.
            // We throw an interactionRequired so that we trigger the interactive sign-in.
            
            if  try self.applicationContext.users().isEmpty {
                throw NSError.init(domain: "MSALErrorDomain", code: MSALErrorCode.interactionRequired.rawValue, userInfo: nil)
            } else {
            
            // Acquire a token for an existing user silently
            
            try self.applicationContext.acquireTokenSilent(forScopes: self.kScopes, user: applicationContext.users().first) { (result, error) in
    
                    if error == nil {
                        self.accessToken = (result?.accessToken)!
                        self.loggingText.text = "Refreshing token silently)"
                        self.loggingText.text = "Refreshed Access token is \(self.accessToken)"
                        
                        self.signoutButton.isEnabled = true;
                        self.getContentWithToken()
    
                    } else {
                        self.loggingText.text = "Could not acquire token silently: \(error ?? "No error information" as! Error)"
    
                    }
                }
            }
        }  catch let error as NSError {
            
            // interactionRequired means we need to ask the user to sign in. This usually happens
            // when the user's Refresh Token is expired or if the user has changed their password
            // among other possible reasons.
            
            if error.code == MSALErrorCode.interactionRequired.rawValue {
                
                self.applicationContext.acquireToken(forScopes: self.kScopes) { (result, error) in
                        if error == nil {
                            self.accessToken = (result?.accessToken)!
                            self.loggingText.text = "Access token is \(self.accessToken)"
                            self.signoutButton.isEnabled = true;
                            self.getContentWithToken()
                            
                        } else  {
                            self.loggingText.text = "Could not acquire token: \(error ?? "No error information" as! Error)"
                        }
                }
                
            }
            
        } catch {
            
            // This is the catch all error.
            
            self.loggingText.text = "Unable to acquire token. Got error: \(error)"
            
        }
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        
        do {
             // Initialize a MSALPublicClientApplication with a given clientID and authority
            self.applicationContext = try MSALPublicClientApplication.init(clientId: kClientID, authority: kAuthority)
        } catch {
            self.loggingText.text = "Unable to create Application Context. Error: \(error)"
        }
    }


    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    override func viewWillAppear(_ animated: Bool) {
        
        if self.accessToken.isEmpty {
            signoutButton.isEnabled = false; 
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Další informace
#### <a name="getting-a-user-token-interactively"></a>Získání tokenu uživatele interaktivně
Volání `acquireToken` metoda výsledky v okně prohlížeče uživatele vyzve k přihlášení. Aplikace obvykle vyžadují uživatel interaktivní přihlášení při prvním potřebují přístup k chráněnému prostředku nebo když tichou operaci získat token selže (například heslo uživatele jeho platnost).

#### <a name="getting-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění
`acquireTokenSilent` Metoda zpracovává tokenu pořízení a obnovení bez nutnosti zásahu uživatele. Po `acquireToken` se spustí poprvé, `acquireTokenSilent` je metoda běžně používají k získání tokeny použít přístup k chráněným prostředkům pro následující volání - volání na vyžádání nebo obnovení tokeny jsou vytvářeny bezobslužně.

Nakonec `acquireTokenSilent` selže – např. uživatel má odhlášení, nebo došlo ke změně hesla na jiném zařízení. Když MSAL zjistí, že problém lze vyřešit tím, že vyžaduje interaktivní akce, aktivuje se ho `MSALErrorCode.interactionRequired` výjimka. Aplikace může zpracovat výjimku dvěma způsoby:

1.  Volání proti `acquireToken` okamžitě, výsledkem výzvy pro uživatele k přihlášení. Tento vzor se obvykle používá v online aplikace tam, kde není žádná offline v aplikaci k dispozici obsah pro uživatele. Ukázková aplikace generované touto s průvodcem instalací používá tento vzor: Zobrazí se v čase akce při prvním spuštění aplikace. Vzhledem k tomu, že žádný uživatel nikdy použili aplikaci, `applicationContext.users().first` bude obsahovat hodnotu null a ` MSALErrorCode.interactionRequired ` dojde k výjimce. Kód v ukázce pak ošetří výjimku voláním `acquireToken` výsledkem výzvy pro uživatele k přihlášení.

2.  Aplikace můžete udělat taky vizuální označení uživateli, které interaktivní přihlášení je povinné, tak, aby si uživatel může vybrat správný čas pro přihlášení, nebo můžete zkusit aplikaci `acquireTokenSilent` později. To se obvykle používá při uživatel může použít další funkce aplikace bez narušení – například je offline obsah k dispozici v aplikaci. V takovém případě se můžete rozhodnout uživatele, když chtějí přihlášení pro přístup k chráněnému prostředku nebo aktualizovat zastaralé informace, nebo aplikaci můžete rozhodnout opakujte `acquireTokenSilent` obnovení po síti poté, co byla dočasně nedostupná.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Volání rozhraní Graph API Microsoft pomocí tokenu, který jste obdrželi

Přidejte novou metodu níže do `ViewController.swift`. Tato metoda se používá, chcete-li `GET` požadavku vůči pomocí Microsoft Graph API *HTTP autorizační hlavičky*:

```swift
func getContentWithToken() {
    
    let sessionConfig = URLSessionConfiguration.default
    
    // Specify the Graph API endpoint
    let url = URL(string: kGraphURI)
    var request = URLRequest(url: url!)
    
    // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
    request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
    let urlSession = URLSession(configuration: sessionConfig, delegate: self, delegateQueue: OperationQueue.main)
    
    urlSession.dataTask(with: request) { data, response, error in
        let result = try? JSONSerialization.jsonObject(with: data!, options: [])
                    if result != nil {
                
                self.loggingText.text = result.debugDescription
            }
        }.resume()
}
```

<!--start-collapse-->
### <a name="making-a-rest-call-against-a-protected-api"></a>Volání REST chráněné rozhraní API

V této ukázkové aplikaci `getContentWithToken()` metoda se používá k zajištění HTTP `GET` požadavku pro chráněný prostředek, který vyžaduje token a pak se vraťte obsah volajícímu. Tato metoda přidá získal token v *HTTP autorizační hlavičky*. Tato ukázka je prostředek Microsoft Graph API *mi* koncového bodu – zobrazí informace o profilu uživatele.
<!--end-collapse-->

## <a name="set-up-sign-out"></a>Nastavení odhlášení

Přidejte následující metodu do `ViewController.swift` se odhlásit uživatele:

```swift 
@IBAction func signoutButton(_ sender: UIButton) {

    do {
        
        // Removes all tokens from the cache for this application for the provided user
        // first parameter:   The user to remove from the cache
        
        try self.applicationContext.remove(self.applicationContext.users().first)
        self.signoutButton.isEnabled = false;
        
    } catch let error {
        self.loggingText.text = "Received error signing user out: \(error)"
    }
}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>Další informace o odhlášení

`signoutButton` Metoda odebere uživatele z mezipaměti uživatele MSAL – se efektivně tak dozví, MSAL zapomenutí aktuálního uživatele, takže budoucí žádost o získání tokenu bude úspěšné pouze v případě, že je k interaktivní.

I když aplikace v této ukázce podporuje jenom jednoho konkrétního uživatele, MSAL podporuje scénáře, kde může být přihlášeni více účtů ve stejnou dobu – příklad je e-mailové aplikace, které má uživatel více účtů.
<!--end-collapse-->

## <a name="register-the-callback"></a>Registrovat zpětné volání

Jakmile se uživatel ověřuje, prohlížeč přesměruje uživatele zpět do aplikace. Postupujte podle pokynů dole zaregistrujte tento zpětného volání:

1.  Otevřete `AppDelegate.swift` a import MSAL:

```swift
import MSAL
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Přidejte následující metodu do vaší <code>AppDelegate</code> třídy pro zpracování zpětná volání:
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

