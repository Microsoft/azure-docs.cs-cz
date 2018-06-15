---
title: Nasazení zabezpečených cluster Service Fabric v zásobníku Azure | Microsoft Docs
description: Zjistěte, jak nasadit cluster Service Fabric zabezpečeným v Azure zásobníku
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: mattbriggs
ms.reviewer: shnatara
ms.openlocfilehash: a88d8dd2af94ac796a3b2e3c667fd40a308f02a1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33883020"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Nasazení clusteru Service Fabric v Azure zásobníku

Použití **Service Fabric Cluster** položku z Azure Marketplace pro nasazení zabezpečené cluster Service Fabric v zásobníku Azure. 

Další informace o práci s Service Fabric najdete v tématu [přehled Frabric služby Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) a [scénáře zabezpečení clusteru Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security), v dokumentaci k Azure.

## <a name="prerequisites"></a>Požadavky

Toto jsou nutné k nasazení clusteru Service Fabric:
1. **Certifikát clusteru**  
   Toto je certifikát X.509 serveru, které přidáte do KeyVault při nasazování Service Fabric. 
   - **CN** na tohoto certifikátu musí odpovídat plně kvalifikované domény název (FQDN) clusteru Service Fabric vytvoříte. 
   - Certifikát musí být ve formátu PFX, jako veřejné a soukromé klíče jsou požadovány. 
   V tématu [požadavky](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) pro vytvoření tohoto certifikátu na straně serveru.

    > [!NOTE]  
    > Pro účely testování můžete použít certifikát podepsaný svým držitelem inplace certifikátu x.509 serveru. Certifikáty podepsané svým držitelem, není nutné tak, aby odpovídala plně kvalifikovaný název domény clusteru.

2.  **Správce klientský certifikát** certifikát, který bude klient používat k ověření clusteru Service Fabric, což může být podepsaný. V tématu [požadavky](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) pro vytvoření tohoto certifikátu klienta.

3.  **V Azure Marketplace zásobníku musí být k dispozici následující položky:**
     - **Windows Server 2016** – šablona používá bitovou kopii systému Windows Server 2016 k vytvoření clusteru.  
     - **Rozšíření skriptů zákazníka** -rozšíření virtuálního počítače od společnosti Microsoft.  
     - **Fáze konfigurace požadovaného prostředí PowerShell** -rozšíření virtuálního počítače od společnosti Microsoft.


## <a name="add-a-secret-to-key-vault"></a>Přidání tajného klíče do služby Key Vault
Pokud chcete nasadit cluster Service Fabric, musíte zadat správný KeyVault *tajný klíč identifikátor* nebo adresa URL pro cluster Service Fabric. Šablony Azure Resource Manageru trvá KeyVault jako vstup a pak načte příslušný certifikát clusteru během instalace clusteru Service Fabric. 

> [!IMPORTANT]  
> Přidejte do KeyVault tajný klíč pro použití s nástrojem Service Fabric, musíte použít PowerShell. Nepoužívejte na portálu.  

