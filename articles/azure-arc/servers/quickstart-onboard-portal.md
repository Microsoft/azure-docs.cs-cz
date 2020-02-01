---
title: Připojení hybridních počítačů k Azure z Azure Portal
description: V tomto článku se dozvíte, jak nainstalovat agenta a připojit počítače k Azure pomocí ARC Azure pro servery (Preview) z Azure Portal.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 01/29/2020
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 165ed8d0aad7011f1ea71ff870ee4629f1f12613
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898597"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Připojení hybridních počítačů k Azure z Azure Portal

Můžete povolit Azure ARC pro servery (Preview) pro jeden nebo malý počet počítačů se systémem Windows nebo Linux v prostředí provedením sady kroků ručně nebo pomocí automatizované metody spuštěním skriptu šablony, který poskytujeme. Tento skript automatizuje stahování a instalaci obou agentů.

Tato metoda instalace vyžaduje, abyste měli na počítači oprávnění správce pro instalaci a konfiguraci agenta. V systému Linux pomocí kořenového účtu a ve Windows jste členem místní skupiny Administrators.

Než začnete, zkontrolujte [požadavky](overview.md#prerequisites) a ověřte, že vaše předplatné a prostředky splňují požadavky.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="generate-install-script-from-the-azure-portal"></a>Vygenerovat instalační skript z Azure Portal

Skript pro automatizaci stahování, instalace a navázání připojení pomocí Azure ARC je dostupný z Azure Portal. Následující kroky popisují, jak tento proces dokončit.

1. V prohlížeči spusťte [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

2. Na stránce **počítače – ARC Azure** buď vyberte **+ Přidat** v levém horním rohu, nebo vyberte možnost **vytvořit počítač – Azure ARC** v dolní části podokna uprostřed. 

3. Na stránce **Vyberte metodu** vyberte z dlaždice **přidat počítače pomocí interaktivního skriptu** **vygenerujte skript**.

4. Na stránce **vygenerovat skript** vyberte předplatné a skupinu prostředků, ve které chcete, aby se počítač spravoval v rámci Azure. Vyberte umístění Azure, kde se budou ukládat metadata počítače.

    >[!NOTE]
    >Azure ARC pro servery (Preview) podporuje jenom tyto oblasti:
    >- WestUS2
    >- WestEurope
    >- WestAsia
    >

5. Na stránce **vygenerovat skript** v rozevíracím seznamu **operační systém** vyberte příslušný operační systém, na kterém bude skript spuštěn.

6. Pokud počítač komunikuje prostřednictvím proxy server, aby se mohl připojit k Internetu, vyberte možnost **Další: proxy server >** . Na kartě **proxy server** zadejte proxy server IP adresu nebo název a číslo portu, které bude počítač používat ke komunikaci s proxy server. Zadejte hodnotu za `http://<proxyURL>:<proxyport>`formátu. Po dokončení vyberte **zkontrolovat a generovat**.  V opačném případě vyberte možnost **zkontrolovat + generovat** a dokončete postup.

7. Na kartě **Revize + generovat** zkontrolujte souhrnné informace a pak vyberte **Stáhnout**. V opačném případě, pokud potřebujete provést změny, můžete vybrat možnost **Předchozí**.

## <a name="install-and-validate-the-agent-on-windows"></a>Instalace a ověření agenta ve Windows

### <a name="install-manually"></a>Ruční instalace

Agenta připojeného počítače můžete nainstalovat ručně spuštěním instalačního balíčku Instalační služba systému Windows `AzureConnectedMachineAgent.msi` po jeho stažení a zkopírování do složky na cílovém serveru nebo ze sdílené síťové složky. Pokud spustíte instalační balíček bez jakýchkoli možností, spustí se Průvodce instalací nástroje, který můžete použít k interaktivní instalaci agenta.

>[!NOTE]
>*Správce* instalace nebo odinstalace agenta jsou nutná oprávnění.

Pokud počítač potřebuje komunikovat prostřednictvím proxy server ke službě, po instalaci agenta musíte spustit příkaz popsaný v níže uvedené části, chcete-li nastavit proměnnou prostředí proxy server systému `https_proxy`.

Následující tabulka obsahuje parametry, které podporují instalaci agenta z příkazového řádku.

| Parametr | Popis |
|:--|:--|
| /? | Vrátí seznam možností příkazového řádku. |
| /S | Provádí tichou instalaci bez zásahu uživatele. |

Pokud třeba chcete spustit instalační program s parametrem `/?`, zadejte `msiexec.exe /i AzureConnectedMachineAgent.msi /?`.

Soubory pro agenta připojeného počítače jsou ve výchozím nastavení nainstalovány ve *složce C:\Program Files\AzureConnectedMachineAgent* . Pokud se agent po dokončení instalace nepovede spustit, podívejte se do protokolů, kde najdete podrobnější informace o chybě. Adresář protokolu je *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-using-scripted-method"></a>Instalace pomocí skriptované metody

1. Přihlaste se na server.

2. Otevřete příkazový řádek PowerShellu se zvýšenými oprávněními.

3. Přejděte do složky nebo sdílené složky, na kterou jste zkopírovali skript a spusťte ho na serveru spuštěním příkazu `./OnboardingScript.ps1`.

### <a name="configure-agent-proxy-setting"></a>Konfigurovat nastavení proxy agenta

Spuštěním následujícího příkazu nastavte proměnnou prostředí proxy server.

```powershell
# If a proxy server is needed, execute these commands with proxy URL and port
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# The agent service needs to be restarted after the proxy environment variable is set in order for the changes to take effect.
Restart-Service -Name himds
```

>[!NOTE]
>Agent nepodporuje nastavení ověřování proxy serveru v této verzi Preview.
>

### <a name="configure-agent-communication"></a>Konfigurace komunikace agenta

Po instalaci agenta je nutné nakonfigurovat agenta, aby komunikoval se službou Azure ARC, spuštěním následujícího příkazu:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Instalace a ověření agenta v systému Linux

Agent připojeného počítače pro Linux je k dispozici v preferovaném formátu balíčku pro distribuci (. Ot./min. nebo. DEB) hostuje [úložiště balíčků](https://packages.microsoft.com/)od Microsoftu. Sada `Install_linux_azcmagent.sh` skriptu prostředí, která se nachází v [https://aka.ms/azcmagent](https://aka.ms/azcmagent) provádí následující akce:

- Nakonfiguruje hostitelský počítač ke stažení balíčku agenta z packages.microsoft.com.
- Nainstaluje balíček hybridního poskytovatele prostředků.
- Volitelně můžete nakonfigurovat agenta pomocí informací o proxy serveru tak, že zahrnete parametr `--proxy "{proxy-url}:{proxy-port}"`.

Skript obsahuje také logiku pro identifikaci podporovaných a nepodporovaných distribucí a také ověření požadovaných oprávnění k provedení instalace. 

Následující příklad stáhne agenta a nainstaluje jej bez provedení žádné z podmíněných kontrol.

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Chcete-li stáhnout a nainstalovat agenta, včetně parametru `--proxy` pro konfiguraci agenta pro komunikaci přes proxy server, spusťte následující příkazy:

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-agent-communication"></a>Konfigurace komunikace agenta

Po instalaci agenta je nutné nakonfigurovat agenta, aby komunikoval se službou Azure ARC, spuštěním následujícího příkazu:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-connection-with-azure-arc"></a>Ověření připojení pomocí ARC Azure

Po provedení kroků k instalaci agenta a jeho konfiguraci pro připojení ke službě Azure ARC pro servery (Preview), navštivte Azure Portal a ověřte, že se server úspěšně připojil. Počítače můžete v Azure Portal zobrazit tak, že navštívíte [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

![Úspěšné zprovoznění](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Vyčištění

Pokud chcete odpojit počítač od Arc Azure pro servery (Preview), musíte provést následující kroky.

1. Otevřete Azure ARC pro servery (Preview) návštěvou [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

2. Vyberte počítač v seznamu, klikněte na elipsu (`...`) a vyberte **Odstranit**.

3. Chcete-li odinstalovat agenta systému Windows z počítače, postupujte takto:

    1. Přihlaste se k počítači pomocí účtu, který má práva správce.

    2. V **Ovládacích panelech**vyberte **programy a funkce**.

    3. V **programech a funkcích**vyberte **Azure Connected Machine agent**, vyberte **odinstalovat**a pak vyberte **Ano**.

        >[!NOTE]
        >Průvodce instalací agenta lze spustit také dvojitým kliknutím na balíček instalačního programu **AzureConnectedMachineAgent. msi** .

    Chcete-li odinstalovat skript, můžete použít následující příklad, který načte kód produktu a odinstaluje agenta pomocí příkazového řádku Msiexec. exe-`msiexec /x {Product Code}`. Otevřete Editor registru a podívejte se do klíče registru `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` a vyhledejte identifikátor GUID kódu produktu. Pak můžete agenta odinstalovat pomocí programu Msiexec.

   Následující příklad ukazuje odinstalaci agenta.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
   ```

4. Chcete-li odinstalovat agenta pro Linux, spusťte následující příkaz k odinstalaci agenta.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přiřazení zásady k připojeným počítačům](../../governance/policy/assign-policy-portal.md)
