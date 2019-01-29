---
title: zahrnout soubor
description: zahrnout soubor
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/25/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: b335cf996de41f4eea15af1899a0c6654c2f679f
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104979"
---
## <a name="open-cli-shell"></a>Otevřete rozhraní příkazového řádku prostředí

Doporučuje se použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) ke spuštění příkazů rozhraní příkazového řádku. **Cloud Shell** je zdarma, interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Ve službě Cloud Shell jsou předinstalované obvyklé nástroje Azure a jsou nakonfigurované pro použití s vaším účtem. Stačí vyberte tlačítko Kopírovat zkopírujte kód, vložte ho ve službě Cloud Shell a stiskněte klávesu Enter pro spuštění. Cloud Shell můžete otevřít několika způsoby:

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

### <a name="login"></a>Přihlásit

Pokud chcete začít používat rozhraní příkazového řádku prostředí (v cloudu nebo místně), spusťte `az login` vytvořit připojení k Azure.

Pokud rozhraní příkazového řádku může spustit výchozí prohlížeč, udělá to a načte přihlašovací stránku. V opačném případě budete muset otevřít stránku prohlížeče a postupujte podle pokynů v příkazovém řádku zadejte autorizační kód po přejití do https://aka.ms/devicelogin v prohlížeči.

### <a name="specify-location-of-files"></a>Zadejte umístění souborů

Mnoho příkazů rozhraní příkazového řádku Media Services umožňují předat parametr s názvem souboru. 

Pokud používáte **Azure Cloud Shell**, nahrát soubor do **Azure Cloud Shell**. Můžete najít na nahrávání a stahování souborů tlačítko v horní části okna prostředí. Pak odkazovat na soubor následujícím způsobem: `@{FileName}.` 

![Nahrání souborů]

Pokud používáte Azure CLI místně, zadejte cestu celý soubor. Například, `@c:\tracks.json`.


[Nahrání souborů]: ./media/media-services-cli/upload-download-files.png
