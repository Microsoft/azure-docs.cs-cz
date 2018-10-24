## <a name="prerequisites"></a>Požadavky
Před zápisem kód pro správu CDN, je nutné provést určitou přípravu umožňující kódu pracovat s Azure Resource Manageru. Tato příprava, budete muset:

* Vytvořte skupinu prostředků tak, aby obsahovala profil CDN, v tomto kurzu vytvořili
* Konfigurovat Azure Active Directory k ověřování pro aplikaci
* Zavedení oprávnění vůči skupinu prostředků, tak, aby jenom Autorizovaní uživatelé z vašeho tenanta Azure AD můžete pracovat s profilem CDN

### <a name="creating-the-resource-group"></a>Vytvoření skupiny prostředků
1. Přihlaste se k [Portálu Azure](https://portal.azure.com).
2. Klikněte na **Vytvořit prostředek**.
3. Vyhledejte **skupiny prostředků** a v okně skupiny prostředků klikněte na tlačítko **vytvořit**.

    ![Vytváří se nová skupina prostředků](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Zadejte název skupiny prostředků *CdnConsoleTutorial*.  Vyberte své předplatné a vyberte umístění blízko vás.  Pokud chcete, můžete kliknout **připnout na řídicí panel** políčko připnout k řídicímu panelu skupiny prostředků na portálu.  Připnutí usnadňuje později najít.  Po provedení výběru klikněte na tlačítko **vytvořit**.

    ![Názvy skupiny prostředků](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Po vytvoření skupiny prostředků, pokud jste ji na řídicí panel, najdete ho kliknutím **Procházet**, pak **skupiny prostředků**.  Pokud chcete soubor otevřít, klikněte na skupinu prostředků.  Poznamenejte si vaše **ID předplatného**. Budeme ho potřebovat později.

    ![Názvy skupiny prostředků](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Vytvoření aplikace Azure AD a přiřazení oprávnění
Existují dva přístupy k ověřování aplikace pomocí Azure Active Directory: jednotlivé uživatele nebo instanční objekt služby. Objekt služby je podobný účet služby ve Windows.  Namísto udělení oprávnění k interakci s profily CDN konkrétního uživatele, se místo toho udělení oprávnění instančnímu objektu služby.  Instanční objekty jsou obvykle používány pro automatizované neinteraktivní procesů.  I když v tomto kurzu je zápis aplikace interaktivní konzolu, zaměříme na přístup instančního objektu služby.

Vytváří se instanční objekt služby se skládá z několika kroků, včetně vytvoření aplikace Azure Active Directory.  K jeho vytvoření, vytvoříme [v tomto kurzu](../articles/active-directory/develop/howto-create-service-principal-portal.md).

> [!IMPORTANT]
> Dodržujte všechny kroky [propojené kurzu](../articles/active-directory/develop/howto-create-service-principal-portal.md).  Je *důležité* , dokončete ji přesně tak, jak je popsáno.  Ujistěte se, že si uvědomit vaše **ID tenanta**, **název domény tenantu** (obvykle *. onmicrosoft.com* domény Pokud jste zadali vlastní domény), **ID klienta** , a **ověřovací klíč klienta**, jak jsme je potřebovat později.  Dejte pozor, abyste chránit vaše **ID klienta** a **ověřovací klíč klienta**, jak tyto přihlašovací údaje slouží všichni k provádění operací jako instanční objekt služby.
>
> Při přechodu na krok s názvem konfigurace aplikace s více tenanty, vyberte **ne**.
>
> Když se zobrazí na krok [přiřazení aplikace k roli](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-the-application-to-a-role), použijte skupinu prostředků vytvořili dříve, *CdnConsoleTutorial*, ale místo **čtečky** role, přiřazení **Přispěvatel profilu CDN** role.  Po přiřazení aplikace **Přispěvatel profilu CDN** role ve vaší skupině prostředků, vraťte se do tohoto kurzu. 
>
>

Po vytvoření instančního objektu služby a přiřadit **Přispěvatel profilu CDN** role, **uživatelé** okno skupiny prostředků by měl vypadat podobně jako na následujícím obrázku.

![Okno Uživatelé](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Interaktivním ověřování uživatelů
Pokud místo instanční objekt, vy byste raději chtěli ověřování interaktivní jednotlivých uživatelů, proces je podobný tomu pro instanční objekt služby.  Ve skutečnosti je potřeba postupujte stejným způsobem, ale mírně upravit několik.

> [!IMPORTANT]
> Následujícím postupem další, pouze pokud zvolíte použití ověřování jednotlivých uživatelů místo instančního objektu služby.
>
>

1. Při vytváření aplikace, místo **webovou aplikaci**, zvolte **nativní aplikace**.

    ![Nativní aplikace](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Na další stránce budete vyzváni k zadání **identifikátor URI pro přesměrování**.  Identifikátor URI nebude ověřena, ale mějte na paměti, co jste zadali. Budete ho potřebovat později.
3. Není nutné vytvořit **ověřovací klíč klienta**.
4. Namísto přiřazení instančního objektu pro **Přispěvatel profilu CDN** role, chceme přiřadit jednotlivým uživatelům nebo skupinám.  V tomto příkladu vidíte, že jsem přiřadil *uživatel Demo CDN* k **Přispěvatel profilu CDN** role.  

    ![Přístup jednotlivých uživatelů](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
