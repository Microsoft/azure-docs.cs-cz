## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Testování dotazů na rozhraní Microsoft Graph API z aplikace pro iOS

Chcete-li spustit kód v simulátoru, stiskněte **příkaz** + **R**.

![Otestujte aplikaci v simulátoru](media/active-directory-develop-guidedsetup-ios-test/iostestscreenshot.png)

Jakmile budete připraveni k testování, vyberte **volat Microsoft Graph API**. Po zobrazení výzvy zadejte svoje uživatelské jméno a heslo.

### <a name="provide-consent-for-application-access"></a>Zadejte svůj souhlas pro přístup k aplikaci
Při prvním přihlášení do aplikace, budete vyzváni k souhlasíte s tím, aby aplikace k profilu a pro přihlášení:

![Zadejte svůj souhlas pro přístup k aplikaci](media/active-directory-develop-guidedsetup-ios-test/iosconsentscreen.png)

### <a name="view-application-results"></a>Zobrazení výsledků aplikace
Po přihlášení, byste měli vidět údajům vašeho uživatelského profilu vrácený voláním rozhraní Microsoft Graph API v **protokolování** oddílu. 

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaná oprávnění

Vyžaduje rozhraní Microsoft Graph API **user.read** obory a čtení profilu uživatele. Tento obor se automaticky přidá ve výchozím nastavení v každé aplikaci, která je zaregistrovaná na portálu pro registraci. Další rozhraní API pro Microsoft Graph, stejně jako vlastní rozhraní API pro back endového serveru může vyžadovat další obory. Vyžaduje rozhraní Microsoft Graph API **Calendars.Read** oboru seznam kalendářů uživatele.

Chcete-li přístup ke kalendářům uživatele v rámci aplikace, přidejte **Calendars.Read** delegovaná oprávnění aplikace informace o registraci. Pak přidejte **Calendars.Read** rozsah **acquireTokenSilent** volání. 

>[!NOTE]
>Uživatel může zobrazit výzva pro další souhlasy zvýšit počet oborů.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
