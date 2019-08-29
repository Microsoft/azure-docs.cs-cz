---
title: Podporované verze Kubernetes ve službě Azure Kubernetes Service
description: Principy zásad podpory a životního cyklu clusterů ve službě Azure Kubernetes (AKS) verze Kubernetes
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: 27b180d8d95d7dad967b8ac2495a795ed70836b9
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147223"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Podporované verze Kubernetes ve službě Azure Kubernetes Service (AKS)

Komunita Kubernetes vydává dílčí verze přibližně každé tři měsíce. Tato vydání obsahují nové funkce a vylepšení. Opravná vydání jsou častější (někdy i každý týden) a slouží jen k opravám kritických chyb v dílčí verzi. Tyto verze oprav obsahují opravy chyb zabezpečení a závažné chyby, které mají vliv na velký počet zákazníků a produktů provozovaných v produkčním prostředí založeném na Kubernetes.

AKS se zaměřuje na certifikaci a vydání nových verzí Kubernetes do 30 dnů od verze nadřazeného vydaných verzí, a to v souladu s touto stabilitou.

## <a name="kubernetes-versions"></a>Verze Kubernetes

Kubernetes používá standardní [sémantickou](https://semver.org/) verzi schématu správy verzí. To znamená, že každá verze Kubernetes se řídí tímto schématem číslování:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Každé číslo ve verzi označuje obecnou kompatibilitu s předchozí verzí:

* Hlavní verze se mění v případě nekompatibilních změn rozhraní API nebo poškození zpětné kompatibility.
* Vedlejší verze se změní, když jsou provedeny změny funkčnosti, které jsou zpětně kompatibilní s ostatními podverzemi.
* Opravy verzí se mění, když jsou provedeny zpětně kompatibilní opravy chyb.

Obecně platí, že by se uživatelé měli Endeavor spustit nejnovější verzi opravy podverze, kterou používají. Pokud je například váš provozní cluster na *1.12.14* a *1.12.15* je dostupná nejnovější dostupná verze opravy pro řadu *1,12* . , měli byste upgradovat na *1.12.15* , jakmile budete mít jistotu, že je váš cluster plně opravený a podporovaný.

## <a name="kubernetes-version-support-policy"></a>Zásady podpory verzí Kubernetes

AKS podporuje čtyři dílčí verze Kubernetes:

* Aktuální dílčí verze vydaná v AKS (N)
* Tři předchozí dílčí verze. Každá podporovaná dílčí verze také podporuje dvě stabilní opravy.

To se označuje jako "N-3" – (N (nejnovější verze)-3 (podverze)).

Například pokud AKS zavádí *otázku 1.13. a* , je k dispozici podpora pro následující verze:

Nová dílčí verze    |    Seznam podporovaných verzí
-----------------    |    ----------------------
1.13. a               |    1.12. a, 1.12. b, 1.11. a, 1.11. b, 1.10. a, 1.10. b

Kde ". a" a ". b" jsou reprezentativními verzemi oprav. " "z" 1.13. a se může lišit od 1.12. a. Například 1.13.9 a 1.12.8.

Podrobnosti o komunikaci týkající se změn a očekávání verzí najdete v části "komunikace" níže.

Pokud je zavedena nová podverze, je podpora nejstarší dílčí verze a vydání oprav zastaralá a odebrána. Pokud je například aktuální seznam podporovaných verzí:

```
1.12.a
1.12.b
1.11.a
1.11.b
1.10.a
1.10.b
1.9.a
1.9.b
```

A AKS verze 1,13. *to znamená, že 1,9.* verze (všechny verze 1,9) se odeberou a nepodporují.

> [!NOTE]
> Upozorňujeme, že pokud na zákaznících běží Nepodporovaná verze Kubernetes, zobrazí se jim výzva k upgradu při žádosti o podporu pro cluster. Clustery, na kterých běží Nepodporovaná vydání Kubernetes, se nevztahují na [zásady podpory AKS](https://docs.microsoft.com/azure/aks/support-policies).


Kromě výše uvedených v podverzi AKS podporuje dvě nejnovější *opravy** verze dané dílčí verze. Například s ohledem na následující podporované verze:

```
Current Supported Version List
------------------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5
```

Pokud Kubernetes vydala 1.12.3 a 1.11.6 a AKS uvolní tyto verze opravy, nejstarší verze opravy jsou zastaralé a odebrané a seznam podporovaných verzí bude:

```
New Supported Version List
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*
```

> [!NOTE]
> Zákazníci by neměli připnout vytváření clusterů, CI ani jiné automatizované úlohy na konkrétní verze oprav. 

### <a name="communications"></a>Komunikace

* Pro nové **menší** verze Kubernetes
  * Všichni uživatelé budou veřejně vyrozuměni o nové verzi a jaká verze bude odebrána.
  * Při vydání nové verze opravy se ve stejnou chvíli odebere nejstarší verze opravy.
  * Zákazníci mají **60 dní** od data veřejné oznámení k upgradu na podporovanou verzi dílčí verze.
* Pro nové verze **patch** Kubernetes
  * Všem uživatelům se zobrazí oznámení o vydání nové verze opravy a k upgradu na nejnovější verzi opravy.
  * Upgrade na novější podporovanou verzi opravy pro uživatele má **30 dní** . Aby uživatelé před odebráním nejstarší verze nastavili upgrade na podporovanou verzi, mají **30 dní** .

AKS definuje "vydanou" verzi jako obecnou dostupnost, která je povolená ve všech měřeních na SLO/kvalitu služeb a dostupná ve všech oblastech.

> [!NOTE]
> Zákazníkům se zobrazí oznámení o vydáních verzí Kubernetes a zastaralosti, pokud je podverze zastaralá/odebraných uživatelů je 60 dní na upgrade na podporovanou verzi. V případě opravných verzí jsou zákazníkům po dobu 30 dní upgradováni na podporovanou verzi.

Oznámení se odesílají prostřednictvím:

* [Poznámky k verzi AKS](https://aka.ms/aks/releasenotes)
* Oznámení Azure Portal
* [Kanál pro aktualizaci Azure][azure-update-channel]

### <a name="policy-exceptions"></a>Výjimky zásad

AKS si vyhrazuje právo přidat nebo odebrat nové/existující verze, které byly identifikovány tak, aby měly jednu nebo více kritických chybových dopadů na produkční prostředí bez předchozího upozornění.

Konkrétní vydané verze oprav se můžou přeskočit nebo se zrychlí v závislosti na závažnosti chyby nebo problému se zabezpečením.

### <a name="azure-portal-and-cli-default-versions"></a>Výchozí verze Azure Portal a CLI

Když nasadíte cluster AKS na portálu nebo pomocí rozhraní příkazového řádku Azure CLI, cluster se vždycky nastaví na N-1 dílčí verzi a nejnovější opravu. Například pokud AKS podporuje *1.13. a*, *1.12. a* + *1.12. b*, *1.11. a* + *1.11. b*, *1.10. a* + *1.10 b*, je výchozí verze pro nové clustery *1.12. b.* .

AKS má výchozí hodnotu N-1 (moll. latestPatch, například 1.12. b), aby zákazníkům poskytovala známou, stabilní a opravenou verzi ve výchozím nastavení.

## <a name="list-currently-supported-versions"></a>Vypsat aktuálně podporované verze

Pokud chcete zjistit, jaké verze jsou aktuálně k dispozici pro vaše předplatné a oblast, použijte příkaz [AZ AKS get-versions][az-aks-get-versions] . Následující příklad uvádí dostupné verze Kubernetes pro oblast *EastUS* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```

Výstup je podobný následujícímu příkladu, který ukazuje, že je nejnovější dostupná verze Kubernetes verze *1.14.6* :

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.14.6               None available
1.14.5               1.14.6
1.13.10              1.14.5, 1.14.6
1.13.9               1.13.10, 1.14.5, 1.14.6
1.12.8               1.13.9, 1.13.10
1.12.7               1.12.8, 1.13.9, 1.13.10
1.11.10              1.12.7, 1.12.8
1.11.9               1.11.10, 1.12.7, 1.12.8
1.10.13              1.11.9, 1.11.10
1.10.12              1.10.13, 1.11.9, 1.11.10
```

## <a name="faq"></a>Nejčastější dotazy

**Co se stane, když zákazník upgraduje cluster Kubernetes s menší verzí, která není podporovaná?**

Pokud se nacházíte na *n-4* verzi, jste mimo podporu a budete vyzváni k upgradu. Pokud upgrade z verze n-4 na n-3 uspěje, jste teď v rámci našich zásad podpory. Příklad:

- Pokud podporované verze AKS jsou *1.13. a*, *1.12. b* + *1.12. c*, *1.11. d* + *1.11. e*a *1.10. f* + *1.10. g* a jste na *1,9. h* nebo *1.9. i* , jste mimo podporu.
- Pokud je upgrade z *1.9. h* nebo *1.9. i* na *1.10. f* nebo *1.10. g* úspěšný, jste zpátky v rámci našich zásad podpory.

Upgrady na verze starší než *n-4* nejsou podporovány. V takových případech doporučujeme zákazníkům vytvořit nové clustery AKS a znovu nasazovat své úlohy.

**Co znamená "mimo podporu"**

' Mimo podporu ' znamená, že verze, kterou používáte, je mimo seznam podporovaných verzí a při žádosti o podporu budete požádáni o upgrade clusteru na podporovanou verzi. AKS navíc neprovádí žádné moduly runtime ani jiné záruky pro clustery mimo seznam podporovaných verzí.

**Co se stane, když zákazník škáluje cluster Kubernetes s menší verzí, která není podporovaná?**

Pro menší verze, které AKS nepodporuje, se škálování nebo zmenšuje i nadále bez problémů.

**Může zákazník stále ve verzi Kubernetes zůstat?**

Ano. Pokud se ale cluster nepoužívá v některé z verzí, které podporuje AKS, cluster neplatí pro zásady podpory AKS. Azure neupgraduje cluster automaticky ani neodstraní.

**Jakou verzi má hlavní podpora v případě, že cluster agenta není v některé z podporovaných verzí AKS?**

Hlavní server se automaticky aktualizuje na nejnovější podporovanou verzi.

## <a name="next-steps"></a>Další postup

Informace o tom, jak upgradovat cluster, najdete v tématu [upgrade clusteru Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
