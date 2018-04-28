---
title: Správa certifikátů v clusteru služby Azure Service Fabric | Microsoft Docs
description: Popisuje, jak přidat nové certifikáty, certifikát výměny a odebrat certifikát do nebo z clusteru Service Fabric.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/23/2018
ms.author: chackdan
ms.openlocfilehash: 70de9ed6175a5fe7a8e3eed9c0a921cf8184d50d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Přidat nebo odebrat certifikáty pro cluster Service Fabric v Azure
Doporučujeme Seznamte se s jak Service Fabric používá certifikáty X.509 a znát [clusteru scénáře zabezpečení](service-fabric-cluster-security.md). Je potřeba pochopit, jaké certifikát clusteru je a co se používá, před pokračováním.

Service fabric umožňuje zadat dva certifikáty clusteru, primárního a sekundárního, když konfigurujete certifikát zabezpečení při vytváření clusteru, kromě klientských certifikátů. Odkazovat na [vytváření clusteru služby azure přes portál](service-fabric-cluster-creation-via-portal.md) nebo [vytváření clusteru služby azure pomocí Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) pro podrobnosti o jejich nastavení na vytvořit čas. Pokud zadáte jenom jeden certifikát clusteru na doba pro vytvoření, pak, který se používá jako primární certifikát. Po vytvoření clusteru můžete přidat nového certifikátu jako sekundární.

> [!NOTE]
> Pro cluster s podporou zabezpečení je vždy nutné alespoň jeden platný clusteru (není odvolaný a ne jeho platnost) certifikátu (primární nebo sekundární) nasazeného (Pokud ne, přestane cluster fungovat). 90 dní před všechny platné certifikáty dosáhnout vypršení platnosti, systém vygeneruje upozornění trasování a také událost stavu upozornění na uzlu. Není aktuálně žádné e-mailu nebo všechna oznámení, která odesílá Service Fabric, v tomto článku. 
> 
> 

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Přidat certifikát sekundární clusteru pomocí portálu
Nelze přidat certifikát sekundární clusteru prostřednictvím portálu Azure, použijte prostředí Azure powershell. Proces popsané dál v tomto dokumentu.

## <a name="swap-the-cluster-certificates-using-the-portal"></a>Vyměnit certifikáty clusteru pomocí portálu
Po úspěšně jste nasadili certifikát sekundární clusteru, pokud chcete Prohodit primární a sekundární, pak přejděte do části zabezpečení a vyberte možnost 'Prohození s primární' v místní nabídce se Prohodit sekundární certifikátu s primární certifikát.

![Swap certifikátu][Delete_Swap_Cert]

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Odebrat certifikát clusteru pomocí portálu
Pro cluster s podporou zabezpečení budete vždy potřebovat alespoň jeden platný (není odvolaný a ne jeho platnost) certifikát (primární nebo sekundární) nasazené Pokud ne, přestane cluster fungovat.

K odebrání sekundární certifikátu používá pro zabezpečení clusteru, přejděte do části zabezpečení a vyberte možnost 'Delete' z kontextové nabídky na sekundární certifikátu.

Pokud vaše záměrem odebrat certifikát, který je označen jako primární, pak budete muset Prohodit s sekundární a potom odstraňte sekundární po dokončení upgradu.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Přidat sekundární certifikát pomocí Správce prostředků Powershell
> [!TIP]
> Teď je lepší a jednodušší způsob, jak přidat sekundární certifikátu pomocí [přidat AzureRmServiceFabricClusterCertificate](/powershell/module/azurerm.servicefabric/add-azurermservicefabricclustercertificate) rutiny. Nemusíte postupujte podle zbývajících kroků v této části.  Navíc není nutné použili k vytvoření a nasazení clusteru při použití šablony [přidat AzureRmServiceFabricClusterCertificate](/powershell/module/azurerm.servicefabric/add-azurermservicefabricclustercertificate) rutiny.

