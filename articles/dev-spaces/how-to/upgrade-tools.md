---
title: Postup upgradu Azure Dev Spacesch nástrojů
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/03/2018
ms.topic: conceptual
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
ms.openlocfilehash: 24a66c5218bb75672ae784b19213116efdb83690
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232443"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Postup upgradu Azure Dev Spacesch nástrojů

Pokud je k dispozici nová verze, kterou už Azure Dev Spaces používáte, možná budete muset upgradovat klientské nástroje Azure Dev Spaces.

## <a name="update-the-azure-cli"></a>Aktualizace rozhraní příkazového řádku Azure

Když aktualizujete nejnovější rozhraní příkazového řádku Azure CLI, získáte také nejnovější verzi rozšíření vývojové prostory CLI.

Nemusíte odinstalovat předchozí verzi, stačí najít příslušné soubory ke stažení v [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Aktualizace rozšíření pro vývoj a nástroje příkazového řádku pro vývojové prostory

Spusťte následující příkaz:

```cmd
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Aktualizace rozšíření VS Code

Po instalaci se rozšíření automaticky aktualizuje. Je možné, že bude nutné znovu načíst rozšíření pro použití nových funkcí. V VS Code otevřete podokno **rozšíření** , vyberte rozšíření **Azure dev Spaces** a vyberte možnost **znovu načíst**.

## <a name="update-the-visual-studio-extension"></a>Aktualizace rozšíření sady Visual Studio

Podobně jako u jiných rozšíření a aktualizací Visual Studio vás upozorní, jakmile bude k dispozici aktualizace Visual Studio Tools for Kubernetes, která zahrnuje Azure Dev Spaces. Vyhledejte ikonu příznaku v pravém horním rohu obrazovky.

Chcete-li aktualizovat nástroje v aplikaci Visual Studio, klikněte na položku nabídky **rozšíření a aktualizace nástroje >** a na levé straně vyberte možnost **aktualizace**. Vyhledejte **Visual Studio Tools for Kubernetes** a klikněte na tlačítko **aktualizovat** .

## <a name="next-steps"></a>Další postup

Otestujte nové nástroje vytvořením nového clusteru. Vyzkoušejte rychlé starty a kurzy na [Azure dev Spaces](/azure/dev-spaces).