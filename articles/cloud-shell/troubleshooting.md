---
title: Řešení potíží s Azure Cloud Shell | Dokumenty společnosti Microsoft
description: Řešení potíží s cloudovým prostředím Azure
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
ms.openlocfilehash: 0b1b22095c77344ed71762d3d51b12f19d9f1811
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458048"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Řešení potíží & omezení Azure Cloud Shellu

Mezi známá řešení problémů s problémy v Azure Cloud Shellu patří:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Obecné řešení potíží

### <a name="error-running-azuread-cmdlets-in-powershell"></a>Při spuštění rutin AzureAD v PowerShellu došlo k chybě.

- **Podrobnosti:** Při spuštění rutin AzureAD `Get-AzureADUser` jako v Cloud Shellu `You must call the Connect-AzureAD cmdlet before calling any other cmdlets`se může zobrazit chyba: . 
- **Rozlišení**: `Connect-AzureAD` Spusťte rutinu. Dříve cloudové prostředí spustilo tuto rutinu automaticky během spuštění prostředí PowerShell. Chcete-li urychlit čas spuštění, rutina již běží automaticky. Předchozí chování můžete obnovit přidáním `Connect-AzureAD` do souboru $PROFILE v prostředí PowerShell.

### <a name="early-timeouts-in-firefox"></a>Časné časové osáty ve FireFoxu

- **Podrobnosti:** Cloud Shell využívá otevřený websocket předat vstup / výstup do vašeho prohlížeče. FireFox má přednastavené zásady, které mohou předčasně zavřít websocket způsobuje časné časové toky v prostředí Cloud Shell.
- **Rozlišení**: Open FireFox a přejděte na "about:config" v poli URL. Vyhledejte "network.websocket.timeout.ping.request" a změňte hodnotu z 0 na 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Zakázání prostředí Cloud Shell v uzavřeném síťovém prostředí

- **Podrobnosti**: Správci mohou chtít zakázat přístup ke cloudovému prostředí pro své uživatele. Cloud Shell využívá přístup `ux.console.azure.com` k doméně, která může být odepřena, zastavení přístupu k vstupníbody Cloud Shell, včetně portal.azure.com, shell.azure.com, Visual Studio Code Rozšíření účtu Azure a docs.microsoft.com. V cloudu vlády USA `ux.console.azure.us`je vstupní bod ; neexistuje žádná odpovídající shell.azure.us.
- **Řešení**: Omezte přístup k `ux.console.azure.com` nastavení sítě nebo `ux.console.azure.us` jeho prostřednictvím do vašeho prostředí. Ikona cloudového prostředí bude na webu Azure Portal stále existovat, ale ke službě se úspěšně nepřipojí.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Dialogové okno úložiště – chyba: 403 RequestDisallowedByPolicy

