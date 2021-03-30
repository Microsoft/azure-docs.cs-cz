---
title: Omezení Azure Cloud Shell | Microsoft Docs
description: Přehled omezení Azure Cloud Shell
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "74951475"
---
# <a name="limitations-of-azure-cloud-shell"></a>Omezení Azure Cloud Shell

Azure Cloud Shell má tato známá omezení:

## <a name="general-limitations"></a>Obecná omezení

### <a name="system-state-and-persistence"></a>Stav systému a trvalost

Počítač, který poskytuje vaši relaci Cloud Shell, je dočasný a bude recyklován po neaktivním spuštění relace po dobu 20 minut. Cloud Shell vyžaduje, aby byla připojena sdílená složka Azure. V důsledku toho musí být vaše předplatné schopné nastavit prostředky úložiště pro přístup k Cloud Shell. Mezi další okolnosti patří:

* V případě připojeného úložiště jsou trvalé pouze změny v `$Home` adresáři.
* Sdílené složky Azure je možné připojit pouze v rámci [přiřazené oblasti](persisting-shell-storage.md#mount-a-new-clouddrive).
  * V bash spusťte příkaz `env` a vyhledejte svou sadu oblastí jako `ACC_LOCATION` .

### <a name="browser-support"></a>Podpora prohlížečů

Cloud Shell podporuje nejnovější verze Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox a Apple Safari. Prohlížeč Safari v privátním režimu není podporován.

### <a name="copy-and-paste"></a>Kopírování a vkládání

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Pro daného uživatele může být aktivní jenom jedno prostředí.

Uživatelé mohou spustit pouze jeden typ prostředí, buď **bash** nebo **PowerShell**. Může se ale stát, že v jednom okamžiku běží víc instancí bash nebo PowerShellu. Záměna mezi bash nebo PowerShellem pomocí nabídky způsobí, že se Cloud Shell restart, což ukončí existující relace. Alternativně můžete bash spustit v PowerShellu tak, že zadáte `bash` a můžete spustit PowerShell v bash zadáním `pwsh` .

### <a name="usage-limits"></a>Limity využití

Cloud Shell je určena pro interaktivní případy použití. V důsledku toho jsou všechny dlouho běžící neinteraktivní relace ukončeny bez upozornění.

## <a name="bash-limitations"></a>Omezení bash

### <a name="user-permissions"></a>Uživatelská oprávnění

Oprávnění se nastaví jako běžné uživatele bez přístupu k sudo. Žádná instalace mimo váš `$Home` adresář není trvalá.

### <a name="editing-bashrc-or-profile"></a>Editace. bashrc nebo $PROFILE

Při úpravách souboru $PROFILE prostředí PowerShell buďte opatrní. v takovém případě může dojít k neočekávaným chybám v Cloud Shell.

## <a name="powershell-limitations"></a>Omezení prostředí PowerShell

### <a name="azuread-module-name"></a>`AzureAD` název modulu

`AzureAD`Název modulu je v současné době `AzureAD.Standard.Preview` , modul nabízí stejné funkce.

### <a name="sqlserver-module-functionality"></a>`SqlServer` funkce modulu

`SqlServer`Modul zahrnutý v Cloud Shell má pouze předprodejní podporu pro PowerShell Core. Konkrétně není `Invoke-SqlCmd` zatím k dispozici.

### <a name="default-file-location-when-created-from-azure-drive"></a>Výchozí umístění souboru při vytvoření z jednotky Azure:

Pomocí rutin PowerShellu uživatelé nemůžou vytvářet soubory v rámci jednotky Azure:. Když uživatelé vytvoří nové soubory pomocí jiných nástrojů, jako je například vim nebo nano, soubory se `$HOME` ve výchozím nastavení uloží do. 

### <a name="gui-applications-are-not-supported"></a>Aplikace GUI nejsou podporované.

Pokud uživatel spustí příkaz, který by vytvořil dialogové okno Windows, zobrazí se jedna chybová zpráva, například: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)` .

### <a name="large-gap-after-displaying-progress-bar"></a>Velká mezera po zobrazení indikátoru průběhu

Pokud uživatel provede akci, která zobrazuje indikátor průběhu, jako je například karta dokončí v `Azure:` jednotce, pak je možné, že kurzor není nastaven správně a mezera se zobrazí tam, kde byl indikátor průběhu dříve.

## <a name="next-steps"></a>Další kroky

[Řešení potíží s Cloud Shell](troubleshooting.md) <br>
[Rychlý start pro Bash](quickstart.md) <br>
[Rychlý start pro PowerShell](quickstart-powershell.md)
