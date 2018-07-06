---
title: Řešení potíží Azure Cloud Shell | Dokumentace Microsoftu
description: Řešení potíží s Azure Cloud Shell.
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
ms.date: 07/03/2018
ms.author: damaerte
ms.openlocfilehash: 21bc0633a9cc607325b48998791cb12631ecd0d7
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856483"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Řešení potíží s & omezení Azure Cloud Shell

Známá řešení pro řešení potíží ve službě Azure Cloud Shell patří:

## <a name="general-troubleshooting"></a>Obecné řešení potíží

### <a name="early-timeouts-in-firefox"></a>Časná vypršení časových limitů ve Firefoxu
- **Podrobnosti o**: využívá Cloud Shell otevřené protokolu websocket předat vstup/výstup do prohlížeče. FireFox má předvolby zásad, které můžete zavřít objekt websocket předčasně způsobí vypršení časového limitu pro dřívější ve službě Cloud Shell.
- **Rozlišení**: Otevřete FireFox a přejděte na "o: konfigurace" v poli Adresa URL. Vyhledejte "network.websocket.timeout.ping.request" a změňte hodnotu od 0 do 10.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Dialogové okno úložiště – Chyba: 403 RequestDisallowedByPolicy
- **Podrobnosti o**: při vytváření účtu úložiště Cloud Shell neúspěšný kvůli zásady služby Azure umístěn váš správce. Chybová zpráva bude obsahovat: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Rozlišení**: Obraťte se na správce odstranit nebo aktualizovat Azure policy odepření vytvoření úložiště Azure.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Dialogové okno úložiště – Chyba: 400 DisallowedOperation
 - **Podrobnosti o**: Pokud používají předplatné Azure Active Directory, nelze vytvořit úložiště.
 - **Rozlišení**: použít předplatné Azure dokáže vytvořit prostředky úložiště. Předplatná Azure AD, nebudou se moct vytváření prostředků Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminál výstup – Chyba: nepovedlo se připojit terminál: protokolu websocket nelze navázat. Stisknutím klávesy `Enter` znovu připojit.
 - **Podrobnosti o**: službě Cloud Shell vyžaduje možnost připojení pomocí protokolu websocket k službě Cloud Shell infrastruktury.
 - **Rozlišení**: Zkontrolujte jste nakonfigurovali nastavení sítě, abyste povolili odeslání žádosti o https a požadavky protokolu websocket na domén na *. console.azure.com.

## <a name="bash-troubleshooting"></a>Řešení potíží s bash

### <a name="cannot-run-the-docker-daemon"></a>Nelze spustit démona dockeru

