---
title: Správa certifikátů v clusteru Azure Service Fabric
description: Popisuje, jak přidat nové certifikáty, certifikát výměny a odebrat certifikát do nebo z Service Fabricho clusteru.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: b1ccf83e666f9106a31809ff41d55062826be78c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88869741"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Přidání nebo odebrání certifikátů clusteru Service Fabric v Azure
Doporučujeme, abyste se seznámili s tím, jak Service Fabric využívá certifikáty X. 509 a znáte [scénáře zabezpečení clusteru](service-fabric-cluster-security.md). Než budete pokračovat, musíte pochopit, co je certifikát clusteru a k čemu se používá.

Výchozí chování při načítání certifikátů v sadě SDK služby Azure Service Fabric je nasazení a používání definovaného certifikátu s nejpozdějším datem vypršení platnosti. bez ohledu na definici primární nebo sekundární konfigurace. Návrat k klasickému chování je nedoporučovaná rozšířená akce a vyžaduje nastavení hodnoty parametru "UseSecondaryIfNewer" na hodnotu false v rámci vaší `Fabric.Code` konfigurace.

Service Fabric umožňuje zadat dva certifikáty clusteru, primární a sekundární, když konfigurujete zabezpečení certifikátu během vytváření clusteru, kromě klientských certifikátů. Podrobnosti o jejich nastavení v době vytvoření najdete v tématu [Vytvoření clusteru Azure prostřednictvím portálu](service-fabric-cluster-creation-via-portal.md) nebo [Vytvoření clusteru Azure prostřednictvím Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) . Pokud zadáte pouze jeden certifikát clusteru v čase vytvoření, bude použit jako primární certifikát. Po vytvoření clusteru můžete přidat nový certifikát jako sekundární.

> [!NOTE]
> V případě zabezpečeného clusteru budete vždycky potřebovat aspoň jeden platný (odvolaný a neprošlý) certifikát clusteru (primární nebo sekundární) nasazený (Pokud ne, cluster přestane fungovat). 90 dní před vypršením platnosti všech platných certifikátů systém vygeneruje trasování upozornění a také událost stavu varování v uzlu. V tuto chvíli neexistují žádné e-maily ani žádná další oznámení, která Service Fabric od tohoto článku odesílají. 
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Přidání certifikátu sekundárního clusteru pomocí portálu
Certifikát sekundárního clusteru nelze přidat prostřednictvím Azure Portal, použijte prostředí Azure PowerShell. Tento postup je popsaný dále v tomto dokumentu.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Odebrání certifikátu clusteru pomocí portálu
V případě zabezpečeného clusteru budete vždycky potřebovat alespoň jeden platný certifikát (nikoli odvolaný a neprošlý platnost). Certifikát nasazený s nejdálený do budoucího data vypršení platnosti se bude používat a jeho odebrání způsobí, že cluster přestane fungovat. Ujistěte se, že jste odebrali jenom certifikát, jehož platnost vypršela, nebo nepoužitý certifikát, jehož platnost brzy vyprší.

Chcete-li odebrat nepoužívaný certifikát zabezpečení clusteru, přejděte do části zabezpečení a vyberte možnost odstranit z kontextové nabídky na nepoužívaném certifikátu.

Pokud je vaším záměrem odebrat certifikát, který je označen jako primární, bude nutné nasadit sekundární certifikát s datem vypršení platnosti do budoucna, než je primární certifikát, a povolit tak chování funkce Automatické přecházení. Po dokončení automatické výměny odstraňte primární certifikát.

## <a name="add-a-secondary-certificate-using-azure-resource-manager"></a>Přidání sekundárního certifikátu pomocí Azure Resource Manager

Tento postup předpokládá, že jste obeznámeni s tím, jak Správce prostředků funguje a že jste nasadili aspoň jeden Service Fabric cluster pomocí šablony Správce prostředků a máte šablonu, kterou jste použili k nastavení praktického clusteru. Také se předpokládá, že máte v kódu JSON pohodlí.

