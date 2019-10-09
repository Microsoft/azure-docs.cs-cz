---
title: Řešení potíží s Azure Cloud Shell | Microsoft Docs
description: Řešení potíží s Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 91dc87cd6bda93663fb4b4eae3d498ae56ba4b3e
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169595"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Řešení potíží s omezeními & Azure Cloud Shell

Známá řešení potíží s Azure Cloud Shell zahrnují:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Obecné řešení potíží

### <a name="early-timeouts-in-firefox"></a>Včasné vypršení časových limitů v prohlížeči FireFox

- **Podrobnosti**: Cloud Shell využívá otevřený WebSocket k předání vstupu a výstupu do prohlížeče. FireFox má přednastavené zásady, které můžou zavřít WebSocket předčasněcí způsobující počáteční vypršení časových limitů v Cloud Shell.
- **Řešení**: Otevřete Firefox a v poli Adresa URL přejděte do části about: config. Vyhledejte "Network. WebSocket. Timeout. otestuje. Request" a změňte hodnotu z 0 na 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Zakázání Cloud Shell v uzamčeném síťovém prostředí

- **Podrobnosti**: Správci můžou chtít zakázat přístup k Cloud Shell pro své uživatele. Cloud Shell využívá přístup k doméně `ux.console.azure.com`, která může být zamítnuta, zastavení jakéhokoli přístupu ke vstupním parametrům Cloud Shell, včetně portal.azure.com, shell.azure.com, Visual Studio Code rozšíření účtu Azure a docs.microsoft.com.
- **Řešení**: Omezte přístup k `ux.console.azure.com` prostřednictvím nastavení sítě do vašeho prostředí. Ikona Cloud Shell bude i nadále existovat v portal.azure.com, ale nebude se k této službě úspěšně připojovat.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Dialog úložiště – chyba: 403 RequestDisallowedByPolicy

- **Podrobnosti**: při vytváření účtu úložiště prostřednictvím Cloud Shell neproběhne úspěšně kvůli zásadám Azure, které ukládá váš správce. Chybová zpráva bude zahrnovat: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Řešení**: obraťte se na správce Azure a odeberte nebo aktualizujte zásady Azure, které zakazují vytváření úložiště.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Dialog úložiště – chyba: 400 DisallowedOperation

- **Podrobnosti**: při použití předplatného Azure Active Directory nemůžete vytvořit úložiště.
- **Řešení**: použijte předplatné Azure, které podporuje vytváření prostředků úložiště. Předplatná Azure AD nemůžou vytvářet prostředky Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Výstup terminálu – Chyba: nepovedlo se připojit terminál: WebSocket se nedá navázat. Znovu se připojíte stisknutím `Enter`.
- **Podrobnosti**: Cloud Shell vyžaduje, aby bylo možné navázat připojení protokolu WebSocket k Cloud Shell infrastruktuře.
- **Řešení**: Ověřte, že jste nakonfigurovali nastavení sítě tak, aby umožňovalo posílání požadavků https a požadavků protokolu WebSocket do domén v umístění *. Console.Azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Nastavení Cloud Shell připojení pro podporu pomocí protokolu TLS 1,2
 - **Podrobnosti**: Chcete-li definovat verzi TLS pro připojení k Cloud Shell, je nutné nastavit nastavení specifické pro prohlížeč.
 - **Řešení**: přejděte do nastavení zabezpečení v prohlížeči a zaškrtněte políčko použít TLS 1,2.

## <a name="bash-troubleshooting"></a>Řešení potíží s bash

### <a name="cannot-run-the-docker-daemon"></a>Nelze spustit démona Docker