- **Podrobnosti o**: využívá kontejner pro hostování vašeho prostředí Cloud Shell, v důsledku spuštění démona se nepovoluje.
- **Rozlišení**: využívat [docker-machine](https://docs.docker.com/machine/overview/), která se instaluje standardně Spravovat kontejnery dockeru ze vzdáleného hostitele Docker.

## <a name="powershell-troubleshooting"></a>Řešení potíží s Powershellu

### <a name="gui-applications-are-not-supported"></a>Aplikace grafického uživatelského rozhraní se nepodporují.

- **Podrobnosti o**: Pokud uživatel spustí aplikaci s grafickým uživatelským rozhraním, řádku tento příkaz nevrací. Například když uživatel duplicity privátní úložiště GitHub, který je dvoufaktorové ověřování povoleno, se zobrazí dialogové okno pro dokončení dvoufaktorového ověřování.  
- **Rozlišení**: zavřete a znovu otevřete prostředí.

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help - online neotevřeno stránky s nápovědou

- **Podrobnosti o**: Pokud uživatel zadá `Get-Help Find-Module -online`, jeden jako zobrazí chybová zpráva: `Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
- **Rozlišení**: Zkopírujte adresu url a ručně ho otevřete ve webovém prohlížeči.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Řešení potíží s Vzdálená správa virtuálních počítačů Azure

- **Podrobnosti o**: z důvodu výchozí nastavení brány Windows Firewall pro WinRM může uživatel zobrazit následující chyba: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Rozlišení**: Spusťte `Enable-AzureRmVMPSRemoting` povolit všechny aspekty vzdálené komunikace Powershellu na cílovém počítači.
 

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir` ukládá do mezipaměti výsledek v Azure disk

- **Podrobnosti o**: výsledek `dir` se uloží do mezipaměti v Azure disk.
- **Rozlišení**: Po vytvoření nebo odebrání prostředku v zobrazení jednotka Azure, spusťte `dir -force` aktualizovat.

## <a name="general-limitations"></a>Obecná omezení
Azure Cloud Shell má tato známá omezení:

### <a name="system-state-and-persistence"></a>Stav systému a trvalosti

Počítač, který poskytuje relace prostředí Cloud Shell je dočasný a bude recyklována po relaci je neaktivní po dobu 20 minut. Cloud Shell vyžaduje sdílenou složku Azure připojit. Předplatné musí být v důsledku toho nám nastavit prostředky úložiště pro přístup k službě Cloud Shell. Mezi další aspekty patří:

* Připojené úložiště, pouze změny v rámci `clouddrive` adresáře jsou trvalé. V prostředí Bash vaše `$Home` adresáře je také zachována.
* Sdílené složky Azure je možné připojit pouze v rámci vaší [přiřazené oblasti](persisting-shell-storage.md#mount-a-new-clouddrive).
  * V prostředí Bash, spusťte `env` najít vaši oblast nastavit jako `ACC_LOCATION`.
* Služba soubory Azure podporuje jen místně redundantní úložiště a účtů geograficky redundantního úložiště.

### <a name="browser-support"></a>Podpora prohlížeče

Cloud Shell podporuje nejnovější verze Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox a Apple Safari. Safari v privátním režimu není podporováno.

### <a name="copy-and-paste"></a>Kopírování a vkládání

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Pro daného uživatele může být aktivní pouze jeden prostředí

Uživatelé mohou pouze spouštět jeden typ prostředí najednou, buď **Bash** nebo **Powershellu**. Však může mít více instancí spuštěných v jednom okamžiku Bashe nebo Powershellu. Prohození mezi Bashe nebo Powershellu způsobí, že službě Cloud Shell k restartování, které ukončuje existující relace.

### <a name="usage-limits"></a>Limity využití

Cloud Shell je určen pro případy použití interaktivní. V důsledku toho se dlouho probíhající relace jako neinteraktivní skončilo bez upozornění.

## <a name="bash-limitations"></a>Omezení bash

### <a name="user-permissions"></a>Uživatelská oprávnění

Oprávnění se nastavují jako běžní uživatelé bez přístupu sudo. Všechny instalace mimo váš `$Home` adresář není trvalý.

### <a name="editing-bashrc"></a>Úpravy .bashrc

Provést upozornění, když úpravy .bashrc, to může způsobit neočekávané chyby ve službě Cloud Shell.

## <a name="powershell-limitations"></a>Omezení prostředí PowerShell

### <a name="azuread-module-name"></a>`AzureAD` Název modulu

`AzureAD` Název modulu je aktuálně `AzureAD.Standard.Preview`, modul nabízí stejné funkce.

### <a name="sqlserver-module-functionality"></a>`SqlServer` modul funkce

`SqlServer` Ve službě Cloud Shell zahrnuje modul má pouze předprodejní podpora pro PowerShell Core. Zejména `Invoke-SqlCmd` ještě není k dispozici.

### <a name="default-file-location-when-created-from-azure-drive"></a>Výchozí umístění souboru při vytvoření z disku Azure:

Pomocí rutin prostředí PowerShell, uživatelé nemůžou vytvářet soubory v Azure disk. Když uživatelé vytvářejí nové soubory pomocí jiných nástrojů, jako je například vim nebo nano, soubory se uloží do `$HOME` ve výchozím nastavení. 

### <a name="gui-applications-are-not-supported"></a>Aplikace grafického uživatelského rozhraní se nepodporují.

Pokud uživatel spustí příkaz, který by vytvoření dialogového okna Windows, jako například `Connect-AzureAD` nebo `Connect-AzureRmAccount`, jeden jako zobrazí chybová zpráva: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="tab-completion-crashes-psreadline"></a>Dokončování pomocí tabulátoru PSReadline dojde k chybě.

Pokud uživatele EditMode v PSReadline je nastavena na (emacs), uživatel se pokusí zobrazit všechny možnosti prostřednictvím tab k dokončování příkazů a velikost okna je příliš malá, zobrazíte všechny možnosti, PSReadline dojde k chybě.

### <a name="large-gap-after-displaying-progress-bar"></a>Velké mezeru po zobrazuje indikátor průběhu

Pokud uživatel provede akci, která se zobrazí indikátor, tato karta při dokončení v `Azure:` jednotky, pak je možné, že kurzor nejsou správně nastaveny a mezera se zobrazí, pokud byla dříve indikátor průběhu.

### <a name="random-characters-appear-inline"></a>Zobrazí vložený náhodných znaků

Například sekvence pozice kurzoru kódů `5;13R`, se mohou objevit v vstupu uživatele.  Znaky lze ručně odebrat.

## <a name="personal-data-in-cloud-shell"></a>Osobní údaje ve službě Cloud Shell

Azure Cloud Shell používá vaše osobní údaje vážně, data, zachytí a uložené ve službě Azure Cloud Shell se používají k zajištění výchozí hodnoty pro vaše prostředí, jako je vaše naposledy použité prostředí, upřednostňované písmo, upřednostňované písmo a sdílených složek podrobnosti který zpět cloudové jednotky. Pokud, budete chtít exportovat nebo odstraňovat data, jsme přidali následující pokyny.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Export
Za účelem **exportovat** uživatelská nastavení uloží Cloud Shell za vás jako upřednostňované prostředí a velikost písma a typ písma, spusťte následující příkazy.

1. Spusťte prostředí Bash ve službě Cloud Shell
2. Spusťte následující příkazy:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
```

### <a name="delete"></a>Odstranění
Za účelem **odstranit** uživatelských nastavení uloží Cloud Shell za vás jako upřednostňované prostředí a velikost písma a typ písma, spusťte následující příkazy. Při příštím spuštění Cloud Shell zobrazí se výzva k připojení sdílené složky znovu. 

Skutečné soubory Azure, sdílené složky nebudou odstraněny, pokud odstraníte uživatelská nastavení, přejděte do služby soubory Azure k dokončení této akce.

1. Spusťte prostředí Bash ve službě Cloud Shell
2. Spusťte následující příkazy:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
```
