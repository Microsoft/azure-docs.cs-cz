---
title: Řešení potíží Azure Cloud prostředí | Microsoft Docs
description: Řešení potíží s prostředí cloudu Azure
services: azure
documentationcenter: ''
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: damaerte
ms.openlocfilehash: 3c01a31eae2b90ecb54cbfba7f565fd140db3773
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Řešení potíží s & omezení Azure cloudové prostředí

Známá řešení pro odstraňování potíží v prostředí cloudu Azure patří:

## <a name="general-troubleshooting"></a>Obecné řešení potíží

### <a name="early-timeouts-in-firefox"></a>Časná vypršení časových limitů ve Firefoxu
- **Podrobnosti o**: prostředí cloudu využívá otevřete websocket předat vstupu a výstupu do prohlížeče. FireFox má přednastavené zásady, které můžete zavřít websocket předčasně způsobuje časná vypršení časových limitů v prostředí cloudu.
- **Řešení**: Otevřete FireFox a přejděte do "o: konfigurace" do pole Adresa URL. Vyhledejte "network.websocket.timeout.ping.request" a změňte hodnotu od 0 do 10.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Dialogové okno úložiště - Chyba: 403 RequestDisallowedByPolicy
- **Podrobnosti o**: při vytváření účtu úložiště prostřednictvím cloudové prostředí, se nezdařilo z důvodu zásady služby Azure umístit váš správce. Chybová zpráva bude obsahovat: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Řešení**: Obraťte se na správce odstranit nebo aktualizovat Azure zásady odepření vytvoření úložiště Azure.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Dialogové okno úložiště - Chyba: 400 DisallowedOperation
 - **Podrobnosti o**: při použití předplatného služby Azure Active Directory, nelze vytvořit úložiště.
 - **Řešení**: používat předplatné Azure podporuje vytváření prostředků úložiště. Předplatná Azure AD, nebudou se moct vytváření prostředků Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminálové výstup - Chyba: připojení terminál se nezdařilo: Nelze vytvořit protokolu websocket. Stiskněte klávesu `Enter` se znovu připojit.
 - **Podrobnosti o**: cloudové prostředí vyžaduje schopnost připojení protokolu websocket prostředí cloudové infrastruktury.
 - **Řešení**: Zkontrolujte jste nakonfigurovali nastavení sítě umožnit odesílání požadavky https a požadavky protokolu websocket do domén v *. console.azure.com.

## <a name="bash-troubleshooting"></a>Bash, řešení potíží

### <a name="cannot-run-az-login"></a>Nelze spustit az přihlášení

- **Podrobnosti o**: spuštění `az login` nebude fungovat, protože již jste přihlášeni pod účtem, použít pro přihlášení do prostředí cloudu nebo Azure na portálu.
- **Řešení**: využívat váš účet použitý k přihlášení nebo odhlášení a novému ověření pomocí účtu Azure určený.

### <a name="cannot-run-the-docker-daemon"></a>Nelze spustit proces démon docker

