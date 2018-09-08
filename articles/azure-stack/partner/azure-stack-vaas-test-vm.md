---
title: Nasaďte místní agent a otestujte image virtuálních počítačů při ověřování služby Azure Stack jako služba | Dokumentace Microsoftu
description: Nasazení místního agenta a otestujte image virtuálních počítačů pro ověřování služby Azure Stack jako služba.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 78136ab00dcba2f8a99df36ba99d384b49995882
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159210"
---
# <a name="deploy-the-local-agent-and-test-virtual-machines"></a>Nasaďte místní agent a testovací virtuální počítače

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Zjistěte, jak chcete použít ověřování jako místní agent service (VaaS) a zkontrolujte, jestli váš hardware. Místní agent musí být nasazené ve službě Azure Stack řešení se ověřují před spuštěním testů pro ověření.

> [!Note]  
> Ujistěte se, že počítači, kde je spuštěná místní agent, nedojde ke ztrátě přístupu k Internetu. Na počítači musí být přístupný uživatelům, kteří jsou oprávněni používat Azure Stack VaaS.

K testování vašich virtuálních počítačů:

1. Nainstalujte místního agenta
2. Vložit chyb do vašeho systému
3. Spusťte místní agent

## <a name="download-and-start-the-local-agent"></a>Stáhněte a spusťte místní agent

Stáhněte agenta do počítače, který splňuje požadavky ve vašem datovém centru, který není součástí systému Azure Stack, ale ten, který má přístup do všech koncových bodů služby Azure Stack.

### <a name="machine-prerequisites"></a>Požadavky na počítač

Zkontrolujte, že váš počítač splňuje následující kritéria:

- Přístup k všechny koncové body služby Azure Stack
- Rozhraní .NET 4.6 a nainstalovaný PowerShell 5.0
- Minimálně 8 GB paměti RAM
- Nejméně 8 procesory
- Minimální 200 GB místa
- Stabilní síťové připojení k Internetu

Azure Stack je testovaného systému. Tento počítač by neměl být součástí služby Azure Stack nebo hostované v cloudu Azure Stack.

### <a name="download-and-install-the-agent"></a>Stažení a instalace agenta

1. V řádku se zvýšenými oprávněními na počítači, který použijete ke spuštění testů otevřete prostředí Windows PowerShell.
2. Spuštěním následujícího příkazu stáhněte místní agent:

    ```PowerShell  
        Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.3.2.0.nupkg" -outfile "OnPremAgent.zip"
        Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent.3.2.0 -Force
        Set-Location VaaSOnPremAgent.3.2.0\lib\net46
    ````

3. Spuštěním následujícího příkazu nainstalujte místního agenta závislosti:

    ```PowerShell  
        $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-VaaSPrerequisites -AadTenantId <AadTenantId> `
        -ServiceAdminCreds <ServiceAdminCreds> `
        -ArmEndpoint https://adminmanagement.<ExternalFqdn> `
        -Region <Region>
    ````

    **Parametry**

    | Parametr | Popis |
    | --- | --- |
    | aadServiceAdminUser | Uživatele s rolí globální správce pro vašeho tenanta Azure AD. Například může být, vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | Heslo pro uživatele s rolí globálního správce. |
    | AadTenantId | ID tenanta Azure AD pro účet Azure zaregistrován ověření jako služba. |
    | ExternalFqdn | Plně kvalifikovaný název domény můžete získat z konfiguračního souboru. Pokyny naleznete v tématu [testovací parametry pro ověření jako službu Azure Stack](azure-stack-vaas-parameters-test.md). |
    | Oblast | Oblast vašeho tenanta Azure AD. |

Tento příkaz stáhne veřejnou image z úložiště (PIR) obrázku (operačního systému virtuálního pevného disku) a zkopírujte ze služby Azure blob storage do úložiště Azure Stack. 

![Stáhnout nezbytné součásti](media/installingprereqs.png)

> [!Note]  
> Pokud se zobrazuje rychlost pomalé sítě při stahování těchto bitových kopií, samostatně stáhnout do místní sdílené složky a zadat parametr **- LocalPackagePath** *FileShareOrLocalPath*. Ještě s něčím poradit na PIR stahování můžete najít v části [popisovač pomalé síťové připojení](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) z [řešení ověřování jako služba](azure-stack-vaas-troubleshoot.md).

## <a name="fault-injection"></a>Vkládání chyb

Microsoft proto navrhl Azure Stack pro odolnost a tolerovat více typů chyb softwaru a hardwaru. Vkládání chyb zvyšuje počet chyb v systému. Toto zvýšení pomáhá odhalit problémy dříve, tak, aby se redukovala počet incidentů, které vyřazení systému z provozu.

Spuštěním následujících příkazů vložení chyb do vašeho systému.

1. Otevřete prostředí Windows PowerShell v řádku se zvýšenými oprávněními.

2. Spusťte následující příkaz:

    ```PowerShell  
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-ServiceFabricSDK Install-ServiceFabricSDK
    ```

## <a name="run-the-agent"></a>Spustit agenta

1. Otevřete prostředí Windows PowerShell v řádku se zvýšenými oprávněními.

2. Spusťte následující příkaz:

    ````PowerShell  
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ````

      **Parametry**  
    
    | Parametr | Popis |
    | --- | --- |
    | VaaSUserId | ID uživatele použít k přihlášení k portálu VaaS (například UserName@Contoso.com) |
    | VaaSTenantId | ID tenanta Azure AD pro účet Azure zaregistrován ověření jako služba. |

    > [!Note]  
    > Při spuštění agenta aktuální pracovní adresář musí být umístění hostitele modulu úlohy, spustitelný soubor, **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Pokud nevidíte všechny chyby ohlásil, místního agenta proběhla úspěšně. Následující příklad text se zobrazí v okně konzoly.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Začínáme agenta](media/startedagent.png)

Agenta je jednoznačně identifikují pomocí jeho názvu. Ve výchozím nastavení použije název domény plně kvalifikovaný název (FQDN) počítače, ve kterém byl spuštěn. Musíte minimalizovat okno, aby všechny náhodného kliknutí v okně jako změna výběru pozastaví dalších akcí.

## <a name="next-steps"></a>Další postup

- [Ověření nové řešení Azure Stack](azure-stack-vaas-validate-solution-new.md)  
- Pokud máte pomalé nebo přerušované připojení k Internetu, si můžete stáhnout PIR image. Další informace najdete v tématu [popisovač pomalé síťové připojení](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity).
- Další informace o [ověřování služby Azure Stack jako služba](https://docs.microsoft.com/azure/azure-stack/partner).
