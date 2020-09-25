---
title: Podporované verze Kubernetes ve službě Azure Kubernetes Service
description: Principy zásad podpory a životního cyklu clusterů ve službě Azure Kubernetes (AKS) verze Kubernetes
services: container-service
ms.topic: article
ms.date: 09/08/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: a36eb588c7128f13fb21b368d308ed00171fbb4b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335530"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Podporované verze Kubernetes ve službě Azure Kubernetes Service (AKS)

Komunita Kubernetes vydává dílčí verze zhruba každé tři měsíce. Nedávno komunita Kubernetes [zvýšila okno podpory pro každou verzi z 9 měsíců na 12 měsíců](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/)počínaje verzí 1,19. Tyto verze obsahují nové funkce a vylepšení. Verze oprav jsou častěji časté (někdy týdně) a jsou určené pro kritické opravy chyb v rámci dílčí verze. Tyto verze oprav obsahují opravy chyb zabezpečení nebo hlavní chyby.

## <a name="kubernetes-versions"></a>Verze Kubernetes

Kubernetes používá standardní [sémantickou](https://semver.org/) verzi schématu správy verzí, což znamená, že každá verze Kubernetes odpovídá tomuto schématu číslování:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Každé číslo ve verzi označuje obecnou kompatibilitu s předchozí verzí:

* Hlavní verze se mění v případě nekompatibilních změn rozhraní API nebo poškození zpětné kompatibility.
* Vedlejší verze se změní, když jsou provedeny změny funkčnosti, které jsou zpětně kompatibilní s ostatními podverzemi.
* Opravy verzí se mění, když jsou provedeny zpětně kompatibilní opravy chyb.

Uživatelé by se měli snažit spustit nejnovější verzi opravy podverze, kterou používají. například pokud je váš provozní cluster zapnutý a má k dispozici **`1.17.7`** **`1.17.8`** nejnovější dostupnou verzi opravy pro řadu *1,17* , měli byste upgradovat na hned **`1.17.8`** , jak budete moci, abyste měli jistotu, že je cluster plně opravený a podporovaný.

## <a name="kubernetes-version-support-policy"></a>Zásady podpory verze Kubernetes

AKS definuje obecně dostupnou verzi jako verzi, která je povolená ve všech měřeních SLO nebo SLA, a když je dostupná ve všech oblastech. AKS podporuje tři vedlejší verze GA pro Kubernetes:

* Nejnovější podverze GA vydaná v AKS (na kterou odkazujeme jako N).
* Dvě předchozí vedlejší verze.
* Každá podporovaná dílčí verze také podporuje maximálně dvě (2) stabilní opravy.
* AKS může podporovat i verze Preview, které jsou explicitně označené a podléhají podmínkám [a ujednáním ve verzi Preview][preview-terms].

> [!NOTE]
> AKS používá bezpečné postupy nasazení, které zahrnují nasazení postupných oblastí. To znamená, že může trvat až 10 pracovních dní pro nové vydání nebo novou verzi, která bude dostupná ve všech oblastech.

Podporované okno verzí Kubernetes v AKS se označuje jako "N-2": (N (nejnovější verze)-2 (dílčí verze)).

Například pokud AKS zavádí *1.17. a* ještě dnes, podpora je k dispozici pro následující verze:

Nová dílčí verze    |    Seznam podporovaných verzí
-----------------    |    ----------------------
1.17. a               |    1.17. a, 1.17. b, 1.16. c, 1.16. d, 1.15. e, 1.15. f

Kde ". Letter" představuje zástupce verzí oprav.

Pokud je zavedena nová podverze, je podpora nejstarší dílčí verze a vydání oprav zastaralá a odebrána. Pokud je například aktuální podporovaná verze seznam:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

A AKS verze 1,18. to \* znamená, že všechny 1,15. \* verze se odeberou a nebudou se podporovat po dobu 30 dnů.

> [!NOTE]
> Upozorňujeme, že pokud na zákaznících běží Nepodporovaná verze Kubernetes, zobrazí se jim výzva k upgradu při žádosti o podporu pro cluster. Clustery, na kterých běží Nepodporovaná vydání Kubernetes, se nevztahují na [zásady podpory AKS](./support-policies.md).

Kromě výše uvedeného AKS podporuje maximálně dvě verze **opravy** dané dílčí verze. Proto byly zadány následující podporované verze:

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

Pokud AKS verze `1.17.9` a `1.16.11` , nejstarší verze opravy jsou zastaralé a odebrané a seznam podporovaných verzí bude:

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>Podporované `kubectl` verze

Můžete použít jednu podverzi starší nebo novější `kubectl` vzhledem k vaší verzi *Kube-apiserver* , která je v souladu se [zásadami podpory Kubernetes pro kubectl](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl).

Například pokud je vaše *Kube-apiserver* v *1,17*, můžete použít verze *1,16* pro *1,18* `kubectl` s tímto *Kube-apiserver*.

Chcete-li nainstalovat nebo aktualizovat svou verzi `kubectl` , spusťte příkaz `az aks install-cli` .

## <a name="release-and-deprecation-process"></a>Proces vydání a vyřazení

V kalendáři vydaných verzí [AKS Kubernetes](#aks-kubernetes-release-calendar)můžete odkazovat na nadcházející verze verzí a zastaralá vydání.

Pro nové **menší** verze Kubernetes
1. AKS publikuje před odebráním předem oznámení s plánovaným datem vydání nové verze a vyřazením původní verze v [poznámkách k verzi AKS](https://aka.ms/aks/releasenotes) nejméně 30 dní před odebráním.
2. AKS zveřejňuje [oznámení o stavu služby](../service-health/service-health-overview.md) dostupné všem uživatelům s přístupem k AKS a portálu a pošle jim e-mail správcům předplatného s plánovanými datem odebrání verze.
3. Odebráním verze na podporu v případě, že uživatelé budou moci pokračovat v poskytování podpory, mají **30 dnů** od odebrání verze k upgradu na podporovanou verzi

Pro nové verze **patch** Kubernetes
  * Z důvodu naléhavé povahy opravných verzí je lze do služby začlenit, jakmile budou k dispozici.
  * Obecně platí, že AKS neprovádí rozsáhlou komunikaci pro vydání nových verzí patch. AKS však nepřetržitě monitoruje a ověřuje dostupné aktualizace, které jsou k dispozici, a včas je podporuje v AKS. Pokud je nalezena kritická oprava nebo je vyžadována akce uživatele, AKS upozorní uživatele na upgrade na nově dostupnou opravu.
  * Uživatelé mají **30 dní** od okamžiku, kdy se odebere oprava z AKS k upgradu na podporovanou opravu a nadále přijímá podporu.

### <a name="supported-versions-policy-exceptions"></a>Výjimky zásad podporovaných verzí

AKS si vyhrazuje právo přidat nebo odebrat nové/existující verze, které byly identifikovány tak, aby měly jednu nebo více kritických chybových dopadů na produkční prostředí bez předchozího upozornění.

Konkrétní vydané verze oprav se můžou přeskočit nebo se zrychlí v závislosti na závažnosti chyby nebo problému se zabezpečením.

## <a name="azure-portal-and-cli-versions"></a>Verze Azure Portal a CLI

Když nasadíte cluster AKS na portálu nebo pomocí rozhraní příkazového řádku Azure CLI, cluster se nastaví jako výchozí v podverzi N-1 a nejnovější oprava. Například pokud AKS podporuje *1.17. a*, *1.17. b*, *1.16. c*, *1.16. d*, *1.15. e*a *1.15. f*, vybraná výchozí verze je *1.16. c*.

Pokud chcete zjistit, jaké verze jsou aktuálně k dispozici pro vaše předplatné a oblast, použijte příkaz [AZ AKS get-versions][az-aks-get-versions] . Následující příklad uvádí dostupné verze Kubernetes pro oblast *EastUS* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>AKS Kubernetes – kalendář vydání

Historii předchozích verzí najdete [tady](https://en.wikipedia.org/wiki/Kubernetes#History).

|  Verze K8s | Verze nadřazeného softwaru  | AKS ve verzi Preview  | AKS GA  | Konec života |
|--------------|-------------------|--------------|---------|-------------|
| 1.16  | SEP-19-19  | Leden 2019   | Březen 2020  | 1,19 GA | 
| 1,17  | Dec-09-19  | Leden 2019   | Července 2020  | 1,20 GA | 
| 1,18  | Březen – 23-20  | Květen 2020   | Srpna 2020  | 1,21 GA | 
| 1,19  | Srpna 04-20  | SEP 2020   | Listopadu 2020  | 1,22 GA | 
| 1,20  | DEC-08-20  | Leden 2021   | Březen 2021  | 1,23 GA | 

## <a name="faq"></a>Časté otázky

**Jak často mám očekávat, že upgradují verze Kubernetes, aby zůstaly v podpoře?**

Informování s Kubernetes 1,19, [Open Source komunita rozšiřuje podporu na 1 rok](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/). AKS potvrzení, aby se povolily opravy a podpora s minimálním počtem nadřazených závazků. To znamená, že se od AKS clusterů v 1,19 bude možné upgradovat minimálně jednou ročně, aby zůstala v podporované verzi. U verzí v 1,18 nebo níže je okno podpora ponecháno v 9 měsících, který vyžaduje upgrade jednou za 9 měsíců, aby zůstala v podporované verzi. Důrazně doporučujeme pravidelně testovat nové verze a připravit se na upgrade na novější verze, abyste mohli zachytit nejnovější stabilní vylepšení v rámci Kubernetes.

**Co se stane, když uživatel upgraduje cluster Kubernetes s menší verzí, která se nepodporuje?**

Pokud používáte verzi *n-3* nebo starší, znamená to, že nejste mimo rámec podpory, a zobrazí se výzva k upgradu. Po úspěšném dokončení upgradu z verze n-3 na n-2 se vrátíte do našich zásad podpory. Příklad:

- Pokud je nejstarší podporovaná verze AKS *1.15.* a a jste na *1.14. b* nebo starší, nejste mimo podporu.
- Po úspěšném dokončení upgradu z aplikace *1.14. b* na *1.15. a* nebo vyšší se vrátíte do našich zásad podpory.

Downgrady nejsou podporovány.

**Co znamená "mimo" střední část podpory "**

' Mimo podporu ' znamená, že verze, kterou používáte, je mimo seznam podporovaných verzí. při žádosti o podporu budete požádáni o upgrade clusteru na podporovanou verzi, pokud nebudete v období odkladu po vyřazení verze 30 dní. AKS navíc neprovádí žádné moduly runtime ani jiné záruky pro clustery mimo seznam podporovaných verzí.

**Co se stane, když uživatel škáluje cluster Kubernetes s menší verzí, která se nepodporuje?**

Pro menší verze, které AKS nepodporuje, by mělo funkce škálování nebo oddálení fungovat, ale neexistují žádné záruky kvality služeb, proto se důrazně doporučuje upgradovat, aby se cluster znovu podporoval.

**Může uživatel trvale zůstat ve verzi Kubernetes?**

Pokud byl cluster nepodporován pro více než tři (3) menší verze a byl nalezen pro rizika zabezpečení, Azure vám umožní proaktivně upgradovat cluster. Pokud neprovedete žádnou akci, Azure si vyhrazuje právo automaticky upgradovat cluster vaším jménem.

**Jakou verzi ovládací plocha podporuje, pokud fond uzlů není v některé z podporovaných verzí AKS?**

Rovina ovládacího prvku musí být v rámci okna verzí ze všech fondů uzlů. Podrobnosti o upgradu roviny ovládacího prvku nebo fondů uzlů najdete v dokumentaci k [upgradu fondů uzlů](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

**Můžu během upgradu clusteru přeskočit několik verzí AKS?**

Pokud upgradujete podporovaný cluster AKS, nelze podverze Kubernetes vynechat. Například upgrady mezi *1.12. x*  ->  *1.13. x* nebo *1.13. x*  ->  *1.14. x* jsou povoleny, ale *1.12. x*  ->  *1.14. x* není.

Chcete-li provést upgrade, z *1.12. x*  ->  *1.14. x*, nejprve upgradujte z *1.12. x*  ->  *1.13. x*a potom proveďte upgrade z *1.13. x*  ->  *1.14. x*.

Přeskočení více verzí lze provést pouze při upgradu z nepodporované verze zpět do podporované verze. Například upgradujte z nepodporovaného prvku *1.10. x* --> podporovanou *1.15. x* je možné dokončit.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak upgradovat cluster, najdete v tématu [upgrade clusteru Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
