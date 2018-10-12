---
title: Hostování kontejnerů Docker v cloudu Azure
description: Služba Azure Container Service umožňuje zjednodušení vytváření, konfigurace a správy clusteru virtuálních počítačů, které jsou předem nakonfigurovány pro spouštění kontejnerizovaných aplikací.
services: container-service
author: rgardler
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 6bb13ad19f9b0b6137cdb2b4a9afbb2f325b9d36
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975050"
---
# <a name="introduction-to-docker-container-hosting-solutions-with-azure-container-service"></a>Úvod k řešením hostování kontejnerů Docker pomocí Azure Container Service 

Služba Azure Container Service vám zjednoduší vytváření, konfiguraci a správu clusteru virtuálních počítačů, které jsou předem nakonfigurovány pro spouštění kontejnerizovaných aplikací. Používá optimalizovanou konfiguraci oblíbených nástrojů open source pro plánování a orchestraci. Díky tomu můžete při nasazování a správě kontejnerových aplikací v Microsoft Azure využívat své stávající dovednosti nebo stavět na rozsáhlých a stále se rozšiřujících odborných znalostech komunity.

![Služba Azure Container Service poskytuje prostředek pro správu kontejnerizovaných aplikací na více hostitelích v Azure.](./media/acs-intro/acs-cluster-new.png)

Služba Azure Container Service využívá k zajištění plné přenositelnosti kontejnerů vaší aplikace formát kontejneru Dockeru. Podporuje také zvolený systém Marathon a systém DC/OS, Docker Swarm nebo Kubernetes, takže můžete tyto aplikace škálovat do tisíců nebo dokonce desítek tisíců kontejnerů.

Díky používání služby Azure Container Service můžete využívat výhody funkcí Azure na podnikové úrovni, a přitom zachovávat přenositelnost aplikací – včetně přenositelnosti v orchestračních vrstvách.

## <a name="using-azure-container-service"></a>Používání služby Azure Container Service
Naším cílem u služby Azure Container Service je poskytnout hostitelské prostředí pro kontejnery díky používání nástrojů a technologií open source, které jsou dnes mezi našimi zákazníky oblíbené. Za tímto účelem zveřejňujeme standardní koncové body rozhraní API pro zvolený orchestrátor (systém DC/OS, Docker Swarm nebo Kubernetes). S použitím těchto koncových bodů můžete využívat veškerý software, který s nimi dokáže komunikovat. V případě koncového bodu Docker Swarm můžete například zvolit použití rozhraní příkazového řádku (CLI) Dockeru. V případě systému DC/OS můžete zvolit rozhraní příkazového řádku systému DC/OS. V případě systému Kubernetes můžete zvolit použití `kubectl`.

## <a name="creating-a-docker-cluster-by-using-azure-container-service"></a>Vytvoření clusteru Dockeru s použitím služby Azure Container Service
Pokud chcete začít používat službu Azure Container Service, nasaďte cluster Azure Container Service prostřednictvím portálu (vyhledejte na Marketplace řetězec **Azure Container Service**) s použitím šablony Azure Resource Manageru ([Docker Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm), [DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) nebo [Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)) nebo [Azure CLI](container-service-create-acs-cluster-cli.md). Poskytované šablony pro rychlý začátek lze upravit tak, aby obsahovaly další nebo rozšířenou konfiguraci Azure. Další informace najdete v části [Nasazení clusteru Azure Container Service](container-service-deployment.md).

## <a name="deploying-an-application"></a>Nasazení aplikace
Služba Azure Container Service poskytuje možnost orchestrace prostřednictvím Docker Swarmu, systému DC/OS nebo Kubernetes. Způsob nasazení aplikace závisí na zvoleném orchestrátoru.

