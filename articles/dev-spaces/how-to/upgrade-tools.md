---
title: Postup upgradu Azure Dev Spacesch nástrojů
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: Informace o upgradu Azure Dev Spaces nástrojů příkazového řádku, Visual Studio Code rozšíření a rozšíření sady Visual Studio
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
ms.openlocfilehash: 748ed4699591a21a5a09da924e093011b0813b46
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265230"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Postup upgradu Azure Dev Spacesch nástrojů

Pokud je k dispozici nová verze, kterou už Azure Dev Spaces používáte, možná budete muset upgradovat klientské nástroje Azure Dev Spaces.

## <a name="update-the-azure-cli"></a>Aktualizace rozhraní příkazového řádku Azure

Když aktualizujete nejnovější rozhraní příkazového řádku Azure CLI, získáte také nejnovější verzi rozšíření vývojové prostory CLI.

Nemusíte odinstalovat předchozí verzi, stačí najít příslušné soubory ke stažení v [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Aktualizace rozšíření pro vývoj a nástroje příkazového řádku pro vývojové prostory

Spusťte následující příkaz:

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Aktualizace rozšíření VS Code

Po instalaci se rozšíření automaticky aktualizuje. Je možné, že bude nutné znovu načíst rozšíření pro použití nových funkcí. V VS Code otevřete podokno **rozšíření** , vyberte rozšíření **Azure dev Spaces** a vyberte možnost **znovu načíst**.

## <a name="update-the-visual-studio-extension"></a>Aktualizace rozšíření sady Visual Studio

Podobně jako u jiných rozšíření a aktualizací Visual Studio vás upozorní, jakmile bude k dispozici aktualizace Visual Studio Tools for Kubernetes, která zahrnuje Azure Dev Spaces. Vyhledejte ikonu příznaku v pravém horním rohu obrazovky.

Chcete-li aktualizovat nástroje v aplikaci Visual Studio, klikněte na položku nabídky **rozšíření a aktualizace nástroje >** a na levé straně vyberte možnost **aktualizace**. Vyhledejte **Visual Studio Tools for Kubernetes** a klikněte na tlačítko **aktualizovat** .

## <a name="next-steps"></a>Další kroky

Otestujte nové nástroje vytvořením nového clusteru. Vyzkoušejte rychlé starty a kurzy na [Azure dev Spaces](/azure/dev-spaces).
