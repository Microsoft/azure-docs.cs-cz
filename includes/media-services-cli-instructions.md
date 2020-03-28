---
title: zahrnout soubor
description: zahrnout soubor
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 5ebbac39c8850737ea6f9ef333e45d305a520655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461210"
---
## <a name="use-cli-shell"></a>Použití prostředí CLI

Doporučujeme použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) ke spuštění příkazů příkazového příkazu. **Cloud Shell** je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku. Ve službě Cloud Shell jsou předinstalované obvyklé nástroje Azure a jsou nakonfigurované pro použití s vaším účtem. Poskytuje flexibilitu při výběru prostředí, které nejlépe vyhovuje vašemu stylu práce. Uživatelé Linuxu si můžou zvolit prostředí Bash, zatímco uživatelé Windows si můžou zvolit PowerShell.

Můžete také nainstalovat cli místně. Pokyny pro vaši [platformu najdete v tématu Instalace příkazového příkazového příkazu k Nastavení nastavení si Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli)

### <a name="sign-in"></a>Přihlášení

Použití místní instalace příkazového příkazového příkazu vyžaduje přihlášení do Azure. Tento krok není vyžadován pro Azure Cloud Shell. Přihlaste `az login` se pomocí příkazu.

Pokud rozhraní příkazového řádku může spustit výchozí prohlížeč, udělá to a načte přihlašovací stránku. V opačném případě je třeba otevřít stránku prohlížeče a podle pokynů na příkazovém řádku zadat autorizační kód po přechodu do https://aka.ms/devicelogin prohlížeče.

### <a name="specify-location-of-files"></a>Určení umístění souborů

Mnoho příkazů rozhraní příkazu CLI služby Media Services umožňuje předat parametr s názvem souboru. Pokud používáte **Cloud Shell**, můžete nahrát soubor na cloudDrive (pomocí Bash nebo PowerShell). 

![Nahrání souborů]

Ať už používáte místní CLI nebo **Cloud Shell**, musíte zadat cestu k souboru podle operačního systému nebo cloudového prostředí (Bash nebo PowerShell), které používáte. Níže jsou uvedeny některé příklady:

Relativní cesta k souboru (všechny operační systém)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Absolutní cesta k souboru na Linuxu/ Macu a Windows OS

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

Použijte, `{file}` pokud příkaz žádá o cestu k souboru. Například, `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Použijte, `@{file}` pokud se příkaz chystá načíst zadaný soubor. Například, `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Nahrání souborů]: ./media/media-services-cli/upload-download-files.png
