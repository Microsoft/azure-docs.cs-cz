## <a name="add-the-applications-registration-information-to-your-app"></a>Přidat informace o registraci aplikace do aplikace

V tomto kroku budete muset nakonfigurovat adresu URL pro přesměrování informace o registraci aplikace a pak přidejte do vaší aplikace JavaScript SPA ID aplikace.

### <a name="configure-redirect-url"></a>Nakonfigurujte URL přesměrování

Konfigurace `Redirect URL` pole s adresou URL pro stránku index.html založené na vašem webovém serveru a pak klikněte na *aktualizace*.


> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Visual Studio pokyny pro získání adresy URL přesměrování
> Získat adresu URL přesměrování:
> 1.    V *Průzkumníku řešení*, vyberte projekt a podívejte se na `Properties` okno (Pokud se nezobrazí okno vlastností, stiskněte klávesu `F4`)
> 2.    Zkopírujte hodnotu z `URL` do schránky:<br/> ![Vlastnosti projektu](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Vložit hodnotu jako `Redirect URL` horní tuto stránku, klikněte `Update`

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Nastavení přesměrování URL pro jazyk Python
> Pro jazyk Python může web nastavit port serveru prostřednictvím příkazového řádku. Tato instalace s průvodcem používá port 8080 odkaz ale chování můžete používat všechny ostatní porty, které jsou k dispozici. Chcete-li nastavit adresu URL přesměrování v informace o registraci aplikace v každém případě postupujte podle následujících pokynů:<br/>
> Nastavit `http://localhost:8080/` jako `Redirect URL` horní této stránky, nebo použijte `http://localhost:[port]/` Pokud používáte vlastní port TCP (kde *[port]* je vlastní číslo portu TCP) a potom klikněte na 'aktualizace.

### <a name="configure-your-javascript-spa-application"></a>Konfiguraci aplikace JavaScript SPA

1.  Vytvořte soubor s názvem `msalconfig.js` obsahující informace o registraci aplikace. Pokud používáte Visual Studio, vyberte projektu (projektu do kořenové složky), klikněte pravým tlačítkem a vyberte: `Add`  >  `New Item`  >  `JavaScript File`. Název `msalconfig.js`
2.  Přidejte následující kód do vaší `msalconfig.js` souboru:

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
