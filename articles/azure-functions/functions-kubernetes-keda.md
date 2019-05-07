---
title: Služba Azure Functions s KEDA kubernetes
description: Porozumět postupu při spouštění Azure Functions v Kubernetes v cloudu nebo místně pomocí KEDA, automatické škálování na základě Kubernetes řízené událostmi.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: Azure functions, funkce, zpracování událostí, dynamické výpočty, architektura bez serveru, kubernetes
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: c82ed7aa841f53f5c81f3281ed1b09926e565e75
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077617"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Služba Azure Functions s KEDA kubernetes

Modul runtime Azure Functions poskytuje flexibilitu při hostování, kde a jak chcete.  [KEDA](https://github.com/kedacore/kore) (Kubernetes události řízené automatického škálování založeného) společně bez problémů s runtime Azure Functions a nástroje, které poskytují měřítko řízené událostmi v Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Pracovní funkce jak na základě Kubernetes

Služba Azure Functions se skládá ze dvou klíčových komponent: modul runtime a měřítka řadiče.  Modul runtime služby Functions spustí a spustí váš kód.  Modul runtime obsahuje logiku o tom, jak aktivovat, protokolování a spravovat provádění funkcí.  Další součást má měřítka řadiče.  Měřítka řadiče sleduje rychlost událostí, které cílí na funkci a proaktivně škálování počtu instancí používající vaši aplikaci.  Další informace najdete v tématu [hostování a škálování Azure Functions](functions-scale.md).

Funkce založené na Kubernetes poskytuje modul runtime služby Functions v [kontejneru Dockeru](functions-create-function-linux-custom-image.md) s založený na událostech prostřednictvím KEDA škálování.  KEDA můžete vertikálně snížit kapacitu na 0 instance (Pokud se žádné události vyskytnou) a až *n* instancí. Dělá to tak, že zveřejnění vlastní metriky pro Kubernetes automatického škálování (vodorovné automatického škálování Podů).  Pomocí funkce kontejnerů s KEDA umožňuje replikovat možnosti funkce bez serveru v libovolného clusteru Kubernetes.  Tyto funkce lze nasadit také pomocí [virtuální uzly služby Kubernetes v Azure (AKS)](../aks/virtual-nodes-cli.md) funkce bez serveru infrastruktury.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Správa KEDA a funkce v Kubernetes

Ke spuštění funkce ve vašem clusteru Kubernetes, je nutné nainstalovat komponentu KEDA. Můžete nainstalovat tuto součást pomocí [nástrojů Azure Functions Core](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Instalace s využitím Azure Functions Core Tools

Ve výchozím nastavení základní nástroje nainstaluje KEDA a Osiris součásti, které podporují založený na událostech a škálování protokolu HTTP, v uvedeném pořadí.  Instalace používá `kubectl` spuštěna v rámci aktuálního kontextu.

Spuštěním následujícího příkazu nainstalujte nainstalujte KEDA ve vašem clusteru:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Nasazení aplikace function app do Kubernetes

Všechny aplikace function app můžete nasadit do clusterů Kubernetes spuštěných KEDA.  Funkce spuštěné v kontejneru Dockeru, musí váš projekt `Dockerfile`.  Pokud ho ještě neexistuje, můžete přidat soubor Dockerfile spuštěním následujícího příkazu v kořenovém adresáři vašeho projektu funkce:

```cli
func init --docker-only
```

Sestavte image a nasazení vašich funkcí do Kubernetes, spusťte následující příkaz:

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Nahraďte `<name-of-function-deployment>` s názvem aplikace function App.

Tím se vytvoří Kubernetes `Deployment` prostředků, `ScaledObject` prostředků, a `Secrets`, což zahrnuje proměnné prostředí, které jsou importovány z vaší `local.settings.json` souboru.

## <a name="removing-a-function-app-from-kubernetes"></a>Odebrání aplikace function app Kubernetes

Po nasazení můžete odebrat funkci tak, že odeberete přidruženého `Deployment`, `ScaledObject`, `Secrets` vytvořili.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Odinstalace KEDA z Kubernetes

Můžete spustit následující příkaz nástroje core KEDA odebrání clusteru Kubernetes:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Podporované aktivační události v KEDA

KEDA je aktuálně ve verzi beta s podporou pro následující funkce Azure Functions aktivační události:

* [Fronty úložiště Azure](functions-bindings-storage-queue.md)
* [Fronty Azure Service Bus](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>Další kroky
Další informace najdete v následujících materiálech:

* [Vytvoření funkce pomocí vlastní image](functions-create-function-linux-custom-image.md)
* [Místní psaní kódu a testování funkcí Azure Functions](functions-develop-local.md)
* [Jak funguje plánu consumption funkce Azure functions](functions-scale.md)