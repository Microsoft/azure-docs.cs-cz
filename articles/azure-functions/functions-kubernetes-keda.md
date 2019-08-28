---
title: Azure Functions v Kubernetes s KEDA
description: Pochopte, jak spouštět Azure Functions v Kubernetes v cloudu nebo v místním prostředí pomocí KEDA na základě Kubernetes automatického škálování založeného na událostech.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: funkce Azure Functions, Functions, zpracování událostí, dynamický výpočet, architektura bez serveru, Kubernetes
ms.service: azure-functions
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: b581d7c9b5876813e36ebbf41be713b44dd97735
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096093"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions v Kubernetes s KEDA

Azure Functions runtime poskytuje flexibilitu při hostování tam, kde a jak chcete.  [Keda](https://github.com/kedacore/kore) (Kubernetes – automatické škálování založené na událostech) plynule s Azure Functions modulem runtime a nástroji pro zajištění škálování na základě událostí v Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Jak fungují funkce založené na Kubernetes

Služba Azure Functions se skládá ze dvou klíčových součástí: modulu runtime a řadiče škálování.  Modul runtime Functions spustí a spustí váš kód.  Modul runtime obsahuje logiku, jak aktivovat, protokolovat a spravovat provádění funkcí.  Druhá součást je kontroler škálování.  Kontroler škálování monitoruje míru událostí, které cílí na vaši funkci, a proaktivně škáluje počet instancí, na kterých běží vaše aplikace.  Další informace najdete v tématu [škálování Azure functions a hostování](functions-scale.md).

Funkce založené na Kubernetes poskytují modul runtime funkcí v [kontejneru Docker](functions-create-function-linux-custom-image.md) s škálováním řízeným událostmi prostřednictvím keda.  KEDA může škálovat dolů na 0 instancí (Pokud k žádné události nedochází) a až *n* instancí. Vystavuje vlastní metriky pro Kubernetes AutoScale (vodorovně pod autoscaleer).  Pomocí kontejnerů Functions s KEDA je možné replikovat funkce bez serveru v jakémkoli clusteru Kubernetes.  Tyto funkce se dají nasadit taky pomocí funkce [virtuálních uzlů Azure Kubernetes Services (AKS)](../aks/virtual-nodes-cli.md) pro infrastrukturu bez serveru.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Správa KEDA a funkcí v Kubernetes

Pokud chcete spouštět funkce v clusteru Kubernetes, musíte nainstalovat komponentu KEDA. Tuto součást můžete nainstalovat pomocí [Azure Functions Core Tools](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Instalace pomocí Azure Functions Core Tools

Ve výchozím nastavení nástroje pro základní nástroje instalují součásti KEDA i Osiris, které podporují škálování na základě událostí a HTTP, v uvedeném pořadí.  Instalace používá `kubectl` běžící v aktuálním kontextu.

Nainstalujte do clusteru KEDA spuštěním následujícího příkazu install:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Nasazení aplikace Function App do Kubernetes

Do clusteru Kubernetes se systémem KEDA můžete nasadit jakoukoli aplikaci Function App.  Vzhledem k tomu, že se vaše funkce spouštějí v kontejneru Docker, `Dockerfile`projekt potřebuje.  Pokud ještě žádný nemáte, můžete přidat souboru Dockerfile spuštěním následujícího příkazu v kořenu projektu Functions:

```cli
func init --docker-only
```

Pokud chcete vytvořit image a nasadit své funkce do Kubernetes, spusťte následující příkaz:

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Nahraďte `<name-of-function-deployment>` názvem vaší aplikace Function App.

Tím se vytvoří prostředek `Deployment` Kubernetes `ScaledObject` , prostředek a `Secrets` `local.settings.json` , což zahrnuje proměnné prostředí importované ze souboru.

## <a name="removing-a-function-app-from-kubernetes"></a>Odebrání aplikace Function App z Kubernetes

Po nasazení můžete odebrat funkci odebráním přidruženého `Deployment`, `ScaledObject` `Secrets` vytvořeného.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Odinstalace KEDA z Kubernetes

K odebrání KEDA z clusteru Kubernetes můžete spustit následující základní příkazy nástrojů:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Podporované triggery v KEDA

KEDA je aktuálně ve verzi beta s podporou následujících aktivačních událostí Azure Functions:

* [Fronty Azure Storage](functions-bindings-storage-queue.md)
* [Fronty Azure Service Bus](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>Další kroky
Další informace naleznete v následujících materiálech:

* [Vytvoření funkce s použitím vlastní image](functions-create-function-linux-custom-image.md)
* [Místní psaní kódu a testování funkcí Azure Functions](functions-develop-local.md)
* [Jak funguje plán spotřeby funkcí Azure](functions-scale.md)