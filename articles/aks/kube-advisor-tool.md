---
title: Vyhledat osvědčené postupy nasazení
titleSuffix: Azure Kubernetes Service
description: Naučte se kontrolovat implementaci osvědčených postupů v nasazeních ve službě Azure Kubernetes pomocí Kube-Advisoru.
services: container-service
author: seanmck
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 2b0078f1aff3ef81ee270f67de0fffddec3abab9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86255247"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Kontrola osvědčených postupů Kubernetes v clusteru

K dispozici je několik doporučených postupů, které byste měli dodržovat v nasazeních Kubernetes, abyste zajistili nejlepší výkon a odolnost vašich aplikací. Nástroj Kube-Advisor můžete použít k vyhledání nasazení, která nenásledují po těchto návrzích.

## <a name="about-kube-advisor"></a>O Kube – poradce

[Nástroj Kube-Advisor][kube-advisor-github] je jeden kontejner, který je určený ke spuštění v clusteru. Dotazuje se na server rozhraní Kubernetes API, kde najdete informace o vašich nasazeních a vrátí sadu navrhovaných vylepšení.

Nástroj Kube-Advisor může vykazovat požadavky na prostředky a omezení chybějící v PodSpecs pro aplikace Windows i pro aplikace pro Linux, ale samotný nástroj Kube-Advisor musí být naplánován na Linux pod. Můžete naplánovat spuštění pod v rámci fondu uzlů s konkrétním operačním systémem pomocí [voliče uzlů][k8s-node-selector] v konfiguraci pod.

> [!NOTE]
> Nástroj Kube-Advisor podporuje společnost Microsoft na nejvyšší úrovni. Problémy a návrhy by se měly zaevidovat na GitHubu.

## <a name="running-kube-advisor"></a>Spuštění Kube-Advisor

Chcete-li spustit nástroj na clusteru, který je konfigurován pro [řízení přístupu na základě role (RBAC)](./azure-ad-integration-cli.md), použijte následující příkazy. První příkaz vytvoří účet služby Kubernetes. Druhý příkaz spustí nástroj ve vztahu pod tímto účtem služby a po jeho ukončení nakonfiguruje pole pod pro odstranění. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }" --namespace default
```

Pokud nepoužíváte RBAC, můžete příkaz spustit takto:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Během několika sekund by se měla zobrazit tabulka s popisem potenciálních vylepšení nasazení.

![Kube – výstup poradce](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Kontroly provedeny

Nástroj ověřuje několik osvědčených postupů Kubernetes, z nichž každá má vlastní navrhovanou nápravu.

### <a name="resource-requests-and-limits"></a>Požadavky a omezení prostředků

Kubernetes podporuje definování [požadavků na prostředky a omezení na základě specifikací pod][kube-cpumem]. Požadavek definuje minimální procesor a paměť, které jsou potřeba ke spuštění kontejneru. Limit definuje maximální procesor a paměť, které by měly být povoleny.

Ve výchozím nastavení nejsou nastaveny žádné požadavky ani omezení na základě specifikací pod. To může vést k přeplánování uzlů a nedostatek kontejnerů. Nástroj Kube-Advisor zvýrazňuje lusky bez nastavených požadavků a omezení.

## <a name="cleaning-up"></a>Vyčištění

Pokud má váš cluster povolenou RBAC, můžete vyčistit `ClusterRoleBinding` po spuštění nástroje pomocí následujícího příkazu:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Pokud nástroj spouštíte pro cluster, který není ve RBAC povolený, není nutné žádné vyčištění.

## <a name="next-steps"></a>Další kroky

- [Řešení potíží se službou Azure Kubernetes](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