- **Podrobnosti:** Při vytváření účtu úložiště prostřednictvím Cloud Shellu je neúspěšné z důvodu zásad Azure, které umístil váš správce. Chybová zpráva bude obsahovat:`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Řešení:** Požádejte správce Azure o odebrání nebo aktualizaci zásad Azure, které zapínají vytváření úložiště.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Dialogové okno úložiště – chyba: 400 nepovolenýchoperace

- **Podrobnosti:** Při použití předplatného služby Azure Active Directory nelze vytvořit úložiště.
- **Řešení:** Použijte předplatné Azure schopné vytvářet prostředky úložiště. Předplatná Azure AD nelze vytvořit prostředky Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Výstup terminálu - Chyba: Připojení terminálu se nezdařilo: websocket nelze vytvořit. Stisknutím `Enter` tlačítka se znovu připojíte.
- **Podrobnosti:** Cloud Shell vyžaduje možnost navázat připojení websocket k infrastruktuře Cloud Shell.
- **Řešení**: Zkontrolujte, zda jste nakonfigurovali nastavení sítě, abyste povolili odesílání požadavků https a požadavků na websocket do domén na adrese *.console.azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Nastavení připojení ke cloudovému prostředí pro podporu pomocí TLS 1.2
 - **Podrobnosti**: Chcete-li definovat verzi protokolu TLS pro připojení ke službě Cloud Shell, musíte nastavit nastavení specifická pro prohlížeč.
 - **Řešení**: Přejděte do nastavení zabezpečení prohlížeče a zaškrtněte políčko vedle možnosti Použít TLS 1.2.

## <a name="bash-troubleshooting"></a>Bash řešení problémů

### <a name="cannot-run-the-docker-daemon"></a>Daemon dockeru nelze spustit.

- **Podrobnosti:** Cloud Shell využívá kontejner pro hostování prostředí prostředí, v důsledku toho spuštění daemon je zakázáno.
- **Řešení**: Využijte [docker-machine](https://docs.docker.com/machine/overview/), který je nainstalován ve výchozím nastavení, ke správě kontejnerů dockeru ze vzdáleného hostitele Dockeru.

## <a name="powershell-troubleshooting"></a>Řešení potíží s Prostředím PowerShell

### <a name="gui-applications-are-not-supported"></a>Grafické uživatelské rozhraní nejsou podporovány.

- **Podrobnosti**: Pokud uživatel spustí gui aplikace, výzva nevrátí. Například když jeden klonovat soukromé úložiště GitHub, který má dva faktorové ověřování povoleno, zobrazí se dialogové okno pro dokončení dvoufaktorové ověřování.
- **Rozlišení**: Zavřete a znovu otevřete prostředí.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Řešení potíží se vzdálenou správou virtuálních počítačů Azure
> [!NOTE]
> Virtuální počítače Azure musí mít veřejnou ip adresu.

- **Podrobnosti**: Vzhledem k výchozímu nastavení brány Windows Firewall pro službu WinRM může uživateli zobrazit následující chybu:`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Řešení:** `Enable-AzVMPSRemoting` Spuštěním povolíte všechny aspekty vzdálené komunikace prostředí PowerShell na cílovém počítači.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir`neaktualizuje výsledek v azure disku

- **Podrobnosti:** Ve výchozím nastavení, chcete-li `dir` optimalizovat pro uživatelské prostředí, výsledky je uložena v mezipaměti na disku Azure.
- **Řešení**: Po vytvoření, aktualizaci nebo `dir -force` odebrání prostředku Azure spusťte a aktualizujte výsledky na disku Azure.

## <a name="general-limitations"></a>Obecná omezení

Azure Cloud Shell má následující známá omezení:

### <a name="quota-limitations"></a>Omezení kvót

Azure Cloud Shell má limit 20 souběžných uživatelů na klienta na oblast. Pokud se pokusíte otevřít více souběžných relací, než je limit, zobrazí se chyba "Klient uživatel nad kvótou". Pokud máte legitimní potřebu mít více otevřených relací (například pro školení), obraťte se na podporu před očekávaným použitím a požádejte o zvýšení kvóty.

Cloud Shell je poskytován jako bezplatná služba a je navržen tak, aby se používal ke konfiguraci prostředí Azure, nikoli jako výpočetní platforma pro obecné účely. Nadměrné automatizované využití může být považováno za porušení smluvních podmínek Azure a může vést k zablokování přístupu ke cloudovému prostředí.

### <a name="system-state-and-persistence"></a>Stav a trvalost systému

Počítač, který poskytuje relaci prostředí Cloud Shell je dočasný a je recyklován poté, co vaše relace je neaktivní po dobu 20 minut. Cloud Shell vyžaduje připojení sdílené složky Azure. V důsledku toho musí být vaše předplatné schopno nastavit prostředky úložiště pro přístup ke Cloud Shellu. Mezi další aspekty patří:

- U připojeného úložiště jsou `clouddrive` trvalé pouze změny v adresáři. V Bash, `$HOME` váš adresář je také trvalé.
- Sdílené složky Azure lze připojit pouze z [přiřazené oblasti](persisting-shell-storage.md#mount-a-new-clouddrive).
  - V Bash, `env` spusťte najít `ACC_LOCATION`oblast nastavena jako .
- Azure Files podporuje jenom místně redundantní úložiště a geograficky redundantní účty úložiště.

### <a name="browser-support"></a>Podpora prohlížečů

Cloud Shell podporuje nejnovější verze následujících prohlížečů:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Safari v soukromém režimu není podporováno.

### <a name="copy-and-paste"></a>Kopírování a vkládání

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Limity použití

Cloud Shell je určen pro interaktivní případy použití. V důsledku toho jsou všechny dlouhotrvající neinteraktivní relace ukončeny bez upozornění.

### <a name="user-permissions"></a>Uživatelská oprávnění

Oprávnění jsou nastavena jako běžní uživatelé bez přístupu sudo. Žádná instalace `$Home` mimo adresář není trvalá.

## <a name="bash-limitations"></a>Bash omezení

### <a name="editing-bashrc"></a>Úpravy .bashrc

Při úpravách .bashrc buďte opatrní, což může způsobit neočekávané chyby v prostředí Cloud Shell.

## <a name="powershell-limitations"></a>Omezení prostředí PowerShell

### <a name="preview-version-of-azuread-module"></a>Náhled verze modulu AzureAD

V současné `AzureAD.Standard.Preview`době je k dispozici verze náhledu modulu .NET Standard. Tento modul poskytuje stejné `AzureAD`funkce jako .

### <a name="sqlserver-module-functionality"></a>`SqlServer`funkce modulu

Modul, `SqlServer` který je součástí cloudového prostředí, má pouze předběžnou podporu pro PowerShell Core. Zejména zatím `Invoke-SqlCmd` není k dispozici.

### <a name="default-file-location-when-created-from-azure-drive"></a>Výchozí umístění souboru při vytvoření z disku Azure

Pomocí rutin prostředí PowerShell uživatelé nemohou vytvářet soubory pod jednotkou Azure. Když uživatelé vytvářejí nové soubory pomocí jiných nástrojů, jako je vim nebo nano, soubory jsou uloženy do výchozího `$HOME` nastavení.

### <a name="tab-completion-can-throw-psreadline-exception"></a>Dokončení karty může vyvolat výjimku PSReadline

Pokud je soubor PSReadline EditMode uživatele nastaven na Emacs, uživatel se pokusí zobrazit všechny možnosti prostřednictvím dokončení karty a velikost okna je příliš malá pro zobrazení všech možností, PSReadline vyvolá neošetřenou výjimku.

### <a name="large-gap-after-displaying-progress-bar"></a>Velká mezera po zobrazení indikátoru průběhu

Pokud příkaz nebo akce uživatele zobrazí indikátor průběhu, například karta dokončení, zatímco v `Azure:` jednotce, pak je možné, že kurzor není správně nastavena a mezera se zobrazí, kde byl indikátor průběhu dříve.

### <a name="random-characters-appear-inline"></a>Náhodné znaky se zobrazí v řádkové

Například `5;13R`kódy pořadí pozice kurzoru se mohou zobrazit v uživatelském vstupu. Znaky lze ručně odebrat.

## <a name="personal-data-in-cloud-shell"></a>Osobní údaje v Cloud Shellu

Azure Cloud Shell bere vaše osobní data vážně, data zachycená a uložená službou Azure Cloud Shell se používají k poskytování výchozích hodnot pro vaše prostředí, jako je vaše naposledy použité prostředí, upřednostňovaná velikost písma, upřednostňovaný typ písma a podrobnosti o sdílení souborů že zpět cloud disk. Chcete-li tato data exportovat nebo odstranit, postupujte podle následujících pokynů.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Export
Chcete-li **exportovat** uživatelská nastavení, cloudové prostředí pro vás uloží, jako je upřednostňované prostředí, velikost písma a typ písma, spusťte následující příkazy.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Spusťte následující příkazy v Bash nebo PowerShellu:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Odstranění
Chcete-li **odstranit** uživatelská nastavení, cloudové prostředí pro vás uloží, jako je upřednostňované prostředí, velikost písma a typ písma, spusťte následující příkazy. Při příštím spuštění prostředí Cloud Shell budete znovu vyzváni ke sdílení souborů. 

>[!Note]
> Pokud odstraníte nastavení uživatele, skutečná sdílená složka Souborů Azure se neodstraní. Přejděte do souborů Azure a dokončete tuto akci.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Spusťte následující příkazy v Bash nebo PowerShellu:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Omezení azure governmentu
Azure Cloud Shell ve službě Azure Government je přístupný jenom přes portál Azure.

>[!Note]
> Připojení k GCC-High nebo Government DoD Clouds pro Exchange Online není momentálně podporováno.
