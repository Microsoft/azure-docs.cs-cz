---
title: Jak směrování funguje s Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Popisuje procesy, které pohánějí Azure Dev Spaces a jak funguje směrování.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: e9bc1875c053335da6a8e2603406bcdb34a6dd04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241384"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Jak směrování funguje s Azure Dev Spaces

Azure Dev Spaces vám nabízí několik způsobů, jak rychle itetovat a ladit aplikace Kubernetes a spolupracovat s týmem v clusteru Služby Azure Kubernetes (AKS). Jakmile váš projekt běží v dev prostoru, Azure Dev Spaces poskytuje další možnosti sítě a směrování pro váš projekt.

Tento článek popisuje, jak směrování funguje s Dev Spaces.

## <a name="how-routing-works"></a>Jak směrování funguje

Dev prostor je postaven na vrcholu AKS a používá stejné [síťové koncepty](../aks/concepts-network.md). Azure Dev Spaces má také centralizovanou službu *ingressmanager* a nasazuje vlastní řadič příchozího přenosu dat do clusteru AKS. Služba *ingressmanager* monitoruje clustery AKS s dev prostory a rozšiřuje řadič příchozího přenosu dat Azure Dev Spaces v clusteru s objekty Ingress pro směrování do podů aplikací. Kontejner proxy devspaces v každém `azds-route-as` podu přidá hlavičku HTTP pro přenos http do dev prostoru na základě adresy URL. Například požadavek na *http://azureuser.s.default.serviceA.fedcba09...azds.io* adresu URL by `azds-route-as: azureuser`získal hlavičku HTTP s . Kontejner proxy devspaces nepřidá `azds-route-as` záhlaví, pokud je již k dispozici.

Při požadavku HTTP pro službu mimo cluster, požadavek přejde do řadiče příchozího přenosu dat. Řadič příchozího přenosu dat směruje požadavek přímo do příslušného podu na základě jeho ingress objekty a pravidla. Kontejner proxy devspaces v podu obdrží požadavek, přidá `azds-route-as` záhlaví na základě adresy URL a potom směruje požadavek do kontejneru aplikace.

Při požadavku HTTP na službu z jiné služby v rámci clusteru, požadavek nejprve prochází volající služby devspaces proxy kontejneru. Kontejner proxy devspaces se podívá na `azds-route-as` požadavek HTTP a zkontroluje záhlaví. Na základě záhlaví bude kontejner proxy devspaces vyhledat adresu IP služby přidružené k hodnotě záhlaví. Pokud je nalezena adresa IP, kontejner devspaces-proxy přesměruje požadavek na tuto adresu IP. Pokud není nalezena adresa IP, kontejner devspaces-proxy směruje požadavek do nadřazeného kontejneru aplikace.

Například aplikace *serviceA* a *serviceB* jsou nasazeny do nadřazeného dev prostoru *nazývaného výchozí*. *serviceA* spoléhá na *serviceB* a provádí volání HTTP k němu. Azure User vytvoří podřízený dev prostor na základě *výchozího* prostoru s názvem *azureuser*. Azure User také nasadí vlastní verzi *serviceA* do svého podřízeného prostoru. Je-li podána *http://azureuser.s.default.serviceA.fedcba09...azds.io*žádost:

![Směrování Azure Dev Spaces](media/how-dev-spaces-works/routing.svg)

