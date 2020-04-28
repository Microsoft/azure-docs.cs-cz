---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 79fb8517ec6880e8a3eae0e74275567a24644b87
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "67174810"
---
## <a name="deployment-customization"></a>Přizpůsobení nasazení

Proces nasazení předpokládá, že soubor. zip, který vložíte, obsahuje aplikaci připravenou k běhu. Ve výchozím nastavení nejsou spuštěny žádné vlastní nastavení. Chcete-li povolit stejné procesy sestavení, které získáte s průběžnou integrací, přidejte do nastavení aplikace následující:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

Při použití nabízeného nasazení. zip je toto nastavení ve výchozím nastavení **false** . Výchozí hodnota je **true** pro nasazení nepřetržité integrace. Při nastavení na **hodnotu true**se vaše nastavení související s nasazením použijí během nasazení. Tato nastavení můžete nakonfigurovat buď jako nastavení aplikace, nebo v konfiguračním souboru. nasazení, který se nachází v kořenovém adresáři souboru. zip. Další informace najdete v části [nastavení týkající se úložiště a nasazení](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) v tématu Reference k nasazení.