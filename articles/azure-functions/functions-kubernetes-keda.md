---
title: Funkce Azure na Kubernetes s KEDA
description: Zjistěte, jak spustit funkce Azure v Kubernetes v cloudu nebo v místním prostředí pomocí automatického škálování založeného na událostech KEDA, Kubernetes.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 2c06fdba8f60243acf4e0fabd23df8b832c210db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301671"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Funkce Azure na Kubernetes s KEDA

Runtime Azure Functions poskytuje flexibilitu při hostování, kde a jak chcete.  [KEDA](https://keda.sh) (Kubernetes-based Event Driven Autoscaling) se bezproblémově spáruje s runtime Azure Functions a nástroji, které poskytují škálování řízené událostmi v Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Jak fungují funkce založené na Kubernetes

Služba Azure Functions se skládá ze dvou klíčových součástí: runtime a řadiče škálování.  Funkce runtime spustí a spustí váš kód.  Runtime obsahuje logiku o tom, jak aktivovat, protokolovat a spravovat spuštění funkcí.  Runtime Azure Functions může běžet *kdekoli*.  Druhou součástí je řadič váhy.  Řadič škálování monitoruje rychlost událostí, které cílí na vaši funkci, a proaktivně škáluje počet instancí, které spouštějí vaši aplikaci.  Další informace najdete v [tématu Škálování funkcí Azure a hostování](functions-scale.md).

Funkce založené na Kubernetes poskytuje funkce runtime v [kontejneru Dockeru](functions-create-function-linux-custom-image.md) s škálováním řízeným událostmi prostřednictvím KEDA.  KEDA můžete škálovat v 0 instancí (když žádné události dochází) a ven *n* instance. Je to tím, že vystavuje vlastní metriky pro Autoscaler Kubernetes (Horizontal Pod Autoscaler).  Pomocí kontejnerů Functions s KEDA je možné replikovat funkce funkcí bez serveru v libovolném clusteru Kubernetes.  Tyto funkce lze také nasadit pomocí funkce [virtuálních uzlů Azure Kubernetes Services (AKS)](../aks/virtual-nodes-cli.md) pro infrastrukturu bez serveru.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Správa KEDA a funkcí v Kubernetes

Chcete-li spustit funkce v clusteru Kubernetes, je nutné nainstalovat komponentu KEDA. Tuto komponentu můžete nainstalovat pomocí [nástrojů Azure Functions Core Tools](functions-run-local.md).

### <a name="installing-with-helm"></a>Instalace s helmou

Existují různé způsoby, jak nainstalovat KEDA v každém clusteru Kubernetes včetně Helm.  Možnosti nasazení jsou popsány na [webu KEDA](https://keda.sh/deploy/).

## <a name="deploying-a-function-app-to-kubernetes"></a>Nasazení aplikace funkcí do Kubernetes

Do clusteru Kubernetes se spuštěnou keda můžete nasadit libovolnou aplikaci funkcí.  Vzhledem k tomu, že vaše funkce `Dockerfile`běží v kontejneru Dockeru, projekt potřebuje .  Pokud ještě jeden nemá, můžete přidat soubor Dockerfile spuštěním následujícího příkazu v kořenovém adresáři projektu Functions:

```cli
func init --docker-only
```

Chcete-li vytvořit bitovou kopii a nasadit své funkce do Kubernetes, spusťte následující příkaz:

> [!NOTE]
> Základní nástroje budou využívat k sestavení a publikování bitové kopie v dockeru. Ujistěte se, že docker je již `docker login`nainstalován a připojen ke svému účtu s .

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Nahraďte `<name-of-function-deployment>` název aplikace funkce.

Tím se vytvoří prostředek `Deployment` `ScaledObject` Kubernetes, `Secrets`prostředek a , který zahrnuje `local.settings.json` proměnné prostředí importované ze souboru.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Nasazení aplikace funkcí z privátního registru

Výše uvedený tok funguje i pro soukromé registry.  Pokud stahujete bitovou kopii kontejneru ze soukromého registru, uveďte `--pull-secret` příznak, který odkazuje `func kubernetes deploy`na tajný klíč Kubernetes, který při spuštění obsahuje pověření soukromého registru .

## <a name="removing-a-function-app-from-kubernetes"></a>Odebrání aplikace funkce z Kubernetes

Po nasazení můžete odebrat funkci odebráním `ScaledObject`přidružené `Secrets` `Deployment`, , vytvořené.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Odinstalování KEDA z Kubernetes

Kroky k odinstalaci KEDA jsou popsány [na webu KEDA](https://keda.sh/deploy/).

## <a name="supported-triggers-in-keda"></a>Podporované aktivační události v KEDA

KEDA má podporu pro následující aktivační události funkce Azure:

* [Fronty Azure Storage](functions-bindings-storage-queue.md)
* [Fronty azure sběrnice](functions-bindings-service-bus.md)
* [Azure Event / IoT Hubs](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [Fronta RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>Podpora aktivační události PROTOKOLU HTTP

Můžete použít funkce Azure, které zveřejňují aktivační události HTTP, ale KEDA je přímo nespravuje.  Aktivační událost KEDA prometheus můžete využít k [škálování funkcí HTTP Azure z 1 na *n* instancí](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42).

## <a name="next-steps"></a>Další kroky
Další informace najdete v následujících materiálech:

* [Vytvoření funkce pomocí vlastního obrázku](functions-create-function-linux-custom-image.md)
* [Místní psaní kódu a testování funkcí Azure Functions](functions-develop-local.md)
* [Jak funguje plán spotřeby funkcí Azure](functions-scale.md)