1. Řadič ingress vyhledá IP pro pod přidružené k adrese URL, což je *serviceA.azureuser*.
1. Řadič ingress vyhledá IP pro pod v prostoru pro dev uživatele Azure a směruje požadavek na *pod serviceA.azureuser.*
1. Kontejner devspaces-proxy v podu *serviceA.azureuser* obdrží `azds-route-as: azureuser` požadavek a přidá jako hlavičku HTTP.
1. Kontejner devspaces-proxy v *podu serviceA.azureuser* směruje požadavek do kontejneru aplikace *serviceA* v podu *serviceA.azureuser.*
1. Aplikace *serviceA* v *podu serviceA.azureuser* provádí volání *serviceB*. Aplikace *serviceA* také obsahuje kód `azds-route-as` pro zachování existující hlavičky, což je `azds-route-as: azureuser`v tomto případě .
1. Kontejner proxy devspaces v podu *serviceA.azureuser* obdrží požadavek a vyhledá IP *serviceB* `azds-route-as` na základě hodnoty hlavičky.
1. Kontejner devspaces-proxy v podu *serviceA.azureuser* nenalezne IP pro *serviceB.azureuser*.
1. Kontejner devspaces-proxy v podu *serviceA.azureuser* vyhledá IP pro *serviceB* v nadřazeném prostoru, což je *serviceB.default*.
1. Kontejner devspaces-proxy v podu *serviceA.azureuser* najde IP pro *serviceB.default* a směruje požadavek na *pod serviceB.default.*
1. Kontejner proxy devspaces v podu *serviceB.default* obdrží požadavek a směruje požadavek do kontejneru aplikace *serviceB* v podu *serviceB.default.*
1. Aplikace *serviceB* v *podu serviceB.default* vrátí odpověď na pod *serviceA.azureuser.*
1. Kontejner devspaces-proxy v podu *serviceA.azureuser* obdrží odpověď a směruje odpověď na kontejner aplikace *serviceA* v podu *serviceA.azureuser.*
1. Aplikace *serviceA* obdrží odpověď a potom vrátí vlastní odpověď.
1. Kontejner devspaces-proxy v podu *serviceA.azureuser* obdrží odpověď z kontejneru aplikace *serviceA* a směruje odpověď na původního volajícího mimo cluster.

Všechny ostatní přenosy TCP, který není HTTP prochází řadič evidenčního přenosu dat a devspaces proxy kontejnery nezměněny.

## <a name="sharing-a-dev-space"></a>Sdílení dev prostoru

Při práci s týmem můžete [sdílet dev prostor v celém týmu](how-to/share-dev-spaces.md) a vytvářet odvozené dev prostory. Dev prostor může použít kdokoli s přístupem přispěvatele do skupiny prostředků dev prostoru.

Můžete také vytvořit novou dev prostor, který je odvozen z jiné hodu. Při vytváření odvozené dev prostoru je *popisek azds.io/parent-space=PARENT-SPACE-NAME* přidán do oboru názvů odvozeného dev prostoru. Všechny aplikace z nadřazeného dev prostoru jsou také sdíleny s odvozeným prostorem pro vývoj. Pokud nasadíte aktualizovanou verzi aplikace do odvozeného dev prostoru, bude existovat pouze v odvozeném prostoru pro vývoj a nadřazený vývojový prostor zůstane nedotčen. Můžete mít maximálně tři úrovně odvozených dev mezer nebo *pranadcích.*

Odvozený dev prostor bude také inteligentně směrovat požadavky mezi vlastními aplikacemi a aplikacemi sdílenými z nadřazeného objektu. Směrování funguje tak, že se pokusíte směrovat požadavek do aplikace v odvozeném prostoru pro vývoj a vrátíte se ke sdílené aplikaci z nadřazeného prostoru pro vývoj. Směrování bude spadat zpět do sdílené aplikace v pranadčím prostoru, pokud aplikace není v nadřazeném prostoru.

Například:
* Výchozí *prostor* pro vývoj má aplikace *serviceA* a *serviceB*.
* Dev space *azureuser* je odvozen od *výchozího*.
* Aktualizovaná verze *serviceA* je nasazena na *azureuser*.

Při použití *azureuser*, všechny požadavky na *serviceA* budou směrovány na aktualizovanou verzi v *azureuser*. Požadavek na *serviceB* se nejprve pokusí směrovat na *azureuser* verzi *serviceB*. Vzhledem k tomu, že neexistuje, bude směrována na *výchozí* verzi *serviceB*. Pokud *azureuser* verze serviceA je *odebrána,* všechny požadavky na *serviceA* se vrátí k použití *výchozí* verze *serviceA*.

## <a name="next-steps"></a>Další kroky

Některé příklady toho, jak Azure Dev Spaces používá směrování k zajištění rychlé iterace a vývoje, najdete v [tématu Jak funguje připojení vývojového počítače k vašemu vývojovému prostoru][how-it-works-connect], [Jak funguje vzdálené ladění kódu pomocí Azure Dev Spaces][how-it-works-remote-debugging]a Akce [GitHubu & azure kubernetes service][pr-flow].

Pokud chcete začít používat směrování s Azure Dev Spaces pro vývoj týmu, podívejte se na vývoj týmu v azure [dev spaces][quickstart-team] quickstart.

[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[troubleshooting]: troubleshooting.md