- **Podrobnosti o**: kontejner pro hostování prostředí shell využívá cloudové prostředí, v důsledku spuštění démona je zakázaná.
- **Řešení**: využívat [počítač docker](https://docs.docker.com/machine/overview/), nainstalované ve výchozím nastavení se ke správě vzdáleného hostitele Docker docker kontejnery.

## <a name="powershell-troubleshooting"></a>Řešení potíží s prostředí PowerShell

### <a name="no-home-directory-persistence"></a>Trvalost No $Home adresáře

- **Podrobnosti o**: všechna data aplikace (například: git, vim a dalších) zapisuje do `$Home` není zachován po relací prostředí PowerShell.
- **Řešení**: ve vašem profilu prostředí PowerShell vytvořit symbolický odkaz na aplikaci v konkrétní složce `clouddrive` na $Home.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>CTRL + C není ukončit rutiny řádku

- **Podrobnosti o**: Při pokusu o ukončení výzvou rutiny `Ctrl+C` neexistuje řádku.
- **Řešení**: ukončíte řádku stiskněte `Ctrl+C` pak `Enter`.

### <a name="gui-applications-are-not-supported"></a>Grafické uživatelské rozhraní aplikací není podporované.

- **Podrobnosti o**: Pokud uživatel spustí aplikaci pomocí grafického uživatelského rozhraní, nevrátí řádku. Například když uživatel provede klonování privátní úložiště GitHub, který je dvoufaktorové ověřování povoleno, se zobrazí dialogové okno pro dokončení dvoufaktorové ověřování.  
- **Řešení**: zavřením a otevřením okna.

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help - online nelze otevřít stránku nápovědy

- **Podrobnosti o**: Pokud typy uživatelů `Get-Help Find-Module -online`, například jeden zobrazí chybovou zprávu: `Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
- **Řešení**: Zkopírujte adresu url a ručně ho otevřete v prohlížeči.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Řešení potíží s vzdálenou správu virtuálních počítačů Azure

- **Podrobnosti o**: z důvodu výchozí nastavení brány Windows Firewall pro WinRM může uživatel zobrazit následující chyba: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Řešení**: Zkontrolujte, že je spuštěný virtuální počítač. Můžete spustit `Get-AzureRmVM -Status` chcete zjistit stav virtuálního počítače.  V dalším kroku přidejte nové pravidlo brány firewall na vzdálené virtuální počítač povolit vzdálená připojení z žádné podsítě, například

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 Můžete použít [rozšíření vlastních skriptů Azure](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript) předejdete přihlášení k virtuálnímu počítači vzdáleného pro přidání nové pravidlo brány firewall.
 Předchozí skript můžete uložit do souboru, například `addfirerule.ps1`a odešlete ji do vašeho kontejneru úložiště Azure.
 Opakujte následující příkaz:

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir` ukládá do mezipaměti výsledek v Azure jednotky

- **Podrobnosti o**: výsledek `dir` se uloží do mezipaměti v Azure jednotce.
- **Řešení**: Po vytvoření nebo odeberete prostředek v Azure jednotky zobrazení, spusťte `dir -force` aktualizovat.

## <a name="general-limitations"></a>Obecná omezení
Prostředí Azure Cloud má tato známá omezení:

### <a name="system-state-and-persistence"></a>Stav systému a trvalost

Na počítač, který obsahuje vaše cloudové prostředí relace je dočasný a bude recyklována po vaše relace je neaktivní po dobu 20 minut. Cloudové prostředí vyžaduje sdílenou složku Azure chcete připojit. Vaše předplatné v důsledku toho musí být schopni nastavit prostředků úložiště pro přístup k prostředí cloudu. Mezi další aspekty patří:

* S použitím připojené úložiště, pouze změny v rámci `clouddrive` adresáře jsou nastavené jako trvalé. V Bash vaše `$Home` adresáře je také jako trvalý.
* Sdílené složky Azure může být připojen pouze z uvnitř vaší [přiřazené oblast](persisting-shell-storage.md#mount-a-new-clouddrive).
  * V Bash, spusťte `env` najít vaší oblasti nastavit jako `ACC_LOCATION`.
* Soubory Azure podporuje pouze místně redundantního úložiště a účty geograficky redundantní úložiště.

### <a name="browser-support"></a>Podpora prohlížeče

Cloudové prostředí podporuje nejnovější verze Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox a Apple Safari. Safari v privátním režimu není podporována.

### <a name="copy-and-paste"></a>Kopírování a vkládání

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Pro daného uživatele může být aktivní pouze jeden prostředí

Uživatelé mohou spouštět pouze jeden typ prostředí najednou, buď **Bash** nebo **prostředí PowerShell**. Ale může mít více instancí spuštěné v jednom okamžiku Bash nebo prostředí PowerShell. Vzájemná záměna mezi Bash nebo prostředí PowerShell příčiny cloudové prostředí k restartování, což Ukončí existující relací.

### <a name="usage-limits"></a>Limity využití

Cloudové prostředí je určen pro případy použití interaktivní. V důsledku toho ukončení relací neinteraktivní všechny dlouhodobé bez upozornění.

## <a name="bash-limitations"></a>Omezení bash

### <a name="user-permissions"></a>Uživatelská oprávnění

Máte nastavená oprávnění jako běžní uživatelé bez přístupu sudo. Všechny instalace mimo vaší `$Home` adresáře není trvalý.

### <a name="editing-bashrc"></a>Úpravy .bashrc

Proveďte opatrní při úpravě .bashrc, tak může způsobit neočekávané chyby v prostředí cloudu.

## <a name="powershell-limitations"></a>Omezení prostředí PowerShell

### <a name="slow-startup-time"></a>Pomalé spuštění

Prostředí PowerShell v prostředí cloudu Azure (Preview) může trvat až 60 sekund k chybě při inicializaci verzi Preview.

### <a name="default-file-location-when-created-from-azure-drive"></a>Výchozí umístění souboru při vytvoření z disku Azure:

Pomocí rutin prostředí PowerShell, uživatelé nemůžou vytvářet soubory v Azure jednotce. Pokud uživatelé vytvářejí nové soubory pomocí jiných nástrojů, jako je například vim nebo nano, soubory se uloží do složky C:\Users ve výchozím nastavení. 

### <a name="gui-applications-are-not-supported"></a>Grafické uživatelské rozhraní aplikací není podporované.

Pokud uživatel spustí příkaz, který by vytvořit dialogové okno Windows, jako například `Connect-AzureAD` nebo `Connect-AzureRmAccount`, například jeden zobrazí chybovou zprávu: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

## <a name="gdpr-compliance-for-cloud-shell"></a>GDPR dodržování předpisů pro cloudové prostředí

Prostředí Azure Cloud vážně trvá vašich osobních údajů, dat zaznamenaných a uložené ve službě Azure Cloud prostředí používané k zajištění výchozí hodnoty pro vaše prostředí, například naposledy použitých prostředí, velikost písma upřednostňované, typ upřednostňované písma a sdílených složek podrobnosti který zpět clouddrive. Měli chcete exportovat nebo odstranění těchto dat, jsme zahrnuli podle následujících pokynů.

### <a name="export"></a>Export
Za účelem **exportovat** uživatelská nastavení prostředí cloudové uloží pro vás, jako upřednostňovaný prostředí, velikost písma a typ písma, spusťte následující příkazy.

1. Spusťte Bash v prostředí cloudu
2. Spusťte následující příkazy:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
```

### <a name="delete"></a>Odstranění
Za účelem **odstranit** uživatelská nastavení prostředí cloudové uloží pro vás, jako upřednostňovaný prostředí, velikost písma a typ písma, spusťte následující příkazy. Při příštím spuštění prostředí cloudu zobrazí se výzva k připojit sdílenou složku znovu. 

Skutečné soubory Azure sdílené složky nebudou odstraněny, pokud odstraníte uživatelská nastavení, přejděte na Azure soubory pro dokončení této akce.

1. Spusťte Bash v prostředí cloudu
2. Spusťte následující příkazy:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
```
