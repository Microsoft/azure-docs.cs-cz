---
title: Migrace ze služby Azure Container Service (ACS) do služby Azure Kubernetes Service (AKS)
description: Migrace ze služby Azure Container Service (ACS) do služby Azure Kubernetes Service (AKS)
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: cb143998ac46f7f86b2dbf47b69cee7843418f5d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43191649"
---
# <a name="migrating-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migrace ze služby Azure Container Service (ACS) do služby Azure Kubernetes Service (AKS)

Cílem tohoto dokumentu je vám pomůžou naplánovat a spustit úspěšná migrace mezi Azure Container Service s Kubernetes (ACS) a Azure Kubernetes Service (AKS). Tato příručka podrobně popisuje rozdíly mezi ACS a AKS, obsahuje základní informace o procesu migrace a využít učinit klíčová rozhodnutí.

## <a name="differences-between-acs-and-aks"></a>Rozdíly mezi ACS a AKS

Služba ACS a AKS se liší mezi klíčové oblasti, které mají vliv migrace. Je vhodné zkontrolovat a naplánovat následující rozdíly před migrací jakékoli řešení.

* Použití uzlů AKS [Managed Disks](../virtual-machines/windows/managed-disks-overview.md)
    * Nespravované disky muset převést předtím, než může být přiřazena uzlů AKS
    * Vlastní `StorageClass` objekty pro disky Azure bude muset změnit z `unmanaged` do `managed`
    * Žádné `PersistentVolumes` musíte použít `kind: Managed`
