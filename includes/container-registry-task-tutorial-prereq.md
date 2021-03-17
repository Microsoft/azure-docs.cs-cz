---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 79e2ca71d6b8178be63c3429edba89cf3bb523e2
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029981"
---
## <a name="prerequisites"></a>Požadavky

### <a name="get-sample-code"></a>Získání vzorového kódu

Tento kurz předpokládá, že jste už dokončili kroky v [předchozím kurzu](../articles/container-registry/container-registry-tutorial-quick-task.md) a že jste vytvořili fork ukázkového úložiště a naklonovali ho. Pokud jste to ještě neudělali, dokončete před pokračováním kroky v části [Požadavky](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites) předchozího kurzu.

### <a name="container-registry"></a>Registr kontejneru

Abyste mohli dokončit tento kurz, musíte mít ve svém předplatném registr kontejneru Azure. Pokud potřebujete registr, podívejte se na [předchozí kurz](../articles/container-registry/container-registry-tutorial-quick-task.md) nebo článek [Rychlý start: Vytvoření registru kontejnerů pomocí Azure CLI](../articles/container-registry/container-registry-get-started-azure-cli.md).

### <a name="create-a-github-personal-access-token"></a>Vytvoření tokenu PAT GitHubu

Aby mohl úkol aktivovat na základě potvrzení do úložiště Git, úlohy ACR vyžadují pro přístup k úložišti token PAT (Personal Access token). Pokud ještě nemáte PAT, vygenerujte si ho na GitHubu pomocí těchto kroků:

1. Přejděte na stránku vytvoření tokenu PAT na GitHubu na adrese https://github.com/settings/tokens/new.
1. Zadejte krátký **popis** tokenu, například „Ukázka služby ACR Tasks“.
1. Vyberte obory pro ACR pro přístup k úložišti. Pokud chcete získat přístup k veřejnému úložišti jako v tomto kurzu, v části **úložiště** povolte **úložiště: stav** a **public_repo** .

   ![Snímek obrazovky se stránkou generování tokenu PAT na GitHubu][build-task-01-new-token]

   > [!NOTE]
   > Chcete-li vygenerovat PAT pro přístup k *soukromému* úložišti, vyberte obor pro úplný ovládací prvek **úložiště** .

1. Vyberte tlačítko **Generate token** (Vygenerovat token). (Můžete být vyzváni k potvrzení hesla.)
1. Vygenerovaný token zkopírujte a uložte na **bezpečné místo** (tento token použijete při definici úlohy v následující části).

   ![Snímek obrazovky s vygenerovaným tokenem PAT na GitHubu][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
