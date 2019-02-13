---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: e6ba2a9c27567ccbd3b744a9bfdc0603758c7068
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56212986"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Příprava k ověřování požadavků Azure Resource Manageru
Všechny operace, které můžete provádět s prostředky pomocí musí ověřit [Azure Resource Manageru] [ lnk-authenticate-arm] s Azure Active Directory (AD). Nejjednodušší způsob, jak nastavit tuto konfiguraci je pomocí Powershellu nebo rozhraní příkazového řádku Azure.

Nainstalujte [rutin prostředí Azure PowerShell] [ lnk-powershell-install] předtím, než budete pokračovat.

Následující kroky ukazují, jak nastavit ověřování heslem pro aplikaci AD pomocí prostředí PowerShell. Spusťte tyto příkazy ve standardní relaci Powershellu.

1. Přihlaste se ke svému předplatnému Azure pomocí následujícího příkazu:

    ```powershell
    Connect-AzureRmAccount
    ```

1. Pokud máte více předplatných Azure, přihlášení k Azure získáte přístup ke Všechna předplatná Azure přidružená svoje přihlašovací údaje. Pomocí následujícího příkazu zobrazíte seznam předplatných Azure k dispozici pro použití:

    ```powershell
    Get-AzureRMSubscription
    ```

    Pomocí následujícího příkazu vyberte předplatné, které chcete použít ke spuštění příkazů pro správu služby IoT hub. Můžete použít název nebo ID předplatného z výstupu předchozího příkazu:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Poznamenejte si vaše **TenantId** a **SubscriptionId**. Budete je potřebovat později.
3. Vytvořte novou aplikaci Azure Active Directory pomocí následující příkaz a nahraďte zástupné znaky místě:
   
   * **{Název}:** zobrazovaný název pro vaši aplikaci, jako **MySampleApp**
   * **{Adresu URL domovské stránky}:** adresa URL domovské stránky vaší aplikace, jako **http://mysampleapp/home**. Tato adresa URL nemusí odkazovat na aplikace skutečný.
   * **{Identifikátor aplikace}:** Jedinečný identifikátor jako **http://mysampleapp**. Tato adresa URL nemusí odkazovat na aplikace skutečný.
   * **{Heslo}:** Heslo, které použijete k ověření s vaší aplikací.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Poznamenejte si, **ApplicationId** o aplikaci, kterou jste vytvořili. Budete ho potřebovat později.
5. Vytvořit nový instanční objekt pomocí následujícího příkazu, přičemž nahraďte **{MyApplicationId}** s **ApplicationId** z předchozího kroku:
   
    ```powershell
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Vytvořit přiřazení role pomocí následujícího příkazu, přičemž nahraďte **{MyApplicationId}** s vaší **ApplicationId**.
   
    ```powershell
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Teď vytvoříte aplikace Azure AD, která umožňuje ověření z vaší vlastní C# aplikace. Tyto hodnoty budete potřebovat později v tomto kurzu:

* TenantId
* SubscriptionId
* ApplicationId
* Heslo

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps
