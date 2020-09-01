---
title: Vytvoření certifikátů pomocí nástroje pro kontrolu připravenosti centra Microsoft Azure Stack | Microsoft Docs
description: V této části najdete popis postupu při vytváření žádostí o certifikáty a následném získání a instalaci certifikátů na zařízení GPU Azure Stack na hraničních zařízeních pomocí nástroje pro kontrolu připravenosti centra Azure Stack.
services: Azure Stack Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 7a6cf265f0be177aab436d544e694c5d59cfffd5
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267373"
---
# <a name="create-certificates-for-your-azure-stack-edge-using-azure-stack-hub-readiness-checker-tool"></a>Vytvoření certifikátů pro Azure Stack Edge pomocí nástroje pro kontrolu připravenosti centra Azure Stack 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Tento článek popisuje, jak vytvořit certifikáty pro Azure Stack Edge pomocí nástroje pro kontrolu připravenosti centra Azure Stack. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Použití nástroje pro kontrolu připravenosti centra Azure Stack

Pomocí nástroje pro kontrolu připravenosti centra Azure Stack můžete vytvořit žádosti o podepsání certifikátu pro nasazení Azure Stack hraničního zařízení. Tyto požadavky můžete vytvořit po umístění objednávky pro Azure Stack hraniční zařízení a čekání na doručení zařízení. 

> [!NOTE]
> Tento nástroj použijte pouze pro účely testování nebo vývoje, nikoli pro produkční zařízení. 

K vyžádání následujících certifikátů můžete použít nástroj pro kontrolu připravenosti centra Azure Stack (AzsReadinessChecker):

- Azure Resource Manager certifikát
- Místní certifikát uživatelského rozhraní
- Certifikát uzlu
- Certifikát BLOB
- Certifikát sítě VPN


## <a name="prerequisites"></a>Požadavky

Pokud chcete vytvořit zástupce pro nasazení Azure Stack hraničních zařízení, ujistěte se, že: 

- Máte klienta se systémem Windows 10 nebo Windows Server 2016 nebo novějším. 
- Stáhli jste nástroj pro kontrolu připravenosti centra Microsoft Azure Stack 1.2002.1133.85 [z Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker) v tomto systému. Možná budete muset tento balíček vyhledat. Pouze tato verze nástroje může vytvářet certifikáty pro Azure Stack hraničních zařízení.
- Pro certifikáty máte k dispozici následující informace:
  - Název zařízení
  - Sériové číslo uzlu
  - Externí plně kvalifikovaný název domény (FQDN)

## <a name="generate-certificate-signing-requests"></a>Generování žádostí o podepsání certifikátu

Pomocí těchto kroků Připravte certifikáty Azure Stack hraničního zařízení:

1. Spusťte PowerShell jako správce (5,1 nebo novější).
2. Nainstalujte nástroj pro kontrolu připravenosti centra Azure Stack. Do příkazového řádku PowerShellu zadejte: 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker -RequiredVersion 1.2002.1133.85 -Force
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


6. Odešlete tyto soubory vaší certifikační autoritě (internímu nebo veřejnému). Ujistěte se, že certifikační autorita generuje certifikáty pomocí vygenerované žádosti, která splňuje požadavky na Azure Stack hraničních certifikátů pro [certifikáty uzlů](azure-stack-edge-j-series-manage-certificates.md#node-certificates), [certifikáty koncových bodů](azure-stack-edge-j-series-manage-certificates.md#endpoint-certificates)a [místní certifikáty uživatelského rozhraní](azure-stack-edge-j-series-manage-certificates.md#local-ui-certificates).

## <a name="prepare-certificates-for-deployment"></a>Příprava certifikátů pro nasazení

Soubory certifikátů, které získáte od certifikační autority (CA), musí být naimportovány a exportovány s vlastnostmi, které odpovídají požadavkům na certifikát Azure Stack hraničního zařízení. Proveďte následující kroky ve stejném systému, ve kterém jste vygenerovali žádosti o podepsání certifikátu.

- Pokud chcete importovat certifikáty, postupujte podle kroků v části [Import certifikátů na klienty, kteří přistupují k zařízení Azure Stack Edge](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).

- Pokud chcete exportovat certifikáty, postupujte podle kroků v části [export certifikátů z klienta přistupujícího k Azure Stack hraničnímu zařízení](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).


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

[Nasazení zařízení Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md)
