---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 26c158145de6ce729d8a7060152b19fb14b63d58
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95559343"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Příprava na ověření Azure Resource Manager požadavků
Je nutné ověřit všechny operace, které provádíte na zdrojích pomocí [Azure Resource Manager][lnk-authenticate-arm] s Azure Active Directory (AD). Nejjednodušší způsob, jak to nakonfigurovat, je použití PowerShellu nebo rozhraní příkazového řádku Azure CLI.

Než budete pokračovat, nainstalujte [rutiny Azure PowerShell][lnk-powershell-install] .

Následující kroky ukazují, jak nastavit ověřování hesla pro aplikaci AD pomocí PowerShellu. Tyto příkazy můžete spustit ve standardní relaci prostředí PowerShell.

1. Přihlaste se k předplatnému Azure pomocí následujícího příkazu:

    ```powershell
    Connect-AzAccount
    ```

1. Pokud máte několik předplatných Azure, přihlaste se k Azure, abyste měli přístup ke všem předplatným Azure přidruženým k vašim přihlašovacím údajům. K vypsání předplatných Azure, která můžete použít, použijte následující příkaz:

    ```powershell
    Get-AzSubscription
    ```

    Pomocí následujícího příkazu vyberte předplatné, které chcete použít ke spuštění příkazů pro správu služby IoT Hub. Můžete použít název nebo ID předplatného z výstupu předchozího příkazu:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Poznamenejte si své **TenantId** a **SubscriptionId**. Budete je potřebovat později.
3. Pomocí následujícího příkazu vytvořte novou Azure Active Directory aplikaci, která nahradí držitele míst:
   
   * **{Display Name}:** zobrazovaný název vaší aplikace, například **MySampleApp**
   * **{Adresa URL domovské stránky}:** adresa URL domovské stránky vaší aplikace, například **http: \/ /MySampleApp/Home**. Tato adresa URL nemusí odkazovat na skutečnou aplikaci.
   * **{Identifikátor aplikace}:** Jedinečný identifikátor, například **http: \/ /MySampleApp**. Tato adresa URL nemusí odkazovat na skutečnou aplikaci.
   * **{Password}:** Heslo, které používáte k ověření ve vaší aplikaci.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Poznamenejte si **ApplicationId** aplikace, kterou jste vytvořili. Budete ho potřebovat později.
5. Pomocí následujícího příkazu vytvořte nový instanční objekt a nahraďte **{MyApplicationId}** řetězcem **ApplicationId** z předchozího kroku:
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Pomocí následujícího příkazu nastavte přiřazení role a nahraďte **{MyApplicationId}** svým **ApplicationId**.
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Nyní jste dokončili vytváření aplikace služby Azure AD, která umožňuje ověření z vlastní aplikace v jazyce C#. Následující hodnoty budete potřebovat později v tomto kurzu:

* TenantId
* SubscriptionId
* ApplicationId
* Heslo

[lnk-authenticate-arm]: /rest/api/
[lnk-powershell-install]: /powershell/azure/install-az-ps