---
title: Podporované verze Kubernetes ve službě Azure Kubernetes
description: Zásady podpory verze Kubernetes a životního cyklu clusterů ve službě Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: 2d555908007f4e43a38b6d0eff909ef5050878ea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069678"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Podporované verze Kubernetes ve službě Azure Kubernetes Service (AKS)

Komunita Kubernetes vydává dílčí verze přibližně každé tři měsíce. Tato vydání obsahují nové funkce a vylepšení. Opravná vydání jsou častější (někdy i každý týden) a slouží jen k opravám kritických chyb v dílčí verzi. Tyto opravy verze zahrnují opravy pro chyby zabezpečení nebo hlavní chyby vliv na velký počet zákazníků a produktů, které běží v produkčním prostředí založené na Kubernetes.

Cílem AKS je certifikovat a vydat nové verze Kubernetes do 30 dní od nadřazeného vydání, v souladu s stabilitu vydání.

## <a name="kubernetes-versions"></a>Verze Kubernetes

Kubernetes používá standardní [Semantic Versioning](https://semver.org/) schéma vytváření verzí. To znamená, že každou verzi Kubernetes následuje toto schéma:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
  1.13.7
```

Všechna čísla ve verzi označuje obecné kompatibility s předchozí verzí:

* Změna hlavních verzí při nekompatibilní změny rozhraní API nebo zpětné kompatibility je pravděpodobně poškozen.
* Dílčí verze změnit, pokud jsou provedeny změny funkce, které jsou zpětně kompatibilní s další vedlejší verze.
* Oprava verze jsou provedené změny při opravy chyb zpětně kompatibilní.

Obecně platí, musí uživatelé endeavor spustit nejnovější vydané verzi opravy vedlejší verze jsou spuštěné, například pokud vaše produkční cluster nachází v *1.13.6* a *1.13.7* je nejnovější dostupné opravy k dispozici pro verzi *1.13* řady, byste měli upgradovat na *1.13.7* také budete moct zajistit clusteru je plně opravují se a.

## <a name="kubernetes-version-support-policy"></a>Zásady podpory verzí Kubernetes

AKS podporuje čtyři dílčí verze Kubernetes:

* Aktuální podverzi, která je vydána v AKS (N)
* Tři předchozí dílčí verze. Každá podporovaná dílčí verze také podporuje dvě stabilní opravy.

To se označuje jako "N-3"-(N (nejnovější vydaná verze) - 3 (dílčí verze)).

Například, pokud představuje AKS *1.13.x* v současné době podpora se poskytuje pro tyto verze:

Nový dílčí verze seznamu podporované verze
-----------------        ----------------------
1.13.x 1.12.a 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b

Kde "x" a ".a" a ".b" jsou reprezentativní oprava.

Podrobnosti o komunikaci týkající se změny verze a očekávané chování naleznete v tématu "Komunikace" níže.

Pokud je zavedená nová podverze, nejstarší vedlejší verze a opravy verze nepodporuje jsou zastaralé a odebrat. Například pokud aktuálně podporovaný seznam verze je:

<a name="supported-version-list"></a>Seznam podporovaných verzí
----------------------
1.12.a, 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b, 1.9.a, 1.9.b

A AKS uvolní 1.13.x, to znamená, že se odeberou 1.9.x verze (všechny verze 1.9) a podpora skončí.

> [!NOTE]
> Mějte prosím na paměti, že pokud zákazníci běží nepodporované verze Kubernetes, bude se mu zobrazit výzva při žádosti o podporu pro cluster upgradovat. Clustery se systémem Nepodporovaná verze Kubernetes nejsou pokryté smlouvou [AKS podporují zásady](https://docs.microsoft.com/azure/aks/support-policies).


Kromě výše uvedeného na dílčí verze AKS podporuje dva nejnovější *opravy** verzemi dané podverze. Mějme například následující podporované verze:

<a name="supported-version-list"></a>Seznam podporovaných verzí
----------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5

Pokud upstreamový Kubernetes uvolněn 1.12.3 a 1.11.6 a uvolní AKS ty oprava verze, nejstarší verze opravy jsou zastaralé a odebrat, a změní seznam podporovaných verzí:

<a name="supported-version-list"></a>Seznam podporovaných verzí
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*

### <a name="communications"></a>Komunikace

* U nových **menší** verze Kubernetes
  * Všichni uživatelé budou upozorněni na novou verzi a verzi se odeberou.
  * Zákazníci, kteří používají verzi **odeberou** budete upozorněni, ke kterým mají **60 dnů** upgradovat na podporovanou verzi (např. dílčí verze).
* U nových **opravy** verze Kubernetes
  * Všem uživatelům se zobrazí oznámení na novou verzi opravy se vydávají a upgradovat na nejnovější vydané verzi opravy.
  * Uživatelé mají **30 dnů** k upgradu na verzi novější, podporovaných opravy. Uživatelé mají **30 dnů** k upgradu na verzi podporovaná oprava než nejstarší se odebere.

AKS definuje "vydání" jako obecné dostupnosti, povolené v všechny cíle na úrovni služby / kvalitní služby měření a dostupné ve všech oblastech.

> [!NOTE]
> Zákazníci budou upozorněni Kubernetes verze uvolní a posíláme upozornění, když dílčí verze je zastaralá nebo odebrání uživatele disponují 60 dnů upgradovat na podporovanou verzi. V případě aktualizací zákazníci disponují 30 dnů upgradovat na podporovanou verzi.

Oznámení se odesílají prostřednictvím:

* [Zpráva k vydání verze AKS](https://aka.ms/aks/releasenotes)
* Oznámení portálu Azure portal
* [Kanál aktualizací Azure][azure-update-channel]

### <a name="policy-exceptions"></a>Výjimky zásad

AKS si vyhrazuje právo Přidat nebo odebrat nové nebo existující verze, které byly identifikovány mít jeden nebo více důležité produkční dopadu na chyby nebo problémy se zabezpečením bez předem.

Daná oprava verzích se může přeskočit, nebo urychlení uvedení v závislosti na závažnosti problému chyby nebo zabezpečení.

### <a name="azure-portal-and-cli-default-versions"></a>Výchozí verze rozhraní příkazového řádku a webu Azure portal

Když nasadíte cluster AKS na portálu nebo pomocí Azure CLI, clusteru je vždycky nastavený na N-1 podverze a nejnovější opravy. Například, pokud podporuje AKS *1.13.x*, *1.12.a* + *1.12.b*, *1.11.a*  +   *1.11.b*, *1.10.a* + *1.10b*, je výchozí verze nových clusterů *1.12.b*.

AKS výchozí hodnota je N-1 (minor.latestPatch, třeba 1.12.b), aby zákazníkům poskytovalo známé, stabilní a opravit verze ve výchozím nastavení.

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

Pokud používáte *n-4* verze, se nachází mimo podporu a budete vyzváni k upgradu. Pokud váš upgrade z verze n-4 n-3 úspěšné, nyní jste v rámci naší zásady poskytování podpory. Příklad:

- Pokud jsou podporované verze AKS *1.13.x*, *1.12.a* + *1.12.b*, *1.11.c*  +  *1.11d*, a *1.10.e* + *1.10F* a jsou na *1.9.g* nebo *1.9.h*, se nachází mimo podpory.
- Pokud se upgrade z *1.9.g* nebo *1.9.h* k *1.10.e* nebo *1.10.f* proběhne úspěšně, jste zpátky v v rámci naší zásady poskytování podpory.

Upgrady na verzi starší než *n-4* nejsou podporovány. V takovém případě doporučujeme zákazníkům vytvoření nových clusterů AKS a znovu nasadit své úlohy.

**Co znamená z podpory**

'Mimo podpory"znamená, že je verze, které jsou spuštěné mimo daný seznam podporovaných verzí a zobrazí se výzva k upgradu clusteru na podporovanou verzi, žádosti o podporu. Kromě toho AKS Nedovolte, aby byly všechny runtime nebo jiných záruky pro clustery mimo daný seznam podporovaných verzí.

**Co se stane, když zákazník škáluje cluster Kubernetes pomocí vedlejší verze, která není podporována?**

Pro dílčí verze není podporována službou AKS škálování snížení nebo navýšení kapacity i nadále fungovat bez problémů.

**Může zákazník zůstat na Kubernetes verze navždy?**

Ano. Pokud ale clusteru není na jednu z verzí podporované službou AKS, clusteru je mimo zásady podpory AKS. Azure automaticky upgradovat cluster nebo ho odstranit.

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
