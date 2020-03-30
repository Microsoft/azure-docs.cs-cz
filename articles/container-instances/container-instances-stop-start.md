---
title: Ručně zastavit nebo spustit skupinu kontejnerů
description: Přečtěte si, jak ručně zastavit nebo spustit skupinu kontejnerů v Azure Container Instances.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: c9f8afea33c65df940d02823ec394697d2786d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533425"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Ruční zastavení nebo spuštění kontejnerů v instanci kontejnerů Azure

Nastavení [zásad restartování](container-instances-restart-policy.md) skupiny kontejnerů určuje, jak se instance kontejneru ve výchozím nastavení spouštějí nebo zastavují. Výchozí nastavení můžete přepsat ručním zastavením nebo spuštěním skupiny kontejnerů.

## <a name="stop"></a>Zastavit

Ručně zastavit spuštěnou skupinu kontejnerů – například pomocí příkazu [az container stop][az-container-stop] nebo portálu Azure. Pro některé úlohy kontejneru můžete chtít zastavit dlouhotrvající skupinu kontejnerů po definovaném období, abyste ušetřili náklady. 

*Když skupina kontejnerů přejde do stavu Zastaveno, ukončí a recykluje všechny kontejnery ve skupině. Nezachová stav kontejneru.*

Při recyklaci kontejnerů [jsou](container-instances-container-groups.md#resource-allocation) prostředky jsou přiděleny a fakturace se zastaví pro skupinu kontejnerů.

Akce stop nemá žádný vliv, pokud skupina kontejnerů již byla ukončena (je ve stavu Úspěšné nebo Neúspěšné). Například skupina kontejnerů s úlohami kontejneru při spuštění, které byly úspěšně spuštěny, bude ukončena ve stavu Úspěšné. Pokusy o zastavení skupiny v tomto stavu nemění stav. 

## <a name="start"></a>Start

Když je skupina kontejnerů zastavena - buď proto, že kontejnery byly ukončeny samostatně, nebo ručně jste skupinu zastavili - můžete kontejnery spustit. Například použijte příkaz [az kontejner uzahájení][az-container-start] nebo portál Azure ručně spustit kontejnery ve skupině. Pokud je aktualizována image kontejneru pro libovolný kontejner, je vyžádána nová bitová kopie. 

Spuštění skupiny kontejnerů začíná nové nasazení se stejnou konfigurací kontejneru. Tato akce vám může pomoci rychle znovu použít známou konfiguraci skupiny kontejnerů, která funguje podle očekávání. Není třeba vytvořit novou skupinu kontejnerů ke spuštění stejné úlohy.

Všechny kontejnery ve skupině kontejnerů jsou spuštěny touto akcí. Ve skupině nelze spustit konkrétní kontejner.

Po ručním spuštění nebo restartování skupiny kontejnerů se skupina kontejnerů spustí podle nakonfigurované zásady restartování.
  
## <a name="restart"></a>Restartování

Skupinu kontejnerů můžete restartovat, když je spuštěna – například pomocí příkazu [az container restart.][az-container-restart] Tato akce restartuje všechny kontejnery ve skupině kontejnerů. Pokud je aktualizována image kontejneru pro libovolný kontejner, je vyžádána nová bitová kopie. 

Restartování skupiny kontejnerů je užitečné, pokud chcete vyřešit problém s nasazením. Například pokud dočasné omezení prostředků zabrání úspěšnému spuštění kontejnerů, restartování skupiny může problém vyřešit.

Všechny kontejnery ve skupině kontejnerů jsou restartovány touto akcí. Nelze restartovat konkrétní kontejner ve skupině.

Po ručním restartování skupiny kontejnerů se skupina kontejnerů spustí podle nakonfigurované zásady restartování.

## <a name="next-steps"></a>Další kroky

Další informace o [nastavení zásad restartování](container-instances-restart-policy.md) v Azure Container Instances.

Kromě ručního zastavení a spuštění skupiny kontejnerů s existující konfigurací můžete [aktualizovat nastavení](container-instances-update.md) spuštěné skupiny kontejnerů.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
