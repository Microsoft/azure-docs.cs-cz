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
ms.openlocfilehash: 06651b06ae84934c16e9f1ac9f604abda8b65615
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55648643"
---
## <a name="open-cli-shell"></a>Otevřete rozhraní příkazového řádku prostředí

Doporučuje se použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) ke spuštění příkazů rozhraní příkazového řádku. **Cloud Shell** je zdarma, interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Ve službě Cloud Shell jsou předinstalované obvyklé nástroje Azure a jsou nakonfigurované pro použití s vaším účtem. Poskytuje možnost vybrat si prostředí, která nejlépe vyhovuje stylu vaší práce. Uživatelé Linuxu si mohou vybrat Bash. Uživatelé Windows se mohou rozhodnout pro PowerShell.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

### <a name="login"></a>Přihlásit

Pokud chcete začít používat rozhraní příkazového řádku prostředí (v cloudu nebo místně), spusťte `az login` vytvořit připojení k Azure.

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


[Nahrání souborů]: ./media/media-services-cli/upload-download-files.png
