---
title: Nasazení Kubernetes pro Azure Stack pomocí Active Directory Federated Services (AD FS) | Dokumentace Microsoftu
description: Informace o nasazení Kubernetes pro Azure Stack pomocí Active Directory Federated Services (AD FS).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: c2ef0d34897171e04d0982405909183634ebb696
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56115398"
---
# <a name="deploy-kubernetes-to-azure-stack-using-active-directory-federated-services"></a>Nasazení do služby Azure Stack pomocí Active Directory Federated Services Kubernetes

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

> [!Note]  
> Kubernetes ve službě Azure Stack je ve verzi preview. Azure Stack odpojené scénář není aktuálně podporován ve verzi preview.

Můžete postupovat podle kroků v tomto článku pro nasazení a nastavit prostředky pro Kubernetes. Pomocí těchto kroků při Active Directory Federated Services (AD FS) je služba pro správu identit.

## <a name="prerequisites"></a>Požadavky 

Abyste mohli začít, ujistěte se, že máte správná oprávnění a že služby Azure Stack je připravena.

1. Generování veřejného a privátního klíče pár SSH pro přihlášení do virtuálního počítače s Linuxem v Azure stacku. Veřejný klíč musíte při vytváření clusteru.

    Generuje se klíč, v tématu [vygenerování klíče SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Zkontrolujte, zda máte platné předplatné na portálu Azure Stack tenant a, že máte dostatek veřejné IP adresy pro přidání nové aplikace k dispozici.

    Cluster se nedá nasadit do služby Azure Stack **správce** předplatného. Je nutné použít **uživatele** předplatného. 

1. Je třeba službu Key Vault ve vašem předplatném Azure Stack.

1. Budete potřebovat Kubernetes Cluster v marketplace. 

Pokud vám chybí službu Key Vault a položky marketplace clusteru Kubernetes, obraťte se na správce služby Azure Stack.

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Potřebujete pracovat se správcem vaší služby Azure Stack a nastavení instančního objektu služby, při použití služby AD FS jako řešení identit. Služby, které poskytuje vaše aplikace přístup k prostředkům Azure Stack.

1. Správce služby Azure Stack vám poskytne certifikátu a informace pro instanční objekt.

    - Informací o instančním objektu služby by měl vypadat:

    ```Text  
        ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
        ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
        Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
        ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
        PSComputerName        : azs-ercs01
        RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
    ```

    - Váš certifikát bude soubor s příponou `.pfx`. V trezoru klíčů se uloží váš certifikát jako tajný kód.

2. Přiřadíte nový instanční objekt služby roli Přispěvatel do vašeho předplatného. Pokyny najdete v tématu [přiřazení role](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

3. Vytvoření trezoru klíčů se uloží váš certifikát pro nasazení. Pomocí těchto skriptů Powershellu, a ne pomocí portálu.

    - Budete potřebovat následující údaje:

        | Hodnota | Popis |
        | ---   | ---         |
        | Koncový bod Azure Resource Manageru | Microsoft Azure Resource Manageru je systém správy, který vám umožňuje správcům nasadit, spravovat a monitorovat prostředky Azure. Azure Resource Manageru dokáže zpracovat tyto úkoly, jako se skupinou, nikoli samostatně, v rámci jedné operace.<br>Koncový bod v Azure Stack Development Kit (ASDK) je: `https://management.local.azurestack.external/`<br>Koncový bod v integrovaných systémech je: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
        | ID vašeho předplatného | [ID předplatného](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) je, jak získat přístup k nabídky ve službě Azure Stack. |
        | Uživatelské jméno | Použít jenom svoje uživatelské jméno spíše než váš název domény a uživatelské jméno, například `username` místo `azurestack\username`. |
        | Název skupiny prostředků  | Název nové skupiny prostředků nebo vyberte existující skupinu prostředků. Název prostředku musí být alfanumerické znaky a malá písmena. |
        | Název trezoru klíčů | Název trezoru.<br> Vzor regulárního výrazu: `^[a-zA-Z0-9-]{3,24}$` |
        | Umístění skupiny prostředků | Umístění skupiny prostředků. Toto je oblast, kterou jste vybrali pro instalaci sady Azure Stack. |

    - Otevřete prostředí PowerShell s řádku se zvýšenými oprávněními a [připojit ke službě Azure Stack](azure-stack-powershell-configure-user.md#connect-with-ad-fs). Spusťte následující skript s parametry, aktualizovat, aby vaše hodnoty:

    ```PowerShell  
        $armEndpoint="<Azure Resource Manager Endpoint>"
        $subscriptionId="<Your Subscription ID>"
        $username="<your user name >"
        $resource_group_name = "<the resource group name >"
        $key_vault_name = "<keyvault name>"
        $resource_group_location="<resource group location>"
        
        # Login Azure Stack Environment
        Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
        $mycreds = Get-Credential
        Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
        
        # Create new Resource group and key vault
        New-AzureRmResourceGroup -Name $resource_group_name -Location $resource_group_location -Force
        
        # Note, Do not omit -EnabledForTemplateDeployment flag
        New-AzureRmKeyVault -VaultName $key_vault_name -ResourceGroupName $resource_group_name -Location $resource_group_location -EnabledForTemplateDeployment
        
        # Obtain the security identifier(SID) of the active directory user
        $adUser = Get-ADUser -Filter "Name -eq '$username'" -Credential $mycreds
        $objectSID = $adUser.SID.Value
        # Set the key vault access policy
        Set-AzureRmKeyVaultAccessPolicy -VaultName $key_vault_name -ResourceGroupName $resource_group_name -ObjectId $objectSID -BypassObjectIdValidation -PermissionsToKeys all -PermissionsToSecrets all
    ```

4. Nahrajte certifikát do služby key vault.

    - Budete potřebovat následující údaje:

        | Hodnota | Popis |
        | ---   | ---         |
        | Cesta k certifikátu | Plně kvalifikovaný název domény nebo cesta k certifikátu. |
        | Heslo certifikátu | Heslo certifikátu. |
        | Název tajného kódu | Název tajného kódu slouží jako odkaz na certifikát uložený v trezoru. |
        | Název trezoru klíčů | Název trezoru klíčů, které jsou vytvořené v předchozím kroku. |
        | Koncový bod Azure Resource Manageru | Koncový bod v Azure Stack Development Kit (ASDK) je: `https://management.local.azurestack.external/`<br>Koncový bod v integrovaných systémech je: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
        | ID vašeho předplatného | [ID předplatného](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) je, jak získat přístup k nabídky ve službě Azure Stack. |

    - Otevřete prostředí PowerShell s řádku se zvýšenými oprávněními a [připojit ke službě Azure Stack](azure-stack-powershell-configure-user.md#connect-with-ad-fs). Spusťte následující skript s parametry, aktualizovat, aby vaše hodnoty:

    ```PowerShell  
        
    # upload the pfx to key vault
    $tempPFXFilePath = "<certificate path>"
    $password = "<certificate password>"
    $keyVaultSecretName = "<secret name>"
    $keyVaultName = "<key vault name>"
    $armEndpoint="<Azure Resource Manager Endpoint>"
    $subscriptionId="<Your Subscription ID>"
    # Login Azure Stack Environment
    Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
    $mycreds = Get-Credential
    Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
    
    $certContentInBytes = [io.file]::ReadAllBytes($tempPFXFilePath)
    $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    $jsonObject = @"
    {
    "data": "$pfxAsBase64EncodedString",
    "dataType" :"pfx",
    "password": "$password"
    }
    "@
    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret 
    ```

## <a name="deploy-kubernetes"></a>Nasazení Kubernetes

1. Otevřít [portálu Azure Stack](https://portal.local.azurestack.external).

1. Vyberte **+ vytvořit prostředek** > **Compute** > **clusteru Kubernetes**. Klikněte na možnost **Vytvořit**.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Základy

1. Vyberte **Základy** ve vytvoření clusteru Kubernetes.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Vyberte vaše **předplatné** ID.

1. Zadejte název nové skupiny prostředků nebo vyberte existující skupinu prostředků. Název prostředku musí být alfanumerické znaky a malá písmena.

1. Vyberte **umístění** skupiny prostředků. Toto je oblast, kterou jste vybrali pro instalaci sady Azure Stack.

### <a name="2-kubernetes-cluster-settings"></a>2. Nastavení clusteru Kubernetes

1. Vyberte **nastavení clusteru Kubernetes** ve vytvoření clusteru Kubernetes.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. Zadejte **uživatelské jméno správce virtuálního počítače Linux**. Uživatelské jméno pro virtuální počítače Linux, které jsou součástí clusteru Kubernetes a DVM.

1. Zadejte **veřejný klíč SSH** se používají pro autorizaci pro všechny počítače s Linuxem vytvořili jako součást clusteru Kubernetes a DVM.

1. Zadejte **předpony DNS profilu hlavní** , které je jedinečné pro danou oblast. Musí se jednat oblasti jedinečný název, jako například `k8s-12345`. Zkuste jste zvolili, je stejná jako skupina prostředků pojmenujte jako nejlepší praxe.

    > [!Note]  
    > Pro každý cluster použijte předponu DNS nových a jedinečných hlavní profilu.

1. Vyberte **počet profilů fondu hlavní Kubernetes**. Počet obsahuje počet uzlů ve fondu hlavní. Může být od 1 do 7. Tato hodnota by měla být liché číslo.

1. Vyberte **The VMSize hlavních virtuálních počítačů Kubernetes**.

1. Vyberte **počet profil fond uzlů Kubernetes**. Počet obsahuje počet agentů v clusteru. 

1. Vyberte **profil úložiště**. Můžete zvolit **objektu Blob disku** nebo **spravovaného disku**. Toto nastavení určuje virtuální počítače uzlů virtuálních počítačů velikosti Kubernetes. 

1. Vyberte **služby AD FS** pro **systém identit Azure Stack** pro vaši instalaci služby Azure Stack.

1. Zadejte **ID klienta instančního objektu** slouží od poskytovatele cloudu Kubernetes Azure. ID klienta označeny jako ID aplikace, když správce služby Azure Stack vytvořit instanční objekt služby.

1. Zadejte **skupina prostředků trezoru klíčů** , který zdrojů služby key vault, která obsahuje váš certifikát.

1. Zadejte **název služby Key Vault** název, který obsahuje váš certifikát jako tajný kód trezoru klíčů. 

1. Zadejte **tajného kódu trezoru klíčů**. Název tajného kódu odkazuje váš certifikát.

1. Zadejte **verze zprostředkovatele služby Kubernetes Azure cloudu**. Toto je verze zprostředkovatele služby Kubernetes Azure. Azure Stack uvolní vlastního sestavení Kubernetes pro každou verzi služby Azure Stack.

### <a name="3-summary"></a>3. Souhrn

1. Vyberte souhrn. V okně zobrazí zprávu ověření pro nastavení konfigurace clusteru Kubernetes.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Zkontrolujte nastavení.

3. Vyberte **OK** k nasazení clusteru.

> [!TIP]  
>  Pokud máte dotazy k vašemu nasazení, můžete se zveřejněte svůj dotaz nebo se pokud někdo už odpověděl na dotaz v [fórum pro Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="next-steps"></a>Další postup

[Připojení k vašemu clusteru](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)
