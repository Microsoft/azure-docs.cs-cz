---
title: Předpoklady Azure CLI pro mezipaměť prostředí Azure HPC
description: Než budete moct pomocí Azure CLI vytvořit nebo upravit mezipaměť prostředí Azure HPC, nastavte postup.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 7d0844e699c012d987c23a75e2b0874005cf535a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87097386"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Nastavení rozhraní příkazového řádku Azure pro Azure HPC Cache

Pomocí těchto kroků Připravte prostředí před použitím Azure CLI k vytvoření nebo správě mezipaměti HPC Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI

Mezipaměť prostředí Azure HPC vyžaduje verzi 2,7 nebo novější rozhraní příkazového řádku Azure CLI. Spusťte `az --version` a zjistěte verzi a závislé knihovny, které jsou nainstalované. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

```azurecli-interactive
az --version
```

### <a name="install-the-azure-hpc-cache-extension"></a>Instalace rozšíření mezipaměti HPC Azure

Funkce mezipaměti HPC Azure nejsou součástí hlavního základu kódu, takže musíte také nainstalovat odkaz na rozšíření. Postupujte podle následujících pokynů.

1. Přihlásit se

   Přihlaste se pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) , pokud používáte místně nainstalovanou verzi rozhraní příkazového řádku.

    ```azurecli
    az login
    ```

    Dokončete proces ověřování podle kroků zobrazených v terminálu.

   > [!TIP]
   > Pokud máte více předplatných, musíte si vybrat jednu z nich. Vyberte ho při spuštění Cloud Shell relace na webu Azure Portal, nebo postupujte podle pokynů v tématu [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in) a nastavte své předplatné z příkazového řádku.

2. Instalace rozšíření Azure CLI

   Funkce mezipaměti HPC Azure jsou k dispozici jako rozšíření Azure CLI – zatím není součástí základního balíčku CLI. Musíte nainstalovat odkaz na rozšíření, abyste ho mohli použít.

   Rozšíření Azure CLI poskytují přístup k experimentálním a předběžným příkazům. Další informace o rozšířeních, včetně aktualizace a odinstalace, najdete v tématu [použití rozšíření pomocí Azure CLI](/cli/azure/azure-cli-extensions-overview).

   Nainstalujte rozšíření pro mezipaměť prostředí Azure HPC spuštěním následujícího příkazu:

    ```azurecli-interactive
    az extension add --name hpc-cache
   ```

## <a name="set-default-resource-group-optional"></a>Nastavit výchozí skupinu prostředků (volitelné)

Většina příkazů mezipaměti HPC vyžaduje, abyste předávali skupinu prostředků mezipaměti. Výchozí skupinu prostředků můžete nastavit pomocí [AZ Configure](/cli/azure/reference-index#az-configure).

## <a name="next-steps"></a>Další kroky

Po instalaci rozšíření Azure CLI a přihlášení můžete pomocí Azure CLI vytvářet a spravovat systémy mezipaměti HPC v Azure.

* [Vytvoření mezipaměti prostředí Azure HPC](hpc-cache-create.md)
* [Azure CLI HPC – dokumentace mezipaměti](/cli/azure/ext/hpc-cache/hpc-cache)