- **Podrobnosti**: Cloud Shell využívá kontejner pro hostování prostředí prostředí, protože spuštění démona není povoleno.
- **Řešení**: Využijte [Docker-Machine](https://docs.docker.com/machine/overview/), který je ve výchozím nastavení nainstalován, ke správě kontejnerů Docker ze vzdáleného hostitele Docker.

## <a name="powershell-troubleshooting"></a>Řešení potíží s PowerShellem

### <a name="gui-applications-are-not-supported"></a>Aplikace GUI nejsou podporované.

- **Podrobnosti**: Pokud uživatel spustí aplikaci grafického uživatelského rozhraní, výzva se nevrátí. Například když jeden klonuje soukromé úložiště GitHub s povoleným dvěma faktorovým ověřováním, zobrazí se dialogové okno pro dokončení ověření dvou faktorů.
- **Řešení**: zavřete a znovu otevřete prostředí.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Řešení potíží se vzdálenou správou virtuálních počítačů Azure
> [!NOTE]
> Virtuální počítače Azure musí mít veřejnou IP adresu.

- **Podrobnosti**: vzhledem k výchozímu nastavení brány Windows Firewall pro WinRM se uživateli může zobrazit následující chyba: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Řešení**: spuštěním `Enable-AzVMPSRemoting` povolíte všechny aspekty vzdálené komunikace prostředí PowerShell v cílovém počítači.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>@no__t – 0 neaktualizuje výsledek v jednotce Azure.

- **Podrobnosti**: ve výchozím nastavení optimalizuje činnost koncového uživatele tak, aby se výsledky `dir` ukládaly do mezipaměti v jednotce Azure.
- **Řešení**: po vytvoření, aktualizaci nebo odebrání prostředku Azure spusťte `dir -force` a aktualizujte výsledky na jednotce Azure.

## <a name="general-limitations"></a>Obecná omezení

Azure Cloud Shell má tato známá omezení:

### <a name="system-state-and-persistence"></a>Stav systému a trvalost

Počítač, který poskytuje vaši relaci Cloud Shell, je dočasný a bude recyklován po neaktivním spuštění relace po dobu 20 minut. Cloud Shell vyžaduje, aby byla připojena sdílená složka Azure. V důsledku toho musí být vaše předplatné schopné nastavit prostředky úložiště pro přístup k Cloud Shell. Mezi další okolnosti patří:

- V případě připojeného úložiště jsou trvalé pouze změny v adresáři `clouddrive`. V bash je váš adresář `$HOME` také trvale uložený.
- Sdílené složky Azure je možné připojit pouze v rámci [přiřazené oblasti](persisting-shell-storage.md#mount-a-new-clouddrive).
  - V bash spusťte `env`, abyste našli oblast nastavenou jako `ACC_LOCATION`.
- Soubory Azure podporují jenom místně redundantní úložiště a geograficky redundantní účty úložiště.

### <a name="browser-support"></a>Podpora prohlížeče

Cloud Shell podporuje nejnovější verze následujících prohlížečů:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Prohlížeč Safari v privátním režimu není podporován.

### <a name="copy-and-paste"></a>Kopírovat a vložit

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Omezení využití

Cloud Shell je určena pro interaktivní případy použití. V důsledku toho jsou všechny dlouho běžící neinteraktivní relace ukončeny bez upozornění.

### <a name="user-permissions"></a>Oprávnění uživatele

Oprávnění se nastaví jako běžné uživatele bez přístupu k sudo. Žádná instalace mimo váš `$Home` adresář není trvalá.

## <a name="bash-limitations"></a>Omezení bash

### <a name="editing-bashrc"></a>Editing. bashrc

Při úpravách. bashrc buďte opatrní. v takovém případě může dojít k neočekávaným chybám v Cloud Shell.

## <a name="powershell-limitations"></a>Omezení prostředí PowerShell

### <a name="preview-version-of-azuread-module"></a>Verze Preview modulu AzureAD

V současné době je k dispozici `AzureAD.Standard.Preview` verze Preview modulu založeného na .NET Standard. Tento modul nabízí stejné funkce jako `AzureAD`.

### <a name="sqlserver-module-functionality"></a>funkce modulu `SqlServer`

Modul `SqlServer` zahrnutý v Cloud Shell obsahuje jenom předprodejní podporu pro PowerShell Core. Konkrétně `Invoke-SqlCmd` zatím není k dispozici.

### <a name="default-file-location-when-created-from-azure-drive"></a>Výchozí umístění souboru při vytvoření z jednotky Azure

Pomocí rutin PowerShellu uživatelé nemůžou vytvářet soubory v rámci jednotky Azure. Když uživatelé vytvoří nové soubory pomocí jiných nástrojů, jako je například vim nebo nano, soubory se ve výchozím nastavení uloží do `$HOME`.

### <a name="tab-completion-can-throw-psreadline-exception"></a>Doplňování tabulátoru může vyvolat výjimku PSReadline.

Pokud je PSReadline EditMode uživatele nastaven na hodnotu (Emacs), uživatel se pokusí zobrazit všechny možnosti prostřednictvím záložky doplňování a velikost okna je příliš malá, aby zobrazila všechny možnosti. PSReadline vyvolá neošetřenou výjimku.

### <a name="large-gap-after-displaying-progress-bar"></a>Velká mezera po zobrazení indikátoru průběhu

Pokud se na základě příkazu nebo uživatele zobrazí indikátor průběhu, na kterém se dokončí, zatímco na jednotce `Azure:`, je možné, že kurzor není správně nastavený a mezera se zobrazí tam, kde byl indikátor průběhu dříve.

### <a name="random-characters-appear-inline"></a>Náhodné znaky se zobrazí jako vložené

Kódy sekvence pozice kurzoru, například `5;13R`, se mohou objevit ve vstupu uživatele. Znaky lze ručně odebrat.

## <a name="personal-data-in-cloud-shell"></a>Osobní údaje v Cloud Shell

Azure Cloud Shell převezme vaše osobní údaje, budou se data zachycená a uložená službou Azure Cloud Shell používat k poskytování výchozích hodnot pro vaše prostředí, jako je například vaše naposledy používané prostředí, upřednostňovaná velikost písma, preferovaný typ písma a podrobnosti sdílení souborů. záložní jednotka cloudu. Pokud chcete tato data exportovat nebo odstranit, postupujte podle následujících pokynů.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Export
Chcete-li **exportovat** nastavení uživatele Cloud Shell Uložit pro vás, jako je preferované prostředí, velikost písma a typ písma, spusťte následující příkazy.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Spustit Azure Cloud Shell")](https://shell.azure.com)
2. Spusťte následující příkazy v bash nebo PowerShellu:

Bash

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

Prostředí

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Odstranit
Chcete-li **Odstranit** nastavení uživatele Cloud Shell uloží, například upřednostňované prostředí, velikost písma a typ písma, spusťte následující příkazy. Při příštím spuštění Cloud Shell budete požádáni o zaregistrování sdílené složky. 

>[!Note]
> Pokud odstraníte nastavení uživatele, skutečná sdílená složka souborů Azure se neodstraní. Tuto akci dokončete tak, že přejdete do svých souborů Azure.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Spustit Azure Cloud Shell")](https://shell.azure.com)
2. Spusťte následující příkazy v bash nebo PowerShellu:

Bash

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

Prostředí

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Omezení Azure Government
Azure Cloud Shell v Azure Government jsou přístupné jenom prostřednictvím Azure Portal.
