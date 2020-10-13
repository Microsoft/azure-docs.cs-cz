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
ms.openlocfilehash: eea64520dd5440467c911b6de42d8c8c31fc1bde
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87543448"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Řešení potíží s omezeními & Azure Cloud Shell

Známá řešení potíží s Azure Cloud Shell zahrnují:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Obecné řešení potíží

### <a name="error-running-azuread-cmdlets-in-powershell"></a>Chyba při spouštění rutin AzureAD v PowerShellu

- **Podrobnosti**: když spustíte rutiny AzureAD, jako `Get-AzureADUser` v Cloud Shell, může se zobrazit chyba: `You must call the Connect-AzureAD cmdlet before calling any other cmdlets` . 
- **Řešení**: Spusťte `Connect-AzureAD` rutinu. Dřív Cloud Shell spustila tuto rutinu automaticky během spouštění PowerShellu. Pokud chcete zrychlit čas spuštění, rutina se už nespustí automaticky. Předchozí chování můžete obnovit tak, že do `Connect-AzureAD` souboru $Profile přidáte v prostředí PowerShell.

### <a name="early-timeouts-in-firefox"></a>Včasné vypršení časových limitů v prohlížeči FireFox

- **Podrobnosti**: Cloud Shell využívá otevřený WebSocket k předání vstupu a výstupu do prohlížeče. FireFox má přednastavené zásady, které můžou zavřít WebSocket předčasněcí způsobující počáteční vypršení časových limitů v Cloud Shell.
- **Řešení**: Otevřete Firefox a v poli Adresa URL přejděte do části about: config. Vyhledejte "Network. WebSocket. Timeout. otestuje. Request" a změňte hodnotu z 0 na 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Zakázání Cloud Shell v uzamčeném síťovém prostředí

- **Podrobnosti**: Správci můžou chtít zakázat přístup k Cloud Shell pro své uživatele. Cloud Shell využívá přístup k `ux.console.azure.com` doméně, která může být zamítnutá, zastavuje přístup k přístavám Cloud Shell, včetně Portal.Azure.com, Shell.Azure.com, Visual Studio Codeho rozšíření účtu Azure a docs.Microsoft.com. V cloudu pro státní správu USA je parametr entryPoint `ux.console.azure.us` ; neexistuje žádný odpovídající Shell.Azure.us.
- **Řešení**: Omezte přístup k `ux.console.azure.com` `ux.console.azure.us` nastavení sítě nebo prostřednictvím něj do vašeho prostředí. Ikona Cloud Shell bude stále existovat v Azure Portal, ale nebude se k ní úspěšně připojovat.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Dialog úložiště – chyba: 403 RequestDisallowedByPolicy

- **Podrobnosti**: při vytváření účtu úložiště prostřednictvím Cloud Shell neproběhne úspěšně z důvodu přiřazení Azure Policy, které ukládá správce. Chybová zpráva bude zahrnovat: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Řešení**: obraťte se na správce Azure a odeberte nebo aktualizujte přiřazení Azure Policy, které zakazuje vytváření úložiště.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Dialog úložiště – chyba: 400 DisallowedOperation

