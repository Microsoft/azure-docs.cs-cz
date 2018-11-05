---
title: Podporované verze Kubernetes ve službě Azure Kubernetes
description: Zásady podpory verze Kubernetes a životního cyklu clusterů ve službě Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 09/21/2018
ms.author: saudas
ms.openlocfilehash: d8da717b83b43395309c695a4f9edaeda8144a8b
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49379191"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Podporované verze Kubernetes ve službě Azure Kubernetes Service (AKS)

Komunitě Kubernetes verze dílčí verze přibližně jednou za tři měsíce. Tyto verze zahrnují nové funkce a vylepšení. Vydání opravy jsou častější (někdy týdně) a jsou určeny pouze pro důležité opravy chyb v dílčí verze. Tyto opravy verze zahrnují opravy pro chyby zabezpečení nebo hlavní chyby vliv na velký počet zákazníků a produktů, které běží v produkčním prostředí založené na Kubernetes.

Dílčí verze je k dispozici v nové Kubernetes [acs-engine] [ acs-engine] na jeden den. AKS cíl SLO (Service Level) cíle pro AKS vydání vedlejší verze clusterů do 30 dní v souladu s stabilitu vydání.

## <a name="kubernetes-version-support-policy"></a>Zásady podpory verze Kubernetes

AKS podporuje čtyři dílčí verze Kubernetes:

- Aktuální podverzi, která je všeobecně dostupné nadřazeného (n)
- Předchozí tři dílčí verze. Všechny podporované vedlejší verze také podporuje dva stabilní opravy.

Například, pokud představuje AKS *1.11.x* v současné době také poskytuje podporu pro *1.10.a* + *1.10.b*, *1.9.c*  +  *1.9d*, *1.8.e* + *1.8F* (kde lettered aktualizací jsou dvě poslední stabilní sestavení).

Pokud je zavedená nová podverze, byly ukončeny nejstarší vedlejší verze a opravy verze nepodporuje. 15 dnů před vydáním nové podverze a nadcházející verzi vyřazení, oznámení se provádí prostřednictvím Azure aktualizačních kanálech. V příkladu výše where *1.11.x* je všeobecně dostupné, jsou vyřazené verze *1.7.g* + *1.7.h*.

Když nasadíte cluster AKS na portálu nebo pomocí Azure CLI, clusteru je vždycky nastavený na n-1 podverze a nejnovější opravy. Například, pokud podporuje AKS *1.11.x*, *1.10.a* + *1.10.b*, *1.9.c* + *1.9 d* , *1.8.e* + *1.8F*, je výchozí verze nových clusterů *1.10.b*.

## <a name="list-currently-supported-versions"></a>Seznam aktuálně podporované verze

Chcete-li zjistit, jaké verze jsou aktuálně dostupné pro vaše předplatné a oblast, použijte [az aks get-versions] [ az-aks-get-versions] příkazu. Následující příklad zobrazí seznam dostupných verzí Kubernetes pro *EastUS* oblasti:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

Výstup je podobný následujícím příkladu, který ukazuje, že Kubernetes verze *1.11.3* je k dispozici nejnovější verze:

```
KubernetesVersion    Upgrades
-------------------  ----------------------
1.11.3               None available
1.11.2               1.11.3
1.10.8               1.11.2, 1.11.3
1.10.7               1.10.8, 1.11.2, 1.11.3
1.9.10               1.10.7, 1.10.8
1.9.9                1.9.10, 1.10.7, 1.10.8
1.8.15               1.9.9, 1.9.10
1.8.14               1.8.15, 1.9.9, 1.9.10
```

## <a name="faq"></a>Nejčastější dotazy

**Co se stane, když zákazník upgraduje cluster Kubernetes pomocí vedlejší verze, která není podporována?**

Pokud používáte *n-4* verze, které jsou mimo cíle na úrovni služby. Pokud váš upgrade z verze n-4 n-3 úspěšné, pak jste zpátky v cíle na úrovni služby. Příklad:

- Pokud jsou podporované verze AKS *1.10.a* + *1.10.b*, *1.9.c* + *1.9d*, *1.8.e* + *1.8F* a jsou na *1.7.g* nebo *1.7.h*, jsou mimo cíle na úrovni služby.
- Pokud se upgrade z *1.7.g* nebo *1.7.h* k *1.8.e* nebo *1.8.f* proběhne úspěšně, jste zpátky v cíle na úrovni služby.

Upgrady na verzi starší než *n-4* nejsou podporovány. V takovém případě doporučujeme zákazníkům vytvoření nových clusterů AKS a znovu nasadit své úlohy.

**Co se stane, když zákazník škáluje cluster Kubernetes pomocí vedlejší verze, která není podporována?**

Pro dílčí verze není podporována službou AKS škálování snížení nebo navýšení kapacity i nadále fungovat bez problémů.

**Může zákazník zůstat na Kubernetes verze navždy?**

Ano. Pokud ale clusteru není na jednu z verzí podporované službou AKS, clusteru je mimo cíle na úrovni služby AKS. Azure automaticky upgradovat cluster nebo ho odstranit.

**Jaká verze nemá hlavní podpory, pokud agent clusteru není v jednom z podporovaných verzí AKS?**

Hlavní server se automaticky aktualizují na nejnovější verzi.

## <a name="next-steps"></a>Další postup

Informace o tom, jak upgradovat váš cluster najdete v tématu [Upgrade clusteru Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions