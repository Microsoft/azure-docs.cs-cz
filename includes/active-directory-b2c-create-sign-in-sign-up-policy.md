[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

V nastavení v části Zásady vyberte **Zásady registrace nebo přihlašování** a klikněte na **+ Přidat**.

![Výběr zásad registrace nebo přihlašování a kliknutí na tlačítko Přidat](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-policy.png)

Zadejte **Název** zásady, který v aplikaci použijete jako referenci. Zadejte například `SiUpIn`.

Vyberte **Zprostředkovatelé identity** a zaškrtněte políčko **Registrace pomocí e-mailu**. Volitelně můžete vybrat také zprostředkovatele sociální identity, pokud už jsou nakonfigurováni. Klikněte na **OK**.

![Výběr možnosti Přihlášení místním účtem jako zprostředkovatele identity a kliknutí na tlačítko OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-identity-providers.png)

Vyberte **Atributy registrace**. Zvolte atributy, které od uživatele chcete během registrace shromažďovat. Zaškrtněte například políčka **Země/oblast**, **Zobrazované jméno** a **PSČ**. Klikněte na **OK**.

![Výběr atributů a kliknutí na tlačítko OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-attributes.png)

Vyberte **Deklarace identit aplikace**. Zvolte deklarace identit, které se mají vracet v autorizačních tokenech odesílaných zpět do aplikace po úspěšné registraci nebo přihlášení. Vyberte například **Zobrazované jméno**, **Zprostředkovatel identity**, **PSČ**, **Uživatel je nový** a **ID objektu uživatele**.

![Výběr deklarací identit aplikace a kliknutí na tlačítko OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-application-claims.png)

Kliknutím na **Vytvořit** přidejte zásadu. Zásada se zobrazí jako **B2C_1_SiUpIn**. K názvu se připojí předpona **B2C_1_**.

Otevřete zásadu výběrem **B2C_1_SiUpIn**. Ověřte nastavení uvedená v tabulce a potom klikněte na **Spustit**.

![Výběr a spuštění zásady](media/active-directory-b2c-create-sign-in-sign-up-policy/run-b2c-signup-signin-policy.png)

| Nastavení      | Hodnota  |
| ------------ | ------ |
| **Aplikace** | Aplikace Contoso B2C |
| **Výběr adresy URL odpovědi** | `https://localhost:44316/` |

Otevře se nová karta prohlížeče a můžete zkontrolovat nakonfigurované uživatelské prostředí pro registraci a přihlášení.

> [!NOTE]
> Trvá až minutu, než se vytvoření zásady a aktualizace projeví.
>