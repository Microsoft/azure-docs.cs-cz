---
title: Možnosti nasazení pro kontejnery platformy Linux
description: Rozhodněte se z vlastního nasazení kontejneru Docker, více kontejnerů a integrované aplikační architektury pro App Service v systému Linux.
keywords: Azure App Service, Web App, Linux, OSS
author: msangapu-msft
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 7c4025e393c196fb98455bac26e94e5f252840b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "74687519"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Vlastní image, více kontejnerů nebo vestavěná image platformy?

[App Service v systému Linux](app-service-linux-intro.md) nabízí tři různé cesty k aplikaci publikované na webu:

- **Nasazení vlastních imagí**: "ukotvovat" vaší aplikace do dokovací image, která obsahuje všechny vaše soubory a závislosti, do balíčku připraveného k spuštění.
- **Nasazení s více**kontejnery: "ukotvovat" vaší aplikace v několika kontejnerech pomocí konfiguračního souboru Docker Compose.
- **Nasazení aplikace s integrovanou imagí platformy**: naše integrované image platforem obsahují běžné moduly runtime a závislosti webových aplikací, jako je například node a php. K nasazení vaší aplikace do úložiště vaší webové aplikace použijte libovolnou z [Azure App Service metody nasazení](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) a pak použijte vestavěnou image platformy pro její spuštění.

## <a name="which-method-is-right-for-your-app"></a>Která metoda je pro vaši aplikaci nejvhodnější? 

Primární faktory, které je potřeba vzít v úvahu:

- **Dostupnost Docker v pracovním postupu vývoje**: vývoj vlastních imagí vyžaduje základní znalosti pracovního postupu Docker pro vývoj. Nasazení vlastní image do webové aplikace vyžaduje publikování vlastní image do hostitele úložiště, jako je Docker Hub. Pokud jste obeznámeni s Docker a můžete přidat úkoly Docker do pracovního postupu sestavení, nebo pokud už aplikaci publikujete jako image Docker, vlastní image je skoro určitě nejlepší volbou.
- **Vícevrstvá architektura**: nasazení více kontejnerů, jako je například vrstva webové aplikace a vrstva rozhraní API, pro oddělení možností pomocí více kontejnerů. 
- **Výkon aplikace**: Zvyšte výkon aplikace s více kontejnery pomocí vrstvy mezipaměti, jako je Redis. Pro dosažení tohoto cíle vyberte více kontejnerů.
- **Jedinečné požadavky na modul runtime**: integrované image platforem jsou navržené tak, aby splňovaly potřeby většiny webových aplikací, ale jsou omezené jejich úpravou. Vaše aplikace může mít jedinečné závislosti nebo jiné požadavky na modul runtime, které překračují, co jsou integrované image schopné.
- **Požadavky na sestavení**: Díky [průběžnému nasazování](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)můžete svou aplikaci v Azure začít používat přímo ze zdrojového kódu. Není vyžadován externí proces sestavení nebo publikace. Nicméně existuje omezení úprav a dostupnosti nástrojů sestavení v rámci modulu nasazení [Kudu](https://github.com/projectkudu/kudu/wiki) . Vaše aplikace může rozšiřovat schopnosti Kudu při zvětšování svých závislostí nebo požadavků pro vlastní logiku sestavení.
- **Požadavky na čtení a zápis na disku**: všechny webové aplikace mají přidělený svazek úložiště pro webový obsah. Tento svazek, který je zálohovaný Azure Storage, je připojený do `/home` systému souborů aplikace. Na rozdíl od souborů v systému souborů kontejnerů jsou soubory ve svazku obsahu přístupné napříč všemi instancemi škálování aplikace a úpravy budou zachovány po restartování aplikace. Latence disku ve svazku obsahu je ale vyšší a větší než latence místního systému souborů kontejnerů a přístup může mít vliv na upgrady platforem, neplánované výpadky a problémy s připojením k síti. Aplikace, které vyžadují velký přístup jen pro čtení k souborům obsahu, můžou využívat vlastní nasazení image, které místo na svazku obsahu umístí soubory v systému souborů imagí.
- **Využití prostředků sestavení**: když se aplikace nasadí ze zdroje, skripty nasazení, které spustí Kudu, používají stejné App Service plánování výpočetních prostředků a prostředků úložiště jako běžící aplikace. Rozsáhlá nasazení aplikací můžou spotřebovávat více prostředků nebo dobu, než je potřeba. Konkrétně mnoho pracovních postupů nasazení generuje na svazku obsahu aplikace velkou činnost disku, která není pro tuto aktivitu optimalizovaná. Vlastní image ukládá všechny soubory a závislosti vaší aplikace do Azure v jednom balíčku bez nutnosti dalších přenosů souborů nebo akcí nasazení.
- **Potřeba rychlé iterace**: Dockerizing aplikace vyžaduje další kroky sestavení. Změny se projeví až po vložení nové image do úložiště s každou aktualizací. Tyto aktualizace jsou pak načteny do prostředí Azure. Pokud některý z vestavěných kontejnerů vyhovuje potřebám vaší aplikace, může nasazení ze zdroje nabízet rychlejší vývojový pracovní postup.

## <a name="next-steps"></a>Další kroky

Vlastní kontejner:
* [Spuštění vlastního kontejneru](quickstart-docker-go.md)

Více kontejnerů:
* [Vytvoření vícekontejnerové aplikace](quickstart-multi-container.md)

Následující články vám pomohou začít s App Service v systému Linux s integrovanou imagí platformy:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](quickstart-php.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)