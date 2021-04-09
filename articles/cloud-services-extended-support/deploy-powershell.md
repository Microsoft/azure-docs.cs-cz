---
title: Nasazení cloudové služby (Rozšířená podpora) – PowerShell
description: Nasazení cloudové služby (Rozšířená podpora) pomocí PowerShellu
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 0c1b67e42e7988a836ec58ac022b11d736210bca
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865617"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Nasazení cloudové služby (Rozšířená podpora) pomocí Azure PowerShell

V tomto článku se dozvíte, jak pomocí `Az.CloudService` modulu PowerShellu nasadit Cloud Services (rozšířenou podporu) v Azure, která má více rolí (webrole a role pracovního procesu) a rozšíření vzdálené plochy. 

> [!IMPORTANT]
> Cloud Services (Rozšířená podpora) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Než začnete

Projděte si [požadavky nasazení](deploy-prerequisite.md) pro Cloud Services (Rozšířená podpora) a vytvořte přidružené prostředky. 

## <a name="deploy-a-cloud-services-extended-support"></a>Nasazení Cloud Services (Rozšířená podpora)
1. Instalace AZ. CloudService PowerShell Module  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

2. Vytvoříte novou skupinu prostředků. Tento krok je nepovinný, pokud používáte existující skupinu prostředků.   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

3. Vytvořte účet úložiště a kontejner, který se použije k uložení balíčku cloudové služby (. cspkg) a souborů konfigurace služby (. cscfg). Pro název účtu úložiště je nutné použít jedinečný název. 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```

4. Nahrajte balíček cloudové služby (cspkg) do účtu úložiště.

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

5.  Nahrajte konfiguraci cloudové služby (cscfg) do účtu úložiště. 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

6. Vytvořte virtuální síť a podsíť. Tento krok je nepovinný, pokud používáte stávající síť a podsíť. V tomto příkladu se používá jedna virtuální síť a podsíť pro role cloudové služby (webrole a role pracovního procesu). 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
7. Vytvořte veřejnou IP adresu a (volitelně) nastavte vlastnost Popisek DNS veřejné IP adresy. Pokud používáte statickou IP adresu, musí se v konfiguračním souboru služby odkazovat jako na Vyhrazená IP adresa.  

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

8. Vytvořte objekt síťového profilu a přidružte veřejnou IP adresu ke front-endu vytvořené platformy pro vyrovnávání zatížení.  

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
9. Vytvoření trezoru klíčů Tato Key Vault bude sloužit k ukládání certifikátů, které jsou přidruženy k rolím cloudové služby (Rozšířená podpora). Key Vault se musí nacházet ve stejné oblasti a předplatném jako cloudová služba a musí mít jedinečný název. Další informace najdete v tématu [použití certifikátů s Azure Cloud Services (Rozšířená podpora)](certificates-and-key-vault.md).

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

10. Aktualizujte zásady přístupu Key Vault a udělte jim oprávnění k certifikátu pro svůj uživatelský účet. 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    Případně můžete nastavit zásady přístupu přes ObjectId (které lze získat spuštěním). `Get-AzADUser` 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

11. Pro účely tohoto příkladu přidáme do Key Vault certifikát podepsaný svým vlastníkem. Kryptografický otisk certifikátu se musí přidat do souboru konfigurace cloudové služby (. cscfg) pro nasazení v rolích cloudové služby. 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
12. Vytvoření profilu operačního systému v paměťovém objektu. Profil operačního systému Určuje certifikáty, které jsou přidružené k rolím cloudové služby. To bude stejný certifikát, který jste vytvořili v předchozím kroku. 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

13. Vytvořte profil role v paměťovém objektu. Profil role definuje specifické vlastnosti pro SKU rolí, jako je název, kapacita a úroveň. V tomto příkladu jsme definovali dvě role: frontendRole a backendRole. Informace o profilu role by se měly shodovat s konfigurací role definovanou v souboru konfigurace (cscfg) a v souboru definice služby (csdef). 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

14. Volitelné Vytvořte profil rozšíření s objektem v paměti, který chcete přidat do své cloudové služby. V tomto příkladu přidáme rozšíření RDP. 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    Všimněte si, že configFile by měl obsahovat pouze značky PublicConfig a měly by obsahovat obor názvů následujícím způsobem:
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
15. Volitelné Definujte značky jako zatřiďovací tabulku PowerShellu, kterou chcete přidat do své cloudové služby. 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

17. Vytvářejte nasazení cloudové služby pomocí objektů profilu & adres URL SAS.

    ```powershell
    $cloudService = New-AzCloudService ` 
    -Name “ContosoCS” ` 
    -ResourceGroupName “ContosOrg” ` 
    -Location “East US” ` 
    -PackageUrl $cspkgUrl ` 
    -ConfigurationUrl $cscfgUrl ` 
    -UpgradeMode 'Auto' ` 
    -RoleProfile $roleProfile ` 
    -NetworkProfile $networkProfile  ` 
    -ExtensionProfile $extensionProfile ` 
    -OSProfile $osProfile `
    -Tag $tag 
    ```

## <a name="next-steps"></a>Další kroky 
- Přečtěte si [Nejčastější dotazy](faq.md) k Cloud Services (Rozšířená podpora).
- Nasaďte cloudovou službu (rozšířenou podporu) pomocí [Azure Portal](deploy-portal.md), [PowerShellu](deploy-powershell.md), [šablony](deploy-template.md) nebo sady [Visual Studio](deploy-visual-studio.md).
- Navštivte [úložiště ukázek Cloud Services (rozšířené podpory)](https://github.com/Azure-Samples/cloud-services-extended-support) .
