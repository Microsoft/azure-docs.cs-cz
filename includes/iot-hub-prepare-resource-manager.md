## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Příprava k ověřování žádostí o Azure Resource Manager
Je třeba ověřit všechny operace, které můžete provádět na prostředky pomocí [Azure Resource Manager] [ lnk-authenticate-arm] s Azure Active Directory (AD). Nejjednodušší způsob, jak nakonfigurovat tuto funkci je pomocí Powershellu nebo rozhraní příkazového řádku Azure.

Nainstalujte [rutin prostředí Azure PowerShell] [ lnk-powershell-install] než budete pokračovat.

Následující kroky ukazují, jak nastavit ověřování hesla pro aplikaci AD pomocí prostředí PowerShell. Tyto příkazy můžete spustit ve standardní relaci prostředí PowerShell.

1. Přihlaste se k předplatnému Azure, pomocí následujícího příkazu:

    ```powershell
    Connect-AzureRmAccount
    ```

1. Pokud máte víc předplatných Azure, přihlášení do Azure uděluje přístup do všech předplatná Azure přidružená přihlašovacích údajů. Pomocí následujícího příkazu zobrazíte seznam předplatných Azure, které je k dispozici pro použití:

    ```powershell
    Get-AzureRMSubscription
    ```

    Pomocí následujícího příkazu vyberte předplatné, které chcete použít ke spuštění příkazů ke správě služby IoT hub. Můžete použít název nebo ID předplatného z výstupu předchozího příkazu:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Poznamenejte si vaše **TenantId** a **SubscriptionId**. Budete potřebovat později.
3. Vytvořte novou aplikaci Azure Active Directory pomocí následujícího příkazu, nahraďte svými držiteli místní:
   
   * **{Zobrazovaný název}:** zobrazovaný název pro vaši aplikaci, jako **MySampleApp**
   * **{Adresu URL domovské stránky}:** adresu URL domovské stránky aplikace jako **http://mysampleapp/home**. Tato adresa URL nemusí tak, aby odkazoval na reálné aplikaci.
   * **{Identifikátor aplikace}:** jedinečný identifikátor, jako **http://mysampleapp**. Tato adresa URL nemusí tak, aby odkazoval na reálné aplikaci.
   * **{Heslo}:** heslo, které použijete k ověření s vaší aplikací.
     
     ```powershell
     New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
     ```
4. Poznamenejte si **ApplicationId** aplikace, které jste vytvořili. Budete potřebovat později.
5. Vytvořit nový objekt služby pomocí následujícího příkazu, nahraďte **{MyApplicationId}** s **ApplicationId** z předchozího kroku:
   
    ```powershell
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Nastavení přiřazení role pomocí následujícího příkazu, nahraďte **{MyApplicationId}** s vaší **ApplicationId**.
   
    ```powershell
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Nyní dokončení vytvoření aplikace Azure AD, která umožňuje ověření z vaší vlastní aplikaci C#. Později v tomto kurzu potřebujete následující hodnoty:

* TenantId
* SubscriptionId
* ApplicationId
* Heslo

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
