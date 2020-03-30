---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6b5aa4f409b8c2f5a9125ab01e8587f4ac9c4ee5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945155"
---
## <a name="create-a-project-zip-file"></a>Vytvořit souboru ZIP projektu

>[!NOTE]
> Pokud jste stáhli soubory do souboru ZIP, extrahujte je jako první. Pokud jste například stáhli soubor ZIP z GitHubu, nemůžete jej nasadit tak, jak je. GitHub přidá další vnořené adresáře, které nefungují se službou App Service. 
>

V okně místního terminálu přejděte do kořenového adresáře projektu aplikace. 

Tento adresář by měl obsahovat vstupní soubor do webové aplikace, například _index.html_, _index.php_a _app.js_. Může také obsahovat soubory pro správu balíčků, jako _je project.json_, _composer.json_, _package.json_, _bower.json_a _requirements.txt_.

Pokud chcete, aby služba App Service spouštěla automatizaci nasazení `npm` `bower`za `gulp` `composer`vás, `pip`spusťte všechny úlohy sestavení (například , , , a ) a ujistěte se, že máte všechny soubory, které potřebujete ke spuštění aplikace. Tento krok je vyžadován, pokud chcete [spustit balíček přímo](../articles/app-service/deploy-run-package.md).

Vytvořte archiv ZIP se všemi položkami ve vašem projektu. Následující příkaz využívá základní nástroj vašeho terminálu:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

