---
title: Správa certifikátů v clusteru Azure Service Fabric
description: Popisuje, jak přidat nové certifikáty, certifikát přechodu a odebrat certifikát do clusteru Service Fabric nebo z něj.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: a3c92e1b39261af32085e4d9b6cb2462d5c0eb64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458349"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Přidání nebo odebrání certifikátů clusteru Service Fabric v Azure
Doporučujeme seznámit se s tím, jak service fabric používá certifikáty X.509, a seznámit se se [scénáři zabezpečení clusteru](service-fabric-cluster-security.md). Než budete pokračovat, musíte pochopit, co je certifikát clusteru a k čemu se používá.

Azure Service Fabrics SDK výchozí chování načtení certifikátu je nasazení a použití definovaného certifikátu s datem vypršení platnosti nejdále do budoucnosti; bez ohledu na jejich primární nebo sekundární definici konfigurace. Návrat ke klasickému chování není doporučena pokročilá akce a vyžaduje nastavení hodnoty parametru "UseSecondaryIfNewer" na hodnotu false v rámci konfigurace. `Fabric.Code`

Service Fabric umožňuje zadat dva certifikáty clusteru, primární a sekundární, při konfiguraci zabezpečení certifikátu během vytváření clusteru, kromě klientských certifikátů. Podrobnosti o jejich nastavení v době vytvoření najdete v [tématu vytvoření clusteru Azure prostřednictvím portálu](service-fabric-cluster-creation-via-portal.md) azure clusteru [prostřednictvím Azure Resource Manageru.](service-fabric-cluster-creation-via-arm.md) Pokud zadáte pouze jeden certifikát clusteru v době vytvoření, pak se používá jako primární certifikát. Po vytvoření clusteru můžete přidat nový certifikát jako sekundární.

> [!NOTE]
> Pro zabezpečený cluster budete vždy potřebovat alespoň jeden platný (neodvolaný a neprošlý) certifikát clusteru (primární nebo sekundární) nasazený (pokud ne, cluster přestane fungovat). 90 dní před vypršením platnosti platnosti všech platných certifikátů systém vygeneruje varovné trasování a také událost stavu upozornění v uzlu. V současné době neexistuje žádný e-mail nebo jiné oznámení, které Service Fabric odešle na tento článek. 
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Přidání sekundárního clusterového certifikátu pomocí portálu
Sekundární clusterový certifikát nejde přidat přes portál Azure, použijte Azure powershell. Proces je popsán dále v tomto dokumentu.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Odebrání certifikátu clusteru pomocí portálu
Pro zabezpečený cluster budete vždy potřebovat alespoň jeden platný (neodvolatený a nevypršela) certifikát. Certifikát nasazený s nejvzdálenějším do budoucího data vypršení platnosti bude používán a jeho odebráním přestane fungovat váš cluster; ujistěte se, že pouze odebrat certifikát, jehož platnost vypršela, nebo nepoužitý certifikát, jehož platnost vyprší nejdříve.

Chcete-li odebrat nepoužitý certifikát zabezpečení clusteru, přejděte do části Zabezpečení a vyberte možnost Odstranit z kontextové nabídky nepoužitého certifikátu.

Pokud máte v úmyslu odebrat certifikát, který je označen jako primární, budete muset nasadit sekundární certifikát s datem vypršení platnosti dále do budoucna než primární certifikát, což umožňuje automatické chování při převrácení. po dokončení automatického přechodu odstraňte primární certifikát.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Přidání sekundárního certifikátu pomocí aplikace Resource Manager Powershell
> [!TIP]
> Nyní existuje lepší a jednodušší způsob, jak přidat sekundární certifikát pomocí rutiny [Add-AzServiceFabricClusterCertificate.](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) Nemusíte postupovat podle zbývajících kroků v této části.  Také nepotřebujete šablonu původně použitou k vytvoření a nasazení clusteru při použití rutiny [Add-AzServiceFabricClusterCertificate.](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate)

