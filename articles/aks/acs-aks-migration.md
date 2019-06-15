---
title: Migrace ze služby Azure Container Service (ACS) do služby Azure Kubernetes Service (AKS)
description: Migrace ze služby Azure Container Service (ACS) do služby Azure Kubernetes Service (AKS).
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: dcee8da943603fb0978caf9992be76347ca197d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65977718"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migrace ze služby Azure Container Service (ACS) do služby Azure Kubernetes Service (AKS)

Tento článek pomůže vám naplánovat a spustit úspěšná migrace mezi Azure Container Service (ACS) s využitím Kubernetes a Azure Kubernetes Service (AKS). Můžete učinit klíčová rozhodnutí, tato příručka podrobně popisuje rozdíly mezi ACS a AKS a obsahuje základní informace o procesu migrace.

## <a name="differences-between-acs-and-aks"></a>Rozdíly mezi ACS a AKS

Služba ACS a AKS se liší mezi klíčové oblasti, které mají vliv migrace. Před migrací jakékoli by měl zkontrolovat a naplánovat vyřešit následující rozdíly:

* Použití uzlů AKS [spravované disky](../virtual-machines/windows/managed-disks-overview.md).
    * Nespravované disky musí být převeden, než budete moct připojit k uzlů AKS.
    * Vlastní `StorageClass` objekty pro disky Azure musí být změněno z `unmanaged` k `managed`.
    * Žádné `PersistentVolumes` používejte `kind: Managed`.
