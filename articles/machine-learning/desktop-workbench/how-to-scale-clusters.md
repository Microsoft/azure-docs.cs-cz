---
title: Postup škálování clusteru Azure Container Service pro Machine Learning | Microsoft Docs
description: Škálování clusteru služby ACS - škálování a statické škálování; škálování počtu uzlů v clusteru
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/04/2017
ms.openlocfilehash: 58709b5a5b060552e1bc349a6f720d3f45348ba0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="scaling-the-cluster-to-manage-web-service-throughput"></a>Škálování clusteru spravovat propustnost webové služby

## <a name="why-scale-the-cluster"></a>Proč škálování clusteru?

Škálování clusteru Azure Container Service (ACS) je efektivní způsob, jak optimalizovat výkon služby zachováním velikost clusteru na snížení nákladů na minimum. 

Abyste lépe pochopili, automatické škálování, zvažte následující příklad clusteru se systémem tři webové služby:

![Příklad: Tři služby v clusteru](media/how-to-scale-clusters/three-services.png)

Služeb mají různé požadavky ve špičce: Service 1 (modré řádku) vyžaduje 40 pracovními stanicemi soustředěnými kolem v poptávky ve špičce, Service 2 (oranžová čára) vyžaduje 38 na ve špičce a Service 3 (Šedá čára) vyžaduje 50 až ve špičce. Pokud rezervovat potřebné ve špičce kapacity pro každou službu jednotlivě, tento cluster potřebovat alespoň 40 + 38 + 50 = 128 celkový počet pracovními stanicemi soustředěnými kolem.

Ale zvažte využití skutečné pod v libovolném bodě v čase, reprezentována černým přerušovanou čáru v grafu. V takovém případě *nejvyšší počet pracovními stanicemi soustředěnými kolem použít v daném okamžiku* je 64, která nastane po Service 3 na ve špičce 20:00. V tomto okamžiku Service 3 používá 50 pracovními stanicemi soustředěnými kolem, ale s 2 používá právě 9 pracovními stanicemi soustředěnými kolem a Service 1 používá jenom 5. Pamatujte si, že toto je *špičkové využití* pro tento cluster. To znamená, že současně clusteru používá víc než 64 pracovními stanicemi soustředěnými kolem – poloviční počítané požadavek 128 pracovními stanicemi soustředěnými kolem pro tři služby nezávisle škálovat pro použití ve špičce.

Změnou přiřazení pracovními stanicemi soustředěnými kolem v clusteru – to znamená, podle změny měřítka – na aktuální potřeby jednotlivých služeb než jednoduše vyžadovat dostatečné prostředky pro poptávky ve špičce všech služeb, může snížit velikost vašeho clusteru. V tomto jednoduchém příkladu automatické škálování sníží požadovaný počet pracovními stanicemi soustředěnými kolem 128 64, vyjímání velikost požadované clusteru v polovině.

Škálování počtu pracovními stanicemi soustředěnými kolem je poměrně rychlé operace, vyžadování méně než minutu, proto není vážně ovlivněná odezvy služby.

> [!NOTE]
> Škálování clusteru nebude pomoct s problémy s latencí požadavku. Pro účely operationalization vertikálním navýšení kapacity doporučujeme zvýšit počet úspěchů a snížit služba není k dispozici chyby. 

## <a name="how-to-scale-web-services-on-your-acs-cluster"></a>Postup škálování webových služeb v clusteru služby ACS

Tato možnost instalace clusteru model správy rozhraní příkazového řádku ve výchozím nastavení nakonfiguruje dva agenti a jeden pod ve vašem prostředí. Nainstaluje taky rozhraní příkazového řádku Kubernetes.

Je možné škálovat webové služby, které jste nasadili služby ACS úpravou:

* Počet agenta uzlů v clusteru
* Počet replik pod Kubernetes spuštěná na uzlech agenta

### <a name="scaling-the-number-of-nodes-in-the-cluster"></a>Škálování počtu uzlů v clusteru

Tento příkaz nastaví počet agenta uzlů v clusteru:

```
az acs scale -g <resource group> -n <cluster name> --new-agent-count <new scale>
```

