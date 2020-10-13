---
title: Správa vzdálených místních prostředků pomocí funkcí PowerShellu
description: Naučte se, jak nakonfigurovat Hybrid Connections v Azure Relay, aby se připojila aplikace funkce PowerShellu k místním prostředkům, které pak můžete použít k vzdálené správě místního prostředku.
author: eamono
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: eamono
ms.openlocfilehash: 6034d1327d263eda49881af5eedf94ae06495128
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83122112"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Správa hybridních prostředí pomocí prostředí PowerShell v Azure Functions a App Service Hybrid Connections

Funkce Azure App Service Hybrid Connections umožňuje přístup k prostředkům v jiných sítích. Další informace o této funkci najdete v dokumentaci k [Hybrid Connections](../app-service/app-service-hybrid-connections.md) . Tento článek popisuje, jak používat tuto možnost ke spouštění funkcí prostředí PowerShell, které cílí na místní server. Tento server pak můžete použít ke správě všech prostředků v místním prostředí pomocí funkce Azure PowerShell.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Konfigurace místního serveru pro vzdálenou komunikaci PowerShellu

Následující skript umožňuje vzdálenou komunikaci PowerShellu a vytvoří nové pravidlo brány firewall a naslouchací proces https protokolu WinRM. Pro účely testování se používá certifikát podepsaný svým držitelem. V produkčním prostředí doporučujeme použít podepsaný certifikát.

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>Vytvoření aplikace funkcí PowerShellu na portálu

Funkce App Service Hybrid Connections je dostupná jenom v cenových plánech Basic, Standard a Isolated. Když vytvoříte aplikaci Function App pomocí PowerShellu, vytvořte nebo vyberte jeden z těchto plánů.

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

1. Na stránce **Nový** vyberte **COMPUTE**  >  **Function App**.

1. Na stránce **základy** použijte nastavení aplikace Function App, jak je uvedeno v následující tabulce.

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace funkcí vytvořena. |
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |
    | **Název aplikace funkcí** | Globálně jedinečný název | Název identifikující novou aplikaci funkcí. Platné znaky jsou `a-z` (bez rozlišování malých a velkých písmen), `0-9` a `-`.  |
    |**Publikovat**| Kód | Možnost publikování souborů kódu nebo kontejneru Docker |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte PowerShell Core. |
    |**Verze**| Číslo verze | Vyberte verzi nainstalovaného modulu runtime.  |
    |**Oblast**| Upřednostňovaná oblast | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž nebo poblíž dalších služeb, ke kterým máte přístup. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-basics.png" alt-text="Vytvořte základní informace o Function App." border="true":::

