---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 40cc1856a5e943ca5596e7d11712febadd30e3ec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67133515"
---
## <a name="prerequisites"></a>Požadavky

### <a name="get-sample-code"></a>Získání vzorového kódu

Tento kurz předpokládá, že jste už dokončili kroky v [předchozím kurzu](../articles/container-registry/container-registry-tutorial-quick-task.md) a že jste vytvořili fork ukázkového úložiště a naklonovali ho. Pokud jste to ještě neudělali, dokončete před pokračováním kroky v části [Požadavky](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites) předchozího kurzu.

### <a name="container-registry"></a>Registr kontejneru

Abyste mohli dokončit tento kurz, musíte mít ve svém předplatném registr kontejneru Azure. Pokud potřebujete registr, podívejte se na [předchozí kurz](../articles/container-registry/container-registry-tutorial-quick-task.md) nebo článek [Rychlý start: Vytvoření registru kontejnerů pomocí Azure CLI](../articles/container-registry/container-registry-get-started-azure-cli.md).

## <a name="create-a-github-personal-access-token"></a>Vytvoření tokenu PAT GitHubu

Chcete-li spustit úlohu na potvrzení úložiště Git, ACR úkoly potřebují osobní přístupový token (PAT) pro přístup k úložišti. Pokud ještě pat nemáte, postupujte takto a vygenerujte ho na GitHubu:

1. Přejděte na stránku vytvoření tokenu PAT na GitHubu na adrese https://github.com/settings/tokens/new.
1. Zadejte krátký **popis** tokenu, například „Ukázka služby ACR Tasks“.
1. Vyberte obory pro ACR pro přístup k opětovnému poklidu. Chcete-li získat přístup k veřejnému repo jako v tomto kurzu, v části **repo**, povolte **repo:status** a **public_repo**

   ![Snímek obrazovky se stránkou generování tokenu PAT na GitHubu][build-task-01-new-token]

   > [!NOTE]
   > Chcete-li vygenerovat pat pro přístup k *soukromému* repo, vyberte obor pro úplné **řízení repo.**

1. Vyberte tlačítko **Generate token** (Vygenerovat token). (Můžete být vyzváni k potvrzení hesla.)
1. Vygenerovaný token zkopírujte a uložte na **bezpečné místo** (tento token použijete při definici úlohy v následující části).

   ![Snímek obrazovky s vygenerovaným tokenem PAT na GitHubu][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
