---
title: Správa hybridní infrastruktury ve velkém měřítku pomocí ARC Azure
description: Naučte se efektivně spravovat počítače zákazníků a Kubernetes clustery mimo Azure.
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 66a798265683045d7ff9f3d8d811141800d08f9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336611"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>Správa hybridní infrastruktury ve velkém měřítku pomocí ARC Azure

Jako poskytovatel služeb můžete mít k [Azure Lighthouse](../overview.md)k dispozici několik klientů pro zákazníky. Azure Lighthouse umožňuje poskytovatelům služeb provádět operace ve velkém měřítku napříč několika klienty Azure Active Directory (Azure AD) najednou a díky tomu se úlohy správy zefektivňují efektivněji.

[Azure ARC](../../azure-arc/overview.md) pomáhá zjednodušit složitá a distribuovaná prostředí v místním, hraničním i cloudovém prostředí a umožňuje nasazení služeb Azure kdekoli a rozšíření správy Azure na jakoukoli infrastrukturu.

U [serverů s podporou ARC Azure](../../azure-arc/servers/overview.md)mohou zákazníci spravovat všechny počítače se systémem Windows a Linux hostované mimo Azure ve své podnikové síti stejným způsobem jako při správě nativních virtuálních počítačů Azure. Propojením hybridního počítače s Azure se počítač stane připojeným a bude se v Azure považovat za prostředek. Poskytovatelé služeb pak můžou spravovat tyto počítače mimo Azure spolu s prostředky Azure svých zákazníků.

[Azure ARC s povoleným Kubernetes (Preview)](../../azure-arc/kubernetes/overview.md) umožňuje zákazníkům připojit a nakonfigurovat Kubernetes clustery v rámci nebo mimo Azure. Když je cluster Kubernetes připojený ke službě Azure ARC, zobrazí se v Azure Portal s ID Azure Resource Manager a spravovanou identitou. Clustery jsou připojené ke standardním předplatným Azure, jsou umístěné ve skupině prostředků a můžou přijímat značky stejně jako všechny ostatní prostředky Azure.

Toto téma poskytuje přehled o tom, jak můžou poskytovatelé služeb používat servery s podporou ARC Azure a Kubernetes (Preview), a to škálovatelným způsobem, jak spravovat hybridní prostředí svých zákazníků s viditelností ve všech spravovaných klientech zákazníků.

> [!TIP]
> I když v tomto tématu odkazujeme na poskytovatele služeb a zákazníky, tyto doprovodné materiály se vztahují také na [podniky, které používají Azure Lighthouse ke správě více tenantů](../concepts/enterprise.md).

## <a name="manage-hybrid-servers-at-scale-with-azure-arc-enabled-servers"></a>Správa hybridních serverů se škálováním pomocí serverů s podporou ARC Azure

Jako poskytovatel služeb můžete spravovat místní počítače se systémem Windows Server nebo Linux mimo Azure, které vaši zákazníci připojili ke svému předplatnému pomocí [agenta připojeného počítače Azure](../../azure-arc/servers/agent-overview.md).

Při zobrazení prostředků delegovaného předplatného v Azure Portal uvidíte tyto připojené počítače označené pomocí **ARC Azure**. Tyto připojené počítače můžete spravovat pomocí konstrukcí Azure, například Azure Policy a označování stejným způsobem jako při správě prostředků Azure zákazníka. Můžete taky pracovat v klientech zákazníků a spravovat všechny připojené hybridní počítače.

Můžete například [zajistit, aby se stejná sada zásad používala v hybridních počítačích zákazníků](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md). Pomocí Azure Security Center můžete také monitorovat dodržování předpisů napříč hybridními prostředími vašich zákazníků nebo [pomocí Azure monitor shromažďovat data přímo z hybridních počítačů](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md) do log Analyticsho pracovního prostoru. [Rozšíření virtuálních počítačů](../../azure-arc/servers/manage-vm-extensions.md) je možné nasadit na virtuální počítače, které nejsou v Azure s Windows a Linux, což zjednodušuje správu hybridních počítačů zákazníka.

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes-preview"></a>Správa hybridních clusterů Kubernetes se škálováním s povoleným Kubernetes ARC Azure (Preview)

> [!NOTE]
> Služba Azure ARC s povoleným Kubernetes je aktuálně ve verzi Preview. V tuto chvíli to nedoporučujeme pro produkční úlohy.

Můžete spravovat clustery Kubernetes [připojené k předplatnému zákazníka pomocí ARC Azure](../../azure-arc/kubernetes/connect-cluster.md), stejně jako kdyby běžely v Azure.

Pokud zákazník vytvořil [účet instančního objektu pro připojování clusterů Kubernetes do Azure ARC](../../azure-arc/kubernetes/create-onboarding-service-principal.md), můžete k tomuto účtu instančního objektu přistupovat a spravovat clustery. To můžou dělat uživatelé ve správě tenanta, kterým bylo udělená integrovaná role "cluster Kubernetes-Azure ARC" Azure ARC, když se předplatné obsahující účet instančního objektu [připojilo do Azure Lighthouse](onboard-customer.md).

[Konfigurace](../../azure-arc/kubernetes/use-gitops-connected-cluster.md) a [Helm grafy](../../azure-arc/kubernetes/use-gitops-with-helm.md) můžete nasadit pomocí GitOps pro připojené clustery.

Připojené clustery můžete také monitorovat pomocí Azure Monitor a [pomocí Azure Policy použít konfigurace clusteru ve velkém měřítku](../../azure-arc/kubernetes/use-azure-policy.md).

## <a name="next-steps"></a>Další kroky

- Prozkoumejte Jumpstarts a ukázky v [úložišti GitHub Azure ARC](https://github.com/microsoft/azure_arc). 
- Přečtěte si o [podporovaných scénářích pro servery s podporou ARC Azure](../../azure-arc/servers/overview.md#supported-scenarios).
- Přečtěte si o [Kubernetes distribucích podporovaných Azure ARC](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions).
- Naučte se, jak [nasadit zásadu ve velkém měřítku](policy-at-scale.md).
- Naučte se [používat protokoly Azure monitor ve velkém měřítku](monitor-at-scale.md).

