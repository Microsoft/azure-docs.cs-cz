---
title: Předpoklady Azure CLI pro mezipaměť prostředí Azure HPC
description: Než budete moct pomocí Azure CLI vytvořit nebo upravit mezipaměť prostředí Azure HPC, nastavte postup.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 30621eceefd69cd3e08de137bb34f1079a17a406
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780474"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Nastavení rozhraní příkazového řádku Azure pro Azure HPC Cache

Pomocí těchto kroků Připravte prostředí před použitím Azure CLI k vytvoření nebo správě mezipaměti HPC Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Mezipaměť prostředí Azure HPC vyžaduje verzi 2,7 nebo novější rozhraní příkazového řádku Azure CLI. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="set-default-resource-group-optional"></a>Nastavit výchozí skupinu prostředků (volitelné)

Většina příkazů mezipaměti HPC vyžaduje, abyste předávali skupinu prostředků mezipaměti. Výchozí skupinu prostředků můžete nastavit pomocí [AZ Configure](/cli/azure/reference-index#az_configure).

## <a name="next-steps"></a>Další kroky

Po instalaci rozšíření Azure CLI a přihlášení můžete pomocí Azure CLI vytvářet a spravovat systémy mezipaměti HPC v Azure.

* [Vytvoření mezipaměti prostředí Azure HPC](hpc-cache-create.md)
* [Azure CLI HPC – dokumentace mezipaměti](/cli/azure/ext/hpc-cache/hpc-cache)
