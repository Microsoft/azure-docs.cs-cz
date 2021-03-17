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
ms.openlocfilehash: 1327ac49920af353d6adbbe592ab4189417e60e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996885"
---
## <a name="use-cli-shell"></a>Použití prostředí CLI

Pro spouštění příkazů rozhraní příkazového řádku se doporučuje použít [Azure Cloud Shell](../articles/cloud-shell/overview.md?view=azure-cli-latest) . **Cloud Shell** je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku. Ve službě Cloud Shell jsou předinstalované obvyklé nástroje Azure a jsou nakonfigurované pro použití s vaším účtem. Poskytuje flexibilitu při výběru prostředí, které nejlépe vyhovuje vašemu stylu práce. Uživatelé Linuxu si můžou zvolit prostředí Bash, zatímco uživatelé Windows si můžou zvolit PowerShell.

Rozhraní příkazového řádku můžete také nainstalovat místně. Pokyny pro vaši platformu najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli) .

### <a name="sign-in"></a>Přihlásit se

Použití místní instalace rozhraní příkazového řádku vyžaduje přihlášení k Azure. Tento krok není vyžadován pro Azure Cloud Shell. Přihlaste se pomocí `az login` příkazu.

Pokud rozhraní příkazového řádku může spustit výchozí prohlížeč, udělá to a načte přihlašovací stránku. V opačném případě je nutné otevřít stránku prohlížeče a podle pokynů v příkazovém řádku zadat autorizační kód po přechodu do https://aka.ms/devicelogin prohlížeče.

### <a name="specify-location-of-files"></a>Zadat umístění souborů

Řada příkazů rozhraní příkazového řádku (CLI) Media Services umožňuje předat parametr s názvem souboru. Pokud používáte **Cloud Shell**, můžete soubor nahrát do svého clouddriveu (pomocí bash nebo PowerShellu). 

![Nahrání souborů]

Bez ohledu na to, jestli používáte místní rozhraní příkazového řádku nebo **Cloud Shell**, musíte zadat cestu k souboru podle operačního systému nebo Cloud Shell (bash nebo PowerShell), který používáte. Níže jsou uvedeny některé příklady:

Relativní cesta k souboru (všechny operační systémy)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Absolutní cesta k souboru na platformě Linux/Mac a v operačním systému Windows

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

Použijte, `{file}` Pokud příkaz žádá o cestu k souboru. Například, `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Použijte, `@{file}` Pokud příkaz nahraje zadaný soubor. Například, `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Nahrání souborů]: ./media/media-services-cli/upload-download-files.png