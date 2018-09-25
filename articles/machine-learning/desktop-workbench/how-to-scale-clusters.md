---
title: Škálování clusteru Azure Container Service pro službu Machine Learning | Dokumentace Microsoftu
description: Škálování clusteru ACS – automatické škálování a statické škálování; škálování počtu uzlů v clusteru
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/04/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 718f351ad4ce325f3585ebcbe7adfc4b5905e734
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998606"
---
# <a name="scaling-the-cluster-to-manage-web-service-throughput"></a>Škálování clusteru pro správu propustnost webové služby

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


## <a name="why-scale-the-cluster"></a>Proč škálování clusteru?

Škálování clusteru Azure Container Service (ACS) je účinný způsob, jak optimalizovat propustnost služby a zajistit přitom ochranu velikost clusteru na minimum, abyste snížili náklady. 

Abyste lépe pochopili, automatické škálování, zvažte následující příklad clusteru se systémem tři webové služby:

![Příklad: Tři služby v clusteru](media/how-to-scale-clusters/three-services.png)

Služby mají různé požadavky ve špičce: Služba 1 (modrá čára) vyžaduje 40 podů na poptávku ve špičce, služba 2 (oranžová čára) vyžaduje 38 Špička a Service 3 (Šedá čára) vyžaduje 50 Špička. Pokud rezervovat kapacity ve špičce potřebné pro každou službu zvlášť, bude nutné tento cluster alespoň 40 + 38 + 50 = 128 celkový počet podů.

Ale zvažte použití skutečné pod v libovolném bodě v čase, reprezentovaný černé přerušovanou čáru v grafu. V takovém případě *nejvyššího počtu podů v daný okamžik používá* je 64, na které dojde po 3 služby Špička 20:00. V tuto chvíli Service 3 používá 50 podů, ale 2 služba používá jenom 9 podů a Service 1 používá jenom 5. Nezapomeňte, že toto je *využití ve špičce* pro tento cluster. To znamená, že v žádné chvíli nemá cluster používat více než 64 podů – poloviční počítané požadavek 128 podů pro tři služby nezávisle pro využití ve špičce.

Změnou přiřazení podů v clusteru – to znamená podle změny měřítka – podle aktuální potřeby jednotlivých služeb spíše než jednoduše vyžaduje dostatek prostředků pro poptávku ve špičce všech služeb, může snížit velikost vašeho clusteru. V tomto jednoduchém příkladu automatické škálování snižuje požadovaný počet podů 128 až 64 cutting velikost požadované clusteru na polovinu.

Škálování počtu podů je poměrně rychlé operace, které vyžadují méně než minutu, tak rychlost odezvy služby není ovlivněná vážně.

> [!NOTE]
> Škálování clusteru nepomůže s problémy s latencí požadavku. Pro účely operacionalizace vertikální navýšení kapacity by měl zvýšit počet úspěchů a snížit chyby služba není dostupná. 

## <a name="how-to-scale-web-services-on-your-acs-cluster"></a>Škálování webové služby v clusteru ACS

Tato možnost instalace clusteru z rozhraní příkazového řádku správy modelů ve výchozím nastavení konfiguruje dva agenti a pod jeden ve vašem prostředí. Kromě toho nainstaluje rozhraní příkazového řádku Kubernetes.

Je možné škálovat webové služby, které jste nasadili do ACS pomocí úpravy:

* Počet uzlů agentů v clusteru
* Počet replik podů Kubernetes spuštěných v agentské uzly

### <a name="scaling-the-number-of-nodes-in-the-cluster"></a>Škálování počtu uzlů v clusteru

Následující příkaz nastavuje počet uzlů agentů v clusteru:

```
az acs scale -g <resource group> -n <cluster name> --new-agent-count <new scale>
```

