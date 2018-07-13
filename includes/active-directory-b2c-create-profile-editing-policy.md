Pokud chcete povolit upravování profilu ve své aplikaci, můžete použít **upravování profilu** zásad. Tato zásada popisuje prostředí, které zákazníci budou procházet při upravování profilu a obsah tokenů, které bude aplikace přijímat po úspěšném dokončení.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

V nastavení v části Zásady vyberte **Zásady upravování profilu** a klikněte na **+ Přidat**.

![Výběr zásad upravování profilu a kliknutí na tlačítko Přidat](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-policy.png)

Zadejte **Název** zásady, který v aplikaci použijete jako referenci. Zadejte například `SiPe`.

Vyberte **Zprostředkovatelé identity** a zaškrtněte políčko **Přihlášení místním účtem**. Volitelně můžete vybrat také zprostředkovatele sociální identity, pokud už jsou nakonfigurováni. Klikněte na **OK**.

![Výběr možnosti Přihlášení místním účtem jako zprostředkovatele identity a kliknutí na tlačítko OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-identity-providers.png)

Vyberte **Atributy profilu**. Zvolte, které atributy může uživatel ve svém profilu zobrazit a upravit. Zaškrtněte například políčka **Země/oblast**, **Zobrazované jméno** a **PSČ**. Klikněte na **OK**.

![Výběr atributů a kliknutí na tlačítko OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-attributes.png)

Vyberte **Deklarace identit aplikace**. Zvolte deklarace identit, které se mají vracet v autorizačních tokenech odesílaných zpět do aplikace po úspěšném upravování profilu. Vyberte například **Zobrazované jméno** a **PSČ**.

![Výběr deklarací identit aplikace a kliknutí na tlačítko OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-application-claims.png)

Kliknutím na **Vytvořit** přidejte zásadu. Zásada se zobrazí jako **B2C_1_SiPe**. K názvu se připojí předpona **B2C_1_**.

Otevřete zásadu výběrem **B2C_1_SiPe**. Ověřte nastavení uvedená v tabulce a potom klikněte na **Spustit**.

![Výběr a spuštění zásady](media/active-directory-b2c-create-profile-editing-policy/run-b2c-editing-policy.png)

| Nastavení      | Hodnota  |
| ------------ | ------ |
| **Aplikace** | Aplikace Contoso B2C |
| **Výběr adresy URL odpovědi** | `https://localhost:44316/` |

Otevře se nová karta prohlížeče a můžete zkontrolovat nakonfigurované uživatelské prostředí pro upravování profilu.

> [!NOTE]
> Trvá až minutu, než se vytvoření zásady a aktualizace projeví.
>