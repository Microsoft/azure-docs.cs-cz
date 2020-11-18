---
title: Předpoklady Azure CLI pro mezipaměť prostředí Azure HPC
description: Než budete moct pomocí Azure CLI vytvořit nebo upravit mezipaměť prostředí Azure HPC, nastavte postup.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 13f45c96a830110bd0f4a2d4a2b422921d7a2e31
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654452"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Nastavení rozhraní příkazového řádku Azure pro Azure HPC Cache

Pomocí těchto kroků Připravte prostředí před použitím Azure CLI k vytvoření nebo správě mezipaměti HPC Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Mezipaměť prostředí Azure HPC vyžaduje verzi 2,7 nebo novější rozhraní příkazového řádku Azure CLI. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="set-default-resource-group-optional"></a>Nastavit výchozí skupinu prostředků (volitelné)

Většina příkazů mezipaměti HPC vyžaduje, abyste předávali skupinu prostředků mezipaměti. Výchozí skupinu prostředků můžete nastavit pomocí [AZ Configure](/cli/azure/reference-index#az-configure).

## <a name="next-steps"></a>Další kroky

Po instalaci rozšíření Azure CLI a přihlášení můžete pomocí Azure CLI vytvářet a spravovat systémy mezipaměti HPC v Azure.

* [Vytvoření mezipaměti prostředí Azure HPC](hpc-cache-create.md)
* [Azure CLI HPC – dokumentace mezipaměti](/cli/azure/ext/hpc-cache/hpc-cache)
