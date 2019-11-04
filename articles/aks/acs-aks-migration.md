---
title: Migrace z Azure Container Service (ACS) do služby Azure Kubernetes Service (AKS)
description: Migrujte z Azure Container Service (ACS) do služby Azure Kubernetes Service (AKS).
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: 84e0af89e2b3247bc922ab84286a79a0934323a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473001"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migrace z Azure Container Service (ACS) do služby Azure Kubernetes Service (AKS)

Tento článek vám pomůže naplánovat a provést úspěšnou migraci mezi Azure Container Service (ACS) pomocí služby Kubernetes a Azure Kubernetes Service (AKS). Tato příručka podrobně popisuje rozdíly mezi ACS a AKS a poskytuje přehled o procesu migrace.

## <a name="differences-between-acs-and-aks"></a>Rozdíly mezi ACS a AKS

Služby ACS a AKS se v některých klíčových oblastech, které mají vliv na migraci, liší. Před migrací byste měli zkontrolovat a naplánovat, aby se vyřešily následující rozdíly:

* Uzly AKS používají [spravované disky](../virtual-machines/windows/managed-disks-overview.md).
    * Než budete moct tyto nespravované disky připojit k AKS uzlům, je nutné je převést.
    * Vlastní `StorageClass` objekty pro disky Azure se musí změnit z `unmanaged` na `managed`.
    * Všechny `PersistentVolumes` by měly používat `kind: Managed`.
