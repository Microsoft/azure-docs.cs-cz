## <a name="prerequisites"></a>Požadavky
Před zápisem kódu správy CDN, musíte udělat některé přípravy povolit kód pro interakci s Azure Resource Manager. Chcete-li provést této přípravy, je potřeba:

* Vytvořte skupinu prostředků tak, aby obsahovala profil CDN vytvořili v tomto kurzu
* Konfigurace služby Azure Active Directory pro zajištění ověřování pro aplikaci
* Použití oprávnění ke skupině prostředků, takže jenom autorizovaným uživatelům z vašeho klienta Azure AD mohou komunikovat s profil CDN

### <a name="creating-the-resource-group"></a>Vytvoření skupiny prostředků
1. Přihlaste se k [Portálu Azure](https://portal.azure.com).
2. Klikněte na **Vytvořit prostředek**.
3. Vyhledejte **skupiny prostředků** a v podokně skupiny prostředků, klikněte na tlačítko **vytvořit**.

    ![Vytvoření nové skupiny prostředků](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Název skupiny prostředků *CdnConsoleTutorial*.  Vyberte předplatné a zvolte umístění okolo vás.  Pokud chcete, můžete kliknout na **připnout na řídicí panel** chcete připnout na řídicí panel skupinu prostředků na portálu.  Připnutí je snazší najít později.  Po provedení váš výběr, klikněte na tlačítko **vytvořit**.

    ![Názvy skupiny prostředků](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Po vytvoření skupiny prostředků nebyla připnout na řídicí panel, najdete ho kliknutím na **Procházet**, pak **skupiny prostředků**.  Otevřete ho kliknutím na skupinu prostředků.  Poznamenejte si vaše **ID předplatného**. Budeme ho potřebovat později.

    ![Názvy skupiny prostředků](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Vytvoření aplikace Azure AD a použití oprávnění
Existují dva přístupy k ověřování aplikace v Azure Active Directory: jednotlivé uživatele nebo hlavní název služby. Objekt služby je podobný účet služby v systému Windows.  Namísto udělení oprávnění k interakci s profily CDN určitého uživatele, se místo toho udělena oprávnění pro objekt služby.  Objekty služby jsou obvykle používány pro neinteraktivní, automatizované procesy.  I když v tomto kurzu je zápis aplikace interaktivní konzoly, zaměříme budete na hlavní přístupu služby.

Vytvoření objektu služby se skládá z několika kroků, včetně vytváření aplikací Azure Active Directory.  K jeho vytvoření, vytvoříme [v tomto kurzu](../articles/resource-group-create-service-principal-portal.md).

> [!IMPORTANT]
> Postupujte podle pokynů v [propojené kurzu](../articles/resource-group-create-service-principal-portal.md).  Je *důležité* dokončit ho přesně tak, jak je popsáno.  Zajistěte, aby si uvědomit, vaše **ID klienta**, **název domény klienta** (běžně *. onmicrosoft.com* domény Pokud jste zadali vlastní domény), **ID klienta** , a **ověřovací klíč klienta**, jak jsme je potřebovat později.  Dávejte pozor, které chrání vaše **ID klienta** a **ověřovací klíč klienta**, jak tyto přihlašovací údaje můžete použít libovolný uživatel k provedení operací jako objekt služby.
>
> Při přechodu na krok s názvem konfigurace víceklientské aplikace, vyberte **ne**.
>
> Při přechodu na krok [přiřazení aplikace k roli](../articles/azure-resource-manager/resource-group-create-service-principal-portal.md#assign-application-to-role), použijte skupinu prostředků vytvořili dříve, *CdnConsoleTutorial*, ale místo **čtečky** role, přiřadit  **Přispěvatel profil CDN** role.  Po přiřazení aplikace **Přispěvatel profil CDN** role ve vaší skupině prostředků, vraťte se do tohoto kurzu. 
>
>

Po vytvoření instančního objektu a přiřadit **Přispěvatel profil CDN** role, **uživatelé** okno skupiny prostředků by měl vypadat podobně jako na následujícím obrázku.

![Okno Uživatelé](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Interaktivním ověřování uživatelů
Pokud místo hlavní název služby, byste měli místo ověřování interaktivní jednotlivých uživatelů, proces je podobný tomu pro hlavní název služby.  Ve skutečnosti budete muset postupujte stejným způsobem, ale provést několik menších změn.

> [!IMPORTANT]
> Následujícím postupem další, pouze pokud se rozhodnete používat ověřování jednotlivých uživatelů místo instanční objekt.
>
>

1. Při vytváření aplikace, místo **webové aplikace**, zvolte **nativní aplikace**.

    ![Nativní aplikace](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Na další stránce budete vyzváni k **identifikátor URI pro přesměrování**.  Identifikátor URI nebude ověřena, ale mějte na paměti, jste zadali. Budete ho potřebovat později.
3. Není nutné vytvořit **ověřovací klíč klienta**.
4. Místo objekt služby pro přiřazení **Přispěvatel profil CDN** role, vytvoříme přiřadit jednotlivým uživatelům nebo skupinám.  V tomto příkladu uvidíte, že jste přidělený *uživatel Demo CDN* k **Přispěvatel profil CDN** role.  

    ![Přístup jednotlivých uživatelů](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