1. Vyberte **Další: hostování**. Na stránce **hostování** zadejte následující nastavení.

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Účet úložiště](../storage/common/storage-account-create.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete použít i existující účet, který musí splňovat [požadavky na účet úložiště](../azure-functions/functions-scale.md#storage-account-requirements). |
    |**Operační systém**| Preferovaný operační systém | Operační systém je předem vybraný pro vás na základě výběru zásobníku modulu runtime, ale v případě potřeby můžete změnit nastavení. |
    | **[Typ plánu](../azure-functions/functions-scale.md)** | **Plán služby App Service** | Vyberte **plán služby App Service**. Pokud používáte plán služby App Service, musíte zajistit správu [škálování vaší aplikace funkcí](../azure-functions/functions-scale.md).  |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-hosting.png" alt-text="Vytvořte základní informace o Function App." border="true":::

1. Vyberte **Další: monitorování**. Na stránce **monitorování** zadejte následující nastavení.

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | Výchozí | Vytvoří Application Insights prostředek stejného *názvu aplikace* v nejbližší podporované oblasti. Rozbalením tohoto nastavení nebo výběrem možnosti **vytvořit nový**můžete změnit název Application Insights nebo zvolit jinou oblast v [geografickém umístění Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , kam chcete ukládat data. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-monitoring.png" alt-text="Vytvořte základní informace o Function App." border="true":::

1. Výběrem možnosti **zkontrolovat + vytvořit** zkontrolujte výběry konfigurace aplikace.

1. Na stránce **Revize + vytvořit** zkontrolujte nastavení a pak vyberte **vytvořit** a nasaďte a nasaďte aplikaci Function App.

1. Vyberte ikonu **oznámení** v pravém horním rohu portálu a sledujte zprávu o **úspěšném nasazení** .

1. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte. Můžete také vybrat **Připnout na řídicí panel**. Připnutí usnadňuje návrat k tomuto prostředku Function App z vašeho řídicího panelu.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Vytvoření hybridního připojení pro aplikaci Function App

Hybridní připojení jsou nakonfigurovaná v části síť aplikace Function App:

1. V části **Nastavení** aplikace Function App, kterou jste právě vytvořili, vyberte **sítě**. 
1. Vyberte **Konfigurovat koncové body hybridních připojení**.
   
    :::image type="content" source="./media/functions-hybrid-powershell/configure-hybrid-connection-endpoint.png" alt-text="Vytvořte základní informace o Function App." border="true":::

1. Vyberte **Přidat hybridní připojení**.
   
    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-overview.png" alt-text="Vytvořte základní informace o Function App." border="true":::

1. Zadejte informace o hybridním připojení, jak je znázorněno na následujícím snímku obrazovky. Máte možnost nastavit, aby se nastavení **hostitele koncového bodu** shodovalo s názvem hostitele místního serveru, aby bylo snazší pamatovat server později při spuštění vzdálených příkazů. Port odpovídá výchozímu portu služby vzdálené správy systému Windows, který byl dříve definován na serveru.
  
      :::image type="content" source="./media/functions-hybrid-powershell/add-hybrid-connection.png" alt-text="Vytvořte základní informace o Function App." border="true":::

    | Nastavení      | Navrhovaná hodnota  |
    | ------------ | ---------------- |
    | **Název hybridního připojení** | ContosoHybridOnPremisesServer |
    | **Hostitel koncového bodu** | finance1 |
    | **Port koncového bodu** | 5986 |
    | **Obor názvů ServiceBus** | Vytvořit nové |
    | **Umístění** | Vybrat dostupné umístění |
    | **Název** | contosopowershellhybrid | 

1. Vyberte **OK** a vytvořte hybridní připojení.

## <a name="download-and-install-the-hybrid-connection"></a>Stažení a instalace hybridního připojení

1. Vyberte možnost **Stáhnout Správce připojení** a uložte soubor *. msi* místně do počítače.

    :::image type="content" source="./media/functions-hybrid-powershell/download-hybrid-connection-installer.png" alt-text="Vytvořte základní informace o Function App." border="true":::

1. Zkopírujte soubor *. msi* z místního počítače na místní server.
1. Spusťte instalační program Správce hybridního připojení a nainstalujte službu na místní server.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-installation.png" alt-text="Vytvořte základní informace o Function App." border="true":::

1. Z portálu otevřete hybridní připojení a pak zkopírujte připojovací řetězec brány do schránky.

    :::image type="content" source="./media/functions-hybrid-powershell/copy-hybrid-connection.png" alt-text="Vytvořte základní informace o Function App." border="true":::

1. Otevřete Správce hybridního připojení uživatelské rozhraní na místním serveru.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-ui.png" alt-text="Vytvořte základní informace o Function App." border="true":::

1. Vyberte **zadat ručně** a vložte připojovací řetězec ze schránky.

    :::image type="content" source="./media/functions-hybrid-powershell/enter-manual-connection.png" alt-text="Vytvořte základní informace o Function App." border="true":::

1. Pokud se nezobrazí jako připojené, restartujte Správce hybridního připojení z PowerShellu.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Vytvoření nastavení aplikace pro heslo účtu správce

1. V části **Nastavení** aplikace Function App vyberte **Konfigurace**. 
1. Vyberte **+ nové nastavení aplikace**.

    :::image type="content" source="./media/functions-hybrid-powershell/select-configuration.png" alt-text="Vytvořte základní informace o Function App." border="true":::

1. Pojmenujte nastavení **ContosoUserPassword**a zadejte heslo. Vyberte **OK**.
1. Vyberte **Uložit** a uložte heslo do aplikace Function.

    :::image type="content" source="./media/functions-hybrid-powershell/save-administrator-password.png" alt-text="Vytvořte základní informace o Function App." border="true":::

## <a name="create-a-function-http-trigger"></a>Vytvoření triggeru funkce HTTP

1. Ve vaší aplikaci Function App vyberte **funkce**a pak vyberte **+ Přidat**.

    :::image type="content" source="./media/functions-hybrid-powershell/create-http-trigger-function.png" alt-text="Vytvořte základní informace o Function App." border="true":::

1. Vyberte šablonu **triggeru http** .

    :::image type="content" source="./media/functions-hybrid-powershell/select-http-trigger-template.png" alt-text="Vytvořte základní informace o Function App." border="true":::

1. Pojmenujte novou funkci a vyberte **vytvořit funkci**.

    :::image type="content" source="./media/functions-hybrid-powershell/create-new-http-function.png" alt-text="Vytvořte základní informace o Function App." border="true":::

## <a name="test-the-function"></a>Testování funkce

1. V nové funkci vyberte **Code + test**. Nahraďte kód PowerShellu ze šablony následujícím kódem:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

1. Vyberte **Uložit**.

    :::image type="content" source="./media/functions-hybrid-powershell/save-http-function.png" alt-text="Vytvořte základní informace o Function App." border="true":::

 1. Vyberte **test**a potom vyberte **Spustit** pro otestování funkce. Zkontrolujte protokoly a ověřte, zda byl test úspěšný.

     :::image type="content" source="./media/functions-hybrid-powershell/test-function-hybrid.png" alt-text="Vytvořte základní informace o Function App." border="true":::

## <a name="managing-other-systems-on-premises"></a>Místní správa jiných systémů

Pomocí připojeného místního serveru se můžete připojit k ostatním serverům a systémům pro správu v místním prostředí. Díky tomu můžete spravovat operace datacentra z Azure pomocí vašich funkcí PowerShellu. Následující skript zaregistruje relaci konfigurace PowerShellu, která se spouští pod zadanými pověřeními. Tyto přihlašovací údaje musí být pro správce na vzdálených serverech. Tuto konfiguraci pak můžete použít pro přístup k dalším koncovým bodům na místním serveru nebo datacentru.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

Nahraďte následující proměnné v tomto skriptu hodnotami platnými z vašeho prostředí:
* $HybridEndpoint
* $RemoteServer

Ve dvou předchozích scénářích se můžete připojit a spravovat v místních prostředích pomocí prostředí PowerShell v Azure Functions a Hybrid Connections. Doporučujeme vám seznámit se s [Hybrid Connections](../app-service/app-service-hybrid-connections.md) a [PowerShellem ve funkcích](./functions-reference-powershell.md).

[Virtuální sítě](./functions-create-vnet.md) Azure můžete použít také k připojení k místnímu prostředí prostřednictvím Azure Functions.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Další informace o práci s funkcemi PowerShellu](functions-reference-powershell.md)
