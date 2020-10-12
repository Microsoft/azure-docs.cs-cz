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
ms.openlocfilehash: 035399924216434de85865102db8838ea3fa15a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85570200"
---
## <a name="create-a-project-zip-file"></a>Vytvořit souboru ZIP projektu

>[!NOTE]
> Pokud jste soubory stáhli v souboru ZIP, nejprve soubory rozbalte. Pokud jste například stáhli soubor ZIP z GitHubu, nemůžete tento soubor nasadit tak, jak je. GitHub přidá další vnořené adresáře, které nefungují s App Service. 
>

V místním okně terminálu přejděte do kořenového adresáře projektu aplikace. 

Tento adresář by měl obsahovat vstupní soubor vaší webové aplikace, například _index.html_, _index. php_a _app.js_. Může také obsahovat soubory správy balíčků, například _project.json_, _composer.json_, _package.json_, _bower.json_a _requirements.txt_.

Pokud nechcete, aby App Service spustila automatizaci nasazení za vás, spusťte všechny úlohy sestavení (například `npm` ,,, `bower` `gulp` `composer` a `pip` ) a ujistěte se, že máte všechny soubory, které potřebujete ke spuštění aplikace. Tento krok je nutný, pokud chcete [balíček spustit přímo](../articles/app-service/deploy-run-package.md).

Vytvořte archiv ZIP se všemi položkami ve vašem projektu. V případě `dotnet` projektů je tato složka výstupní složkou `dotnet publish` příkazu. Následující příkaz využívá základní nástroj vašeho terminálu:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

