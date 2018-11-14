---
title: Správa certifikátů v clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Popisuje, jak přidat nové certifikáty, certifikát výměny a odebrat certifikát do nebo z clusteru Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2018
ms.author: aljo-microsoft
ms.openlocfilehash: cca48e78278cf234d6f6062eadac4f85ecbcb79b
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51625925"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Přidat nebo odebrat certifikáty pro cluster Service Fabric v Azure
Doporučujeme seznámit se s jak Service Fabric používá certifikáty X.509 a znáte [scénáře zabezpečení clusteru](service-fabric-cluster-security.md). Musíte porozumět tomu, jaký certifikát clusteru je a k čemu slouží, než budete pokračovat dál.

Azure Service Fabric SDK výchozí certifikát zatížení chování, je při nasazení a používání definovaného certifikátu do budoucna; nejvzdálenější datum konce platnosti bez ohledu na jejich definice primární nebo sekundární konfiguraci. Návrat k classic chování je bez doporučená pokročilé akce a vyžaduje nastavení hodnotu "UseSecondaryIfNever" nastavení parametru na hodnotu false v rámci konfigurace Fabric.Code.

Service fabric umožňuje zadat dva certifikáty clusteru, primární a sekundární lokalitou, při konfiguraci certifikátů zabezpečení během vytváření clusteru, kromě klientských certifikátů. Odkazovat na [vytváření clusteru služby azure prostřednictvím portálu](service-fabric-cluster-creation-via-portal.md) nebo [vytváření clusteru služby azure prostřednictvím Azure Resource Manageru](service-fabric-cluster-creation-via-arm.md) pro informace o nastavení je na čas vytvoření. Pokud zadáte jenom jeden certifikát clusteru na doba pro vytvoření, pak, která se používá jako primární certifikát. Po vytvoření clusteru můžete přidat nový certifikát jako sekundární.

> [!NOTE]
> K zabezpečenému clusteru budete vždy potřebovat aspoň jeden platný (není odvolaný a nesmí mít ukončenou platnost) clusteru certifikátu (primární nebo sekundární) nasazeného (Pokud ne, přestane cluster fungovat). 90 dní, než všechny platné certifikáty oslovit vypršení platnosti, systém vygeneruje upozornění trasování a také událost stavu upozornění na uzlu. Neexistuje momentálně žádný e-mailu nebo jiné oznámení, která rozešle Service Fabric v tomto článku. 
> 
> 

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Přidání sekundárního certifikátu clusteru pomocí portálu
Nelze přidat sekundárního certifikátu clusteru na webu Azure portal, pomocí Azure powershellu. Proces je popsaný dále v tomto dokumentu.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Odebrat certifikát clusteru pomocí portálu
K zabezpečenému clusteru musíte vždy alespoň jeden platný certifikát (není odvolaný a nesmí mít ukončenou platnost). Certifikát nasadili s nejvzdálenější do budoucí datum konce platnosti bude používán, a jeho odebrání způsobí, že váš cluster přestane fungovat, Nezapomeňte odebrat jenom certifikát, jehož platnost vypršela nebo certifikát nevyužité, jejíž platnost vyprší nejdříve.

Odebrat certifikát zabezpečení nevyužité clusteru, přejděte do části zabezpečení a vyberte možnost "Odstranit" z místní nabídky se nepoužívaný certifikát odebral.

Pokud máte v úmyslu odebrat certifikát, který je označen jako primární, pak budete muset nasadit sekundární certifikát, u nichž vyprší platnost datum dále do budoucna než primární certifikát povolení Změna chování automatického; Odstraňte primární certifikát po dokončení automatického přechodu.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Přidání sekundárního certifikátu přes Powershell Resource Manageru
> [!TIP]
> To je teď vyšší a jednodušší způsob přidání sekundárního certifikátu pomocí [Add-AzureRmServiceFabricClusterCertificate](/powershell/module/azurerm.servicefabric/add-azurermservicefabricclustercertificate) rutiny. Není nutné postupujte podle zbývajících kroků v této části.  Navíc není nutné původně použili k vytvoření a nasazení clusteru při použití šablony [Add-AzureRmServiceFabricClusterCertificate](/powershell/module/azurerm.servicefabric/add-azurermservicefabricclustercertificate) rutiny.

