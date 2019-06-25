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
ms.openlocfilehash: db431d7815cfcc006563bd6da438154ef77ae6e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814705"
---
## <a name="cli-shell"></a>Rozhraní příkazového řádku prostředí

Doporučuje se použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) ke spuštění příkazů rozhraní příkazového řádku. **Cloud Shell** je zdarma, interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Ve službě Cloud Shell jsou předinstalované obvyklé nástroje Azure a jsou nakonfigurované pro použití s vaším účtem. Poskytuje možnost vybrat si prostředí, která nejlépe vyhovuje stylu vaší práce. Uživatelé Linuxu si mohou vybrat Bash. Uživatelé Windows se mohou rozhodnout pro PowerShell.

Můžete také nainstalovat rozhraní příkazového řádku místně. Zobrazit [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) pokyny pro vaši platformu.

### <a name="sign-in"></a>Přihlášení

Použití místní instalaci rozhraní příkazového řádku vyžaduje přihlášení do Azure. Tento krok není povinný pro Azure Cloud Shell. Přihlaste se `az login` příkazu.

Pokud rozhraní příkazového řádku může spustit výchozí prohlížeč, udělá to a načte přihlašovací stránku. V opačném případě budete muset otevřít stránku prohlížeče a postupujte podle pokynů v příkazovém řádku zadejte autorizační kód po přejití do https://aka.ms/devicelogin v prohlížeči.

### <a name="specify-location-of-files"></a>Zadejte umístění souborů

Mnoho příkazů rozhraní příkazového řádku Media Services umožňují předat parametr s názvem souboru. Pokud používáte **Cloud Shell**, můžete nahrát soubor do vašeho clouddrive (použijte PowerShell nebo Bash). 

![Nahrání souborů]

Určuje, zda používáte místní rozhraní příkazového řádku nebo **Cloud Shell**, budete muset zadat cestu k souboru podle operačního systému nebo službě Cloud Shell (Bashe nebo Powershellu), kterou používáte. Dole najdete několik příkladů:

Relativní cesta k souboru (všechny OS)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Absolutní cestu k souboru v systému Linux/Mac a operačního systému Windows

* `@ "/usr/home/mytestfile.json"`
*   `@"c:\tmp\user\mytestfile.json"`

Použití `{file}` Pokud příkaz je požadující cestu k souboru. Například, `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Použití `@{file}` Pokud příkaz k načtení zadaného souboru. Například, `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Nahrání souborů]: ./media/media-services-cli/upload-download-files.png