> [!NOTE]
> Pokud hledáte ukázkovou šablonu a parametry, které můžete použít k následnému nebo jako výchozímu bodu, Stáhněte si ho z tohoto [úložiště Git](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Úprava šablony Správce prostředků

Pro usnadnění následujících funkcí se vzorový 5-VM-1-NodeTypes-Secure_Step2.JSna obsahuje všechny úpravy, které budeme dělat. Tato ukázka je k dispozici v [úložišti Git](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample).

**Ujistěte se, že provedete všechny kroky.**

1. Otevřete šablonu Správce prostředků, kterou jste použili k nasazení clusteru. (Pokud jste si stáhli ukázku z předchozího úložiště, použijte 5-VM-1-NodeTypes-Secure_Step1.JSna k nasazení zabezpečeného clusteru a pak otevřete tuto šablonu).

2. Přidejte **dva nové parametry** "secCertificateThumbprint" a "secCertificateUrlValue" typu "String" do oddílu parametru vaší šablony. Následující fragment kódu můžete zkopírovat a přidat ho do šablony. V závislosti na zdroji šablony už možná máte tato definovaná definice, pokud se tak chcete přesunout k dalšímu kroku. 
 
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

3. Proveďte změny v prostředku **Microsoft. ServiceFabric/Clusters** – v šabloně vyhledejte definici prostředků Microsoft. ServiceFabric/Clusters. V části vlastnosti této definice najdete značku JSON "Certificate", která by měla vypadat jako následující fragment kódu JSON:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Přidejte novou značku "thumbprintSecondary" a přiřaďte jí hodnotu "[Parameters", "secCertificateThumbprint")] ".  

    Takže teď definice prostředků by měla vypadat jako v následujícím příkladu (v závislosti na zdroji šablony to nemusí být přesně stejně jako fragment kódu). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Pokud chcete **Přejít na certifikát**, zadejte nový certifikát jako primární a přemístěte aktuální primární objekt as Secondary. Výsledkem je výměna aktuálního primárního certifikátu k novému certifikátu v jednom kroku nasazení.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Proveďte **změny v** definicích prostředků **Microsoft. COMPUTE/virtualMachineScaleSets** – vyhledejte definici prostředků Microsoft. COMPUTE/virtualMachineScaleSets. V části "virtualMachineProfile" přejděte na vydavatele ": Microsoft. Azure. ServiceFabric".

    V nastavení vydavatele Service Fabric by se mělo zobrazit něco podobného.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Přidejte do něj nové položky certifikátu.
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    Vlastnosti by teď měly vypadat takto.
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Pokud chcete **Přejít na certifikát**, zadejte nový certifikát jako primární a přemístěte aktuální primární objekt as Secondary. Výsledkem je výměna aktuálního certifikátu k novému certifikátu v jednom kroku nasazení.     

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

    Vlastnosti by teď měly vypadat takto.    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Proveďte **změny v** definicích prostředků **Microsoft. COMPUTE/virtualMachineScaleSets** – vyhledejte definici prostředků Microsoft. COMPUTE/virtualMachineScaleSets. Posuňte se do části "vaultCertificates": v části "OSProfile". mělo by to vypadat nějak takto.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Přidejte do něj secCertificateUrlValue. použijte následující fragment kódu:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Výsledný JSON by teď měl vypadat nějak takto.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Ujistěte se, že jste provedli opakované kroky 4 a 5 pro všechny definice prostředků NodeType/Microsoft. COMPUTE/virtualMachineScaleSets v šabloně. Pokud jste si jeden z nich nedostali, certifikát se v této sadě škálování virtuálního počítače nenainstaluje a v clusteru budete mít nepředvídatelné výsledky, včetně clusteru (Pokud skončíte s žádnými platnými certifikáty, které může cluster pro zabezpečení použít). Před dalším pokračováním zakontrolujte.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Upravte soubor šablony tak, aby odrážel nové parametry, které jste přidali výše.
Pokud používáte ukázku z [úložiště Git](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample) , abyste mohli postupovat podle, můžete začít provádět změny v ukázkové 5-VM-1-NodeTypes-Secure.parameters_Step2.JSdne. 

