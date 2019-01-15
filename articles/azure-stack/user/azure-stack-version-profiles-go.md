---
title: Použití profilů verzí API s využitím GO ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o použití profilů verzí API s využitím GO ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 30969dcb7549f4107eb72b54d444a639c35b7ba0
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264768"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Použití profilů verzí API s využitím Go ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

## <a name="go-and-version-profiles"></a>Profily go a verze

Profil je kombinace různých typů prostředků s různými verzemi z různých služeb. Pomocí profilu můžete kombinovat a párovat mezi různých typů prostředků. Profily můžete zadat:

 - Stabilitu pro vaši aplikaci tím, že zamknete ke konkrétním verzím rozhraní API.
 - Kompatibilita pro vaše aplikace se službami Azure Stack a regionálních Datacenter Azure.

V sadě Go SDK jsou k dispozici v části profily profily / cestu, jejich verze **rrrr-MM-DD** formátu. Právě teď nejnovější Azure Stack je verze profilu **2017-03-09**. Chcete-li importovat dané služby z profilu, budete muset importovat jeho odpovídající modul z profilu. Například, chcete-li importovat **Compute** služba **2017-03-09** profilu:

````go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>Nainstalujte sadu Azure SDK for Go

  1. Instalace Gitu. Pokyny najdete v tématu [Začínáme - instalaci Gitu](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
  2. Nainstalujte [programovací jazyk Go](https://golang.org/dl).  
  Profily rozhraní API pro Azure budou vyžadovat Go verze 1.9 nebo novější.
  3. Spuštěním následujícího příkazu bashe instalaci Go Azure SDK a jeho závislosti:
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>GO SDK

Můžete najít další informace o sadě SDK Azure přejděte na:
- Přejít Azure SDK na [instalace sady Azure SDK for Go](https://docs.microsoft.com/go/azure/azure-sdk-go-install).
- Je veřejně dostupný na Githubu v Azure Go SDK [azure sdk pro go](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Závislosti na Přejít AutoRest

GO SDK závisí na Azure Go-AutoRest moduly, které chcete odesílat požadavky REST do koncových bodů Azure Resource Manageru. Budete muset importovat závislosti modulu Azure Go-AutoRest z [Go AutoRest Azure na Githubu](https://github.com/Azure/go-autorest). Příkazy bash instalace v můžete najít **nainstalovat** části.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Použití profilů GO SDK ve službě Azure Stack

Spuštění ukázky kódu Go ve službě Azure Stack:
  1. Nainstalujte sadu Azure SDK for Go a jeho závislosti. Pokyny najdete v předchozí části [nainstalujte sadu Azure SDK for Go](#install-azure-sdk-for-go).
  2. Získejte informace o metadatech z koncového bodu Resource Manageru. Koncový bod vrátí soubor JSON s informacemi potřebnými pro spouštění vašeho kódu Go.

  > [!Note]  
  > **ResourceManagerUrl** je v Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/`  
  > **ResourceManagerUrl** v integrovaných systémech je: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > Načíst metadata vyžaduje: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
  Ukázkový soubor JSON:

  ```json
  { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
    "graphEndpoint": "https://graph.windows.net/",  
    "portal Endpoint": "https://portal.local.azurestack.external/", 
    "authentication": {
      "loginEndpoint": "https://login.windows.net/", 
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
    }
  }
  ```

  3. Pokud není k dispozici, vytvořte předplatné a uložte ID předplatného pro pozdější použití. Informace o vytvoření předplatného najdete v tématu [vytvářet předplatná na nabídky ve službě Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
  4. Vytvoření instančního objektu s rozsahem "Předplatné" a **vlastníka** role. Uložte instanční objekty ID a tajný klíč. Informace o vytvoření instančního objektu pro Azure Stack najdete v tématu [vytvořit instanční objekt](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad). Nastavení vašeho prostředí Azure Stack.
  5. Importujte modul služby z Go SDK profilu ve vašem kódu. Aktuální verze profilu služby Azure Stack je **2017-03-09**. Například naimportovat modul sítě z **2017-03-09** typ profilu: 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. Ve své funkci, vytvoření a ověření klienta s **nový** volání funkce klienta. K vytvoření virtuální sítě klienta, můžete použít následující kód:  

  ````go
  package main 

  import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 

  func main() { 
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
  ````

  Nastavte `<baseURI>` k **ResourceManagerUrl** hodnota použitá v kroku 2.
  Nastavte `<subscriptionID>` k **SubscriptionID** hodnotu uložili v kroku 3.
  Pokud chcete vytvořit token, najdete v části ověřování následující části.  

  7. Vyvolání metody rozhraní API pomocí klienta, který jste vytvořili v předchozím kroku. Chcete-li například vytvořit virtuální síť pomocí našich klienta z předchozího kroku: 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  Úplný příklad vytvoření virtuální sítě s využitím Go SDK profilu ve službě Azure Stack, najdete v části [příklad](#example).

## <a name="authentication"></a>Authentication

Získat vlastnost Authorizer ze služby Azure Active Directory pomocí Go SDK, nainstalujte moduly Go AutoRest. Tyto moduly by byla již nainstalována s instalací "Go SDK"; Pokud ne, nainstalujte [ověřovací balíček na Githubu](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Jako authorizer pro klienta prostředků musí být nastavena prvek Authorizer. Existují různé metody, chcete-li získat Authorizer; Úplný seznam najdete tady.

Tato část představuje běžný způsob, jak získat tokeny authorizer ve službě Azure Stack s použitím přihlašovacích údajů klienta:

  1. Pokud objekt služby s rolí vlastník předplatného je k dispozici, tento krok přeskočte. V opačném případě vytvořte instanční objekt služby [pokyny]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) a přiřaďte ho roli "vlastník" oboru k vašemu předplatnému [pokyny]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal). Uložení ID aplikace instančního objektu služby a tajný klíč. 

  2. Import **adal** balíček z Go AutoRest ve vašem kódu. 
  
  ````go
  package main
  import "github.com/Azure/go-autorest/autorest/adal" 
  ````

  3. Vytvoření oauthConfig pomocí metody NewOAuthConfig z **adal** modulu. 
  
  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/ada1" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
  ````
   
  Nastavte `<activeDirectoryEndpoint>` na hodnotu "loginEndpoint" vlastnost z metadat ResourceManagerUrl načítat v předchozí části tohoto dokumentu.
  Nastavte `<tenantID>` hodnotu vašeho ID tenanta služby Azure Stack. 

  4. Nakonec vytvořte tokenu instančního objektu služby pomocí metody NewServicePrincipalToken z modulu adal. 

  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/adal" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  ````
  
  Nastavte `<activeDirectoryResourceID>` na jednu z hodnot v "cílovou skupinu" načíst seznam z metadat ResourceManagerUrl v předchozí části tohoto dokumentu.  
  Nastavte `<clientID>` k hlavní aplikaci služby uloží ID při vytvoření instančního objektu v předchozí části tohoto dokumentu.  
  Nastavte `<clientSecret>` k hlavní aplikaci služby tajný klíč uloženy při vytváření instančního objektu v předchozí části tohoto dokumentu.  

## <a name="example"></a>Příklad:

Tato část uvádí ukázku kódu Go k vytvoření virtuální sítě v Azure stacku. Kompletní příklady Go SDK naleznete v části [úložiště ukázek Azure Go SDk](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Azure Stack – ukázky kódu jsou k dispozici v rámci hybridní / cestě uvnitř složky služby úložiště.

> [!Note]  
> Spustí kód v tomto příkladu, ověřte, zda má předplatné použité **sítě** poskytovatele prostředků uvedené jako **registrované**. Ověřte, vyhledejte předplatné na portálu Azure Stack a klikněte na **poskytovatele prostředků.**

1. Importujte požadované balíčky v kódu. Nejnovější dostupné profilu ve službě Azure Stack používejte k importu modulu sítě. 
  
  ````go
  package main

  import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
  )
  ````

2. Definování proměnné prostředí. Chcete-li vytvořit virtuální síť máte skupinu prostředků. 

  ````go
  var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
      subscriptionID = "yourSubscriptionID"
      baseURI = "yourResourceManagerURL"
      resourceGroupName = "existingResourceGroupName"
  )
  ````

3. Teď, když jste definovali proměnné prostředí, přidejte metodu k vytvoření ověřovacího tokenu pomocí **adal** balíčku. Zobrazte podrobnosti o ověřování v předchozí části.
  
  ````go
  //CreateToken creates a service principal token
  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  }
  ````

4. Přidejte metodu main. Metoda main nejprve získá token pomocí metody, která je definována v předchozím kroku. Potom vytvoří klienta pomocí modulu sítě z profilu. Nakonec se vytvoří virtuální síť. 
  
````go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
    "github.com/Azure/go-autorest/autorest"
    "github.com/Azure/go-autorest/autorest/adal"
    "github.com/Azure/go-autorest/autorest/to"
)

var (
    activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
    tenantID = "yourAzureStackTenantID"
    clientID = "yourServicePrincipalApplicationID"
    clientSecret = "yourServicePrincipalSecret"
    activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
    subscriptionID = "yourSubscriptionID"
    baseURI = "yourResourceManagerURL"
    resourceGroupName = "existingResourceGroupName"
)

//CreateToken creates a service principal token
func CreateToken() (adal.OAuthTokenProvider, error) {
    var token adal.OAuthTokenProvider
    oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
    token, err = adal.NewServicePrincipalToken(
        *oauthConfig,
        clientID,
        clientSecret,
        activeDirectoryResourceID)
    return token, err
}

func main() {
    token, _ := CreateToken()
    vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
    vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
    future, _ := vnetClient.CreateOrUpdate(
        context.Background(),
        resourceGroupName,
        "sampleVnetName",
        network.VirtualNetwork{
            Location: to.StringPtr("local"),
            VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                AddressSpace: &network.AddressSpace{
                    AddressPrefixes: &[]string{"10.0.0.0/8"},
                },
                Subnets: &[]network.Subnet{
                    {
                        Name: to.StringPtr("subnetName"),
                        SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                            AddressPrefix: to.StringPtr("10.0.0.0/16"),
                        },
                    },
                },
            },
        })
    err := future.WaitForCompletion(context.Background(), vnetClient.Client)
    if err != nil {
        fmt.Printf(err.Error())
        return
    }
}

````

## <a name="next-steps"></a>Další postup
* [Instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md)
* [Konfigurace prostředí PowerShell uživatele Azure stacku](azure-stack-powershell-configure-user.md)  
