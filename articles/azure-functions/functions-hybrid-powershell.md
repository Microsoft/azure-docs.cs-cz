---
title: Správa vzdálených místních prostředků pomocí funkcí Prostředí PowerShell
description: Zjistěte, jak nakonfigurovat hybridní připojení ve službě Azure Relay pro připojení aplikace funkcí PowerShellu k místním prostředkům, které pak můžou vzdáleně spravovat místní prostředky.
author: eamono
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 36fc4c873dccfe9fa814bddccd829ed04207f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226940"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Správa hybridních prostředí pomocí PowerShellu v Azure Functions and App Service Hybrid Connections

Funkce hybridnípřipojení služby Azure App Service umožňuje přístup k prostředkům v jiných sítích. Další informace o této funkci naleznete v dokumentaci [hybridní připojení.](../app-service/app-service-hybrid-connections.md) Tento článek popisuje, jak tuto funkci používat ke spuštění funkcí prostředí PowerShell, které cílí na místní server. Tento server pak můžete použít ke správě všech prostředků v místním prostředí z funkce Azure PowerShell.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Konfigurace místního serveru pro vzdálené hlasování prostředí PowerShell

Následující skript umožňuje vzdálené volání prostředí PowerShell a vytvoří nové pravidlo brány firewall a naslouchací proces https služby WinRM. Pro účely testování se používá certifikát podepsaný svým držitelem. V provozním prostředí doporučujeme použít podepsaný certifikát.

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

Funkce Hybridní připojení služby App Service je k dispozici pouze v základních, standardních a izolovaných cenových plánech. Když vytvoříte aplikaci funkcí s PowerShellem, vytvořte nebo vyberte jeden z těchto plánů.

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **+ Vytvořit prostředek** v nabídce vlevo a pak vyberte Aplikace **Funkce**.

1. V **případě hostování plánu**vyberte Plán **služby App Service**a pak vyberte **Plán/umístění služby App Service**.

