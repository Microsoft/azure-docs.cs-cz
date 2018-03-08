
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Konfigurace webové aplikace ASP.NET s informace o registraci aplikace

V tomto kroku nakonfigurujete svůj projekt na používání protokolu SSL a pak použijte adresu URL SSL konfigurovat informace o registraci vaší aplikace. Potom tuto aplikaci přidat, informace o registraci do řešení prostřednictvím *web.config*.

1.  V Průzkumníku řešení, vyberte projekt a podívejte se na `Properties` okno (Pokud se nezobrazí okno Vlastnosti stisknutím klávesy F4)
2.  Změna `SSL Enabled` na`True`
3.  Zkopírujte hodnotu z `SSL URL` výše a vložte jej do `Redirect URL` pole nahoře na této stránce a pak klikněte na *aktualizace*:<br/><br/>![Vlastnosti projektu](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
4.  Přidejte následující v `web.config` soubor umístěný na kořenové složky, části `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
