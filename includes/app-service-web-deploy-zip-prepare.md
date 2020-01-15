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
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945155"
---
## <a name="create-a-project-zip-file"></a>Vytvořit souboru ZIP projektu

>[!NOTE]
> Pokud jste soubory stáhli v souboru ZIP, nejprve soubory rozbalte. Pokud jste například stáhli soubor ZIP z GitHubu, nemůžete tento soubor nasadit tak, jak je. GitHub přidá další vnořené adresáře, které nefungují s App Service. 
>

V místním okně terminálu přejděte do kořenového adresáře projektu aplikace. 

Tento adresář by měl obsahovat vstupní soubor vaší webové aplikace, jako je například _index. html_, _index. php_a _App. js_. Může také obsahovat soubory správy balíčků jako _Project. JSON_, _skladatele. JSON_, _Package. JSON_, _Bower. JSON_a _požadavky. txt_.

Pokud nechcete, aby App Service spustila automatizaci nasazení, spusťte všechny úlohy sestavení (například `npm`, `bower`, `gulp`, `composer`a `pip`) a ujistěte se, že máte všechny soubory, které potřebujete ke spuštění aplikace. Tento krok je nutný, pokud chcete [balíček spustit přímo](../articles/app-service/deploy-run-package.md).

Vytvořte archiv ZIP se všemi položkami ve vašem projektu. Následující příkaz využívá základní nástroj vašeho terminálu:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

