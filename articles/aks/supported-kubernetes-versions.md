---
title: Podporované verze Kubernetes ve službě Azure Kubernetes Service
description: Principy zásad podpory a životního cyklu clusterů ve službě Azure Kubernetes (AKS) verze Kubernetes
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 5f396ba6ec1ecc6bd111e048ce34e3546c7364dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84345032"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Podporované verze Kubernetes ve službě Azure Kubernetes Service (AKS)

Komunita Kubernetes vydává dílčí verze zhruba každé tři měsíce. Tyto verze obsahují nové funkce a vylepšení. Verze oprav jsou častěji časté (někdy týdně) a jsou určeny pouze pro kritické opravy chyb v dílčí verzi. Tyto verze oprav obsahují opravy chyb zabezpečení a závažné chyby, které mají vliv na velký počet zákazníků a produktů provozovaných v produkčním prostředí založeném na Kubernetes.

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

Uživatelé by měli mít na úmysl spustit nejnovější verzi opravy podverze, kterou používají. například pokud je váš provozní cluster na *1.12.14* a *1.12.15* je nejnovější dostupná verze opravy dostupná pro řadu *1,12* , měli byste upgradovat na *1.12.15* , jakmile budete moci zajistit, aby byl cluster plně opravený a podporovaný.

## <a name="kubernetes-version-support-policy"></a>Zásady podpory verze Kubernetes

AKS podporuje tři dílčí verze Kubernetes:

* Aktuální dílčí verze vydaná v AKS (N)
* Dvě předchozí vedlejší verze. Každá podporovaná dílčí verze také podporuje dvě stabilní opravy.

To se označuje jako "N-2": (N (nejnovější verze)-2 (podverze)).

Například pokud AKS zavádí *1.15. a* ještě dnes, podpora je k dispozici pro následující verze:

Nová dílčí verze    |    Seznam podporovaných verzí
-----------------    |    ----------------------
1.15. a               |    1.15. a, 1.15. b, 1.14. c, 1.14. d, 1.13. e, 1.13. f

Kde ". Letter" představuje zástupce verzí oprav.

Podrobnosti o komunikaci týkající se změn a očekávání verzí najdete v části "komunikace" níže.

Pokud je zavedena nová podverze, je podpora nejstarší dílčí verze a vydání oprav zastaralá a odebrána. Pokud je například aktuální podporovaná verze seznam:

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

A AKS verze 1,16. *to znamená, že 1,13.* verze (všechny verze 1,13) se odeberou a nepodporují se.