Tyto kroky předpokládají, že jste obeznámeni s tím, jak Správce prostředků funguje a nasadili alespoň jeden cluster Service Fabric pomocí šablony Správce prostředků a máte šablonu, kterou jste použili k nastavení clusteru po ruce. Předpokládá se také, že jste spokojeni s používáním JSON.

> [!NOTE]
> Pokud hledáte ukázkovou šablonu a parametry, které můžete použít k sledování spolu nebo jako výchozí bod, stáhněte si ji z tohoto [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Úprava šablony Správce prostředků

Pro snadné sledování podél, vzorek 5-VM-1-NodeTypes-Secure_Step2.JSON obsahuje všechny úpravy budeme dělat. vzorek je k dispozici na [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample).

**Ujistěte se, že postupujte podle všech kroků**

1. Otevřete šablonu Správce prostředků, kterou jste použili k nasazení clusteru. (Pokud jste stáhli ukázku z předchozího úložiště, použijte 5-VM-1-NodeTypes-Secure_Step1.JSON k nasazení zabezpečeného clusteru a otevřete tuto šablonu).

2. Přidejte **dva nové parametry** "secCertificateThumbprint" a "secCertificateUrlValue" typu "řetězec" do části parametrů šablony. Můžete zkopírovat následující fragment kódu a přidat ho do šablony. V závislosti na zdroji šablony již můžete tyto definice definovat, pokud ano, přejděte k dalšímu kroku. 
 
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

3. Proveďte změny prostředku **Microsoft.ServiceFabric/clusters** - Vyhledejte definici prostředku Microsoft.ServiceFabric/clusters v šabloně. Pod vlastnostmi této definice najdete značku JSON "Certificate", která by měla vypadat podobně jako následující úryvek JSON:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Přidejte novou značku "thumbprintSecondary" a přidejte jí hodnotu "[parameters('secCertificateThumbprint')]".  

    Takže nyní definice prostředku by měla vypadat takto (v závislosti na zdroji šablony nemusí být přesně jako fragment níže). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Pokud chcete **certifikát převrátit**, zadejte nový certifikát jako primární a přesunutí aktuální primární jako sekundární. Výsledkem je přechod aktuálního primárního certifikátu na nový certifikát v jednom kroku nasazení.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Proveďte změny **ve všech** definicích prostředků **Microsoft.Compute/virtualMachineScaleSets** – Vyhledejte definici prostředků Microsoft.Compute/virtualMachineScaleSets. Přejděte na "vydavatel": "Microsoft.Azure.ServiceFabric", v části "virtualMachineProfile".

    V nastavení vydavatele Service Fabric byste měli vidět něco takového.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Přidání nových položek certifikátu do něj
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    Vlastnosti by nyní měly vypadat takto
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Pokud chcete **certifikát převrátit**, zadejte nový certifikát jako primární a přesunutí aktuální primární jako sekundární. Výsledkem je přechod aktuálního certifikátu na nový certifikát v jednom kroku nasazení.     

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

    Vlastnosti by nyní měly vypadat takto    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Proveďte změny ve **všech** definicích prostředků **Microsoft.Compute/virtualMachineScaleSets** – Vyhledejte definici prostředků Microsoft.Compute/virtualMachineScaleSets. Přejděte na "vaultCertificates": , v části "OSProfile". Mělo by to vypadat nějak takhle.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Přidejte do něj hodnotu secCertificateUrlValue. použijte následující úryvek:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Nyní výsledný Json by měl vypadat nějak takto.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Ujistěte se, že máte opakované kroky 4 a 5 pro všechny definice prostředků Nodetypes/Microsoft.Compute/virtualMachineScaleSets ve vaší šabloně. Pokud jeden z nich zmeškáte, certifikát se nenainstaluje do škálovací sady virtuálních strojů a v clusteru budete mít nepředvídatelné výsledky, včetně toho, že cluster bude mimo platnost (pokud skončíte bez platných certifikátů, které může cluster použít pro zabezpečení. Takže dvakrát zkontrolujte, než budete pokračovat dále.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Úprava souboru šablony tak, aby odrážel nové parametry, které jste přidali výše
Pokud používáte ukázku z [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample) následovat, můžete začít provádět změny v vzorku 5-VM-1-NodeTypes-Secure.parameters_Step2.JSON 

Upravte parametr Šablona správce prostředků Soubor, přidejte dva nové parametry pro secCertificateThumbprint a secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Nasazení šablony do Azure

- Teď jste připraveni nasadit šablonu do Azure. Otevřete příkazový řádek Azure PS verze 1+.
- Přihlaste se ke svému účtu Azure a vyberte konkrétní předplatné Azure. To je důležitý krok pro lidi, kteří mají přístup k více než jednomu předplatnému Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

Před nasazením šablonu otestujte. Použijte stejnou skupinu prostředků, do které je cluster aktuálně nasazen.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Nasaďte šablonu do skupiny prostředků. Použijte stejnou skupinu prostředků, do které je cluster aktuálně nasazen. Spusťte příkaz New-AzResourceGroupDeployment. Není nutné zadávat režim, protože výchozí hodnota je **přírůstková**.

> [!NOTE]
> Pokud nastavíte režim na dokončení, můžete neúmyslně odstranit prostředky, které nejsou v šabloně. Takže nepoužívejte v tomto scénáři.
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Zde je vyplněný příklad stejné powershellu.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

Po dokončení nasazení se připojte ke clusteru pomocí nového certifikátu a proveďte některé dotazy. Pokud jste schopni udělat. Potom můžete odstranit starý certifikát. 

Pokud používáte certifikát podepsaný svým držitelem, nezapomeňte jej importovat do místního úložiště certifikátů TrustedPeople.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Pro rychlou orientaci je zde příkaz pro připojení k zabezpečenému clusteru 

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
Pro rychlou orientaci zde je příkaz pro získání stavu clusteru

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>Nasazení klientských certifikátů do clusteru.

Ke nasazení certifikátů z trezoru klíčů do uzlů můžete použít stejné kroky, jak je uvedeno v předchozích krocích 5. Stačí definovat a používat různé parametry.


## <a name="adding-or-removing-client-certificates"></a>Přidání nebo odebrání klientských certifikátů

Kromě certifikátů clusteru můžete přidat klientské certifikáty k provádění operací správy v clusteru Service Fabric.

Můžete přidat dva druhy klientských certifikátů – správce nebo jen pro čtení. Ty pak lze použít k řízení přístupu k operacím správce a operacím dotazů v clusteru. Ve výchozím nastavení jsou certifikáty clusteru přidány do seznamu povolených certifikátů správce.

můžete zadat libovolný počet klientských certifikátů. Výsledkem každého přidání nebo odstranění je aktualizace konfigurace clusteru Service Fabric


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Přidání klientských certifikátů – správce nebo jen pro čtení prostřednictvím portálu

1. Přejděte do části Zabezpečení a v horní části části zabezpečení vyberte tlačítko +Authentication.
2. V části Přidat ověřování zvolte typ ověřování – klient jen pro čtení nebo Klient správce.
3. Nyní zvolte metodu Autorizace. To znamená service fabric, zda by měl vyhledat tento certifikát pomocí názvu subjektu nebo kryptografický otisk. Obecně není vhodné používat metodu autorizace názvu subjektu. 

![Přidat klientský certifikát][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Odstranění klientských certifikátů – správce nebo jen pro čtení pomocí portálu

Chcete-li odebrat sekundární certifikát z použití pro zabezpečení clusteru, přejděte do části Zabezpečení a vyberte možnost Odstranit z kontextové nabídky konkrétního certifikátu.

## <a name="adding-application-certificates-to-a-virtual-machine-scale-set"></a>Přidání aplikačních certifikátů do škálovací sady virtuálních strojů

Informace o nasazení certifikátu, který používáte pro aplikace, do clusteru naleznete [v této ukázkové mašle Powershellu](scripts/service-fabric-powershell-add-application-certificate.md).

## <a name="next-steps"></a>Další kroky
Další informace o správě clusteru naleznete v těchto článcích:

* [Proces upgradu clusteru Service Fabric a očekávání od vás](service-fabric-cluster-upgrade.md)
* [Nastavení přístupu založeného na rolích pro klienty](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