* Podporuje AKS [více fondy uzlů](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) (aktuálně ve verzi preview).
* Uzly založené na Windows Server jsou nyní v [ve verzi preview ve službě AKS](https://azure.microsoft.com/blog/kubernetes-on-azure/).
* AKS podporuje omezenou sadu [oblastech](https://docs.microsoft.com/azure/aks/quotas-skus-regions).
* AKS je spravovaná služba s hostovanou rovina řízení Kubernetes. Můžete potřebovat upravit vaše aplikace, pokud jste upravili dříve konfigurace hlavní služby ACS.

## <a name="differences-between-kubernetes-versions"></a>Rozdíly mezi verzemi Kubernetes

Pokud migrujete na novější verzi Kubernetes (např. z 1.7.x k 1.9.x), projděte si následující zdroje informací o několik změn rozhraní API Kubernetes:

* [Migrace ThirdPartyResource CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [Úlohy rozhraní API změny verze 1.8 a 1.9](https://kubernetes.io/docs/reference/workloads-18-19/)

## <a name="migration-considerations"></a>Požadavky na migraci

### <a name="agent-pools"></a>Fondy agentů

I když AKS spravuje rovina řízení Kubernetes, definujete stále velikosti a počtu uzlů, které chcete zahrnout do nového clusteru. Za předpokladu, že chcete mapování 1:1 z ACS na AKS, budete chtít zachytit stávající informace o uzlu služby ACS. Tato data používejte při vytváření nového clusteru AKS.

Příklad:

| Name | Count | Velikost virtuálního počítače | Operační systém |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Protože další virtuální počítače se nasadí do vašeho předplatného při migraci, je třeba ověřit, že kvóty a omezení jsou dostačující pro tyto prostředky. 

Další informace najdete v tématu [předplatného Azure a omezení služeb](https://docs.microsoft.com/azure/azure-subscription-service-limits). Ke kontrole aktuální kvóty, na webu Azure Portal, přejděte [okně předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), vyberte své předplatné a pak vyberte **využití a kvóty**.

### <a name="networking"></a>Sítě

Pro komplexní aplikace budete obvykle migrovat postupně nikoli všechny najednou. To znamená, že staré a nové prostředí může být nutné pro komunikaci přes síť. Aplikace, které se dřív používal `ClusterIP` služby pro komunikaci může být potřeba zpřístupnit jako typ `LoadBalancer` a řádně zabezpečena.

K dokončení migrace, budete chtít nasměrování klientů nové služby, které jsou spuštěny v AKS. Doporučujeme, abyste přesměrování provozu aktualizací DNS tak, aby odkazoval na nástroj pro vyrovnávání zatížení, který je umístěný před clusteru AKS.

### <a name="stateless-applications"></a>Bezstavové aplikace

Migrace bezstavové aplikace je nejjednodušší případ. Budete použít YAML definic do nového clusteru, ujistěte se, že všechno funguje podle očekávání a přesměrovat provoz k aktivaci nového clusteru.

### <a name="stateful-applications"></a>Stavové aplikace

Pečlivě naplánujte si migraci stavových aplikací, aby se zabránilo ztrátě dat nebo neočekávaný výpadek.

#### <a name="highly-available-applications"></a>Vysoce dostupné aplikace

Můžete nasadit několik stavových aplikací v konfiguraci s vysokou dostupností. Tyto aplikace můžete kopírovat data na repliky. Pokud aktuálně používáte tento typ nasazení, je možné k vytvoření nového člena v novém clusteru AKS a následná migrace s minimálním vlivem na příjem dat volající. Obecně platí tady jsou kroky migrace pro tento scénář:

1. Vytvořte nový sekundární repliky v AKS.
2. Vyčkat, než data replikovat.
3. Neprovede více než sekundární repliky nový primární.
4. Přejděte na provoz clusteru AKS.

#### <a name="migrating-persistent-volumes"></a>Migrace svazků trvalé

Pokud migrujete existující trvalé svazky s AKS, budete obvykle postupujte takto:

1. Uveďte do stavu nečinnosti se zapíše do aplikace. (Tento krok je volitelný a nevyžaduje výpadek.)
2. Pořídit snímky disků.
3. Vytvořte nové spravované disky ze snímků.
4. Vytvořte trvalé svazky ve službě AKS.
5. Aktualizovat pod specifikací [použít existující svazky](https://docs.microsoft.com/azure/aks/azure-disk-volume) místo PersistentVolumeClaims (statické zřizování).
6. Nasaďte aplikaci do AKS.
7. Ověření.
8. Přejděte na provoz clusteru AKS.

> [!IMPORTANT]
> Pokud se rozhodnete uvést do stavu nečinnosti zápisy, budete potřebovat k replikaci dat na nové nasazení. Jinak budete přeskočíte data, která byla zapsána po se pořídily snímky disků.

Některé open source nástroje vám umožňují vytvářet spravované disky a svazky mezi clustery Kubernetes migrovat:

* [Rozšíření Azure CLI kopie disku](https://github.com/noelbundick/azure-cli-disk-copy-extension) zkopíruje a převede disky napříč skupinami prostředků a oblasti Azure.
* [Rozšíření Azure Kube CLI](https://github.com/yaron2/azure-kube-cli) vytvoří výčet svazky ACS Kubernetes a migraci do clusteru AKS.

#### <a name="azure-files"></a>Soubory Azure

Na rozdíl od disků je možné připojit soubory Azure na několika hostitelích současně. V clusteru AKS Azure a Kubernetes není znemožňují vytvoření pod, která stále používá cluster služby ACS. Pokud chcete zabránit ztrátě dat a neočekávané chování, ujistěte se, že není clustery zapisovat do stejné soubory, které ve stejnou dobu.

Pokud vaše aplikace můžete hostovat víc replik, které odkazují na stejnou sdílenou složku, postupujte podle kroků migrace bezstavové a nasadit vaše definice YAML do nového clusteru. Pokud ne, jedním z přístupů je to možné migrace zahrnuje následující kroky:

1. Nasazení aplikace do AKS s počtem replik 0.
2. Škálování aplikace na služby ACS na hodnotu 0. (Tento krok vyžaduje výpadek.)
3. Škálování aplikací v AKS až do 1.
4. Ověření.
5. Přejděte na provoz clusteru AKS.

Pokud chcete začít s prázdnou sdílenou složku a vytvoření kopie zdrojových dat, můžete použít [ `az storage file copy` ](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) příkazy migrovat svoje data.

### <a name="deployment-strategy"></a>Strategie nasazení

Doporučujeme, abyste konfiguraci známého úspěšného nasazení do AKS pomocí svého existujícího kanálu CI/CD. Klonovat vaše stávající úkoly nasazení a ujistěte se, že `kubeconfig` odkazuje na novém clusteru AKS.

Pokud to není možné exportovat definice prostředků ze služby ACS a použít je u AKS. Můžete použít `kubectl` exportovat objekty.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Několik nástrojů pro open source vám může pomoci, v závislosti na potřebách nasazení:

* [Velero](https://github.com/heptio/ark) (Tento nástroj vyžaduje Kubernetes 1.7).
* [Rozšíření Azure Kube CLI](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Kroky migrace

1. [Vytvoření clusteru AKS](https://docs.microsoft.com/azure/aks/create-cluster) prostřednictvím webu Azure portal, Azure CLI nebo šablony Azure Resource Manageru.

   > [!NOTE]
   > Najít ukázkové šablony Azure Resource Manageru pro AKS na [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) úložišti na Githubu.

2. Proveďte potřebné změny do definic YAML. Nahraďte třeba `apps/v1beta1` s `apps/v1` pro `Deployments`.

3. [Migrace svazků](#migrating-persistent-volumes) (volitelné) z clusteru služby ACS pro váš cluster AKS.

4. Používejte systém CI/CD pro nasazení aplikací do AKS. Nebo použijte kubectl definice YAML.

5. Ověření. Ujistěte se, že vaše aplikace fungovat podle očekávání a jakékoli migrovaná data zkopírovala přes.

6. Přesměrování provozu. Aktualizujte DNS tak, aby vaše nasazení služby AKS nasměrování klientů.

7. Dokončete úkoly po migraci. Pokud jste migrovali svazky a nezahrnuli uvedení zápisy, zkopírujte tato data do nového clusteru.