1. Vyberte **Vytvořit nový**, zadejte název **plánu služby App Service,** zvolte **umístění** v [oblasti](https://azure.microsoft.com/regions/) ve vašem okolí nebo v blízkosti jiných služeb, ke které vaše funkce přistupují, a pak vyberte **Cenovou úroveň**.

1. Zvolte standardní plán S1 a pak vyberte **Použít**.

1. Chcete-li plán vytvořit, vyberte **ok** a pak nakonfigurujte zbývající nastavení **aplikace funkce,** jak je uvedeno v tabulce, bezprostředně za následujícím snímkem obrazovky:

    ![Aplikace pro funkce PowerShell U jádra](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky jsou `a-z`, `0-9` a `-`.  | 
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. |
    | **Skupina prostředků** |  myResourceGroup | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. Můžete také použít navrhovanou hodnotu. |
    | **OS** | Upřednostňovaný operační operační hod | Vyberte Windows. |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Zvolte PowerShell Core. |
    | **Úložiště** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku od 3 do 24 znaků a mohou obsahovat pouze čísla a malá písmena. Můžete taky použít existující účet.
    | **Application Insights** | Výchozí | Vytvoří prostředek Application Insights se stejným *názvem aplikace* v nejbližší podporované oblasti. Rozšířením tohoto nastavení můžete změnit **název nového prostředku** nebo zvolit jiné **umístění** v oblasti geografické oblasti [Azure,](https://azure.microsoft.com/global-infrastructure/geographies/) kde chcete ukládat data. |

1. Po ověření nastavení vyberte **Vytvořit**.

1. Vpravém horním rohu portálu vyberte ikonu **Oznámení** a počkejte na zprávu "Nasazení proběhlo úspěšně".

1. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte. Můžete také vybrat **připnout na řídicí panel**. Připnutí usnadňuje návrat k tomuto prostředku aplikace funkce z řídicího panelu.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Vytvoření hybridního připojení pro aplikaci funkcí

Hybridní připojení jsou konfigurována z síťové části aplikace funkce:

1. V aplikaci funkce vyberte kartu **Funkce platformy** a pak vyberte **Síť .** 
   ![Přehled aplikací pro síť platformy](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. Vyberte **Konfigurovat koncové body hybridních připojení**.
   ![Sítě](./media/functions-hybrid-powershell/select-network-feature.png)  
1. Vyberte **Přidat hybridní připojení**.
   ![Hybridní připojení](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Zadejte informace o hybridním připojení, jak je znázorněno hned za následujícím snímku obrazovky. Máte možnost nastavit **koncový bod hostitele** odpovídat názvu hostitele místního serveru, aby bylo snazší si server později zapamatovat při spuštění vzdálených příkazů. Port odpovídá výchozímu portu služby vzdálené správy systému Windows, který byl definován na serveru dříve.
  ![Přidat hybridní připojení](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **Název hybridního připojení**: ContosoHybridOnPremisesServer
    
    **Hostitel koncového bodu**: finance1
    
    **Port koncového bodu**: 5986
    
    **Obor názvů ServiceBus**: Vytvořit nový
    
    **Umístění**: Vyberte dostupné místo
    
    **Název**: contosopowershellhybrid

5. Chcete-li vytvořit hybridní připojení, vyberte **možnost OK.**

## <a name="download-and-install-the-hybrid-connection"></a>Stažení a instalace hybridního připojení

1. Výběrem **možnosti Stáhnout správce připojení** uložíte soubor MSI místně do počítače.
![Stáhnout instalační program](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. Zkopírujte soubor MSI z místního počítače na místní server.
1. Spusťte instalační program Nástroje pro hybridní připojení a nainstalujte službu na místní server.
![Instalace hybridního připojení](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. Z portálu otevřete hybridní připojení a zkopírujte připojovací řetězec brány do schránky.
![Kopírovat hybridní připojovací řetězec](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Otevřete umístního serveru hybridní ho sazí.
![Otevřené hybridní připojení uj.](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. Vyberte tlačítko **Zadat ručně** a vložte připojovací řetězec ze schránky.
![Vložit připojení](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Pokud se správce hybridního připojení nezobrazuje jako připojený, restartujte správce hybridního připojení z prostředí PowerShell.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Vytvoření nastavení aplikace pro heslo účtu správce

1. V aplikaci funkce vyberte kartu **Funkce platformy.**
1. V části **Obecné nastavení**vyberte **Konfigurace**.
![Vybrat konfiguraci platformy](./media/functions-hybrid-powershell/select-configuration.png)  
1. Chcete-li vytvořit nové nastavení hesla, rozbalte **nové nastavení aplikace.**
1. Pojmenujte nastavení _ContosoUserPassword_a zadejte heslo.
1. Vyberte **OK** a pak uložte heslo do aplikace funkce.
![Přidání nastavení aplikace pro heslo](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Vytvoření funkce http aktivační událost pro testování

1. Vytvořte novou funkci aktivační události HTTP z aplikace funkce.
![Vytvořit novou aktivační událost HTTP](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. Nahraďte kód Prostředí PowerShell ze šablony následujícím kódem:

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

3. Chcete-li funkci otestovat, vyberte **možnost Uložit** a **spustit.**
![Testování aplikace funkcí](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>Správa dalších místních systémů

Připojený místní server můžete použít k připojení k jiným serverům a systémům správy v místním prostředí. To vám umožní spravovat operace datového centra z Azure pomocí funkcí PowerShellu. Následující skript registruje relaci konfigurace prostředí PowerShell, která běží pod zadanými pověřeními. Tato pověření musí být pro správce na vzdálených serverech. Tuto konfiguraci pak můžete použít pro přístup k dalším koncovým bodům na místním serveru nebo datovém centru.

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

Nahraďte následující proměnné v tomto skriptu příslušnými hodnotami z vašeho prostředí:
* $HybridEndpoint
* $RemoteServer

Ve dvou předchozích scénářích můžete připojit a spravovat místní prostředí pomocí PowerShellu v Azure Functions a hybridní připojení. Doporučujeme vám, abyste se dozvěděli více o [hybridních připojeních](../app-service/app-service-hybrid-connections.md) a [PowerShellu ve funkcích](./functions-reference-powershell.md).

[Virtuální sítě](./functions-create-vnet.md) Azure můžete taky použít k připojení k místnímu prostředí prostřednictvím Azure Functions.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Další informace o práci s funkcemi PowerShellu](functions-reference-powershell.md)