Upravte soubor parametrů šablony Správce prostředků přidejte dva nové parametry pro secCertificateThumbprint a secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Nasazení šablony do Azure

- Teď jste připraveni nasadit šablonu do Azure. Otevřete Azure PS verze 1 a příkazový řádek.
- Přihlaste se ke svému účtu Azure a vyberte konkrétní předplatné Azure. Toto je důležitý krok pro lidé, který má přístup k více než jednomu předplatnému Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

Před nasazením šablonu otestujte. Použijte stejnou skupinu prostředků, do které je váš cluster aktuálně nasazený.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Nasaďte šablonu do vaší skupiny prostředků. Použijte stejnou skupinu prostředků, do které je váš cluster aktuálně nasazený. Spusťte příkaz New-AzResourceGroupDeployment. Nemusíte určovat režim, protože výchozí hodnota je **přírůstková**.

> [!NOTE]
> Pokud nastavíte režim na dokončeno, můžete nechtěně odstranit prostředky, které nejsou ve vaší šabloně. Nepoužívejte ho proto v tomto scénáři.
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Tady je vyplněný příklad stejného prostředí PowerShell.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

Po dokončení nasazení se připojte ke clusteru pomocí nového certifikátu a proveďte některé dotazy. Pokud je to možné. Pak můžete odstranit starý certifikát. 

Pokud používáte certifikát podepsaný svým držitelem, nezapomeňte ho importovat do úložiště certifikátů místní TrustedPeople.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Stručné informace najdete v tématu připojení k zabezpečenému clusteru. 

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
Stručný přehled najdete tady příkazem pro získání stavu clusteru.

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>Nasazují se klientské certifikáty do clusteru.

Můžete použít stejný postup, jak je uvedeno v předchozích krocích 5, aby byly certifikáty nasazeny z trezoru klíčů do uzlů. Pouze potřebujete definovat a používat jiné parametry.


## <a name="adding-or-removing-client-certificates"></a>Přidávání nebo odebírání klientských certifikátů

Kromě certifikátů clusteru můžete přidat klientské certifikáty k provádění operací správy v clusteru Service Fabric.

Můžete přidat dva typy klientských certifikátů – správce nebo jen pro čtení. Ty pak můžete použít k řízení přístupu k operacím správy a operacím dotazů v clusteru. Ve výchozím nastavení se certifikáty clusteru přidají do seznamu povolených certifikátů správců.

Můžete zadat libovolný počet klientských certifikátů. Každé přidání nebo odstranění má za následek aktualizaci konfigurace clusteru Service Fabric.


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Přidání klientských certifikátů – správce nebo Read-Only prostřednictvím portálu

1. Přejděte do části zabezpečení a v části zabezpečení vyberte tlačítko + ověřování.
2. V části přidat ověřování vyberte typ ověřování – klient jen pro čtení nebo klient pro správu.
3. Nyní vyberte metodu autorizace. To znamená, že Service Fabric, zda by měl vyhledat tento certifikát pomocí názvu subjektu nebo kryptografického otisku. Obecně platí, že není dobrým zvykem zabezpečení použít metodu autorizace názvu subjektu. 

![Přidat klientský certifikát][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Odstranění klientských certifikátů – správce nebo Read-Only pomocí portálu

Chcete-li odebrat sekundární certifikát ze systému, který se používá pro zabezpečení clusteru, přejděte do části zabezpečení a vyberte možnost odstranit z kontextové nabídky u konkrétního certifikátu.

## <a name="adding-application-certificates-to-a-virtual-machine-scale-set"></a>Přidání certifikátů aplikace do sady škálování virtuálních počítačů

Pokud chcete nasadit certifikát, který používáte pro vaše aplikace do clusteru, podívejte se do [tohoto ukázkového skriptu PowerShellu](scripts/service-fabric-powershell-add-application-certificate.md).

## <a name="next-steps"></a>Další kroky
Další informace o správě clusterů najdete v těchto článcích:

* [Service Fabric proces upgradu clusteru a očekávání od vás](service-fabric-cluster-upgrade.md)
* [Nastavení přístupu na základě role pro klienty](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