> [!NOTE]
> Upozorňujeme, že pokud na zákaznících běží Nepodporovaná verze Kubernetes, zobrazí se jim výzva k upgradu při žádosti o podporu pro cluster. Clustery, na kterých běží Nepodporovaná vydání Kubernetes, se nevztahují na [zásady podpory AKS](https://docs.microsoft.com/azure/aks/support-policies).

Kromě výše uvedených v podverzi AKS podporuje dvě nejnovější **opravy** vydané dílčí verze. Například s ohledem na následující podporované verze:

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

Pokud Kubernetes vydala 1.15.3 a 1.14.6 a AKS uvolní tyto verze opravy, nejstarší verze opravy jsou zastaralé a odebrané a seznam podporovaných verzí bude:

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>Komunikace

* Pro nové **menší** verze Kubernetes
  * Všichni uživatelé budou veřejně vyrozuměni o nové verzi a jaká verze bude odebrána.
  * Při vydání nové verze opravy se ve stejnou chvíli odebere nejstarší verze opravy.
  * Podpora Azure poskytuje zákazníkům **30 dnů** od data veřejné oznámení k upgradu na podporovanou verzi dílčí verze. Po uplynutí 30 dnů budete muset aktualizovat podverzi, aby nadále přijímala podporu.
* Pro nové verze **patch** Kubernetes
  * Všem uživatelům se zobrazí oznámení o vydání nové verze opravy a k upgradu na nejnovější verzi opravy.
  * Podpora Azure poskytuje zákazníkům **30 dní** na upgrade na podporovanou verzi opravy po odebrání starší verze opravy. Po uplynutí 30 dnů budete muset aktualizovat verzi opravy, aby nadále přijímala podporu.

AKS definuje "vydanou verzi" jako všeobecně dostupné verze, která je povolená u všech oblastí SLO/Quality of Service měření a dostupná ve všech oblastech. AKS může podporovat i verze Preview, které jsou explicitně označené a podléhají podmínkám a ujednáním ve verzi Preview.

#### <a name="notification-channels-for-aks-changes"></a>Kanály oznámení pro AKS změny

AKS publikuje pravidelné aktualizace služeb, které shrnují nové verze Kubernetes, změny služeb a aktualizace součástí, které byly vydány v rámci služby na [GitHubu](https://github.com/Azure/AKS/releases).

Tyto změny jsou v rámci pravidelné údržby nabízené jako součást spravované služby zahrnuty do všech zákazníků, některé vyžadují explicitní upgrady, zatímco jiné nevyžadují žádnou akci.

Oznámení se odesílají taky prostřednictvím:

* [Poznámky k verzi AKS](https://aka.ms/aks/releasenotes)
* Oznámení portálu Azure Portal
* [Kanál pro aktualizaci Azure][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>Výjimky zásad podporovaných verzí

AKS si vyhrazuje právo přidat nebo odebrat nové/existující verze, které byly identifikovány tak, aby měly jednu nebo více kritických chybových dopadů na produkční prostředí bez předchozího upozornění.

Konkrétní vydané verze oprav se můžou přeskočit nebo se zrychlí v závislosti na závažnosti chyby nebo problému se zabezpečením.

### <a name="azure-portal-and-cli-default-versions"></a>Výchozí verze Azure Portal a CLI

Když nasadíte cluster AKS na portálu nebo pomocí rozhraní příkazového řádku Azure CLI, cluster se nastaví jako výchozí v podverzi N-1 a nejnovější oprava. Například pokud AKS podporuje *1.15. a*, *1.15. b*, *1.14. c*, *1.14. d*, *1.13. e*a *1.13. f*, vybraná výchozí verze je *1.14. c*.

AKS zvolí výchozí hodnotu N-1, aby ve výchozím nastavení poskytovala zákazníkům známou, stabilní a opravenou verzi.

## <a name="list-currently-supported-versions"></a>Vypsat aktuálně podporované verze

Pokud chcete zjistit, jaké verze jsou aktuálně k dispozici pro vaše předplatné a oblast, použijte příkaz [AZ AKS get-versions][az-aks-get-versions] . Následující příklad uvádí dostupné verze Kubernetes pro oblast *EastUS* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>Nejčastější dotazy

**Co se stane, když zákazník upgraduje cluster Kubernetes s menší verzí, která není podporovaná?**

Pokud používáte verzi *n-3* , jste mimo podporu a budete vyzváni k upgradu. Pokud váš upgrade z verze n-3 na n-2 uspěje, jste teď v rámci našich zásad podpory. Příklad:

- Pokud je nejstarší podporovaná verze AKS *1.13.* a a jste na *1.12. b* nebo starším, jste mimo podporu.
- Pokud upgrade z *1.12. b* na *1.13. a* nebo vyšší je úspěšný, vrátíte se do našich zásad podpory.

Nepodporují se upgrady na verze starší než podporované okno *N-2* . V takových případech doporučujeme zákazníkům vytvářet nové clustery AKS a znovu nasazovat úlohy s verzemi v podporovaném okně.

**Co znamená "mimo" střední část podpory "**

' Mimo podporu ' znamená, že verze, kterou používáte, je mimo seznam podporovaných verzí a při žádosti o podporu budete požádáni o upgrade clusteru na podporovanou verzi. AKS navíc neprovádí žádné moduly runtime ani jiné záruky pro clustery mimo seznam podporovaných verzí.

**Co se stane, když zákazník škáluje cluster Kubernetes s menší verzí, která není podporovaná?**

Pro podverze, které AKS nepodporuje, by se škálování nebo oddálení mělo dál pracovat, ale důrazně doporučujeme upgradovat cluster zpátky na podporu.

**Může zákazník stále ve verzi Kubernetes zůstat?**

Pokud byl cluster nepodporován pro více než 3 podverze a byl nalezen pro rizika zabezpečení, Azure vám umožní proaktivně upgradovat cluster. Pokud neprovedete žádnou akci, Azure si vyhrazuje právo vynutit upgrade vašeho clusteru vaším jménem.

**Jakou verzi ovládací plocha podporuje, pokud fond uzlů není v některé z podporovaných verzí AKS?**

Rovina ovládacího prvku musí být v rámci okna verzí ze všech fondů uzlů. Podrobnosti o upgradu roviny ovládacího prvku nebo fondů uzlů najdete v dokumentaci k [upgradu fondů uzlů](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

## <a name="next-steps"></a>Další kroky

Informace o tom, jak upgradovat cluster, najdete v tématu [upgrade clusteru Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