* AKS v současné době podporuje pouze jednoho agenta pro fond
* Jsou nyní v systému Windows na serveru uzly [ve verzi private preview](https://azure.microsoft.com/en-us/blog/kubernetes-on-azure/)
* Zkontrolujte seznam AKS [podporované oblasti](https://docs.microsoft.com/azure/aks/container-service-quotas)
* AKS je spravovaná služba s hostovanou rovina řízení Kubernetes. Budete muset upravit vaše aplikace, pokud jste upravili dříve konfigurace hlavní služby ACS

### <a name="differences-between-kubernetes-versions"></a>Rozdíly mezi verzemi Kubernetes

Pokud migrujete na novější verzi Kubernetes (ex: 1.7.x k 1.9.x), několik změn k8s rozhraní API, které vyžadují vaši pozornost.

* [Migrace ThirdPartyResource CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [Úlohy rozhraní API změny verze 1.8 a 1.9](https://kubernetes.io/docs/reference/workloads-18-19/).

## <a name="migration-considerations"></a>Aspekty migrace

### <a name="agent-pools"></a>Fondy agentů

Zatímco AKS spravuje rovina řízení Kubernetes, definujete stále velikosti a počtu uzlů, které chcete zahrnout do nového clusteru. Za předpokladu, že chcete mapování 1:1 z ACS na AKS, budete chtít zachytit stávající informace o uzlu služby ACS. Tato data budete používat při vytváření nového clusteru AKS.

Příklad:

| Název | Počet | Velikost virtuálního počítače | Operační systém |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Protože další virtuální počítače se nasadí do vašeho předplatného při migraci, je třeba ověřit, že kvóty a omezení jsou dostačující pro tyto prostředky. Další informace o kontrole [předplatného Azure a omezení služeb](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits). Pokud chcete zkontrolovat aktuální kvóty, přejděte na [okně předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure Portal, vyberte své předplatné a pak vyberte `Usage + quotas`.

### <a name="networking"></a>Sítě

Pro komplexní aplikace budete obvykle migrovat postupně nikoli všechny najednou. Která znamená, že staré a nové prostředí může komunikovat přes síť. Aplikace, které byly dřív možné používat `ClusterIP` služby pro komunikaci možná muset být vystavena jako typ `LoadBalancer` a řádně zabezpečena.

K dokončení migrace, budete chtít nasměrování klientů nové služby spuštěné v AKS. Doporučený postup pro přesměrování přenosu dat, je aktualizace DNS tak, aby odkazoval na nástroj pro vyrovnávání zatížení, který je umístěný před clusteru AKS.

### <a name="stateless-applications"></a>Bezstavové aplikace

Migrace bezstavové aplikace je nejjednodušší případ. Budete použít YAML definic do nového clusteru, ověřte, že vše funguje podle očekávání a přesměrování provozu do nového clusteru aktivovali.

### <a name="stateful-applications"></a>Stavové aplikace

Migrace stavové aplikace vyžaduje pečlivé plánování, aby se zabránilo ztrátě dat nebo neočekávaný výpadek.

#### <a name="highly-available-applications"></a>Vysoce dostupné aplikace

Některé stavových aplikací je možné nasadit v konfiguraci vysoké dostupnosti a může kopírovat data mezi repliky. Pokud se to popisuje vaše aktuální nasazení, je možné, že k vytvoření nového člena v novém clusteru AKS a migrace s minimálním dopadem na příjem dat volající. Obecně jsou kroky v tomto scénáři migrace:

1. Vytvořit nový sekundární repliky v AKS
2. Vyčkat, než replikaci dat
3. Neprovede více než sekundární repliky nový primární
4. Přejděte na provoz clusteru AKS

#### <a name="migrating-persistent-volumes"></a>Migrace svazků trvalé

Existuje několik faktorů vzít v úvahu, pokud migrujete existující svazky s trvalou AKS. Potřebný postup se obecně:

1. (Volitelné) Uveďte do stavu nečinnosti zapisuje do aplikace (nevyžaduje výpadek)
2. Disky se snímky
3. Vytvoření nové služby Managed Disks ze snímků
4. Vytvářet trvalé svazky ve službě AKS
5. Aktualizovat Pod specifikací [použít existující svazky](https://docs.microsoft.com/en-us/azure/aks/azure-disk-volume) místo PersistentVolumeClaims (statické zřizování)
6. Nasazení aplikace do AKS
7. Ověření
8. Přejděte na provoz clusteru AKS

> **Důležité**: Pokud se rozhodnete uvést do stavu nečinnosti zápisy, bude nutné k replikaci dat na nové nasazení, jako nebudete mít k dispozici data, která byla zapsána od snímku disku

Existují Open source nástroje, které vám může pomoct vytvořit Managed Disks a migrace svazků mezi clustery Kubernetes.

* [noelbundick/azure-cli-disk-extension](https://github.com/noelbundick/azure-cli-disk-copy-extension) – kopírování a převést disky napříč skupinami prostředků a oblasti Azure
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli) – zobrazení výčtu svazků ACS Kubernetes a migrace do clusteru AKS

#### <a name="azure-files"></a>Soubory Azure

Na rozdíl od disků je možné připojit soubory Azure na několika hostitelích současně. Azure ani Kubernetes brání ve vytvoření Pod ve vašem clusteru AKS, který je stále používá cluster služby ACS. Aby se zabránilo ztrátě dat a neočekávané chování, měli byste zajistit, že oba clustery nejsou zápisu do stejné soubory, které ve stejnou dobu.

Pokud vaše aplikace může hostovat víc replik odkazuje na stejnou sdílenou složku, můžete postupujte podle kroků migrace bezstavové a nasadit vaše definice YAML do nového clusteru.

Pokud ne, jedním z přístupů je to možné migrace zahrnuje následující kroky:

1. Nasazení aplikace do AKS s počtem replik 0
2. Škálování aplikace na služby ACS na hodnotu 0 (nevyžaduje výpadek)
3. Škálování aplikací v AKS až 1
4. Ověření
5. Přejděte na provoz clusteru AKS

V případech, kde chcete začít s prázdnou sdílenou složku a potom vytvořte kopii zdrojová data, můžete použít [ `az storage file copy` ](https://docs.microsoft.com/en-us/cli/azure/storage/file/copy?view=azure-cli-latest) příkazy migrovat svoje data.

### <a name="deployment-strategy"></a>Strategie nasazení

Doporučeným způsobem je konfigurace známého úspěšného nasazení do AKS pomocí svého existujícího kanálu CI/CD. Budete klonování vaše stávající úkoly nasazení a ujistěte se, že vaše `kubeconfig` odkazuje na novém clusteru AKS.

V případech, kdy které uživatele není možné musíte exportovat definice prostředků ze služby ACS a použít je u AKS. Můžete použít `kubectl` exportovat objekty.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Existují také několik open source nástroje, které můžou pomoct podle svých potřeb:

* [heptio/ark](https://github.com/heptio/ark) -vyžaduje k8s 1.7
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli)
* [mhausenblas/reshifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Kroky migrace

### <a name="1-create-an-aks-cluster"></a>1. Vytvoření clusteru AKS

Můžete postupovat podle dokumentace k [vytvoření clusteru AKS](https://docs.microsoft.com/en-us/azure/aks/create-cluster) prostřednictvím webu Azure portal, Azure CLI nebo šablony Resource Manageru.

> Ukázkové šablony Azure Resource Manageru můžete najít pro AKS na [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) úložišti na Githubu

### <a name="2-modify-applications"></a>2. Upravit aplikace

Ujistěte se, veškeré nezbytné úpravy definic YAML. Např.: nahrazení `apps/v1beta1` s `apps/v1` pro `Deployments`

### <a name="3-optional-migrate-volumes"></a>3. (Volitelné) Migrace svazků

Migrace svazků z clusteru služby ACS do clusteru AKS. Další podrobnosti najdete v [migrace trvalé svazky](#Migrating-Persistent-Volumes) oddílu.

### <a name="4-deploy-applications"></a>4. Nasazení aplikací

Používejte systém CI/CD a nasadit aplikace pro AKS pomocí kubectl použít definice YAML.

### <a name="5-validate"></a>5. Ověření

Ověřte, že jsou vaše aplikace funguje podle očekávání a jakékoli migrovaná data zkopírovala přes.

### <a name="6-redirect-traffic"></a>6. Přesměrování provozu

Aktualizujte DNS tak, aby vaše nasazení služby AKS nasměrování klientů.

### <a name="7-post-migration-tasks"></a>7. Úkoly po migraci

Pokud jste migrovali svazky a nezahrnuli zápisy uveďte do stavu nečinnosti, bude nutné zkopírovat tato data do nového clusteru.
