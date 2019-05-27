---
title: Podporované verze Kubernetes ve službě Azure Kubernetes
description: Zásady podpory verze Kubernetes a životního cyklu clusterů ve službě Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: a7b3176e39ccaa0f9ddb1ef45c33ec6902e62f1c
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956323"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Podporované verze Kubernetes ve službě Azure Kubernetes Service (AKS)

Komunita Kubernetes vydává dílčí verze přibližně každé tři měsíce. Tato vydání obsahují nové funkce a vylepšení. Opravná vydání jsou častější (někdy i každý týden) a slouží jen k opravám kritických chyb v dílčí verzi. Tyto opravy verze zahrnují opravy pro chyby zabezpečení nebo hlavní chyby vliv na velký počet zákazníků a produktů, které běží v produkčním prostředí založené na Kubernetes.

Dílčí verze je k dispozici v nové Kubernetes [aks-engine] [ aks-engine] na jeden den. AKS Service Level Objective (SLO) má za cíl vydat dílčí verzi pro clustery AKS během 30 dní, v závislosti na stabilitě vydané verze.

## <a name="kubernetes-version-support-policy"></a>Zásady podpory verzí Kubernetes

AKS podporuje čtyři dílčí verze Kubernetes:

- Aktuální podverzi, která je všeobecně dostupné nadřazeného (n)
- Tři předchozí dílčí verze. Každá podporovaná dílčí verze také podporuje dvě stabilní opravy.

Například, pokud představuje AKS *1.13.x* v současné době také poskytuje podporu pro *1.12.a* + *1.12.b*, *1.11.c*  +  *1.11d*, *1.10.e* + *1.10F* (kde lettered aktualizací jsou dvě poslední stabilní sestavení).

Při uvedení nové dílčí verze se přestane podporovat nejstarší dílčí verze a její opravná vydání. 30 dní před vydáním nové podverze a nadcházející verzi vyřazení, oznámení se provádí prostřednictvím [Azure aktualizačních kanálech][azure-update-channel]. V příkladu výše where *1.13.x* je všeobecně dostupné, jsou vyřazené verze *1.9.g* + *1.9.h*.

Když nasadíte cluster AKS na portálu nebo pomocí Azure CLI, cluster se vždycky nastaví na dílčí verzi n-1 a nejnovější opravu. Například, pokud podporuje AKS *1.13.x*, *1.12.a* + *1.12.b*, *1.11.c*  +   *1.11d*, *1.10.e* + *1.10F*, je výchozí verze nových clusterů *1.11.b*.

## <a name="list-currently-supported-versions"></a>Seznam aktuálně podporované verze

Chcete-li zjistit, jaké verze jsou aktuálně dostupné pro vaše předplatné a oblast, použijte [az aks get-versions] [ az-aks-get-versions] příkazu. Následující příklad zobrazí seznam dostupných verzí Kubernetes pro *EastUS* oblasti:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

Výstup je podobný následujícím příkladu, který ukazuje, že Kubernetes verze *1.13.5* je k dispozici nejnovější verze:

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.13.5               None available
1.12.7               1.13.5
1.12.6               1.12.7, 1.13.5
1.11.9               1.12.6, 1.12.7
1.11.8               1.11.9, 1.12.6, 1.12.7
1.10.13              1.11.8, 1.11.9
1.10.12              1.10.13, 1.11.8, 1.11.9
```

## <a name="faq"></a>Nejčastější dotazy

**Co se stane, když zákazník upgraduje cluster Kubernetes pomocí vedlejší verze, která není podporována?**

Pokud používáte *n-4* verze, které jsou mimo cíle na úrovni služby. Pokud váš upgrade z verze n-4 n-3 úspěšné, pak jste zpátky v cíle na úrovni služby. Příklad:

- Pokud jsou podporované verze AKS *1.13.x*, *1.12.a* + *1.12.b*, *1.11.c*  +  *1.11d*, a *1.10.e* + *1.10F* a jsou na *1.9.g* nebo *1.9.h*, jsou mimo cíle na úrovni služby.
- Pokud se upgrade z *1.9.g* nebo *1.9.h* k *1.10.e* nebo *1.10.f* proběhne úspěšně, jste zpátky v cíle na úrovni služby.

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
