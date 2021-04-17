---
title: Podporované verze Kubernetes ve službě Azure Kubernetes Service
description: Principy zásad podpory a životního cyklu clusterů ve službě Azure Kubernetes (AKS) verze Kubernetes
services: container-service
ms.topic: article
ms.date: 03/29/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: fac2eb75d210a34f4c5cd50c4649921aadfcd5ee
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588492"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Podporované verze Kubernetes ve službě Azure Kubernetes Service (AKS)

Komunita Kubernetes vydává dílčí verze zhruba každé tři měsíce. V poslední době komunita Kubernetes [zvýšila okno podpory pro každou verzi z 9 měsíců na 12 měsíců](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/)počínaje verzí 1,19. 

Verze podverze obsahují nové funkce a vylepšení. Verze oprav jsou častěji časté (někdy týdně) a jsou určené pro kritické opravy chyb v rámci dílčí verze. Verze oprav obsahují opravy chyb zabezpečení nebo hlavní chyby.

## <a name="kubernetes-versions"></a>Verze Kubernetes

Kubernetes používá standardní [sémantickou](https://semver.org/) verzi schématu pro každou verzi:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Každé číslo ve verzi označuje obecnou kompatibilitu s předchozí verzí:

* **Hlavní verze** se změní, pokud se nekompatibilní aktualizace rozhraní API nebo dojde k přerušení kompatibility zpět.
* **Vedlejší verze** se změní, když jsou provedeny aktualizace funkčnosti, které jsou zpětně kompatibilní s ostatními podverzemi.
* **Opravy verzí** se mění, když jsou provedeny zpětně kompatibilní opravy chyb.

Cílem je spustit nejnovější verzi opravy, kterou používáte. Například váš provozní cluster je zapnutý **`1.17.7`** . **`1.17.8`** je dostupná nejnovější dostupná verze opravy pro řadu *1,17* . Měli byste upgradovat na **`1.17.8`** co nejdříve, abyste měli jistotu, že je váš cluster plně opravený a podporovaný.

## <a name="kubernetes-version-support-policy"></a>Zásady podpory verze Kubernetes

AKS definuje obecně dostupnou verzi jako verzi, která je povolená ve všech měřeních SLO nebo SLA a dostupná ve všech oblastech. AKS podporuje tři vedlejší verze GA pro Kubernetes:

* Nejnovější podverze GA vydaná v AKS (na kterou odkazujeme jako N).
* Dvě předchozí vedlejší verze.
    * Každá podporovaná dílčí verze také podporuje maximálně dvě (2) stabilní opravy.

AKS může podporovat i verze Preview, které jsou explicitně označené a podléhají podmínkám [a ujednáním ve verzi Preview][preview-terms].

> [!NOTE]
> AKS používá bezpečné postupy nasazení, které zahrnují nasazení postupných oblastí. To znamená, že může trvat až 10 pracovních dní pro nové vydání nebo novou verzi, která bude dostupná ve všech oblastech.

Podporované okno verzí Kubernetes v AKS se označuje jako "N-2": (N (nejnovější verze)-2 (dílčí verze)).

Například pokud AKS zavádí *1.17. a* ještě dnes, podpora je k dispozici pro následující verze:

Nová dílčí verze    |    Seznam podporovaných verzí
-----------------    |    ----------------------
1.17. a               |    1.17. a, 1.17. b, 1.16. c, 1.16. d, 1.15. e, 1.15. f

Kde ". Letter" představuje zástupce verzí oprav.

Pokud je zavedena nová podverze, je podpora nejstarší dílčí verze a vydání oprav zastaralá a odebrána. Například aktuální seznam podporovaných verzí:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

AKS Release 1,18. \* odeberete všechny 1,15. \* verze z podpory v průběhu 30 dnů.

> [!NOTE]
> Pokud na zákaznících běží Nepodporovaná verze Kubernetes, zobrazí se jim výzva k upgradu při žádosti o podporu pro cluster. Clustery, na kterých běží Nepodporovaná vydání Kubernetes, se nevztahují na [zásady podpory AKS](./support-policies.md).

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

V `kubectl` souladu se [zásadami podpory Kubernetes pro kubectl](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl)můžete použít jednu nebo novější verzi vzhledem k verzi *Kube-apiserver* .

Například pokud je vaše *Kube-apiserver* v *1,17*, můžete použít verze *1,16* pro *1,18* `kubectl` s tímto *Kube-apiserver*.

Chcete-li nainstalovat nebo aktualizovat svou verzi `kubectl` , spusťte příkaz `az aks install-cli` .

## <a name="release-and-deprecation-process"></a>Proces vydání a vyřazení

V kalendáři vydaných verzí [AKS Kubernetes](#aks-kubernetes-release-calendar)můžete odkazovat na nadcházející verze verzí a zastaralá vydání.

Pro nové **menší** verze Kubernetes:
  * AKS publikuje před odebráním předem oznámení s plánovaným datem vydání nové verze a vyřazením původní verze v [poznámkách k verzi AKS](https://aka.ms/aks/releasenotes) nejméně 30 dní před odebráním.
  * AKS používá [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) k upozornění uživatelů v případě, že nová verze způsobí, že v clusteru dojde k problémům z důvodu zastaralých rozhraní API. Azure Advisor se také používá k upozornění uživatele, pokud se v tuto chvíli nepodporují.
  * AKS zveřejňuje [oznámení o stavu služby](../service-health/service-health-overview.md) dostupné všem uživatelům s přístupem k AKS a portálu a pošle jim e-mail správcům předplatného s plánovanými datem odebrání verze.

    ````
    To find out who is your subscription administrators or to change it, please refer to [manage Azure subscriptions](../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator).
    ````
  * Odebráním verze na podporu v případě, že uživatelé budou moci pokračovat v poskytování podpory, mají **30 dnů** od odebrání verze k upgradu na podporovanou verzi

Pro nové verze **opravy** Kubernetes:
  * Z důvodu naléhavé povahy opravných verzí je lze začlenit do služby, jakmile budou k dispozici.
  * Obecně platí, že AKS neoznamuje v podstatě vydání nových verzí oprav. AKS však nepřetržitě monitoruje a ověřuje dostupné aktualizace, které jsou k dispozici, a včas je podporuje v AKS. Pokud je nalezena kritická oprava nebo je vyžadována akce uživatele, AKS upozorní uživatele na upgrade na nově dostupnou opravu.
  * Pro upgrade na podporovanou opravu a pokračování příjmu podpory mají uživatelé **30 dní** od odebrání opravy z AKS.

### <a name="supported-versions-policy-exceptions"></a>Výjimky zásad podporovaných verzí

AKS si vyhrazuje právo přidat nebo odebrat nové/existující verze s jednou nebo více kritickými chybami v produkčním prostředí nebo problémy zabezpečení bez předchozího upozornění.

Konkrétní vydání opravy se můžou v závislosti na závažnosti chyby nebo problému se zabezpečením vynechat nebo zrychlit.

## <a name="azure-portal-and-cli-versions"></a>Verze Azure Portal a CLI

Když nasadíte cluster AKS na portálu nebo pomocí Azure CLI, cluster se ve výchozím nastavení použije jako podverze N-1 a nejnovější oprava. Například pokud AKS podporuje *1.17. a*, *1.17. b*, *1.16. c*, *1.16. d*, *1.15. e* a *1.15. f*, vybraná výchozí verze je *1.16. c*.

Pokud chcete zjistit, jaké verze jsou aktuálně k dispozici pro vaše předplatné a oblast, použijte příkaz [AZ AKS get-versions][az-aks-get-versions] . Následující příklad uvádí dostupné verze Kubernetes pro oblast *EastUS* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>AKS Kubernetes – kalendář vydání

Historii minulých verzí najdete v tématu [Kubernetes](https://en.wikipedia.org/wiki/Kubernetes#History).

|  Verze K8s | Verze nadřazeného softwaru  | AKS ve verzi Preview  | AKS GA  | Konec života |
|--------------|-------------------|--------------|---------|-------------|
| 1,18  | Březen – 23-20  | Květen 2020   | Srpna 2020  | 1,21 GA | 
| 1,19  | Srpna 04-20  | SEP 2020   | Listopadu 2020  | 1,22 GA | 
| 1.20  | DEC-08-20  | Leden 2021   | Březen 2021  | 1,23 GA |
| 1,21  | Duben – 08-21 | Květen 2021   | Červen 2021  | 1,24 GA |



## <a name="faq"></a>Časté otázky

**Jak Microsoft oznamuje nové verze Kubernetes?**

Tým AKS publikuje předem oznámení s plánovanými kalendářními daty nových verzí Kubernetes v naší dokumentaci, v našem [GitHubu](https://github.com/Azure/AKS/releases) i v e-mailech pro správce předplatného, kteří vlastní clustery, na které se bude vztahovat podpora.  Kromě oznámení AKS také používá [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) k upozornění zákazníka v rámci webu Azure Portal, pokud se nepodporují, a upozorní na zastaralá rozhraní API, která budou mít vliv na jejich aplikaci nebo proces vývoje. 

**Jak často mám očekávat, že upgradují verze Kubernetes, aby zůstaly v podpoře?**

Od Kubernetes 1,19 [vystavila komunita Open Source rozšířenou podporu na 1 rok](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/). AKS potvrzení, aby se povolily opravy a podpora, které odpovídají nadřazeným závazkům. V případě AKS clusterů na 1,19 a vyšších verzích budete moct upgradovat minimálně jednou za rok, aby zůstala v podporované verzi. 

U verzí v 1,18 nebo níže může okno podpora zůstat v 9 měsících a vyžadovat upgrade jednou za 9 měsíců na podporovanou verzi. Pravidelně testujte nové verze a připravte se na upgrade na novější verze, abyste mohli zachytit nejnovější stabilní vylepšení v rámci Kubernetes.

**Co se stane, když uživatel upgraduje cluster Kubernetes s menší verzí, která se nepodporuje?**

Pokud používáte verzi *n-3* nebo starší, znamená to, že nejste mimo rámec podpory, a zobrazí se výzva k upgradu. Po úspěšném dokončení upgradu z verze n-3 na n-2 se vrátíte do našich zásad podpory. Například:

- Pokud je nejstarší podporovaná verze AKS *1.15.* a a jste na *1.14. b* nebo starší, nejste mimo podporu.
- Po úspěšném upgradu z aplikace *1.14. b* na *1.15. a* nebo vyšší se vrátíte do našich zásad podpory.

Downgrady nejsou podporovány.

**Co znamená "mimo" střední část podpory "**

' Mimo podporu ' znamená, že:
* Verze, kterou používáte, je mimo seznam podporovaných verzí.
* Při žádosti o podporu budete požádáni o upgrade clusteru na podporovanou verzi, pokud nejste součástí 30denní lhůty odkladu po vyřazení verze. 

AKS navíc neprovádí žádné moduly runtime ani jiné záruky pro clustery mimo seznam podporovaných verzí.

**Co se stane, když uživatel škáluje cluster Kubernetes s menší verzí, která se nepodporuje?**

Pro menší verze, které AKS nepodporuje, by mělo dál fungovat škálování nebo oddálení. Vzhledem k tomu, že neexistují žádné záruky kvality služeb, doporučujeme upgradovat cluster zpátky na podporu.

**Může uživatel trvale zůstat ve verzi Kubernetes?**

Pokud byl cluster nepodporován pro více než tři (3) menší verze a byl nalezen pro rizika zabezpečení, Azure proaktivně kontaktuje upgrade clusteru. Pokud neprovedete žádnou akci, Azure si vyhrazuje právo automaticky upgradovat cluster vaším jménem.

**Jakou verzi ovládací plocha podporuje, pokud fond uzlů není v některé z podporovaných verzí AKS?**

Rovina ovládacího prvku musí být v rámci okna verzí ze všech fondů uzlů. Podrobnosti o upgradu roviny ovládacího prvku nebo fondů uzlů najdete v dokumentaci k [upgradu fondů uzlů](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

**Můžu během upgradu clusteru přeskočit několik verzí AKS?**

Pokud upgradujete podporovaný cluster AKS, nelze podverze Kubernetes vynechat. Například upgrady mezi:
  * *1.12. x*  ->  *1.13. x*: povoleno.
  * *1.13. x*  ->  *1.14. x*: povoleno.
  * *1.12. x*  ->  *1.14. x*: není povoleno.

Upgrade z *1.12. x*  ->  *1.14. x*:
1. Upgradujte z *1.12. x*  ->  *1.13. x*.
1. Upgradujte z aplikace *1.13. x*  ->  *1.14. x*.

Přeskočení více verzí lze provést pouze při upgradu z nepodporované verze zpět do podporované verze. Například můžete upgradovat z nepodporovaného prvku *1.10. x* na podporovanou *1.15. x*.

**Můžu během 30 dnů podpory vytvořit nový cluster 1. xx. x?**

No. Jakmile je verze zastaralá nebo odebraná, nemůžete vytvořit cluster s touto verzí. V rámci změny se začne zobrazovat stará verze ze seznamu verzí. Tento proces může trvat až dva týdny od oznámení, postupně podle oblasti.

**Mám čerstvě vystaralou verzi, můžu dál přidávat nové fondy uzlů? Nebo budete muset upgradovat?**

No. Do clusteru nebudete moct přidávat fondy uzlů s neschválenou verzí. Můžete přidat fondy uzlů nové verze. To však může vyžadovat, abyste nejprve aktualizovali plochu ovládacího prvku. 

## <a name="next-steps"></a>Další kroky

Informace o tom, jak upgradovat cluster, najdete v tématu [upgrade clusteru Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
