---
title: Ručně zastavení nebo spuštění kontejnerů ve službě Azure Container Instances
description: Zjistěte, jak ručně zastavit nebo spustit skupinu kontejnerů ve službě Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 50f3ecf69561313a5bda67827cfb02d2f61d461f
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610198"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Ručně zastavení nebo spuštění kontejnerů ve službě Azure Container Instances

[Zásady restartování](container-instances-restart-policy.md) nastavení skupiny kontejnerů Určuje, jak instance kontejneru, spuštění nebo zastavení ve výchozím nastavení. Můžete přepsat výchozí nastavení ručně zastavení nebo spuštění skupiny kontejnerů.

## <a name="stop"></a>Zastavit

Ručně zastavte spuštěnou skupinu kontejnerů – například s použitím [az container stop] [ az-container-stop] příkaz nebo webu Azure portal. Pro některé úlohy kontejneru, můžete chtít zastavit skupiny kontejnerů dlouhotrvající po definované období a Šetřete na náklady. 

*Když skupiny kontejnerů přejde do stavu Zastaveno, ukončí a recykluje všechny kontejnery ve skupině. Stav kontejneru nezachová.*

I když jsou recyklovány kontejnerů ve skupině kontejnerů Zastaveno [prostředky](container-instances-container-groups.md#resource-allocation) zůstanou přidělené pro použití. Proto se pokračuje fakturace pro skupinu kontejnerů zastaveno.

Akce zastavení nemá žádný vliv, pokud již byla ukončena skupiny kontejnerů (je ve stavu úspěšné nebo neúspěšné). Například skupinu kontejnerů s úlohy spouštěné jednou kontejneru, které byly úspěšně spuštěny skončí ve stavu úspěch. Limitu pokusí zastavit skupině, stav se nezmění stav. 

## <a name="start"></a>Start

Když skupiny kontejnerů je zastavený – buď protože kontejnery ukončen na své vlastní nebo k ručnímu zastavení skupina – můžete spustit kontejnery. Například použít [az container start] [ az-container-start] příkaz nebo webu Azure portal ohledně ručního spuštění kontejnerů ve skupině. Pokud image kontejneru pro každý kontejner je aktualizován, je vyžádá novou bitovou kopii. 

Počáteční skupiny kontejnerů začne nové nasazení se stejnou konfigurací kontejneru. Tuto akci můžete rychle znovu použít konfiguraci skupiny známé kontejneru, která funguje podle očekávání. Není nutné vytvořit novou skupinu kontejnerů ke spuštění stejná úloha.

Spustí se všechny kontejnery ve skupině kontejnerů pomocí této akce. Nelze spustit konkrétní kontejner ve skupině.

Po ruční spuštění nebo restartování skupiny kontejnerů, spuštění kontejneru skupiny podle nakonfigurované zásady restartování.
  
## <a name="restart"></a>Restartování

Během jejího běhu – například pomocí je možné restartovat skupinu kontejnerů [az container restartování] [ az-container-restart] příkazu. Tato akce restartuje všechny kontejnery ve skupině kontejnerů. Pokud image kontejneru pro každý kontejner je aktualizován, je vyžádá novou bitovou kopii. 

Restartování skupiny kontejnerů je užitečné, pokud chcete k vyřešení problému nasazení. Například pokud omezení dočasný prostředek, který brání kontejnery v úspěšném spuštění, restartování skupiny můžou tyto potíže vyřešit.

Touto akcí se restartují všechny kontejnery ve skupině kontejnerů. Nelze restartovat konkrétní kontejner ve skupině.

Po ruční restartování skupiny kontejnerů, spuštění kontejneru skupiny podle nakonfigurované zásady restartování.

## <a name="next-steps"></a>Další postup

Další informace o [restartování nastavení zásad](container-instances-restart-policy.md) ve službě Azure Container Instances.

Kromě ručně zastavení a spuštění skupiny kontejnerů s existující konfiguraci, můžete [aktualizovat nastavení](container-instances-update.md) spuštěné skupiny kontejnerů.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
