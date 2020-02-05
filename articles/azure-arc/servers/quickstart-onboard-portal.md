---
title: Připojení hybridních počítačů k Azure z Azure Portal
description: V tomto článku se dozvíte, jak nainstalovat agenta a připojit počítače k Azure pomocí Azure ARC pro servery (Preview) z Azure Portal.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 01/29/2020
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 81083a9d94f782201a8eb765ac1f88093c0337c4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024088"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Připojení hybridních počítačů k Azure z Azure Portal

Můžete povolit Azure ARC pro servery (Preview) pro jeden nebo malý počet počítačů se systémem Windows nebo Linux v prostředí provedením sady kroků ručně. Nebo můžete použít automatizovanou metodu spuštěním skriptu šablony, který poskytujeme. Tento skript automatizuje stahování a instalaci obou agentů.

Tato metoda vyžaduje, abyste na počítači měli oprávnění správce pro instalaci a konfiguraci agenta. V systému Linux, pomocí kořenového účtu a ve Windows, jste členem místní skupiny Administrators.

Než začnete, zkontrolujte [požadavky](overview.md#prerequisites) a ověřte, že vaše předplatné a prostředky splňují požadavky.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Vygenerujte instalační skript z Azure Portal

Skript pro automatizaci stahování a instalace a navázání připojení pomocí ARC Azure je dostupný z Azure Portal. Chcete-li dokončit proces, postupujte následovně:

1. V prohlížeči přejdete na [Azure Portal](https://aka.ms/hybridmachineportal).

1. Na stránce **počítače – ARC Azure** vyberte buď **Přidat**, v levém horním rohu nebo možnost **vytvořit počítač – Azure ARC** v dolní části prostředního podokna. 

1. Na stránce **Vyberte metodu** vyberte dlaždici **přidat počítače pomocí interaktivního skriptu** a pak vyberte **vygenerovat skript**.

1. Na stránce **vygenerovat skript** vyberte předplatné a skupinu prostředků, ve které chcete, aby se počítač spravoval v rámci Azure. Vyberte umístění Azure, kam se budou ukládat metadata počítače.

    >[!NOTE]
    >Azure ARC pro servery (Preview) podporuje jenom tyto oblasti:
    >- WestUS2
    >- WestEurope
    >- WestAsia
    >

1. Na stránce **vygenerovat skript** v rozevíracím seznamu **operační systém** vyberte operační systém, ve kterém bude skript spuštěn.

1. Pokud počítač komunikuje prostřednictvím proxy server pro připojení k Internetu, vyberte **Další: proxy server**. 
1. Na kartě **proxy server** zadejte proxy server IP adresu nebo název a číslo portu, které bude počítač používat ke komunikaci s proxy server. Zadejte hodnotu ve formátu `http://<proxyURL>:<proxyport>`. 
1. Vyberte **zkontrolovat a generovat**.

1. Na kartě **Revize + generovat** zkontrolujte souhrnné informace a pak vyberte **Stáhnout**. Pokud stále potřebujete dělat změny, vyberte **Předchozí**.

## <a name="install-and-validate-the-agent-on-windows"></a>Instalace a ověření agenta ve Windows

### <a name="install-manually"></a>Ruční instalace
Agenta připojeného počítače můžete nainstalovat ručně spuštěním balíčku Instalační služba systému Windows *AzureConnectedMachineAgent. msi*. 

> [!NOTE]
> * Chcete-li nainstalovat nebo odinstalovat agenta, je nutné mít oprávnění *správce* .
> * Nejdřív musíte stáhnout a zkopírovat instalační balíček do složky na cílovém serveru nebo ze sdílené síťové složky. Pokud spustíte instalační balíček bez jakýchkoli možností, spustí se Průvodce instalací nástroje, který můžete použít k interaktivní instalaci agenta.

Pokud počítač potřebuje komunikovat prostřednictvím proxy server ke službě, po instalaci agenta musíte spustit příkaz, který je popsán dále v článku. Tím se nastaví proměnná prostředí proxy server systému `https_proxy`.

Následující tabulka obsahuje parametry, které podporují instalaci agenta z příkazového řádku.

| Parametr | Popis |
|:--|:--|
| /? | Vrátí seznam možností příkazového řádku. |
| /S | Provádí tichou instalaci bez zásahu uživatele. |

Pokud třeba chcete spustit instalační program s parametrem `/?`, zadejte `msiexec.exe /i AzureConnectedMachineAgent.msi /?`.

Soubory pro agenta připojeného počítače jsou ve výchozím nastavení nainstalovány ve *složce C:\Program Files\AzureConnectedMachineAgent* . Pokud se agent po dokončení instalace nepovede spustit, podívejte se do protokolů, kde najdete podrobnější informace o chybě. Adresář protokolu je *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Instalace pomocí skriptované metody

1. Přihlaste se k serveru.

1. Otevřete příkazový řádek PowerShellu se zvýšenými oprávněními.

1. Přejděte do složky nebo sdílené složky, do které jste zkopírovali skript, a spusťte ho na serveru spuštěním skriptu `./OnboardingScript.ps1`.

### <a name="configure-the-agent-proxy-setting"></a>Konfigurace nastavení proxy agenta

Chcete-li nastavit proměnnou prostředí proxy server, spusťte následující příkaz:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>Agent nepodporuje nastavení ověřování proxy serveru v této verzi Preview.
>

### <a name="configure-agent-communication"></a>Konfigurace komunikace agenta

Po instalaci agenta je nutné nakonfigurovat agenta, aby komunikoval se službou Azure ARC, spuštěním následujícího příkazu:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Instalace a ověření agenta v systému Linux

Agent připojeného počítače pro Linux je k dispozici v preferovaném formátu balíčku pro distribuci (. Ot./min. nebo. DEB), která je hostovaná v [úložišti balíčků](https://packages.microsoft.com/)Microsoftu. [Sada prostředků skriptu prostředí `Install_linux_azcmagent.sh`](https://aka.ms/azcmagent) provádí následující akce:

- Nakonfiguruje hostitelský počítač ke stažení balíčku agenta z packages.microsoft.com.
- Nainstaluje balíček hybridního poskytovatele prostředků.

Volitelně můžete agenta nakonfigurovat pomocí informací o proxy serveru zahrnutím parametru `--proxy "{proxy-url}:{proxy-port}"`.

Skript obsahuje také logiku pro identifikaci podporovaných a nepodporovaných distribucí a ověřuje oprávnění, která jsou nutná k provedení instalace. 

Následující příklad stáhne agenta a nainstaluje ho:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Chcete-li stáhnout a nainstalovat agenta, včetně parametru `--proxy` pro konfiguraci agenta pro komunikaci přes proxy server, spusťte následující příkazy:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>Konfigurace komunikace agenta

Když nainstalujete agenta, nakonfigurujte ho tak, aby komunikoval se službou Azure ARC, spuštěním následujícího příkazu:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>Ověření připojení pomocí ARC Azure

Po nainstalování agenta a jeho konfiguraci pro připojení k Azure ARC pro servery (Preview), navštivte Azure Portal a ověřte, že se server úspěšně připojil. Zobrazte počítače v [Azure Portal](https://aka.ms/hybridmachineportal).

![Úspěšné připojení k serveru](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Vyčištění

Pokud chcete odpojit počítač od Arc Azure pro servery (Preview), udělejte toto:

1. Kliknutím na [Azure Portal](https://aka.ms/hybridmachineportal)otevřete položku Azure ARC pro servery (Preview).

1. Vyberte počítač v seznamu, vyberte tři tečky ( **...** ) a pak vyberte **Odstranit**.

1. Chcete-li odinstalovat agenta systému Windows z počítače, postupujte takto:

    a. Přihlaste se k počítači pomocí účtu, který má oprávnění správce.  
    b. V **Ovládacích panelech**vyberte **programy a funkce**.  
    c. V **programech a funkcích**vyberte **Azure Connected Machine agent**, vyberte **odinstalovat**a pak vyberte **Ano**.  

    >[!NOTE]
    > Průvodce instalací agenta můžete také spustit dvojitým kliknutím na balíček instalačního programu **AzureConnectedMachineAgent. msi** .

    Pokud chcete odinstalaci skriptovat, můžete použít následující příklad, který načte kód produktu a odinstaluje agenta pomocí příkazového řádku Msiexec. exe-`msiexec /x {Product Code}`. Postupujte následovně:  
    
    a. Otevřete Editor registru.  
    b. V části klíč registru `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`vyhledejte a zkopírujte identifikátor GUID kódu produktu.  
    c. Pak můžete agenta odinstalovat pomocí programu Msiexec.

    Následující příklad ukazuje, jak odinstalovat agenta:

    ```powershell
    Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
    Get-ItemProperty | `
    Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
    ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
    ```

1. Chcete-li odinstalovat agenta pro Linux, spusťte následující příkaz:

      ```bash
      sudo apt purge hybridagent
      ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přiřazení zásady k připojeným počítačům](../../governance/policy/assign-policy-portal.md)
