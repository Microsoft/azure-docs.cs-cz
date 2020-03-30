---
title: Vytvoření clusteru pomocí běžného názvu certifikátu
description: Zjistěte, jak vytvořit cluster Service Fabric pomocí běžného názvu certifikátu ze šablony.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 4a4448c88fa9493979f075f6b9c669927dd1d39e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614549"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Nasazení clusteru Service Fabric, který používá běžný název certifikátu namísto kryptografického otisku
Žádné dva certifikáty mohou mít stejný kryptografický otisk, což ztěžuje přechod na certifikát y clusteru nebo správu. Více certifikátů však může mít stejný běžný název nebo předmět.  Cluster používající běžné názvy certifikátů usnadňuje správu certifikátů. Tento článek popisuje, jak nasadit cluster Service Fabric použít běžný název certifikátu namísto kryptografického otisku certifikátu.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Získání certifikátu
Nejprve získejte certifikát od [certifikační autority .](https://wikipedia.org/wiki/Certificate_authority)  Běžný název certifikátu by měl být pro vlastní doménu, kterou vlastníte, a měl by být zakoupen od doménového registrátora. Například "azureservicefabricbestpractices.com"; ti, kteří nejsou zaměstnanci společnosti Microsoft, nemohou zřídit certifikáty pro domény MS, takže nemůžete používat názvy DNS vašeho LB nebo Traffic Manageru jako běžné názvy pro váš certifikát a budete muset zřídit [Zónu Azure DNS,](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) pokud má být vaše vlastní doména v Azure řešitelná. Budete také chtít deklarovat vlastní doménu, kterou vlastníte jako "managementEndpoint" vašeho clusteru, pokud chcete, aby portál odrážel vlastní alias domény pro váš cluster.

Pro účely testování můžete získat certifikát podepsaný certifikační autoritou od svobodné nebo otevřené certifikační autority.

> [!NOTE]
> Certifikáty podepsané svým držitelem, včetně certifikátů generovaných při nasazování clusteru Service Fabric na webu Azure Portal, nejsou podporovány. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Nahrání certifikátu do trezoru klíčů
V Azure se cluster Service Fabric nasazuje na škálovací sadě virtuálních strojů.  Nahrajte certifikát do trezoru klíčů.  Když se cluster nasadí, certifikát se nainstaluje do škálovací sady virtuálních strojů, na které je cluster spuštěný.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>Stažení a aktualizace ukázkové šablony
Tento článek používá [5-node zabezpečené clusteru příklad](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) šablony a parametry šablony. Stáhněte si soubory *azuredeploy.json* a *azuredeploy.parameters.json* do počítače.

### <a name="update-parameters-file"></a>Aktualizovat soubor parametrů
Nejprve otevřete soubor *azuredeploy.parameters.json* v textovém editoru a přidejte následující hodnotu parametru:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
```

Dále nastavte hodnoty parametrů *certificateCommonName*, *sourceVaultValue*a *certificateUrlValue* na hodnoty vrácené předchozím skriptem:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Aktualizace souboru šablony
Dále otevřete soubor *azuredeploy.json* v textovém editoru a proveďte tři aktualizace pro podporu běžného názvu certifikátu.

1. V části **parametry** přidejte parametr *certificateCommonName:*
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Authority Issuer Thumpbrint for Commonname cert"
      }
    },
    ```

    Zvažte také odebrání *certifikátuOtisk palce*, nemusí být již potřeba.

2. Nastavte hodnotu proměnné *sfrpApiVersion* na "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. V prostředku **Microsoft.Compute/virtualMachineScaleSets** aktualizujte rozšíření virtuálního počítače tak, aby používalo běžný název v nastavení certifikátu namísto kryptografického otisku.  V rozšíření **virtualMachineProfileProfil**->**extensionProfile**->rozšíření**nastavení**->**certifikátu**->**vlastností**->, přidat**certificate** 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    a `"thumbprint": "[parameters('certificateThumbprint')]",`odstranit .

    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": [
                     "[parameters('certificateCommonName')]"
                  ],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4. V prostředku **Microsoft.ServiceFabric/clusters** aktualizujte verzi rozhraní API na "2018-02-01".  Také přidejte nastavení **certificateCommonNames** s vlastností **commonNames** a odeberte nastavení **certifikátu** (s vlastností thumbprint) jako v následujícím příkladu:
   ```json
   {
       "apiVersion": "2018-02-01",
       "type": "Microsoft.ServiceFabric/clusters",
       "name": "[parameters('clusterName')]",
       "location": "[parameters('clusterLocation')]",
       "dependsOn": [
       "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
       ],
       "properties": {
       "addonFeatures": [
           "DnsService",
           "RepairManager"
       ],        
       "certificateCommonNames": {
           "commonNames": [
           {
               "certificateCommonName": "[parameters('certificateCommonName')]",
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > Pole "certificateIssuerThumbprint" umožňuje zadat očekávané vystavitely certifikátů s daným běžným názvem předmětu. Toto pole přijímá výčet kryptografických otisků SHA1 oddělených čárkami. Všimněte si, že se jedná o posílení ověření certifikátu - v případě, že vystavitel není zadán nebo prázdný, certifikát bude přijat k ověření, pokud jeho řetěz může být sestaven a skončí v kořenovém adresáři důvěryhodném validátorem. Pokud je zadán vystavit, certifikát bude přijat, pokud kryptografický otisk jeho přímého vystavittele odpovídá některé z hodnot uvedených v tomto poli - bez ohledu na to, zda je kořen důvěryhodný nebo ne. Upozorňujeme, že soubor PKI může k vydávání certifikátů pro stejný předmět používat různé certifikační úřady, a proto je důležité specifikovat všechny očekávané otisky prstů vystavittele pro daný předmět.
   >
   > Určení vystavittele je považováno za osvědčený postup; zatímco vynechání bude i nadále fungovat - pro certifikáty řetězení do důvěryhodného kořene - toto chování má omezení a může být vyřazeno v blízké budoucnosti. Všimněte si také, že clustery nasazené v Azure a zabezpečené certifikáty X509 vydanými privátní infrastrukturou infrastruktury infrastruktury a deklarovanými subjektem nemusí být možné ověřit službou Azure Service Fabric (pro komunikaci mezi clustery a službami), pokud zásady zásad y certifikátů infrastruktury veřejné hod. není zjistitelný, dostupný a přístupný. 

## <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony
Po provedení změn znovu nasaďte aktualizovanou šablonu.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzResourceGroup -Name $groupname -Location $clusterloc

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Další kroky
* Informace o [zabezpečení clusteru](service-fabric-cluster-security.md).
* Přečtěte si, jak [převést certifikát clusteru](service-fabric-cluster-rollover-cert-cn.md)
* [Aktualizace a správa certifikátů clusteru](service-fabric-cluster-security-update-certs-azure.md)
* Zjednodušení správy certifikátů [změnou clusteru z kryptografického otisku certifikátu na běžný název](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