### <a name="using-dcos"></a>S použitím systému DC/OS
DC/OS je distribuovaný operační systém založený na jádru distribuovaných systémů Apache Mesos. Apache Mesos spravuje nadace Apache Software Foundation a mezi jeho uživatele a přispěvatele patří i [největší osobnosti IT](http://mesos.apache.org/documentation/latest/powered-by-mesos/).

![Služba Azure Container Service nakonfigurovaná pro systém DC/OS se zobrazením agentů a nadřízených serverů.](media/acs-intro/dcos.png)

Systém DC/OS a Apache Mesos zahrnují rozsáhlou nabídku funkcí:

* Ověřená škálovatelnost
* Replikované nadřízené a podřízené servery odolné proti chybám s použitím Apache ZooKeeper
* Podpora kontejnerů ve formátu Dockeru
* Nativní izolace mezi úkoly s kontejnery Linuxu
* Plánování s více prostředky (paměť, procesor, disk a porty)
* Rozhraní API jazyků Java, Python a C++ pro vývoj nových paralelních aplikací
* Webové uživatelské rozhraní pro zobrazení stavu clusteru

Ve výchozím nastavení systém DC/OS spuštěný ve službě Azure Container Service zahrnuje platformu pro orchestraci systému Marathon, která umožňuje plánování úloh. Součástí nasazení systému DC/OS se službou ACS je však Mesosphere Universe obsahující služby, které je možné přidat do vaší služby. Mezi služby v Universe patří Spark, Hadoop, Cassandra a mnoho dalších.

![Universe pro systém DC/OS ve službě Azure Container Service](media/dcos/universe.png)

#### <a name="using-marathon"></a>S použitím systému Marathon
Marathon je celoclusterový inicializační a řídicí systém pro služby ve skupinách cgroup – nebo (v případě služby Azure Container Service) v kontejnerech s formátem Dockeru. Systém Marathon poskytuje webové uživatelské rozhraní, ze kterého můžete nasazovat své aplikace. K dispozici je přístup prostřednictvím adresy URL, která vypadá přibližně takto: `http://DNS_PREFIX.REGION.cloudapp.azure.com`.
Proměnné DNS\_PREFIX a REGION se definují v době nasazení. Samozřejmě můžete také zadat vlastní název DNS. Další informace o spouštění kontejneru prostřednictvím webového uživatelského rozhraní systému Marathon najdete v části [Správa kontejnerů systému DC/OS prostřednictvím webového uživatelského rozhraní systému Marathon](container-service-mesos-marathon-ui.md).

![Seznam aplikací systému Marathon](media/dcos/marathon-applications-list.png)

Pro komunikaci se systémem Marathon můžete používat i rozhraní API REST. Existuje mnoho klientských knihoven, které jsou k dispozici pro jednotlivé nástroje. Pokrývají široké spektrum jazyků – a v každém jazyku můžete samozřejmě používat protokol HTTP. Podporu pro systém Marathon navíc poskytuje mnoho oblíbených nástrojů DevOps. Díky tomu má váš provozní tým při práci s clusterem Azure Container Service k dispozici maximální flexibilitu. Další informace o spouštění kontejneru prostřednictvím rozhraní API REST systému Marathon najdete v části [Správa kontejnerů systému DC/OS prostřednictvím rozhraní API REST systému Marathon](container-service-mesos-marathon-rest.md).

### <a name="using-docker-swarm"></a>S použitím Docker Swarmu
Docker Swarm poskytuje nativní clustering pro Docker. Protože Docker Swarm obsluhuje standardní rozhraní API Dockeru, může kterýkoli nástroj, který už s démonem Dockeru komunikuje, používat Swarm pro transparentní škálování na více hostitelů ve službě Azure Container Service.

![Služba Azure Container Service nakonfigurovaná pro používání Swarmu.](media/acs-intro/acs-swarm2.png)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Mezi podporované nástroje pro správu kontejnerů v clusteru Swarmu patří mimo jiné následující:

* Dokku
* Rozhraní příkazového řádku Dockeru a Docker Compose
* Krane
* Jenkins

### <a name="using-kubernetes"></a>S použitím Kubernetes
Kubernetes je často používaný opensourcový nástroj pro orchestraci kontejnerů na úrovni produkce. Kubernetes automatizuje nasazení, škálování a správu kontejnerizovaných aplikací. Protože se jedná o opensourcové řešení řízené opensourcovou komunitou, bezproblémově běží v Azure Container Service a jde ho ve velkém použít k nasazování kontejnerů v Azure Container Service.

![Služba Azure Container Service nakonfigurovaná pro používání Kubernetes.](media/acs-intro/kubernetes.png)

Má bohatou výbavu funkcí, například:
* Horizontální škálování
* Zjišťování služeb a vyrovnávání zatížení
* Správa tajných klíčů a konfigurace
* Automatická uvedení a vrácení zpět založená na rozhraní API
* Samoopravení

## <a name="videos"></a>Videa
Začínáme se službou Azure Container Service (101):  

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Container-Service-101/player]
>
>

Sestavování aplikací s použitím služby Azure Container Service (sestavení 2016)

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/B822/player]
>
>

## <a name="next-steps"></a>Další kroky

Nasaďte cluster služby Container Service pomocí [portálu](container-service-deployment.md) nebo [Azure CLI](container-service-create-acs-cluster-cli.md).
