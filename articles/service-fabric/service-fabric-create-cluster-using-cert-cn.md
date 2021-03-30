---
title: Vytvoření clusteru pomocí běžného názvu certifikátu
description: Naučte se vytvořit cluster Service Fabric s využitím běžného názvu certifikátu ze šablony.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: c852b40d35f936753d3c16420159676da239b6c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86246431"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Nasazení clusteru Service Fabric, který místo kryptografického otisku používá běžný název certifikátu
Žádné dva certifikáty nemohou mít stejný kryptografický otisk, což způsobuje, že je změna nebo Správa certifikátu clusteru obtížná. Více certifikátů však může mít stejný společný název nebo předmět.  Cluster s použitím společných názvů certifikátů výrazně zjednodušuje správu certifikátů. Tento článek popisuje, jak nasadit cluster Service Fabric tak, aby místo kryptografického otisku certifikátu používal běžný název certifikátu.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Získat certifikát
Nejdřív Získejte certifikát od certifikační [autority (CA)](https://wikipedia.org/wiki/Certificate_authority).  Běžný název certifikátu by měl být pro vlastní doménu, kterou vlastníte, a koupit z doménového registrátora. Například "azureservicefabricbestpractices.com"; Uživatelé, kteří nejsou zaměstnanci Microsoftu, nemůžou zřídit certifikáty pro domény MS, takže nebudete moct používat jako běžné názvy pro svůj certifikát názvy DNS ani Traffic Manager, a pokud vaše vlastní doména budete moct přeložit v Azure, budete muset zřídit [Azure DNS zónu](../dns/dns-delegate-domain-azure-dns.md) . Pokud chcete, aby portál odrážel vlastní alias domény pro váš cluster, budete také chtít deklarovat vlastní doménu, kterou vlastníte jako "managementEndpoint" clusteru.

Pro účely testování byste mohli získat certifikát podepsaný certifikační autoritou od bezplatné nebo otevřené certifikační autority.

> [!NOTE]
> Certifikáty podepsané svým držitelem, včetně těch generovaných při nasazení clusteru Service Fabric v Azure Portal, nejsou podporovány. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Nahrajte certifikát do trezoru klíčů.
V Azure je Cluster Service Fabric nasazený v sadě škálování virtuálního počítače.  Nahrajte certifikát do trezoru klíčů.  Po nasazení clusteru se certifikát nainstaluje do sady škálování virtuálních počítačů, ve které je cluster spuštěný.

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
Tento článek používá příklad šablony a parametrů šablony [zabezpečeného clusteru s pěti uzly](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) . Stáhněte si *azuredeploy.js* a *azuredeploy.parameters.js* soubory do počítače.

### <a name="update-parameters-file"></a>Aktualizovat soubor parametrů
Nejprve otevřete *azuredeploy.parameters.js* v souboru v textovém editoru a přidejte následující hodnotu parametru:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
```

Dále nastavte hodnoty parametru *certificateCommonName*, *sourceVaultValue* a *certificateUrlValue* na hodnoty vrácené předchozím skriptem:
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
V dalším kroku otevřete *azuredeploy.js* v souboru v textovém editoru a proveďte tři aktualizace, aby podporovaly běžný název certifikátu.

1. Do části **Parameters (parametry** ) přidejte parametr *certificateCommonName* :
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

    Zvažte také odebrání *certificateThumbprint*, a proto už nemusí být potřeba.

2. Nastavte hodnotu proměnné *sfrpApiVersion* na "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. V prostředku **Microsoft. COMPUTE/virtualMachineScaleSets** aktualizujte rozšíření virtuálního počítače tak, aby místo kryptografického otisku používalo běžný název v nastavení certifikátu.  V **Nastavení virtualMachineProfile** -> **extensionProfile** -> **rozšíření**– -> **vlastnosti** ->  -> **certifikátu**– přidat 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    a odeberte `"thumbprint": "[parameters('certificateThumbprint')]",` .

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

4. V prostředku **Microsoft. ServiceFabric/clustery** aktualizujte verzi rozhraní API na "2018-02-01".  Přidejte také nastavení **certificateCommonNames** s vlastností **commonNames** a odeberte nastavení **certifikátu** (s vlastností kryptografický otisk), jak je znázorněno v následujícím příkladu:
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
   > Pole certificateIssuerThumbprint umožňuje zadat očekávané vystavitele certifikátů s daným běžným názvem daného subjektu. Toto pole přijímá čárkami oddělený výčet kryptografických otisků SHA1. Všimněte si, že se jedná o posílení ověření certifikátu – v případě, že vystavitel není zadán nebo je prázdný, certifikát bude přijat k ověřování, pokud je jeho řetěz sestavený a končí v kořenovém adresáři, který validátor považuje za důvěryhodný. Pokud je vystavitel zadaný, certifikát se přijme, pokud kryptografický otisk svého přímého vystavitele odpovídá některé z hodnot uvedených v tomto poli – bez ohledu na to, jestli je kořen důvěryhodný, nebo ne. Upozorňujeme, že infrastruktura veřejných klíčů (PKI) může k vystavování certifikátů pro stejný předmět používat jiné certifikační autority, a proto je důležité pro daný předmět zadat všechny očekávané kryptografické otisky vystavitele.
   >
   > Zadání vystavitele je považováno za osvědčený postup; i když tento parametr vynecháte, bude i nadále fungovat – pro certifikáty, které se řetězí až k důvěryhodnému kořenovému adresáři – toto chování má omezení a v blízké budoucnosti může být ukončeno. Upozorňujeme také, že clustery nasazené v Azure a zabezpečené pomocí certifikátů x509 vydaných privátní infrastrukturou veřejných klíčů (pro komunikaci mezi clustery) nemůžou ověřit služba Azure Service Fabric (pro komunikaci mezi clustery), pokud zásada certifikátu PKI není zjistitelná, dostupná a přístupná. 

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
* Přečtěte si o [zabezpečení clusteru](service-fabric-cluster-security.md).
* Informace o tom, jak [vyměnit certifikát clusteru](service-fabric-cluster-rollover-cert-cn.md)
* [Aktualizace a Správa certifikátů clusteru](service-fabric-cluster-security-update-certs-azure.md)
* Zjednodušení správy certifikátů [změnou clusteru z kryptografického otisku certifikátu na běžný název](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
