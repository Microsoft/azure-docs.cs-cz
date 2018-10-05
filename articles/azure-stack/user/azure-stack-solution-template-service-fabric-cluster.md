---
title: Nasazení zabezpečeného clusteru Service Fabric v Azure stacku | Dokumentace Microsoftu
description: Informace o nasazení zabezpečeného clusteru Service Fabric v Azure stacku
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
ms.date: 09/12/2018
ms.author: mattbriggs
ms.reviewer: shnatara
ms.openlocfilehash: d402b2bcd5187cbb6ece78d7e981068c279c1f75
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804425"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Nasazení clusteru Service Fabric v Azure stacku

Použití **Service Fabric Cluster** položku z Azure Marketplace k nasazení zabezpečeného clusteru Service Fabric v Azure stacku. 

Další informace o práci s platformou Service Fabric najdete v tématu [přehled Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) a [scénáře zabezpečení clusteru Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security), dokumentace k Azure.

## <a name="prerequisites"></a>Požadavky

Následující jsou nutné k nasazení clusteru Service Fabric:
1. **Certifikát clusteru**  
   To je certifikát X.509 serveru, které přidáte do trezoru klíčů při nasazování Service Fabric. 
   - **CN** na tento certifikát se musí shodovat plně kvalifikovaný název domény (FQDN) clusteru Service Fabric můžete vytvořit. 
   - Certifikát musí být ve formátu PFX, veřejné a soukromé klíče jsou povinné. 
   Zobrazit [požadavky](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) pro vytvoření tohoto certifikátu na straně serveru.

    > [!NOTE]  
    > Pro účely testování můžete použít certifikát podepsaný svým držitelem místní server certifikátu x.509. Certifikáty podepsané svým držitelem tak, aby odpovídala plně kvalifikovaný název domény clusteru není nutné.

1.  **Klient pro správu certifikátů** jedná se o certifikát, který klient použije k ověření do clusteru Service Fabric, který může být podepsaný držitelem. Zobrazit [požadavky](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) pro vytvoření tohoto certifikátu klienta.

1.  **Následující položky musí být dostupné v Tržišti Azure Stack:**
     - **Windows Server 2016** – šablony k vytvoření clusteru používá image Windows serveru 2016.  
     - **Rozšíření vlastních skriptů** – rozšíření virtuálního počítače od Microsoftu.  
     - **Prostředí PowerShell Desired Configuration fázi** – rozšíření virtuálního počítače od Microsoftu.


## <a name="add-a-secret-to-key-vault"></a>Přidání tajného klíče do služby Key Vault
Pokud chcete nasadit cluster Service Fabric, je nutné zadat správný trezor klíčů *identifikátor tajného kódu* nebo adresu URL pro cluster Service Fabric. Šablony Azure Resource Manageru trvá KeyVault jako vstup a potom načte příslušný certifikát clusteru během instalace clusteru Service Fabric. 

> [!IMPORTANT]  
> Přidání tajného klíče do trezoru klíčů pro použití se službou Service Fabric, musíte použít PowerShell. Nepoužívejte na portálu.  

