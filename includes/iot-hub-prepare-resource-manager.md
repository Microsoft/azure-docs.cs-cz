---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 4eb794fa35164e3f86a5e3d6f67d446321f91f0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67133569"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Příprava k ověření požadavků Správce prostředků Azure
Je nutné ověřit všechny operace, které provádíte na prostředky pomocí [Správce prostředků Azure][lnk-authenticate-arm] s Azure Active Directory (AD). Nejjednodušší způsob, jak to nakonfigurovat, je použití PowerShellu nebo Azure CLI.

Než budete pokračovat, nainstalujte [rutiny Prostředí Azure PowerShell.][lnk-powershell-install]

Následující kroky ukazují, jak nastavit ověřování hesla pro aplikaci služby AD pomocí prostředí PowerShell. Tyto příkazy můžete spustit ve standardní relaci prostředí PowerShell.

1. Přihlaste se k předplatnému Azure pomocí následujícího příkazu:

    ```powershell
    Connect-AzAccount
    ```

1. Pokud máte více předplatných Azure, přihlášení k Azure vám uděluje přístup ke všem předplatným Azure přidruženým k vašim přihlašovacím údajům. Pomocí následujícího příkazu zobrazíte seznam předplatných Azure, která můžete použít:

    ```powershell
    Get-AzSubscription
    ```

    Pomocí následujícího příkazu vyberte předplatné, které chcete použít ke spuštění příkazů ke správě služby IoT hub. Můžete použít název nebo ID předplatného z výstupu předchozího příkazu:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Poznamenejte si vaše **TenantId** a **SubscriptionId**. Budete je potřebovat později.
3. Vytvořte novou aplikaci Azure Active Directory pomocí následujícího příkazu, který nahradí zástupné symboly:
   
   * **{Display name}:** zobrazovaný název aplikace, například **MySampleApp**
   * **{Adresa URL domovské stránky}:** adresa URL domovské stránky aplikace, například **http:\//mysampleapp/home**. Tato adresa URL nemusí ukazovat na skutečnou aplikaci.
   * **{Identifikátor aplikace}:** Jedinečný identifikátor, například **http:\//mysampleapp**. Tato adresa URL nemusí ukazovat na skutečnou aplikaci.
   * **{Heslo}:** Heslo, které používáte k ověření pomocí aplikace.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Poznamenejte si **ApplicationId** aplikace, kterou jste vytvořili. Budeš to potřebovat později.
5. Vytvořte nový instanční objekt pomocí následujícího příkazu, který nahradí **{MyApplicationId}** **id aplikace** z předchozího kroku:
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Nastavte přiřazení role pomocí následujícího příkazu a nahraďte **{MyApplicationId}** **id aplikace ApplicationId**.
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Nyní jste dokončili vytváření aplikace Azure AD, která umožňuje ověření z vlastní aplikace Jazyka C#. Následující hodnoty potřebujete dále v tomto kurzu:

* TenantId
* SubscriptionId
* ApplicationId
* Heslo

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: /powershell/azure/install-az-ps
