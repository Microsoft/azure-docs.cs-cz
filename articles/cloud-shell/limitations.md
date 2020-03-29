---
title: Omezení cloudového prostředí Azure | Dokumenty společnosti Microsoft
description: Přehled omezení Azure Cloud Shellu
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: damaerte
ms.openlocfilehash: 092dccab82326bb9983f11ff64fe50aee7b1084d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951475"
---
# <a name="limitations-of-azure-cloud-shell"></a>Omezení Azure Cloud Shellu

Azure Cloud Shell má následující známá omezení:

## <a name="general-limitations"></a>Obecná omezení

### <a name="system-state-and-persistence"></a>Stav a trvalost systému

Počítač, který poskytuje relaci prostředí Cloud Shell je dočasný a je recyklován poté, co vaše relace je neaktivní po dobu 20 minut. Cloud Shell vyžaduje připojení sdílené složky Azure. V důsledku toho musí být vaše předplatné schopno nastavit prostředky úložiště pro přístup ke Cloud Shellu. Mezi další aspekty patří:

* U připojeného úložiště jsou `$Home` trvalé pouze změny v adresáři.
* Sdílené složky Azure lze připojit pouze z [přiřazené oblasti](persisting-shell-storage.md#mount-a-new-clouddrive).
  * V Bash, `env` spusťte najít `ACC_LOCATION`oblast nastavena jako .

### <a name="browser-support"></a>Podpora prohlížečů

Cloud Shell podporuje nejnovější verze Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox a Apple Safari. Safari v soukromém režimu není podporováno.

### <a name="copy-and-paste"></a>Kopírování a vkládání

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Pro daného uživatele může být aktivní pouze jeden shell

Uživatelé mohou spustit pouze jeden typ prostředí najednou, buď **Bash** nebo **PowerShell**. Však může mít více instancí Bash nebo PowerShell spuštěna najednou. Prohození mezi Bash nebo PowerShell pomocí nabídky způsobí, že Cloud Shell restartovat, který ukončí existující relace. Případně můžete spustit bash uvnitř prostředí PowerShell zadáním `bash`a můžete spustit Prostředí `pwsh`PowerShell uvnitř bash zadáním .

### <a name="usage-limits"></a>Limity použití

Cloud Shell je určen pro interaktivní případy použití. V důsledku toho jsou všechny dlouhotrvající neinteraktivní relace ukončeny bez upozornění.

## <a name="bash-limitations"></a>Bash omezení

### <a name="user-permissions"></a>Uživatelská oprávnění

Oprávnění jsou nastavena jako běžní uživatelé bez přístupu sudo. Žádná instalace `$Home` mimo adresář není trvalá.

### <a name="editing-bashrc-or-profile"></a>Úpravy %.bashrc nebo $PROFILE

Při úpravách souboru $PROFILE .bashrc nebo PowerShellu buďte opatrní, což může způsobit neočekávané chyby v prostředí Cloud Shell.

## <a name="powershell-limitations"></a>Omezení prostředí PowerShell

### <a name="azuread-module-name"></a>`AzureAD`název modulu

Název `AzureAD` modulu je `AzureAD.Standard.Preview`aktuálně , modul poskytuje stejné funkce.

### <a name="sqlserver-module-functionality"></a>`SqlServer`funkce modulu

Modul, `SqlServer` který je součástí cloudového prostředí, má pouze předběžnou podporu pro PowerShell Core. Zejména zatím `Invoke-SqlCmd` není k dispozici.

### <a name="default-file-location-when-created-from-azure-drive"></a>Výchozí umístění souboru při vytvoření z disku Azure:

Pomocí rutin prostředí PowerShell uživatelé nemohou vytvářet soubory pod jednotkou Azure: . Když uživatelé vytvářejí nové soubory pomocí jiných nástrojů, jako je vim nebo nano, soubory jsou uloženy do výchozího `$HOME` nastavení. 

### <a name="gui-applications-are-not-supported"></a>Grafické uživatelské rozhraní nejsou podporovány.

Pokud uživatel spustí příkaz, který by vytvořil dialogové okno systému Windows, zobrazí se chybová zpráva, například: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="large-gap-after-displaying-progress-bar"></a>Velká mezera po zobrazení indikátoru průběhu

Pokud uživatel provede akci, která zobrazuje indikátor průběhu, například `Azure:` kartu dokončení v jednotce, pak je možné, že kurzor není správně nastavena a mezera se zobrazí, kde indikátor průběhu byl dříve.

## <a name="next-steps"></a>Další kroky

[Řešení potíží s prostředím Cloud Shell](troubleshooting.md) <br>
[Rychlý start pro Bash](quickstart.md) <br>
[Rychlý start pro PowerShell](quickstart-powershell.md)