Pomocí následujícího skriptu vytvořte trezor klíčů a přidat *certifikát clusteru* k němu. (Viz [požadavky](#prerequisites).) Před spuštěním skriptu projděte si ukázkový skript a aktualizujte uvedené parametry, aby odpovídaly vašemu prostředí. Tento skript se také výstupní hodnoty, které je potřeba zadat do šablony Azure Resource Manageru. 

> [!TIP]  
> Předtím, než můžete úspěšně skript, musí být veřejná nabídka, která zahrnuje služby pro výpočty, síť, úložiště a trezoru klíčů. 

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


Další informace najdete v tématu [Spravovat trezor klíčů ve službě Azure Stack s prostředím PowerShell](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-kv-manage-powershell).

## <a name="deploy-the-marketplace-item"></a>Nasadit položky Marketplace

1. Na portálu user portal, přejděte na **+ vytvořit prostředek** > **Compute** > **Service Fabric Cluster**. 

   ![Vyberte Service Fabric Cluster](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

1. Pro každou stránku jako je *Základy*, vyplňte formulář nasazení. Použijte výchozí hodnoty, pokud si nejste jisti hodnoty. Vyberte **OK** k přechodu na další stránku:

   ![Základy](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

1. Na *nastavení sítě* stránky, můžete zadat konkrétní porty otevřete pro vaše aplikace:

   ![Nastavení sítě](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

1. Na *zabezpečení* stránky, přidá hodnoty, které jste získali z [vytvoření Azure Key Vaultu](#add-a-secret-to-key-vault) a nahrajete ji tajný klíč.

   Pro *kryptografický otisk klientského certifikátu správce*, zadejte kryptografický otisk *správce klientský certifikát*. (Viz [požadavky](#prerequisites).)
   
   - Key Vault zdroj: Zadejte celý *id trezoru klíčů* řetězec ve výsledcích skriptu. 
   - Adresa URL certifikátu clusteru: Zadejte celou adresu URL z *tajný klíč Id* ve výsledcích skriptu. 
   - Kryptografický otisk certifikátu clusteru: Zadejte *kryptografický otisk certifikátu clusteru* ve výsledcích skriptu.
   - Správce klienta kryptografické otisky certifikátu: Zadejte *kryptografický otisk klientského certifikátu správce* jste vytvořili v rámci požadavků. 

   ![Výstup skriptu](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Zabezpečení](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

1. Dokončete průvodce a pak vyberte **vytvořit** k nasazení clusteru Service Fabric.



## <a name="access-the-service-fabric-cluster"></a>Přístup ke clusteru Service Fabric
Cluster Service Fabric můžete přistupovat pomocí rozhraní Service Fabric Exploreru nebo Powershellu pro Service Fabric.


### <a name="use-service-fabric-explorer"></a>Použít Service Fabric Explorer
1.  Ověřte, že webový prohlížeč má přístup k vaší certifikát klienta pro správu a ověřit cluster Service Fabric.  

    a. Otevřete aplikaci Internet Explorer a přejděte na **Možnosti Internetu** > **obsahu** > **certifikáty**.
  
    b. S certifikáty, vyberte **Import** spustit *Průvodce importem certifikátu*a potom klikněte na tlačítko **Další**. Na *importovat soubor* stránce klikněte na **Procházet**a vyberte **správce klientský certifikát** jste poskytli šablonu Azure Resource Manageru.
        
       > [!NOTE]  
       > Tento certifikát není certifikát clusteru, který byl dříve přidán do trezoru klíčů.  

    c. Ujistěte se, že máte "Formát Personal Information Exchange" vybrali v rozevíracím seznamu rozšíření okna Průzkumníka souborů.  

       ![Formát Personal information exchange](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. Na *certifikát Store* stránce **osobní**a poté průvodce dokončete.  
       ![Úložiště certifikátů](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
1. Hledání plně kvalifikovaného názvu domény ve vašem clusteru Service Fabric:  

    a. Přejděte do skupiny prostředků, který je spojen s využitím Service Fabric cluster a vyhledejte *veřejnou IP adresu* prostředků. Vyberte objekt přidružený k veřejné IP adresu otevřete *veřejnou IP adresu* okno.  

      ![Veřejná IP adresa](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. V okně veřejnou IP adresu plně kvalifikovaný název domény zobrazí jako *název DNS*.  

      ![Název DNS](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

1. Chcete-li vyhledat adresu URL pro Service Fabric Explorer a koncový bod připojení klienta, zkontrolujte výsledky nasazení šablony.

1. V prohlížeči přejděte na https://*plně kvalifikovaný název domény*: 19080. Nahraďte *plně kvalifikovaný název domény* s plně kvalifikovaný název vašeho clusteru Service Fabric z kroku 2.   
   Pokud jste použili vlastnoručně podepsaný certifikát, zobrazí se vám upozornění, že připojení není zabezpečený. Přejděte na webovou stránku, vyberte **. Další informace**a potom **přejděte na webovou stránku**. 

1. K ověření na webu musíte vybrat certifikát pro použití. Vyberte **víc možností**, vyberte příslušný certifikát a potom klikněte na tlačítko **OK** pro připojení k Service Fabric Explorer. 

   ![Ověření](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>Použití Powershellu pro Service Fabric

1. Nainstalujte *Microsoft Azure Service Fabric SDK* z [Příprava vývojového prostředí ve Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) v dokumentaci k Azure Service Fabric.  

1. Po dokončení instalace, konfigurace proměnných prostředí systému zajistit, že rutiny Service Fabric je přístupný z prostředí PowerShell.  
    
    a. Přejděte na **ovládací panely** > **systém a zabezpečení** > **systému**a pak vyberte **Upřesnit nastavení systému**.  
    
      ![Ovládací prvek panel](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. Na **Upřesnit** kartě *vlastnosti systému*vyberte **proměnné prostředí**.  

    c. Pro *systémové proměnné*, upravit **cesta** a ujistěte se, že **C:\\Program Files\\Microsoft Service Fabric\\bin\\prostředků infrastruktury \\Fabric.Code** je v horní části seznamu proměnných prostředí.  

      ![Seznam proměnných prostředí](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

1. Po změně pořadí proměnné prostředí, restartujte prostředí PowerShell a spusťte následující skript prostředí PowerShell k získání přístupu do clusteru Service Fabric:

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
   > Neexistuje žádná *https://* před název clusteru ve skriptu. Port 19000 je povinný.
 
