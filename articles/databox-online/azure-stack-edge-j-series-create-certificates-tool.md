---
title: Vytvoření certifikátů pomocí nástroje pro kontrolu připravenosti centra Microsoft Azure Stack | Microsoft Docs
description: V této části najdete popis postupu vytváření žádostí o certifikáty a následné získání a instalaci certifikátů na zařízení GPU Azure Stack Edge pro pomocí nástroje pro kontrolu připravenosti centra Azure Stack.
services: Azure Stack Edge Pro
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: 5e5cb077868a224620d1a23e1ff1aac9c8d9f095
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874470"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-using-azure-stack-hub-readiness-checker-tool"></a>Vytvoření certifikátů pro Azure Stack Edge pro pomocí nástroje pro kontrolu připravenosti centra Azure Stack 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Tento článek popisuje, jak vytvořit certifikáty pro Azure Stack Edge pro pomocí nástroje pro kontrolu připravenosti centra Azure Stack. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Použití nástroje pro kontrolu připravenosti centra Azure Stack

Pomocí nástroje pro kontrolu připravenosti centra Azure Stack můžete vytvořit žádosti o podepsání certifikátu pro nasazení zařízení Azure Stack Edge pro. Tyto požadavky můžete vytvořit po umístění objednávky pro zařízení Azure Stack Edge pro a čekání na doručení zařízení. 

> [!NOTE]
> Tento nástroj použijte pouze pro účely testování nebo vývoje, nikoli pro produkční zařízení. 

K vyžádání následujících certifikátů můžete použít nástroj pro kontrolu připravenosti centra Azure Stack (AzsReadinessChecker):

- Azure Resource Manager certifikát
- Místní certifikát uživatelského rozhraní
- Certifikát uzlu
- Certifikát BLOB
- Certifikát sítě VPN


## <a name="prerequisites"></a>Předpoklady

Pokud chcete vytvořit zástupce pro nasazení zařízení Azure Stack Edge pro, ujistěte se, že: 

- Máte klienta se systémem Windows 10 nebo Windows Server 2016 nebo novějším. 
- Stáhli jste nástroj pro kontrolu připravenosti centra Microsoft Azure Stack [z Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker) v tomto systému.
- Pro certifikáty máte k dispozici následující informace:
  - Název zařízení
  - Sériové číslo uzlu
  - Externí plně kvalifikovaný název domény (FQDN)

## <a name="generate-certificate-signing-requests"></a>Generování žádostí o podepsání certifikátu

Pomocí těchto kroků Připravte certifikáty zařízení Azure Stack Edge pro:

1. Spusťte PowerShell jako správce (5,1 nebo novější).
2. Nainstalujte nástroj pro kontrolu připravenosti centra Azure Stack. Do příkazového řádku PowerShellu zadejte: 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

    Nainstalovanou verzi ověříte tak, že zadáte:  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. Vytvořte adresář pro všechny certifikáty, pokud neexistuje. Zadejte: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. Pokud chcete vytvořit žádost o certifikát, zadejte následující informace. Pokud vygenerujete certifikát sítě VPN, některé z těchto vstupů se nevztahují. 
    
    |Vstup |Popis  |
    |---------|---------|
    |`OutputRequestPath`|Cesta k souboru v místním klientovi, kde chcete vytvořit žádosti o certifikát.        |
    |`DeviceName`|Název zařízení na stránce **zařízení** v místním webovém uživatelském rozhraní vašeho zařízení. <br> Toto pole není vyžadováno pro certifikát sítě VPN.         |
    |`NodeSerialNumber`|Sériové číslo uzlu zařízení na stránce **síť** v místním webovém uživatelském rozhraní vašeho zařízení. <br> Toto pole není vyžadováno pro certifikát sítě VPN.       |
    |`ExternalFQDN`|Hodnota DNSDomain na stránce **zařízení** v místním webovém uživatelském rozhraní vašeho zařízení.         |
    |`RequestType`|Typ požadavku může být pro `MultipleCSR` různé koncové body, nebo `SingleCSR` jeden certifikát pro všechny koncové body. <br> Toto pole není vyžadováno pro certifikát sítě VPN.     |

    U všech certifikátů kromě certifikátů sítě VPN zadejte: 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    Pokud vytváříte certifikát sítě VPN, zadejte: 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. Soubory žádostí o certifikát najdete v adresáři, který jste zadali v parametru OutputRequestPath výše. Při použití parametru se v `MultipleCSR` rozšíření zobrazí 4 soubory `.req` . Soubory jsou následující:

    
    |Názvy souborů  |Typ žádosti o certifikát  |
    |---------|---------|
    |Od `DeviceName`     |Žádost o certifikát místního webového uživatelského rozhraní      |
    |Od `NodeSerialNumber`     |Žádost o certifikát uzlu         |
    |Od `login`     |Žádost o certifikát koncového bodu Azure Resource Manager       |
    |Od `wildcard`     |Žádost o certifikát BLOB Storage; obsahuje zástupný znak, protože se zabývá všemi účty úložiště, které můžete na zařízení vytvořit.          |
    |Od `AzureStackEdgeVPNCertificate`     |Žádost o certifikát klienta VPN         |

    Zobrazí se také Složka INF. Obsahuje správu. <Edge-název_zařízení> informační soubor v nejasném textu vysvětlujícího podrobnosti o certifikátu.  


6. Odešlete tyto soubory vaší certifikační autoritě (internímu nebo veřejnému). Ujistěte se, že certifikační autorita generuje certifikáty pomocí vygenerované žádosti, která splňuje požadavky na certifikáty Azure Stack Edge pro u [certifikátů uzlů](azure-stack-edge-j-series-manage-certificates.md#node-certificates), [certifikátů koncových bodů](azure-stack-edge-j-series-manage-certificates.md#endpoint-certificates)a [místních certifikátů uživatelského rozhraní](azure-stack-edge-j-series-manage-certificates.md#local-ui-certificates).

## <a name="prepare-certificates-for-deployment"></a>Příprava certifikátů pro nasazení

Soubory certifikátů, které získáte od certifikační autority (CA), musí být importovány a exportovány s vlastnostmi, které odpovídají Azure Stack požadavky na certifikát pro zařízení Edge pro. Proveďte následující kroky ve stejném systému, ve kterém jste vygenerovali žádosti o podepsání certifikátu.

- Pokud chcete importovat certifikáty, postupujte podle kroků v části [Import certifikátů na klienty, kteří přistupují k zařízení Azure Stack Edge pro](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).

- Pokud chcete exportovat certifikáty, postupujte podle kroků v části [export certifikátů z klienta přistupujícího k zařízení Azure Stack Edge pro](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).


## <a name="validate-certificates"></a>Ověřit certifikáty

Nejprve vygenerujete správnou strukturu složek a umístíte certifikáty do odpovídajících složek. Jenom pak ověříte certifikáty pomocí tohoto nástroje.

1. Spusťte PowerShell jako správce.

2. Pro vygenerování příslušné struktury složek na příkazovém řádku zadejte:

    `New-AzsCertificateFolder -CertificateType AzureStackEdge -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. Převeďte heslo PFX na zabezpečený řetězec. Zadejte:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. Dále ověřte certifikáty. Zadejte:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdge -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>Další kroky

[Nasazení zařízení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-prep.md)
