---
title: Podporované verze Kubernetes ve službě Azure Kubernetes
description: Zásady podpory verze Kubernetes a životního cyklu clusterů ve službě Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 02/15/2018
ms.author: saudas
ms.openlocfilehash: 37a9712749a1575f81086d28ad461a665bef36d9
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313445"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Podporované verze Kubernetes ve službě Azure Kubernetes Service (AKS)

Komunita Kubernetes vydává dílčí verze přibližně každé tři měsíce. Tato vydání obsahují nové funkce a vylepšení. Opravná vydání jsou častější (někdy i každý týden) a slouží jen k opravám kritických chyb v dílčí verzi. Tyto opravy verze zahrnují opravy pro chyby zabezpečení nebo hlavní chyby vliv na velký počet zákazníků a produktů, které běží v produkčním prostředí založené na Kubernetes.

Dílčí verze je k dispozici v nové Kubernetes [aks-engine] [ aks-engine] na jeden den. AKS Service Level Objective (SLO) má za cíl vydat dílčí verzi pro clustery AKS během 30 dní, v závislosti na stabilitě vydané verze.

## <a name="kubernetes-version-support-policy"></a>Zásady podpory verzí Kubernetes

AKS podporuje čtyři dílčí verze Kubernetes:

- Aktuální podverzi, která je všeobecně dostupné nadřazeného (n)
- Tři předchozí dílčí verze. Každá podporovaná dílčí verze také podporuje dvě stabilní opravy.

Například, pokud představuje AKS *1.12.x* v současné době také poskytuje podporu pro *1.11.a* + *1.11.b*, *1.10.c*  +  *1.10d*, *1.9.e* + *1.9f* (kde lettered aktualizací jsou dvě poslední stabilní sestavení).

Při uvedení nové dílčí verze se přestane podporovat nejstarší dílčí verze a její opravná vydání. 15 dnů před vydáním nové podverze a nadcházející verzi vyřazení, oznámení se provádí prostřednictvím [Azure aktualizačních kanálech][azure-update-channel]. V příkladu výše where *1.12.x* je všeobecně dostupné, jsou vyřazené verze *1.8.g* + *1.8.h*.

Když nasadíte cluster AKS na portálu nebo pomocí Azure CLI, cluster se vždycky nastaví na dílčí verzi n-1 a nejnovější opravu. Například, pokud podporuje AKS *1.12.x*, *1.11.a* + *1.11.b*, *1.10.c*  +   *1.10d*, *1.9.e* + *1.9f*, je výchozí verze nových clusterů *1.10.b*.

## <a name="list-currently-supported-versions"></a>Seznam aktuálně podporované verze

Chcete-li zjistit, jaké verze jsou aktuálně dostupné pro vaše předplatné a oblast, použijte [az aks get-versions] [ az-aks-get-versions] příkazu. Následující příklad zobrazí seznam dostupných verzí Kubernetes pro *EastUS* oblasti:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

Výstup je podobný následujícím příkladu, který ukazuje, že Kubernetes verze *1.12.5* je k dispozici nejnovější verze:

```
KubernetesVersion    Upgrades
-------------------  -----------------------
1.12.5               None available
1.12.4               1.12.5
1.11.7               1.12.4, 1.12.5
1.11.6               1.11.7, 1.12.4, 1.12.5
1.10.12              1.11.6, 1.11.7
1.10.9               1.10.12, 1.11.6, 1.11.7
1.9.11               1.10.9, 1.10.12
1.9.10               1.9.11, 1.10.9, 1.10.12
```

## <a name="faq"></a>Nejčastější dotazy

**Co se stane, když zákazník upgraduje cluster Kubernetes pomocí vedlejší verze, která není podporována?**

Pokud používáte *n-4* verze, které jsou mimo cíle na úrovni služby. Pokud váš upgrade z verze n-4 n-3 úspěšné, pak jste zpátky v cíle na úrovni služby. Příklad:

- Pokud jsou podporované verze AKS *1.12.x*, *1.11.a* + *1.11.b*, *1.10.c*  +  *1.10d*, a *1.9.e* + *1.9f* a jsou na *1.8.g* nebo *1.8.h*, jsou mimo cíle na úrovni služby.
- Pokud se upgrade z *1.8.g* nebo *1.8.h* k *1.9.e* nebo *1.9.f* proběhne úspěšně, jste zpátky v cíle na úrovni služby.

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
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
