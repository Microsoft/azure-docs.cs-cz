---
title: Omezení služby Azure Cloud Shell | Dokumentace Microsoftu
description: Přehled omezení služby Azure Cloud Shell
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: juluk
ms.openlocfilehash: 135496e17ae884db580922aa31f6824b2e7fd934
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855980"
---
# <a name="limitations-of-azure-cloud-shell"></a>Omezení služby Azure Cloud Shell

Azure Cloud Shell má tato známá omezení:

## <a name="general-limitations"></a>Obecná omezení

### <a name="system-state-and-persistence"></a>Stav systému a trvalosti

Počítač, který poskytuje relace prostředí Cloud Shell je dočasný a bude recyklována po relaci je neaktivní po dobu 20 minut. Cloud Shell vyžaduje sdílenou složku Azure připojit. Předplatné musí být v důsledku toho nám nastavit prostředky úložiště pro přístup k službě Cloud Shell. Mezi další aspekty patří:

* Připojené úložiště, pouze změny v rámci `$Home` adresáře jsou trvalé.
* Sdílené složky Azure je možné připojit pouze v rámci vaší [přiřazené oblasti](persisting-shell-storage.md#mount-a-new-clouddrive).
  * V prostředí Bash, spusťte `env` najít vaši oblast nastavit jako `ACC_LOCATION`.

### <a name="browser-support"></a>Podpora prohlížeče

Cloud Shell podporuje nejnovější verze Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox a Apple Safari. Safari v privátním režimu není podporováno.

### <a name="copy-and-paste"></a>Kopírování a vkládání

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

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

## <a name="next-steps"></a>Další postup

[Řešení potíží s Cloud Shell](troubleshooting.md) <br>
[Rychlý start pro Bash](quickstart.md) <br>
[Rychlý start pro PowerShell](quickstart-powershell.md)
