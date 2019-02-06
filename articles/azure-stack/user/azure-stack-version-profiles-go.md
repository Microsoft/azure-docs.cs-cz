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
ms.date: 01/19/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: fbca9eb3703777c0d20998e65bf446b5951c5091
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745397"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Použití profilů verzí API s využitím Go ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

## <a name="go-and-version-profiles"></a>Profily go a verze

Profil je kombinace různých typů prostředků s různými verzemi z různých služeb. Pomocí profilu umožňuje kombinovat a párovat mezi různých typů prostředků. Profily můžete poskytují následující výhody:

- Stabilitu pro vaši aplikaci tím, že zamknete ke konkrétním verzím rozhraní API.
- Kompatibilita pro vaše aplikace se službami Azure Stack a regionálních Datacenter Azure.

V sadě Go SDK profily jsou k dispozici v rámci cesty profily, jejich verze **rrrr-MM-DD** formátu. V tuto chvíli je nejnovější verze rozhraní API služby Azure Stack profilu **2017-03-09**. Chcete-li importovat dané služby z profilu, importujte jeho odpovídající modul z profilu. Například, chcete-li importovat **Compute** služba **2017-03-09** Profilovat, použijte následující kód:

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute"
```

## <a name="install-azure-sdk-for-go"></a>Nainstalujte sadu Azure SDK for Go

1. Instalace Gitu. Pokyny najdete v tématu [Začínáme - instalaci Gitu](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Nainstalujte [programovací jazyk Go](https://golang.org/dl). Profily rozhraní API pro Azure vyžadují Go verze 1.9 nebo novější.
3. Spuštěním následujícího příkazu bashe instalaci Go Azure SDK a jeho závislosti:

   ```bash
   go get -u -d github.com/Azure/azure-sdk-for-go/...
   ```

### <a name="the-go-sdk"></a>Go SDK

Další informace o Azure GO SDK najdete na následujících odkazech:

- Přejít Azure SDK na [instalace sady Azure SDK for Go](/go/azure/azure-sdk-go-install).
- Je veřejně dostupný na Githubu v sadě SDK Azure přejděte [azure sdk pro go](https://github.com/Azure/azure-sdk-for-go) úložiště.

### <a name="go-autorest-dependencies"></a>Závislosti na Přejít AutoRest

Go SDK závisí na Azure **Go AutoRest** modulů k odesílání požadavků REST do koncových bodů Azure Resource Manageru. Je nutné naimportovat Azure **Go AutoRest** závislostí modulů z [Go AutoRest Azure na Githubu](https://github.com/Azure/go-autorest). Příkazy bash instalace v můžete najít **nainstalovat** části.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Použití profilů Go SDK ve službě Azure Stack

Spuštění ukázky kódu Go ve službě Azure Stack, postupujte podle těchto kroků:

1. Nainstalujte sadu Azure SDK for Go a jeho závislosti. Pokyny najdete v předchozí části [nainstalujte sadu Azure SDK for Go](#install-azure-sdk-for-go).
2. Získejte informace o metadatech z koncového bodu Resource Manageru. Koncový bod vrátí soubor JSON s informacemi potřebnými pro spouštění vašeho kódu Go.

   > [!NOTE]  
   > **ResourceManagerUrl** je v Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/`  
   > **ResourceManagerUrl** v integrovaných systémech je: `https://management.<region>.<fqdn>/`  
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

3. Pokud není k dispozici, vytvořte předplatné a uložte ID předplatného pro pozdější použití. Informace o vytváření předplatného najdete v tématu [vytvářet předplatná na nabídky ve službě Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).

4. Vytvořit instanční objekt s **předplatné** oboru a **vlastníka** role. Uložení ID instančního objektu a tajného klíče. Informace o vytvoření instančního objektu pro Azure Stack najdete v tématu [vytvořit instanční objekt](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad). Vaše prostředí Azure Stack je teď nastavené.

5. Importujte modul služby z profilu Go SDK ve vašem kódu. Aktuální verze profilu služby Azure Stack je **2017-03-09**. Například naimportovat modul sítě z **2017-03-09** typ profilu, použijte následující kód:

   ```go
   package main
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
   ```