Tento postup předpokládá se seznámíte s fungování Resource Manager a nasadili alespoň jeden pomocí šablony Resource Manageru cluster Service Fabric a mít šablony, která jste použili k nastavení užitečné clusteru. Taky se předpokládá, že umíte pomocí JSON.

> [!NOTE]
> Pokud hledáte vzorové šablony a parametry, které můžete použít postup popsaný společně nebo jako výchozí bod, poté ji stáhnout z tohoto [úložiště git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Upravte svou šablonu Resource Manager

Pro usnadnění následující společně obsahuje ukázkové 5-VM-1-NodeTypes-Secure_Step2.JSON všechny úpravy, které budeme provádět. Ukázka je dostupná v [úložiště git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

**Zajistěte, aby k provedení všech kroků**

1. Otevřete šablony Resource Manageru, můžete použít k nasazení můžete clusteru. (Pokud jste si stáhli ukázky z předchozí úložiště, pak 5-VM-1-NodeTypes-Secure_Step1.JSON použijte k nasazení clusteru s podporou zabezpečení a potom otevře tato šablona).

2. Přidat **dva nové parametry** "secCertificateThumbprint" a "secCertificateUrlValue" z typu "řetězec" do části parametr šablony. Můžete zkopírovat následující fragment kódu a přidání do šablony. V závislosti na zdroji šablony už můžete mít tyto definována, pokud tak přesunout k dalšímu kroku. 
 
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

3. Proveďte změny **Microsoft.ServiceFabric/clusters** prostředku, vyhledejte "Microsoft.ServiceFabric/clusters" definice prostředků ve vaší šabloně. V části Vlastnosti definice zjistíte "Certifikát" JSON značku, která by měla vypadat podobně jako následujícím fragmentu kódu JSON:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Přidat novou značku "thumbprintSecondary" a dejte mu hodnotu "[parameters('secCertificateThumbprint')]".  

    Ano, teď by měl vypadat jako následující definici prostředků (v závislosti na vaší zdrojové šablony, nemusí být úplně stejně jako na následujícím fragmentu). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Pokud chcete **mění certifikát**, zadejte nový certifikát jako primární a přesunutí aktuální primární jako sekundární. Výsledkem výměny aktuální primární certifikátu na nový certifikát v jednom kroku nasazení.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Proveďte změny **všechny** **Microsoft.Compute/virtualMachineScaleSets** definice prostředků - vyhledání Microsoft.Compute/virtualMachineScaleSets definici prostředků. Posuňte se "vydavatel": "Microsoft.Azure.ServiceFabric" v části "virtualMachineProfile".

    V nastavení vydavatele Service Fabric měli byste vidět zhruba takhle.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Do ní přidejte nové položky certifikátu.
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    Vlastnosti by teď měl vypadat takto
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Pokud chcete **mění certifikát**, zadejte nový certifikát jako primární a přesunutí aktuální primární jako sekundární. Výsledkem výměny aktuální certifikát na nový certifikát v jednom kroku nasazení.     

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

5. Proveďte změny **všechny** **Microsoft.Compute/virtualMachineScaleSets** definice prostředků - vyhledání Microsoft.Compute/virtualMachineScaleSets definici prostředků. Posuňte se "vaultCertificates":, v části "OSProfile". by měla vypadat přibližně takto.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    SecCertificateUrlValue přidejte do ní. použijte následující fragment kódu:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Výsledný formát Json by měl nyní vypadat přibližně takto.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Ujistěte se, že obsahovat opakované kroky 4 a 5 pro všechny Nodetypes/Microsoft.Compute/virtualMachineScaleSets definice prostředků ve vaší šabloně. Pokud jeden z nich přeskočíte, certifikát získat nenainstalují, škálovací sady virtuálních počítačů a máte vést k neočekávaným výsledkům v clusteru, včetně clusteru směrem dolů (Pokud skončili žádné platné certifikáty, které může cluster používat pro zabezpečení. Ano Překontrolujte, než budete pokračovat.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Upravte svůj soubor šablony tak, aby odrážely novou parametry, které jste přidali výše
Pokud používáte ukázku z [úložiště git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) se podle nich zorientujete, můžete začít proveďte změny v ukázkové 5-VM-1-NodeTypes-Secure.paramters_Step2.JSON 

Upravit daný parametr šablony Resource Manageru soubor, přidejte dva nové parametry pro secCertificateThumbprint a secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Nasazení šablony Azure

- Nyní jste připraveni k nasazení vaší šablony do Azure. Otevřete příkazový řádek se verze 1 + Azure PS.
- Přihlaste se k účtu Azure a vybrat konkrétní předplatné azure. Toto je důležitý krok pro zaměstnance, kteří mají přístup k více než jedno předplatné.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subcription ID> 

```

Testování šablony před jeho nasazení. Použijte stejnou skupinu prostředků clusteru aktuálně nasazené na.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Nasazení šablony do skupiny prostředků. Použijte stejnou skupinu prostředků clusteru aktuálně nasazené na. Spuštěním příkazu New-AzureRmResourceGroupDeployment. Není potřeba určit režimu, protože výchozí hodnota je **přírůstkové**.

> [!NOTE]
> Pokud nastavíte režim na dokončeno, můžete nechtěně odstranit prostředky, které nejsou ve vaší šabloně. Nepoužívejte ho v tomto scénáři.
> 
> 

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Tady je příklad vyplněné stejné prostředí PowerShell.

```powershell
$ResouceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResouceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResouceGroup2

```

Po dokončení nasazení se připojit ke clusteru pomocí nového certifikátu a provádět některé dotazy. Pokud budete moci provést. Potom můžete odstranit starý certifikát. 

Pokud používáte certifikát podepsaný svým držitelem, nezapomeňte znovu importujte je do místního úložiště certifikátů TrustedPeople.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Pro rychlou referenci tady je příkaz k připojení do clusteru s podporou zabezpečení 

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

## <a name="deploying-application-certificates-to-the-cluster"></a>Nasazení certifikátů aplikací do clusteru.

Jak je uvedeno v předchozí kroky 5 můžete pomocí stejných kroků certifikátů z keyvault nasazena do uzlů. Stačí nutné definovat a použít jiné parametry.


## <a name="adding-or-removing-client-certificates"></a>Přidání nebo odebrání klientských certifikátů

Kromě certifikátů clusteru můžete přidat klientských certifikátů mohli provádět operace správy na cluster Service Fabric.

Můžete přidat dva druhy klientské certifikáty - správce nebo jen pro čtení. Tyto pak můžete použít k řízení přístupu k dotazu operace na clusteru a operace správce. Ve výchozím nastavení jsou certifikáty clusteru přidat do seznamu povolených certifikáty správce.

můžete zadat libovolný počet klientských certifikátů. Každý přidávání a odstraňování výsledků v aktualizaci konfigurace clusteru Service Fabric


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Přidání klientské certifikáty - správce nebo jen pro čtení přes portál

1. Přejděte do části zabezpečení a vyberte '+ ověřování' tlačítko nad části zabezpečení.
2. V části "přidat ověření vyberte"Ověřování typu"- 'jen pro čtení klienta' nebo 'správce klienta.
3. Teď zvolte metoda autorizace. To znamená do Service Fabric, zda by měla vypadat tohoto certifikátu pomocí názvu subjektu nebo kryptografický otisk. Obecně platí není dobrým zvykem lze pomocí této metody ověřování názvu subjektu. 

![Přidání certifikátu klienta][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Odstranění klientské certifikáty - správce nebo jen pro čtení pomocí portálu

K odebrání sekundární certifikátu používá pro zabezpečení clusteru, přejděte do části zabezpečení a vyberte možnost 'Delete' z kontextové nabídky na konkrétní certifikátu.

## <a name="next-steps"></a>Další postup
Přečtěte si tyto články pro další informace o správě clusteru:

* [Proces upgradu Service Fabric Cluster a očekávání od vás](service-fabric-cluster-upgrade.md)
* [Instalační program přístup na základě rolí pro klienty](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Delete_Swap_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_09.PNG
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