Tyto kroky předpokládají se seznámíte s tím, jak funguje Resource Manageru a nasadili alespoň jeden cluster Service Fabric pomocí šablony Resource Manageru a jste šablonu, kterou jste použili k nastavení clusteru po ruce. Taky se předpokládá, že jste obeznámeni pomocí formátu JSON.

> [!NOTE]
> Pokud hledáte Ukázková šablona a parametry, které vám umožní sledovat podél nebo jako výchozí bod, pak si ho stáhnout z této [úložiště git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Upravte svou šablonu Resource Manageru

Z důvodu snadnějšího následující podél obsahuje ukázkové 5-VM-1-NodeTypes-Secure_Step2.JSON všechny úpravy, které budeme provádět. Ukázka je k dispozici na [úložiště git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

**Ujistěte se, že všechny kroky**

1. Otevřete šablonu Resource Manageru, můžete použít k nasazení clusteru. (Pokud jste si stáhli ukázku z předchozích úložiště, pak pomocí 5-VM-1-NodeTypes-Secure_Step1.JSON nasazení zabezpečeného clusteru a pak otevřou tuto šablonu).

2. Přidat **dva nové parametry** "secCertificateThumbprint" a "secCertificateUrlValue" z typ "řetězec" části šablony. Můžete zkopírovat následující fragment kódu a přidat do šablony. V závislosti na zdroji šablony už můžete mít tyto definováno, pokud tedy možnost přesunout k dalšímu kroku. 
 
    ```json
       "secCertificateThumbprint": {
          "type": "string",
          "metadata": {
            "description": "Certificate Thumbprint"
          }
        },
        "secCertificateUrlValue": {
          "type": "string",
          "metadata": {
            "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
          }
        },
    
    ```

3. Proveďte změny **Microsoft.ServiceFabric/clusters** prostředek – najděte definici prostředku "Microsoft.ServiceFabric/clusters" v šabloně. V části Vlastnosti definice zjistíte "Certifikátu" JSON značky, který by měl vypadat přibližně jako následující fragment kódu JSON:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Přidat novou značku "thumbprintSecondary" a přiřaďte jí hodnotu "[parameters('secCertificateThumbprint')]".  

    Takže teď definice prostředků by měl vypadat jako následující (v závislosti na zdroji šablony, nemusí být přesně podobný následujícímu fragmentu). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Pokud chcete **nespotřebujete cert**, zadejte nový certifikát jako primární a přesunutí aktuální primární jako sekundární. Výsledkem je váš aktuální primární certifikát výměny na nový certifikát v jednom kroku nasazení.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Proveďte změny v **všechny** **Microsoft.Compute/virtualMachineScaleSets** definic prostředků – najděte definici prostředku Microsoft.Compute/virtualMachineScaleSets. Přejít na "vydavatele": "Microsoft.Azure.ServiceFabric" v části "virtualMachineProfile".

    V nastavení aplikace publisher Service Fabric byste měli vidět asi takhle nějak.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Přidání nové položky certifikátu k němu
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    Vlastnosti by teď měl vypadat takto
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Pokud chcete **nespotřebujete cert**, zadejte nový certifikát jako primární a přesunutí aktuální primární jako sekundární. Výsledkem je váš aktuální certifikát výměny na nový certifikát v jednom kroku nasazení.     

    ```json
                   "certificate": {
                       "thumbprint": "[parameters('secCertificateThumbprint')]",
                       "x509StoreName": "[parameters('certificateStoreValue')]"
                         },
                   "certificateSecondary": {
                        "thumbprint": "[parameters('certificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    ```

    Vlastnosti by teď měl vypadat takto    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Proveďte změny v **všechny** **Microsoft.Compute/virtualMachineScaleSets** definic prostředků – najděte definici prostředku Microsoft.Compute/virtualMachineScaleSets. Přejít na "vaultCertificates":, v části "OSProfile". by měl vypadat asi takhle nějak.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Přidejte secCertificateUrlValue do ní. Pomocí následujícího fragmentu kódu:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Výsledný kód Json by měl nyní vypadat asi takhle nějak.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Ujistěte se, že mají opakovat kroky 4 a 5 pro všechny definice Nodetypes/Microsoft.Compute/virtualMachineScaleSets prostředků ve vaší šabloně. Pokud jeden z nich neproběhly, certifikátu získat nenainstalují, škálovací sady virtuálních počítačů a budete mít nepředvídatelné výsledky v clusteru, včetně clusteru směrem dolů (je-li skončit s žádné platné certifikáty, které cluster může použít pro zabezpečení. Takže Překontrolujte, než budete pokračovat.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Upravte soubor šablony tak, aby odrážela nové parametry, které jste přidali výše
Pokud používáte ukázku z [úložiště git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) Pokud chcete postup sledovat, můžete začít provádět změny v ukázkové 5-VM-1-NodeTypes-Secure.parameters_Step2.JSON 

Upravit parametr šablony Resource Manageru souboru, přidejte dva nové parametry pro secCertificateThumbprint a secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Nasazení šablony do Azure

- Nyní jste připraveni k nasazení šablony do Azure. Otevřete příkazový řádek verze 1 a Azure PS.
- Přihlaste se ke svému účtu Azure a vybrat konkrétní předplatné azure. Toto je důležitý krok pro ty, kteří mají přístup k více než jedno předplatné azure.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subcription ID> 

```

Otestujte šablonu před jeho nasazení. Použijte stejnou skupinu prostředků, který váš cluster je aktuálně nasazený do.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Nasazení šablony do vaší skupiny prostředků. Použijte stejnou skupinu prostředků, který váš cluster je aktuálně nasazený do. Spusťte příkaz New-AzureRmResourceGroupDeployment. Není potřeba určení režimu, protože výchozí hodnota je **přírůstkové**.

> [!NOTE]
> Pokud nastavíte režim na dokončeno, můžete neúmyslně odstranit prostředky, které nejsou v šabloně. Proto nepoužívejte ho v tomto scénáři.
> 
> 

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Zde je příklad vyplněné stejné prostředí PowerShell.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

Po dokončení nasazení se připojit ke clusteru pomocí nového certifikátu a provést některé dotazy. Pokud je to možné udělat. Potom můžete odstranit starý certifikát. 

Pokud používáte certifikát podepsaný svým držitelem, nezapomeňte je importovat do místního úložiště certifikátů TrustedPeople.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Pro rychlou referenci tady je příkaz pro připojení k zabezpečenému clusteru 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
Pro rychlou referenci tady je příkaz pro získání stavu clusteru

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-application-certificates-to-the-cluster"></a>Nasazení certifikátů aplikace do clusteru.

Jak je uvedeno v předchozí kroky 5 můžete použít stejný postup k získání certifikátů nasazenému do uzlů z trezoru klíčů. Stačí definovat a použít různé parametry.


## <a name="adding-or-removing-client-certificates"></a>Přidání nebo odebrání klientské certifikáty

Kromě certifikátů clusteru můžete přidat klientské certifikáty k provádění operací správy v clusteru Service Fabric.

Můžete přidat dva druhy klientské certifikáty - správce nebo jen pro čtení. To pak umožňuje řídit přístup ke konzole správce operations Console a operace dotazů v clusteru. Ve výchozím nastavení certifikáty clusteru se přidají do seznamu povolených certifikátů správce.

můžete zadat libovolný počet klientských certifikátů. Každý přidávání a odstraňování výsledkem aktualizace konfigurace do clusteru Service Fabric


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Přidání klientské certifikáty - správce nebo jen pro čtení přes portál

1. Přejděte do části zabezpečení a vyberte "+ ověřování" tlačítko nad části zabezpečení.
2. V části "Přidání ověření" Vyberte "Ověřování typu" – "client jen pro čtení" nebo "Klient pro správu.
3. Teď vyberte metodu ověřování. To znamená do Service Fabric, zda ji by měl tento certifikát vyhledat pomocí názvu předmětu nebo kryptografického otisku. Obecně platí není osvědčeným postupem zabezpečení použijte metodu ověření názvu subjektu. 

![Přidat klientský certifikát][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Odstranění klientské certifikáty - správce nebo jen pro čtení pomocí portálu

K odebrání sekundární certifikát se používají pro zabezpečení clusteru, přejděte do části zabezpečení a vyberte možnost "Odstranit" z místní nabídky u konkrétního certifikátu.

## <a name="next-steps"></a>Další postup
Přečtěte si tyto články pro další informace o správě clusteru:

* [Proces upgradu clusteru Service Fabric a očekáváních od vás](service-fabric-cluster-upgrade.md)
* [Nastavení přístupu na základě rolí pro klienty](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


