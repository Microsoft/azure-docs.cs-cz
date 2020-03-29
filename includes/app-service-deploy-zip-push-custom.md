---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 79fb8517ec6880e8a3eae0e74275567a24644b87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174810"
---
## <a name="deployment-customization"></a>Přizpůsobení nasazení

Proces nasazení předpokládá, že soubor ZIP, který push obsahuje aplikaci připravenou ke spuštění. Ve výchozím nastavení nejsou spuštěna žádná vlastní nastavení. Chcete-li povolit stejné procesy sestavení, které získáte s průběžnou integrací, přidejte do nastavení aplikace následující:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

Při použití push nasazení ZIP je toto nastavení ve výchozím nastavení **nepravdivé.** Výchozí hodnota **platí** pro nasazení průběžné integrace. Pokud je nastavena na **hodnotu true**, nastavení související s nasazením se používají během nasazení. Tato nastavení můžete nakonfigurovat buď jako nastavení aplikace, nebo v konfiguračním souboru .deployment, který je umístěn v kořenovém adresáři souboru ZIP. Další informace naleznete v [tématu úložiště a nastavení související s nasazením](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) v odkazu na nasazení.