Dokončení tohoto procesu může několik minut trvat. Další informace o vertikálním navýšení počtu uzlů v clusteru najdete v tématu [škálování uzlů agentů v clusteru Container Service](https://docs.microsoft.com/azure/container-service/container-service-scale).

### <a name="scaling-the-number-of-kubernetes-pod-replicas-in-a-cluster"></a>Měřítko počet replik podů Kubernetes v clusteru
 
Můžete škálovat počet replik podů, které jsou přiřazeny ke clusteru pomocí rozhraní příkazového řádku Azure Machine Learning nebo [řídicí panel Kubernetes] (https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/).

Další informace o replik podů Kubernetes najdete v článku [Podů Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/pod/) dokumentaci.

#### <a name="scaling-a-cluster-with-the-azure-machine-learning-cli"></a>Škálování clusteru pomocí rozhraní příkazového řádku Azure Machine Learning

Existují dva způsoby, jak škálovat cluster pomocí rozhraní příkazového řádku:

- Automatické škálování
- Statické škálování

Automatické škálování je ve výchozím nastavení aktivní, když služba se vytvoří a ve většině případů je upřednostňovanou metodou škálování.

##### <a name="autoscale"></a>Automatické škálování

Následující příkaz povolí automatické škálování a Nastaví minimální a maximální počet replik pro službu.

```
az ml service update realtime -i <service id> --autoscale-enabled true --autoscale-min-replicas <positive number> --autoscale-max-replicas <positive number>
```

Například nastavení `autoscale-min-replicas` 5 vytvoří pět repliky. Můžete přejít na optimální počet pro webovou službu, nastavte hodnoty, jako je 10 a sledovat počet 503 chybové zprávy. Odpovídajícím způsobem upravte počet.


| Název parametru | Typ | Popis |
|--------------------|--------------------|--------------------|
| `autoscale-enabled` | Boolean | Určuje, jestli je povolené automatické škálování. Výchozí: true |
| `autoscale-min-replicas` | integer | Určuje minimální počet podů. Musí být 0 nebo větší. Výchozí: 1 |
| `autoscale-max-replicas` | integer | Určuje maximální počet podů. Musí být 1 nebo větší. Pokud automatické škálování. maximální počet replik je menší než automatického škálování. minimální počet replik, ignorují se automatické škálování. maximální počet replik. Výchozí: 10 |
| `autoscale-refresh-period-seconds` | integer | Určuje dobu v sekundách mezi aktualizacemi automatického škálování. Výchozí: 1 |
| `autoscale-target-utilization` | integer | Určuje procento využití, který cílí automatického škálování, od 1 do 100. Výchozí: 70 |

Automatické škálování funguje a jak můžete zajistit, tyto dvě podmínky:

1. Cílové využití je splněna.
2. Nastavení minimální a maximální škálování nikdy překračuje.

Služby v clusteru soutěží o prostředky clusteru. Služby došlo ke ztrátě schopnosti zvýší jeho využití prostředků clusteru jako své požadavky na zvýšení druhý (předávajících stran) a bude pomalu uvolnit prostředky jako snížení RPS. Prostředky clusteru se získat na vyžádání, tak dlouho, dokud tyto prostředky existují pro službu získat.

Další informace o používání parametrů automatického škálování najdete v článku [referenční informace k rozhraní příkazového řádku pro správu modelu](model-management-cli-reference.md) dokumentaci.

##### <a name="static-scale"></a>Statické škálování

Obecně platí, statické škálování mělo by se vyhnout, protože neumožňuje snížení velikosti clusteru automatického škálování. I tak v některých situacích statické škálování může být nedoporučuje. Například pokud cluster je vyhrazen pro jednu službu, automatické škálování nepřináší žádné výhody, všechny prostředky clusteru měla být přiřazena k této službě.

Pokud chcete staticky škálování clusteru, musí být vypnutý automatického škálování. Zakážete automatické škálování pomocí následujícího příkazu:

```
az ml service update realtime -i <service id> --autoscale-enabled false
```

Po vypnutí automatického škálování, následující příkaz přímo škáluje počet replik pro službu.

```
az ml service update realtime -i <service id> -z <replica count>
```
 
Další informace o vertikálním navýšení počtu uzlů v clusteru najdete v tématu škálování uzlů agentů v clusteru Container Service.

#### <a name="scaling-number-of-replicas-using-the-kubernetes-dashboard"></a>Škálování počtu replik na řídicím panelu Kubernetes

Na příkazovém řádku zadejte:

```
kubectl proxy
```

Na Windows umístění instalace Kubernetes nepřidá automaticky na cestu. Nejprve přejděte do složky instalace:

```
c:\users\<user name>\bin
```

Po spuštění příkazu by se zobrazit následující informační zpráva:

```
Starting to serve on 127.0.0.1:8001
```

Pokud port, který se už používá, zobrazí zpráva podobná následujícímu příkladu:

```
F0612 21:49:22.459111   59621 proxy.go:137] listen tcp 127.0.0.1:8001: bind: address already in use
```

Můžete zadat alternativní port čísla pomocí *– port* parametru.

```
kubectl proxy --port=8010
Starting to serve on 127.0.0.1:8010
```

Po zahájení server řídicího panelu otevřete prohlížeč a zadejte následující adresu URL:

```
127.0.0.1:<port number>/ui
```

Z hlavní obrazovky řídicího panelu, klikněte na tlačítko **nasazení** na levém navigačním panelu. Pokud v navigačním podokně nezobrazí, když tuto ikonu vyberete ![nabídky skládající se z tři vodorovné čáry krátký](media/how-to-scale-clusters/icon-hamburger.png) v levém horním rohu.

Najděte nasazení k úpravě a kliknutím na tuto ikonu ![ikonu nabídky, který se skládá ze tří svislých teček](media/how-to-scale-clusters/icon-kebab.png) na pravé straně a pak klikněte na tlačítko **umožňuje zobrazit či upravit YAML**.

Na obrazovce pro úpravy nasazení, vyhledejte *specifikace* uzlu, změnit *repliky* hodnoty a klikněte na tlačítko **aktualizace**.