Pomocí následujícího skriptu KeyVault vytvořit a přidat *clusteru certifikát* k němu. (Viz [požadavky](#prerequisites).) Před spuštěním skriptu projděte si ukázkový skript a aktualizujte uvedené parametry odpovídaly vašemu prostředí. Tento skript také výstup hodnoty, které je třeba zadat šablonu Azure Resource Manager. 

> [!TIP]  
> Než skript mohlo být úspěšné, musí být veřejné nabídka, která zahrnuje službu pro výpočty, sítě, úložiště a KeyVault. 

  ```PowerShell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


Další informace najdete v tématu [spravovat KeyVault v zásobníku Azure pomocí prostředí PowerShell](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-kv-manage-powershell).

## <a name="deploy-the-marketplace-item"></a>Nasadit položku Marketplace.

1. V portálu user portal, přejděte do **nový** > **výpočetní** > **Service Fabric Cluster**. 

   ![Vyberte Service Fabric Cluster](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

2. Pro jednotlivé stránky jako je *Základy*, vyplňte formulář nasazení. Pokud si nejste jistí hodnoty, použijte výchozí hodnoty. Vyberte **OK** přejdete na další stránku:

   ![Základy](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

3. Na *nastavení sítě* stránky, můžete zadat konkrétní portech, které pro vaše aplikace:

   ![Nastavení sítě](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

4. Na *zabezpečení* přidejte hodnoty, které jste získali z [vytváření Azure KeyVault](#add-a-secret-to-key-vault) a odesílání tajný klíč.

   Pro *kryptografický otisk certifikátu klienta správce*, zadejte kryptografický otisk *správce klientský certifikát*. (Viz [požadavky](#prerequisites).)
   
   - Zdroj Key Vault: Zadejte celý *keyVault id* řetězec z výsledků skriptu. 
   - Adresa URL certifikátu clusteru: Zadejte celou adresu URL z *tajný klíč Id* z výsledků skriptu. 
   - Cluster kryptografický otisk certifikátu: Zadejte *kryptografický otisk certifikátu clusteru* z výsledků skriptu.
   - Správce klienta kryptografické otisky certifikátu: Zadejte *kryptografický otisk certifikátu klienta správce* jste vytvořili v požadavky. 

   ![Výstup skriptu](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Zabezpečení](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

5. Dokončete průvodce a potom vyberte **vytvořit** nasadit Service Fabric Cluster.



## <a name="access-the-service-fabric-cluster"></a>Přístup k Service Fabric Cluster
Můžete přístup ke clusteru Service Fabric pomocí Service Fabric Explorer nebo Service Fabric prostředí PowerShell.


### <a name="use-service-fabric-explorer"></a>Použít Service Fabric Exploreru
1.  Ověřte, že webový prohlížeč má přístup k vašemu certifikátu Správce klienta a ověřit váš cluster Service Fabric.  

    a. Otevřete Internet Explorer a přejděte na **Možnosti Internetu** > **obsahu** > **certifikáty**.
  
    b. Certifikáty, vyberte **Import** spustit *Průvodce importem certifikátu*a potom klikněte na **Další**. Na *importovat soubor* klikněte na stránce **Procházet**a vyberte **správce klientský certifikát** jste zadali do šablony Azure Resource Manager.
        
       > [!NOTE]  
       > Tento certifikát není clusteru certifikát, který byl dříve přidán do KeyVault.  

    c. Ujistěte se, že máte "Personal Information Exchange" vybraný v rozevírací nabídce rozšíření okna Průzkumníka souborů.  

       ![Formát Personal information exchange](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. Na *úložiště certifikátů* vyberte **osobní**a pak dokončete průvodce.  
       ![úložiště certifikátů](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
2. Plně kvalifikovaný název domény clusteru Service Fabric najdete takto:  

    a. Přejděte do skupiny prostředků, který je přidružen Service Fabric clusteru a najděte *veřejnou IP adresu* prostředků. Vyberte objekt přidružený veřejnou IP adresu, kterou otevřete *veřejnou IP adresu* okno.  

      ![Veřejná IP adresa](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. V okně veřejných IP adres, plně kvalifikovaný název domény zobrazí jako *název DNS*.  

      ![Název DNS](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

3. Pokud chcete vyhledat adresu URL pro Service Fabric Explorer a koncového bodu připojení klienta, přečtěte si výsledky nasazení šablony.

4. V prohlížeči přejděte na https://*plně kvalifikovaný název domény*: 19080. Nahraďte *plně kvalifikovaný název domény* plně kvalifikovaný název domény clusteru Service Fabric z kroku 2.   
   Pokud jste použili certifikát podepsaný sám sebou, budete se zobrazí upozornění, že připojení není zabezpečený. Chcete-li pokračovat na web, vyberte **více informací**a potom **přejděte na webovou stránku**. 

5. K ověření do lokality musíte vybrat certifikát pro použití. Vyberte **další možnosti**, vyberte příslušný certifikát a pak klikněte na tlačítko **OK** pro připojení k Service Fabric Exploreru. 

   ![Ověření](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>Použít Service Fabric prostředí PowerShell

1. Nainstalujte *Microsoft Azure Service Fabric SDK* z [Příprava vývojového prostředí v systému Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) v dokumentaci k Azure Service Fabric.  

2. Po dokončení instalace je nakonfigurujte proměnných prostředí systému zajistit, že jsou přístupné z prostředí PowerShell rutiny Service Fabric.  
    
    a. Přejděte na **ovládací panely** > **systém a zabezpečení** > **systému**a potom vyberte **Upřesnit nastavení systému**.  
    
      ![Ovládací panely](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. Na **Upřesnit** kartě *vlastnosti systému*, vyberte **proměnné prostředí**.  

    c. Pro *systémové proměnné*, upravit **cesta** a ujistěte se, že **C:\\Program Files\\Microsoft Service Fabric\\bin\\prostředků infrastruktury \\Fabric.Code** je v horní části seznamu proměnných prostředí.  

      ![Seznam proměnných prostředí](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

3. Po změně pořadí proměnné prostředí, restartujte prostředí PowerShell a spusťte následující skript prostředí PowerShell k získání přístupu k cluster Service Fabric:

   ````PowerShell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ````
   
   > [!NOTE]  
   > Neexistuje žádné *https://* před název clusteru ve skriptu. Port 19000 je povinný.
 