- **Podrobnosti**: při použití předplatného Azure Active Directory nemůžete vytvořit úložiště.
- **Řešení**: použijte předplatné Azure, které podporuje vytváření prostředků úložiště. Předplatná Azure AD nemůžou vytvářet prostředky Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Výstup terminálu – Chyba: nepovedlo se připojit terminál: WebSocket se nedá navázat. `Enter`Znovu se připojte stisknutím.
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
- **Řešení**: Spusťte `Enable-AzVMPSRemoting` , chcete-li povolit všechny aspekty vzdálené komunikace prostředí PowerShell v cílovém počítači.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` neaktualizuje výsledek na jednotce Azure.

- **Podrobnosti**: ve výchozím nastavení se optimalizuje činnost koncového uživatele a výsledky `dir` se ukládají do mezipaměti v jednotce Azure.
- **Řešení**: po vytvoření, aktualizaci nebo odebrání prostředku Azure spusťte příkaz `dir -force` a aktualizujte výsledky na jednotce Azure.

## <a name="general-limitations"></a>Obecná omezení

Azure Cloud Shell má tato známá omezení:

### <a name="quota-limitations"></a>Omezení kvóty

Azure Cloud Shell má limit 20 souběžných uživatelů na jeden tenant a oblast. Pokud se pokusíte otevřít víc souběžných relací, než je limit, zobrazí se chyba "uživatel klienta nad kvótou". Pokud máte legitimní potřebu mít otevřeno více relací, než je to (například pro školicí cvičení), obraťte se na podporu předem s předpokládaným využitím a vyžádejte si zvýšení kvóty.

Cloud Shell poskytujeme jako bezplatnou službu a je navržená tak, aby se dala použít ke konfiguraci prostředí Azure, ne jako výpočetní platformy pro obecné účely. Nadměrné automatizované použití se může považovat za porušení podmínek služby Azure a může vést k zablokování Cloud Shellho přístupu.

### <a name="system-state-and-persistence"></a>Stav systému a trvalost

Počítač, který poskytuje vaši relaci Cloud Shell, je dočasný a bude recyklován po neaktivním spuštění relace po dobu 20 minut. Cloud Shell vyžaduje, aby byla připojena sdílená složka Azure. V důsledku toho musí být vaše předplatné schopné nastavit prostředky úložiště pro přístup k Cloud Shell. Mezi další okolnosti patří:

- V případě připojeného úložiště jsou trvalé pouze změny v `clouddrive` adresáři. V bash je váš `$HOME` adresář také trvale uložený.
- Sdílené složky Azure je možné připojit pouze v rámci [přiřazené oblasti](persisting-shell-storage.md#mount-a-new-clouddrive).
  - V bash spusťte příkaz `env` a vyhledejte svou sadu oblastí jako `ACC_LOCATION` .
- Soubory Azure podporují jenom místně redundantní úložiště a geograficky redundantní účty úložiště.

### <a name="browser-support"></a>Podpora prohlížečů

Cloud Shell podporuje nejnovější verze následujících prohlížečů:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Prohlížeč Safari v privátním režimu není podporován.

### <a name="copy-and-paste"></a>Kopírování a vkládání

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Limity využití

Cloud Shell je určena pro interaktivní případy použití. V důsledku toho jsou všechny dlouho běžící neinteraktivní relace ukončeny bez upozornění.

### <a name="user-permissions"></a>Uživatelská oprávnění

Oprávnění se nastaví jako běžné uživatele bez přístupu k sudo. Žádná instalace mimo váš `$Home` adresář není trvalá.

## <a name="bash-limitations"></a>Omezení bash

### <a name="editing-bashrc"></a>Editing. bashrc

Při úpravách. bashrc buďte opatrní. v takovém případě může dojít k neočekávaným chybám v Cloud Shell.

## <a name="powershell-limitations"></a>Omezení prostředí PowerShell

### <a name="preview-version-of-azuread-module"></a>Verze Preview modulu AzureAD

V současné době `AzureAD.Standard.Preview` je k dispozici verze Preview modulu založeného na .NET Standard. Tento modul nabízí stejné funkce jako `AzureAD` .

## <a name="personal-data-in-cloud-shell"></a>Osobní údaje v Cloud Shell

Azure Cloud Shell převezme vaše osobní údaje, budou se data zachycená a uložená službou Azure Cloud Shell používat k poskytování výchozích hodnot pro vaše prostředí, jako je například vaše naposledy používané prostředí, upřednostňovaná velikost písma, preferovaný typ písma a podrobnosti o sdílené složce, které back-ROM Cloud Drive používá. Pokud chcete tato data exportovat nebo odstranit, postupujte podle následujících pokynů.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Export
Chcete-li **exportovat** nastavení uživatele Cloud Shell Uložit pro vás, jako je preferované prostředí, velikost písma a typ písma, spusťte následující příkazy.

1. [![Obrázek znázorňující tlačítko s názvem spuštění Azure Cloud Shell.](https://shell.azure.com/images/launchcloudshell.png)](https://shell.azure.com)

2. Spusťte následující příkazy v bash nebo PowerShellu:

Bash

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Odstranit
Chcete-li **Odstranit** nastavení uživatele Cloud Shell uloží, například upřednostňované prostředí, velikost písma a typ písma, spusťte následující příkazy. Při příštím spuštění Cloud Shell budete požádáni o zaregistrování sdílené složky. 

>[!Note]
> Pokud odstraníte nastavení uživatele, skutečná sdílená složka souborů Azure se neodstraní. Tuto akci dokončete tak, že přejdete do svých souborů Azure.

1. [![Obrázek znázorňující tlačítko s názvem spuštění Azure Cloud Shell.](https://shell.azure.com/images/launchcloudshell.png)](https://shell.azure.com)

2. Spusťte následující příkazy v bash nebo PowerShellu:

Bash

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Omezení Azure Government
Azure Cloud Shell v Azure Government jsou přístupné jenom prostřednictvím Azure Portal.

>[!Note]
> Připojení k GCC-High nebo cloudu DoD pro Exchange Online není v současné době podporováno.