Bude to trvat několik minut. Další informace o škálování počtu uzlů v clusteru najdete v tématu [škálování agenta uzly v clusteru Container Service](https://docs.microsoft.com/azure/container-service/container-service-scale).

### <a name="scaling-the-number-of-kubernetes-pod-replicas-in-a-cluster"></a>Škálování počtu Kubernetes pod repliky v clusteru
 
Je možné škálovat počet replik pod přiřadili ke clusteru pomocí rozhraní příkazového řádku Azure Machine Learning nebo [řídicí panel Kubernetes] (https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/).

Další informace o pracovními stanicemi soustředěnými kolem Kubernetes repliky, najdete v článku [Kubernetes pracovními stanicemi soustředěnými kolem](https://kubernetes.io/docs/concepts/workloads/pods/pod/) dokumentaci.

#### <a name="scaling-a-cluster-with-the-azure-machine-learning-cli"></a>Škálování clusteru pomocí rozhraní příkazového řádku Azure Machine Learning

Existují dva způsoby, jak škálování clusteru pomocí rozhraní příkazového řádku:

- Automatické škálování
- Statické škálování

Pokud tato služba je vytvořena a ve většině případů je upřednostňovaná metoda škálování při automatickém škálování je ve výchozím nastavení aktivní.

##### <a name="autoscale"></a>Automatické škálování

Následující příkaz povolí automatické škálování a Nastaví minimální a maximální počet replik pro službu.

```
az ml service update realtime -i <service id> --autoscale-enabled true --autoscale-min-replicas <positive number> --autoscale-max-replicas <positive number>
```

Například nastavení `autoscale-min-replicas` 5 vytvoří pět repliky. K dosažení představuje optimální počet pro webovou službu, nastavte počet hodnoty, jako například 10 a sledovat počet 503 chybové zprávy. Upravte počet odpovídajícím způsobem.


| Název parametru | Typ | Popis |
|--------------------|--------------------|--------------------|
| `autoscale-enabled` | Boolean | Určuje, zda je povoleno automatické škálování. Výchozí: true |
| `autoscale-min-replicas` | celé číslo | Určuje minimální počet pracovními stanicemi soustředěnými kolem. Musí být 0 nebo větší. Výchozí: 1 |
| `autoscale-max-replicas` | celé číslo | Určuje maximální počet pracovními stanicemi soustředěnými kolem. Musí být 1 nebo vyšší. Je-li škálování. maximální počet replik je menší než škálování min repliky, automatické škálování. maximální počet replik budou ignorovány. Výchozí: 10 |
| `autoscale-refresh-period-seconds` | celé číslo | Určuje dobu v sekundách mezi aktualizacemi škálování. Výchozí: 1 |
| `autoscale-target-utilization` | celé číslo | Určuje procento využití s cílem škálování, od 1 do 100. Výchozí: 70 |

Škálování funguje zajistit tyto dvě podmínky:

1. Při splnění v cílové využití
2. Škálování nikdy překračuje minimální a maximální nastavení

Služby v clusteru se pokouší o prostředky clusteru. Služby došlo zvýší jeho využití prostředků clusteru jako jeho požadavky na druhý zvyšuje (RPS) a bude pomalu uvolnění prostředků jako RPS neklesne. Prostředky clusteru bude možné získat na vyžádání, dokud tyto prostředky existují pro službu, kterou chcete získat.

Další informace o používání parametrů škálování najdete v tématu [modelu managementu rozhraní příkazového řádku](model-management-cli-reference.md) dokumentaci.

##### <a name="static-scale"></a>Statické škálování

Obecně platí statické škálování je nutno, protože neumožňuje snižování velikosti clusteru automatické škálování. I tak v některých situacích statické škálování může být nedoporučuje. Například když clusteru je vyhrazen pro jednu službu, automatické škálování poskytuje žádné výhody; všechny prostředky clusteru lze přiřadit k této službě.

Chcete-li staticky škálování clusteru, je nutné vypnout automatické škálování. Zakážete automatické škálování pomocí následujícího příkazu:

```
az ml service update realtime -i <service id> --autoscale-enabled false
```

Po vypnutí automatického škálování, následující příkaz přímo škáluje počtu replik pro služby.

```
az ml service update realtime -i <service id> -z <replica count>
```
 
Další informace o škálování počtu uzlů v clusteru najdete v části škálování agenta uzlů v clusteru Container Service.

#### <a name="scaling-number-of-replicas-using-the-kubernetes-dashboard"></a>Škálování počet replik Kubernetes řídicího panelu

Na příkazovém řádku zadejte:

```
kubectl proxy
```

V systému Windows není umístění instalace Kubernetes automaticky přidat do cesty. Nejprve přejděte do složky instalace:

```
c:\users\<user name>\bin
```

Po spuštění příkazu měli vidět následující informační zprávu:

```
Starting to serve on 127.0.0.1:8001
```

Pokud port je již používán, uvidíte zprávu podobnou té v následujícím příkladu:

```
F0612 21:49:22.459111   59621 proxy.go:137] listen tcp 127.0.0.1:8001: bind: address already in use
```

Můžete zadat k alternativní port číslo pomocí *--port* parametr.

```
kubectl proxy --port=8010
Starting to serve on 127.0.0.1:8010
```

Jakmile jste spustili server řídicího panelu, otevřete prohlížeč a zadejte následující adresu URL:

```
127.0.0.1:<port number>/ui
```

Z hlavní obrazovky řídicí panel, klikněte na tlačítko **nasazení** na levém navigačním panelu. Pokud se nezobrazí v navigačním podokně, vyberte ikonu ![nabídky skládající se z tři vodorovné čáry krátké](media/how-to-scale-clusters/icon-hamburger.png) v levé horní části.

Najděte nasazení upravte a kliknutím na tuto ikonu ![ikonu nabídky skládající se z tři tečky vertikálně](media/how-to-scale-clusters/icon-kebab.png) na pravé straně a pak klikněte na **umožňuje zobrazit či upravit YAML**.

V dialogovém okně Upravit nasazení vyhledejte *specifikace* uzlu upravit *repliky* hodnoty a klikněte na **aktualizace**.