6. Ve své funkci, vytvoření a ověření klienta s **nový** volání funkce klienta. K vytvoření virtuální sítě klienta, můžete použít následující kód:  

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"

   func main() {
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
   ```

   Nastavte `<baseURI>` k **ResourceManagerUrl** hodnota použitá v kroku 2. Nastavte `<subscriptionID>` k **SubscriptionID** hodnota uložená v kroku 3.

   K vytvoření tohoto tokenu, najdete v následující části.  

7. Vyvolání metody rozhraní API pomocí klienta, který jste vytvořili v předchozím kroku. Například pokud chcete vytvořit virtuální síť pomocí klienta z předchozího kroku, viz následující příklad:

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
   func main() {
   vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
   vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

   vnetClient .CreateOrUpdate( )
   ```

Úplný příklad vytvoření virtuální sítě s použitím profilu Go SDK ve službě Azure Stack, najdete v článku [příklad](#example).

## <a name="authentication"></a>Authentication

Chcete-li získat **Authorizer** vlastnost z Azure Active Directory pomocí Go SDK, nainstalujte **Go AutoRest** moduly. Tyto moduly by byla již nainstalována s instalací "Go SDK"; Pokud ne, nainstalujte [ověřovací balíček z Githubu](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Jako authorizer pro klienta prostředků musí být nastavena prvek Authorizer. Existují různé způsoby, jak získat tokeny authorizer ve službě Azure Stack s použitím přihlašovacích údajů klienta:

1. Pokud objekt služby s rolí vlastník předplatného je k dispozici, tento krok přeskočte. V opačném případě vytvořte [instanční objekt služby](azure-stack-create-service-principals.md) a přiřaďte ho roli "vlastník" [obor ke svému předplatnému](azure-stack-create-service-principals.md#assign-the-service-principal-to-a-role). Uložení ID aplikace instančního objektu služby a tajný klíč.

2. Import **adal** balíček z Go AutoRest ve vašem kódu.

   ```go
   package main
   import "github.com/Azure/go-autorest/autorest/adal"
   ```

3. Vytvoření **oauthConfig** pomocí metody NewOAuthConfig z **adal** modulu.

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/ada1"

   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
   }
   ```

   Nastavte `<activeDirectoryEndpoint>` na hodnotu `loginEndpoint` vlastnost z `ResourceManagerUrl` načte metadata v předchozí části tohoto dokumentu. Nastavte `<tenantID>` hodnotu vašeho ID tenanta služby Azure Stack.

4. Nakonec vytvořte pomocí tokenu instančního objektu služby `NewServicePrincipalToken` metodu z **adal** modul:

   ```go
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
   ```

    Nastavte `<activeDirectoryResourceID>` na jednu z hodnot v seznamu "cílová skupina" **ResourceManagerUrl** načte metadata v předchozí části tohoto článku.
    Nastavte `<clientID>` k hlavní aplikaci služby uloží ID při vytvoření instančního objektu v předchozí části tohoto článku.
    Nastavte `<clientSecret>` na tajný klíč instančního objektu aplikace služby neuloží, když se objekt služby byl vytvořen v předchozí části tohoto článku.

## <a name="example"></a>Příklad:

Tento příklad ukazuje, ukázky kódu Go, který vytvoří virtuální síť v Azure stacku. Kompletní příklady Go SDK, najdete v článku [úložiště ukázek Azure Go SDK](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Ukázky v Azure stacku jsou k dispozici v rámci cesty hybridní uvnitř složky služby úložiště.

> [!NOTE]  
> Spustí kód v tomto příkladu, ověřte, zda má předplatné použité **sítě** poskytovatele prostředků uvedené jako **registrované**. K ověření, vyhledejte předplatné na portálu Azure Stack a vybrat **poskytovatele prostředků.**

1. Importujte požadované balíčky v kódu. Použijte nejnovější dostupné profilu ve službě Azure Stack se importovat modul sítě:

   ```go
   package main

   import (
       "context"
       "fmt"
       "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
       "github.com/Azure/go-autorest/autorest"
       "github.com/Azure/go-autorest/autorest/adal"
       "github.com/Azure/go-autorest/autorest/to"
   )
   ```

2. Definování proměnné prostředí. Pokud chcete vytvořit virtuální síť, musí mít skupinu prostředků.

   ```go
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
   ```

3. Teď, když jste definovali proměnné prostředí, přidejte metodu k vytvoření ověřovacího tokenu pomocí **adal** balíčku. Další informace o ověřování najdete v předchozí části.

   ```go
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
   ```

4. Přidat `main` metody. `main` Metoda nejprve získá token pomocí metody, která je definována v předchozím kroku. Potom vytvoří klienta pomocí modulu sítě z profilu. Nakonec se vytvoří virtuální síť.

   ```go
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
   ```

## <a name="next-steps"></a>Další postup

- [Instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md)
- [Konfigurace prostředí PowerShell uživatele Azure stacku](azure-stack-powershell-configure-user.md)  
