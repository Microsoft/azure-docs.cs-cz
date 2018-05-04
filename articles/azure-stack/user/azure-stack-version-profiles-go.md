---
title: Pomocí profilů verze rozhraní API přejděte v zásobníku Azure | Microsoft Docs
description: Další informace o použití profilů verze rozhraní API s přejděte v zásobníku Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 84ca616856f363e4d3d68ab1cc45b97f7c589185
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Použití profilů verze rozhraní API s přejděte v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

## <a name="go-and-version-profiles"></a>Profily přejděte a verze

Profil je kombinaci různých typů prostředků s různými verzemi z jiné služby. Pomocí profilu můžete kombinovat a párovat mezi různé typy prostředků. Můžete zadat profily.

 - Stabilitu pro vaši aplikaci blokováním na konkrétní verze rozhraní API.
 - Kompatibility pro vaši aplikaci s Azure zásobníku a místní datových centrech Azure.

V sadě SDK přejděte profily jsou k dispozici v části profily nebo cestu s jejich verze v **rrrr-MM-DD** formátu. Nyní, nejnovější zásobník Azure je verze profilu **2017-03-09**. Chcete-li importovat dané služby z profilu, musíte importovat jeho odpovídající modul z profilu. Například pro import **výpočetní** služby z **2017-03-09** profil:

````go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>Nainstalovat sadu Azure SDK pro přejděte

  1. Nainstalujte Git. Pokyny najdete v tématu [Začínáme - instalace Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
  2. Nainstalujte [přejděte programovací jazyk](https://golang.org/dl).  
  Profily rozhraní API pro Azure bude vyžadovat přejděte verze 1,9 nebo novější.
  3. Spuštěním následujícího příkazu bash nainstalujte sadu Azure SDK přejděte a jeho závislé součásti:
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>PŘEJDĚTE SDK

Můžete najít další informace o Azure SDK přejděte na:
- Přejděte Azure SDK na [instalace sady Azure SDK pro přejděte](https://docs.microsoft.com/go/azure/azure-sdk-go-install).
- Azure přejděte SDK je veřejně dostupné na webu GitHub na [azure sdk pro přejděte](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Přejděte AutoRest závislosti

Sada SDK přejděte závisí na moduly Azure přejděte-AutoRest na odesílání žádostí REST na koncové body Azure Resource Manager. Budete muset importovat závislostí modulu Azure přejděte-AutoRest z [Azure přejděte-AutoRest na Githubu](https://github.com/Azure/go-autorest). Můžete najít bash příkazy instalace v **nainstalovat** části.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Použití profilů přejděte SDK v Azure zásobníku

Spustit ukázku kódu přejděte v zásobníku Azure:
  1. Nainstalujte sadu Azure SDK pro přejděte a jeho závislosti. Pokyny najdete v předchozí části [instalovat sadu Azure SDK pro přejděte](#install-azure-sdk-for-go).
  2. Získáte informace metadat z koncového bodu správce prostředků. Koncový bod vrátí soubor JSON s informacemi potřebnými pro spouštění vašeho kódu přejděte.
  > [!note]  
  > **ResourceManagerUrl** je v Azure zásobníku Development Kit (ASDK): `https://management.local.azurestack.external/`  
  > **ResourceManagerUrl** v integrované systémy: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > Pro načtení metadat vyžaduje: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
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

  3. Pokud není k dispozici, vytvořit odběr a uložte ID předplatného pro pozdější použití. Informace o vytvoření odběru naleznete v tématu [vytvoření odběrů nabídky v Azure zásobníku](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
  4. Vytvoření instančního objektu s obor "Předplatného" a **vlastníka** role. Uložte objekty služby ID a tajný klíč. Informace o vytvoření objektu služby pro Azure zásobníku najdete v tématu [vytvoření instančního objektu](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad). Nastavení prostředí Azure zásobníku.
  5. Importujte modulu služby z profilu přejděte SDK do vašeho kódu. Aktuální verze profilu zásobník Azure je **2017-03-09**. Například k importu modulu sítě z **2017-03-09** profilu typu: 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. Ve funkci, vytvoření a ověření klienta s **nový** volání funkce klienta. Pokud chcete vytvořit virtuální síť klienta, můžete použít následující kód:  

  ````go
  package main 

  import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 

  func main() { 
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
  ````

  Nastavit `<baseURI>` k **ResourceManagerUrl** hodnoty používané v kroku 2.
  Nastavit `<subscriptionID>` k **SubscriptionID** hodnotu z kroku tři uložit.
  Vytvoření tokenu ověřování najdete v části níže.  

  7. Vyvolání metody rozhraní API pomocí klienta, který jste vytvořili v předchozím kroku. Chcete-li například vytvořit virtuální síť pomocí našeho klienta v předchozím kroku: 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetC1ient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  Úplný příklad vytvoření virtuální sítě v zásobníku Azure pomocí profilu přejděte SDK najdete v tématu [příklad](#example).

## <a name="authentication"></a>Authentication

GET pro vlastnost prvku Authorizer z Azure Active Directory pomocí sady SDK přejít, instalace přejděte AutoRest modulů. Tyto moduly by byl již nainstalován ve instalace "Přejděte SDK"; Pokud není, nainstalujte [ověřovací balíček na Githubu](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Prvku Authorizer musí být nastavena jako prvek authorizer pro klienta prostředků. Existují různé metody, které potřebujete prvku Authorizer; Úplný seznam najdete v tématu sem.

Tato část představuje běžné způsob, jak získat tokeny prvku authorizer v zásobníku Azure pomocí pověření klienta:

  1. Pokud objekt služby s roli vlastníka na odběru je k dispozici, tento krok přeskočte. V opačném případě vytvoření instančního objektu [pokyny]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) a přiřaďte ho roli "vlastník" obor k vašemu předplatnému [pokyny]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal). Uložte ID hlavní aplikace služby a tajný klíč. 

  2. Import **adal** balíček z AutoRest přejděte ve vašem kódu. 
  
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
   
  Nastavit `<activeDirectoryEndpoint>` na hodnotu "loginEndpoint" vlastnost z metadat ResourceManagerUrl načítat v předchozí části tohoto dokumentu.
  Nastavit `<tenantID>` hodnota, která má vaše ID klienta Azure zásobníku 

  4. Nakonec vytvořte hlavní token služby pomocí metody NewServicePrincipalToken z modulu adal. 

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
  
  Nastavit `<activeDirectoryResourceID>` na jednu z hodnot v "cílová skupina" seznamu z metadat ResourceManagerUrl načítat v předchozí části tohoto dokumentu.  
  Nastavit `<clientID>` k hlavní aplikaci služby ID uložen, pokud objekt služby byl vytvořen v předchozí části tohoto dokumentu.  
  Nastavit `<clientSecret>` k hlavní aplikaci služby tajný klíč uložen, pokud objekt služby byl vytvořen v předchozí části tohoto dokumentu.  

## <a name="example"></a>Příklad:

Tato část uvádí ukázku kódu přejděte k vytvoření virtuální sítě v Azure zásobníku. Dokončení příklady přejděte SDK najdete v tématu [úložiště Azure přejděte SDk ukázky](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Ukázek Azure zásobníku jsou k dispozici v části hybridní / cestě uvnitř složky služby úložiště.
> [!note]  
> Spustí kód v tomto příkladu, ověřte, zda má předplatné použité **sítě** poskytovatele prostředků uveden jako **registrovaná**. Chcete-li ověřit, vyhledejte odběru na portálu Azure zásobníku a klikněte na **zprostředkovatelé prostředků.**

1. Importujte požadované balíčky do vašeho kódu. K importu modulu sítě by měl použijte nejnovější dostupné profil v zásobníku Azure. 
  
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

2. Definujte proměnných prostředí. Všimněte si, že k vytvoření virtuální sítě je potřeba mít skupiny prostředků. 

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

3. Teď, když jste definovali proměnných prostředí, přidejte metodu k vytvoření ověřovacího tokenu pomocí **adal** balíčku. Zobrazit podrobnosti o ověřování v předchozí části.
  
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

4. Přidejte hlavní metodu. Hlavní metoda nejdřív získá token pomocí metody, která je definována v předchozím kroku. Potom vytvoří klienta pomocí modulu sítě z profilu. Nakonec se vytvoří virtuální síť. 
  
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
* [Konfigurace prostředí PowerShell Azure zásobník uživatele](azure-stack-powershell-configure-user.md)  
