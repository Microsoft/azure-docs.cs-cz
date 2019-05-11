---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 6e0175173f17ae0958522517360b94ee80f3b2f9
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544957"
---
## <a name="prerequisites"></a>Požadavky

### <a name="get-sample-code"></a>Získání vzorového kódu

Tento kurz předpokládá, že jste už dokončili kroky v [předchozím kurzu](../articles/container-registry/container-registry-tutorial-quick-task.md) a že jste vytvořili fork ukázkového úložiště a naklonovali ho. Pokud jste to ještě neudělali, dokončete před pokračováním kroky v části [Požadavky](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites) předchozího kurzu.

### <a name="container-registry"></a>Registr kontejneru

Abyste mohli dokončit tento kurz, musíte mít ve svém předplatném registr kontejneru Azure. Pokud potřebujete registr, najdete v článku [předchozí kurz o službě](../articles/container-registry/container-registry-tutorial-quick-task.md), nebo [rychlý start: Vytvoření registru kontejnerů pomocí Azure CLI](../articles/container-registry/container-registry-get-started-azure-cli.md).

## <a name="create-a-github-personal-access-token"></a>Vytvoření tokenu PAT GitHubu

Aktivovat úlohu na potvrzení změn do úložiště Git, třeba ACR úlohy osobní přístupový token PAT pro přístup k úložišti. Pokud již nemáte PAT, postupujte podle těchto kroků vygenerujete jednu v Githubu:

1. Přejděte na stránku vytvoření tokenu PAT na GitHubu na adrese https://github.com/settings/tokens/new.
1. Zadejte krátký **popis** tokenu, například „Ukázka služby ACR Tasks“.
1. V části **repo** (úložiště) povolte **repo:status** (úložiště:stav) a **public_repo** (veřejné_úložiště).

   ![Snímek obrazovky se stránkou generování tokenu PAT na GitHubu][build-task-01-new-token]

1. Vyberte tlačítko **Generate token** (Vygenerovat token). (Můžete být vyzváni k potvrzení hesla.)
1. Vygenerovaný token zkopírujte a uložte na **bezpečné místo** (tento token použijete při definici úlohy v následující části).

   ![Snímek obrazovky s vygenerovaným tokenem PAT na GitHubu][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