* AKS podporuje [více fondů uzlů](https://docs.microsoft.com/azure/aks/use-multiple-node-pools).
* Uzly založené na Windows serveru jsou momentálně ve [verzi Preview v AKS](https://azure.microsoft.com/blog/kubernetes-on-azure/).
* AKS podporuje omezené sady [oblastí](https://docs.microsoft.com/azure/aks/quotas-skus-regions).
* AKS je spravovaná služba s hostovanou rovinou ovládacího prvku Kubernetes. Pokud jste předtím změnili konfiguraci vašich hlavních serverů ACS, možná budete muset aplikace upravit.

## <a name="differences-between-kubernetes-versions"></a>Rozdíly mezi verzemi Kubernetes

Pokud migrujete na novější verzi Kubernetes, přečtěte si následující zdroje, abyste porozuměli strategiím správy verzí Kubernetes:

* [Zásady podpory pro zkosení verze Kubernetes a verze](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)

## <a name="migration-considerations"></a>Požadavky na migraci

### <a name="agent-pools"></a>Fondy agentů

I když AKS spravuje rovinu ovládacího prvku Kubernetes, stále definujete velikost a počet uzlů, které chcete zahrnout do nového clusteru. Za předpokladu, že chcete, aby bylo mapování 1:1 z ACS na AKS, budete chtít zachytit existující informace o uzlu ACS. Tato data použijte při vytváření nového clusteru AKS.

Příklad:

| Name (Název) | Počet | Velikost virtuálního počítače | Operační systém |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1\. místo | Standard_D2_v2 | Windows |

Vzhledem k tomu, že během migrace budou do svého předplatného nasazené další virtuální počítače, měli byste ověřit, že vaše kvóty a limity jsou pro tyto prostředky dostatečné. 

Další informace najdete v tématu [omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits). Pokud chcete zjistit aktuální kvóty, v Azure Portal v okně pro [předplatné](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)vyberte své předplatné a pak vyberte **využití + kvóty**.

### <a name="networking"></a>Sítě

U složitých aplikací se obvykle provádí migrace v čase, nikoli najednou. To znamená, že stará a nová prostředí můžou potřebovat komunikovat přes síť. Aplikace, které dřív používaly `ClusterIP` služby ke komunikaci, můžou být nutné vystavit jako `LoadBalancer` typu a správně zabezpečit.

K dokončení migrace budete chtít klienty nasměrovat na nové služby, které běží na AKS. Pro přesměrování provozu doporučujeme, abyste provedli aktualizaci DNS tak, aby odkazovaly na Load Balancer, která je umístěná před clusterem AKS.

### <a name="stateless-applications"></a>Bezstavové aplikace

Nestavová migrace aplikace je nejjednodušším případem. Vaše definice YAML se aplikují na nový cluster, ujistěte se, že vše funguje podle očekávání, a přesměrujte provoz na aktivaci nového clusteru.

### <a name="stateful-applications"></a>Stavové aplikace

Pečlivě naplánujte migraci stavových aplikací, abyste se vyhnuli ztrátě dat nebo neočekávanému výpadku.

#### <a name="highly-available-applications"></a>Vysoce dostupné aplikace

V konfiguraci vysoké dostupnosti můžete nasazovat některé stavové aplikace. Tyto aplikace mohou kopírovat data mezi replikami. Pokud tento způsob nasazení aktuálně používáte, může být možné vytvořit nového člena v novém clusteru AKS a pak migrovat s minimálním účinkem na volajících. Obecně platí postup migrace pro tento scénář:

1. Vytvoří novou sekundární repliku v AKS.
2. Počkejte na replikaci dat.
3. Převzetí služeb při selhání za účelem vytvoření sekundární repliky jako nového primárního.
4. Nasměrujte provoz do clusteru AKS.

#### <a name="migrating-persistent-volumes"></a>Migrace trvalých svazků

Pokud migrujete existující trvalé svazky na AKS, obecně postupujte podle těchto kroků:

1. Neuvést do stavu zápisy do aplikace. (Tento krok je nepovinný a vyžaduje výpadek.)
2. Pořizovat snímky disků.
3. Vytvořte nové spravované disky ze snímků.
4. Vytvořte v AKS trvalé svazky.
5. V případě, že chcete [použít existující svazky](https://docs.microsoft.com/azure/aks/azure-disk-volume) místo PersistentVolumeClaims (statické zřizování), aktualizujte specifikace pod.
6. Nasaďte aplikaci do AKS.
7. Oproti.
8. Nasměrujte provoz do clusteru AKS.

> [!IMPORTANT]
> Pokud se rozhodnete neuvést zápisy do nečinnosti, bude nutné replikovat data do nového nasazení. V opačném případě nebudete mít data, která byla napsána po provedení snímků disku.

Některé open source nástroje vám pomůžou vytvořit spravované disky a migrovat svazky mezi Kubernetes clustery:

* [Rozšíření kopírování disku Azure CLI](https://github.com/noelbundick/azure-cli-disk-copy-extension) : zkopíruje a převede disky mezi skupinami prostředků a oblastmi Azure.
* [Rozšíření Azure Kube CLI](https://github.com/yaron2/azure-kube-cli) vyčísluje svazky Kubernetes ACS a migruje je do clusteru AKS.

#### <a name="azure-files"></a>Azure Files

Na rozdíl od disků je možné soubory Azure připojit k více hostitelům současně. V clusteru AKS vám Azure a Kubernetes nebrání v vytváření pod tím, že váš cluster ACS stále používá. Aby nedošlo ke ztrátě dat a neočekávanému chování, zajistěte, aby clustery nepsaly do stejných souborů současně.

Pokud vaše aplikace může hostovat několik replik, které odkazují na stejnou sdílenou složku, postupujte podle kroků migrace bez stavů a nasaďte definice YAML do nového clusteru. Pokud ne, jeden z možných způsobů migrace zahrnuje následující kroky:

1. Nasaďte aplikaci do AKS s počtem replik 0.
2. Škálovat aplikaci na ACS na 0. (Tento krok vyžaduje výpadek.)
3. Škálujte aplikaci na AKS až na 1.
4. Oproti.
5. Nasměrujte provoz do clusteru AKS.

Pokud chcete začít s prázdnou sdílenou složkou a vytvořit kopii zdrojových dat, můžete k migraci dat použít [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) příkazy.

### <a name="deployment-strategy"></a>Strategie nasazení

Pro nasazení známé konfigurace do AKS doporučujeme použít stávající kanál CI/CD. Naklonujte stávající úlohy nasazení a zajistěte, aby `kubeconfig` odkazovaly na nový cluster AKS.

Pokud to není možné, exportujte definice prostředků z ACS a pak je použijte na AKS. K exportu objektů můžete použít `kubectl`.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

V závislosti na potřebách nasazení může pomáhat několik open-source nástrojů:

* [Velero](https://github.com/heptio/ark) (Tento nástroj vyžaduje Kubernetes 1,7.)
* [Rozšíření CLI Azure Kube](https://github.com/yaron2/azure-kube-cli)
* [Posunutí](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Kroky migrace

1. [Vytvořte cluster AKS](https://docs.microsoft.com/azure/aks/create-cluster) prostřednictvím Azure Portal, Azure CLI nebo šablony Azure Resource Manager.

   > [!NOTE]
   > V úložišti [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) na GitHubu najdete ukázku šablon Azure Resource Manager pro AKS.

2. Proveďte potřebné změny v definicích YAML. `apps/v1beta1` například nahraďte `apps/v1` pro `Deployments`.

3. [Migrujte svazky](#migrating-persistent-volumes) (volitelné) z vašeho clusteru ACS do clusteru AKS.

4. K nasazení aplikací do AKS použijte svůj systém CI/CD. Nebo použijte kubectl k použití definicí YAML.

5. Oproti. Ujistěte se, že vaše aplikace fungují podle očekávání a že se přesunula veškerá migrovaná data.

6. Přesměrování provozu. Aktualizujte DNS tak, aby odkazovaly na klienty na nasazení AKS.

7. Dokončete úkoly po migraci. Pokud jste migrovali svazky a neuvedli do nečinnosti zápisy, zkopírujte tato data do nového clusteru.
