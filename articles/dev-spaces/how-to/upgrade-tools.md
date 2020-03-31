---
title: Jak upgradovat nástroje Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: Zjistěte, jak upgradovat nástroje příkazového řádku Azure Dev Spaces, rozšíření kódu Sady Visual Studio a rozšíření Sady Visual Studio
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
ms.openlocfilehash: 748ed4699591a21a5a09da924e093011b0813b46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265230"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Jak upgradovat nástroje Azure Dev Spaces

Pokud je k dispozici nová verze a už používáte Azure Dev Spaces, možná budete muset upgradovat vaše nástroje klienta Azure Dev Spaces.

## <a name="update-the-azure-cli"></a>Aktualizace nastavení příkazu k onomu Azure

Když aktualizujete nejnovější nastavení příkazového příkazu k dispozici pro Azure, získáte také nejnovější verzi rozšíření příkazového příkazu pro dev spaces.

Nemusíte odinstalovat předchozí verzi, stačí najít odpovídající stáhnout na [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Aktualizace rozšíření a nástrojů příkazového řádku Dev Spaces

Spusťte následující příkaz:

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Aktualizace rozšíření VS Code

Po instalaci se rozšíření automaticky aktualizuje. Možná budete muset znovu načíst rozšíření používat nové funkce. V kódu VS otevřete podokno **Rozšíření,** zvolte rozšíření **Azure Dev Spaces** a zvolte **Znovu načíst**.

## <a name="update-the-visual-studio-extension"></a>Aktualizace rozšíření sady Visual Studio

Stejně jako u jiných rozšíření a aktualizací vás Visual Studio upozorní, když je k dispozici aktualizace nástrojů sady Visual Studio pro Kubernetes, která zahrnuje Azure Dev Spaces. V pravém horním rohu obrazovky vyhledejte ikonu vlajky.

Chcete-li aktualizovat nástroje v sadě Visual Studio, zvolte položku nabídky **Nástroje > rozšíření a aktualizace** a na levé straně zvolte **Aktualizace**. Najděte **nástroje Visual Studia pro Kubernetes** a zvolte tlačítko **Aktualizovat.**

## <a name="next-steps"></a>Další kroky

Vyzkoušejte nové nástroje vytvořením nového clusteru. Vyzkoušejte rychlé starty a kurzy v [Azure Dev Spaces](/azure/dev-spaces).
