---
title: Možnosti nasazení pro linuxové kontejnery
description: Rozhodněte se mezi vlastním nasazením kontejneru Dockeru, vícekontejnerovým a integrovaným aplikačním rámcem pro službu App Service na Linuxu.
keywords: azure app service, webová aplikace, linux, oss
author: msangapu-msft
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 7c4025e393c196fb98455bac26e94e5f252840b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687519"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Vlastní image, vícekontejnerový nebo vestavěný obraz platformy?

[App Service na Linuxu](app-service-linux-intro.md) nabízí tři různé cesty k získání aplikace zveřejněny na webu:

- **Vlastní nasazení bitové kopie**: "Dockerize" aplikace do image Dockeru, který obsahuje všechny soubory a závislosti v balíčku připraveném ke spuštění.
- **Nasazení s více kontejnery**: "Dockerize" aplikace přes více kontejnerů pomocí konfiguračního souboru Docker Compose.
- **Nasazení aplikací s integrovanou image platformy**: Naše integrované image platformy obsahují běžné běhové časy webových aplikací a závislosti, jako je Uzel a PHP. Použijte některou z [metod nasazení služby Azure App Service](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) k nasazení aplikace do úložiště webové aplikace a pak ji spusťte pomocí integrované image platformy.

## <a name="which-method-is-right-for-your-app"></a>Která metoda je pro vaši aplikaci vhodná? 

Hlavní faktory, které je třeba zvážit, jsou:

- **Dostupnost Dockeru ve vašem vývojovém pracovním postupu**: Vývoj vlastních bitových obrázků vyžaduje základní znalosti pracovního postupu vývoje Dockeru. Nasazení vlastní image do webové aplikace vyžaduje zveřejnění vlastní bitové kopie hostiteli úložiště, jako je Docker Hub. Pokud jste obeznámeni s Dockerem a můžete přidat úlohy Dockeru do pracovního postupu sestavení, nebo pokud už publikujete aplikaci jako image Dockeru, vlastní image je téměř jistě nejlepší volbou.
- **Vícevrstvá architektura:** Nasazení více kontejnerů, jako je například vrstva webové aplikace a vrstva rozhraní API, pro oddělení možností pomocí více kontejnerů. 
- **Výkon aplikace**: Zvyšte výkon aplikace s více kontejnery pomocí vrstvy mezipaměti, jako je Redis. K dosažení tohoto cíle vyberte více kontejnerů.
- **Jedinečné požadavky na běhový čas**: Integrované image platformy jsou navrženy tak, aby vyhovovaly potřebám většiny webových aplikací, ale jsou omezeny v jejich přizpůsobitelnosti. Vaše aplikace může mít jedinečné závislosti nebo jiné požadavky na běh, které přesahují, čeho jsou integrované bitové kopie schopné.
- **Požadavky na sestavení**: Díky [průběžnému nasazování](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)můžete aplikaci zprovoznit v Azure přímo ze zdrojového kódu. Není vyžadován žádný externí proces sestavení nebo publikování. Existuje však omezení přizpůsobitelnosti a dostupnosti nástrojů sestavení v rámci modulu nasazení [Kudu.](https://github.com/projectkudu/kudu/wiki) Vaše aplikace může přerůst kudu schopnosti, jak roste v jeho závislosti nebo požadavky na vlastní logiku sestavení.
- **Požadavky na čtení a zápis disku**: Všem webovým aplikacím je přidělen svazek úložiště pro webový obsah. Tento svazek, podporovaný službou Azure `/home` Storage, se připevní do souborového systému aplikace. Na rozdíl od souborů v souborovém systému kontejneru jsou soubory ve svazku obsahu přístupné ve všech instancích aplikace škálování a změny budou přetrvávat v rámci restartování aplikace. Latence disku svazku obsahu je však vyšší a více proměnné než latence místního souborového systému kontejneru a přístup může být ovlivněn upgrady platformy, neplánovanými prostoji a problémy s připojením k síti. Aplikace, které vyžadují velký přístup jen pro čtení k souborům obsahu, mohou mít prospěch z vlastního nasazení bitových obrázků, které umístí soubory do souborového systému bitové kopie namísto na svazek obsahu.
- **Využití prostředků sestavení**: Když je aplikace nasazená ze zdroje, skripty nasazení spuštěné kudu používají stejné výpočetní prostředky a prostředky úložiště App Service Plan jako spuštěná aplikace. Nasazení velkých aplikací může spotřebovat více prostředků nebo času, než je požadováno. Zejména mnoho pracovních postupů nasazení generovat velké aktivity disku na svazku obsahu aplikace, který není optimalizován pro tuto aktivitu. Vlastní image doručuje všechny soubory a závislosti vaší aplikace do Azure v jednom balíčku bez nutnosti dalších přenosů souborů nebo akcí nasazení.
- **Potřeba rychlé iterace**: Dockerizing aplikace vyžaduje další kroky sestavení. Aby se změny projevily, je nutné s každou aktualizací posunout nový obrázek do úložiště. Tyto aktualizace se pak natáhnou do prostředí Azure. Pokud jeden z integrovaných kontejnerů splňuje potřeby vaší aplikace, nasazení ze zdroje může nabídnout rychlejší pracovní postup vývoje.

## <a name="next-steps"></a>Další kroky

Vlastní kontejner:
* [Spuštění vlastního kontejneru](quickstart-docker-go.md)

Vícekontejnerových kontejnerů:
* [Vytvoření vícekontejnerové aplikace](quickstart-multi-container.md)

Následující články vám pomohou začít s App Service na Linuxu s integrovanou image platformy:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](quickstart-php.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)