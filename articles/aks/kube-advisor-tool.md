---
title: Kontrola doporučených postupů v nasazení
titleSuffix: Azure Kubernetes Service
description: Zjistěte, jak zkontrolovat implementaci osvědčených postupů ve vašich nasazeních ve službě Azure Kubernetes service pomocí kube-advisor
services: container-service
author: seanmck
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 17e21c142dc354de7b72bc17396b19366027c5cd
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668400"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Kontrola osvědčených postupů Kubernetes v clusteru

Existuje několik osvědčených postupů, které byste měli dodržovat na nasazení Kubernetes, abyste zajistili nejlepší výkon a odolnost vašich aplikací. Nástroj kube-advisor můžete použít k vyhledání nasazení, která se těmito návrhy neřídí.

## <a name="about-kube-advisor"></a>O společnosti kube-advisor

[Nástroj kube-advisor][kube-advisor-github] je jeden kontejner určený ke spuštění v clusteru. Dotazuje se serveru Rozhraní API Kubernetes na informace o vašich nasazeních a vrátí sadu navrhovaných vylepšení.

Nástroj kube-advisor může podávat zprávy o žádosti o prostředky a omezuje chybějící v PodSpecs pro aplikace systému Windows, stejně jako linuxové aplikace, ale samotný nástroj kube-advisor musí být naplánován na pod linuxu. Pod můžete naplánovat tak, aby se spouštěl ve fondu uzlů s konkrétním osem pomocí [voliče uzlů][k8s-node-selector] v konfiguraci podu.

> [!NOTE]
> Nástroj kube-advisor je podporován společností Microsoft na základě nejlepšího úsilí. Problémy a návrhy by měly být podány na GitHubu.

## <a name="running-kube-advisor"></a>Běh kube-poradce

Spuštění nástroje v clusteru, který je nakonfigurován pro [řízení přístupu na základě rolí (RBAC),](azure-ad-integration.md)pomocí následujících příkazů. První příkaz vytvoří účet služby Kubernetes. Druhý příkaz spustí nástroj v podu pomocí tohoto účtu služby a nakonfiguruje pod pro odstranění po jeho ukončení. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }"
```

Pokud nepoužíváte RBAC, můžete spustit příkaz následujícím způsobem:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Během několika sekund byste měli vidět tabulku popisující potenciální vylepšení vašich nasazení.

![Kube-poradce výstup](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Provedené kontroly

Nástroj ověřuje několik doporučených postupů Kubernetes, z nichž každý má vlastní navrhovanou nápravu.

### <a name="resource-requests-and-limits"></a>Požadavky a omezení prostředků

Kubernetes podporuje definování [požadavků na prostředky a omezení specifikací podu][kube-cpumem]. Požadavek definuje minimální procesor a paměť potřebnou ke spuštění kontejneru. Limit definuje maximální procesor a paměť, která by měla být povolena.

Ve výchozím nastavení jsou nastaveny žádné požadavky nebo omezení na pod specifikace. To může vést k přeplánování uzlů a hladovění kontejnerů. Nástroj kube-advisor zvýrazní pody bez požadavků a limitů nastavených.

## <a name="cleaning-up"></a>Čištění

Pokud je v clusteru povolen rbac, můžete `ClusterRoleBinding` po spuštění nástroje vyčistit pomocí následujícího příkazu:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Pokud používáte nástroj proti clusteru, který není povolen RBAC, není vyžadováno žádné vyčištění.

## <a name="next-steps"></a>Další kroky

- [Řešení problémů se službou Azure Kubernetes](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors