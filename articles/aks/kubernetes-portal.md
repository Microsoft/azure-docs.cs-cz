---
title: Přístup k prostředkům Kubernetes z Azure Portal (Preview)
description: Naučte se pracovat s prostředky Kubernetes a spravovat cluster Azure Kubernetes Service (AKS) z Azure Portal.
services: container-service
author: laurenhughes
ms.topic: article
ms.date: 09/21/2020
ms.author: lahugh
ms.openlocfilehash: 6a9567669445cb5aa94c1108051c961a216fabad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335598"
---
# <a name="access-kubernetes-resources-from-the-azure-portal-preview"></a>Přístup k prostředkům Kubernetes z Azure Portal (Preview)

Azure Portal obsahuje prohlížeč prostředků Kubernetes (Preview) pro snadný přístup k prostředkům Kubernetes v clusteru Azure Kubernetes Service (AKS). Zobrazení prostředků Kubernetes z Azure Portal omezuje přepínání kontextu mezi Azure Portal a `kubectl` nástrojem příkazového řádku, což zjednodušuje možnosti zobrazení a úprav prostředků Kubernetes. Prohlížeč prostředků aktuálně obsahuje několik typů prostředků, jako jsou nasazení, lusky a sady replik.

Zobrazení prostředků Kubernetes z Azure Portal nahrazuje [doplněk řídicího panelu AKS][kubernetes-dashboard], který je nastaven pro vyřazení.

>[!NOTE]
>Capabilty se v tuto chvíli nepodporuje u [privátních clusterů služby Azure Kubernetes](https://docs.microsoft.com/azure/aks/private-clusters).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Požadavky

K zobrazení prostředků Kubernetes v Azure Portal potřebujete cluster AKS. Podporuje se libovolný cluster, ale pokud používáte integraci Azure Active Directory (Azure AD), musí cluster používat [integraci Azure AD spravovanou pomocí AKS][aks-managed-aad]. Pokud váš cluster používá starší verzi služby Azure AD, můžete cluster upgradovat na portálu nebo pomocí rozhraní příkazového [řádku Azure CLI][cli-aad-upgrade].

## <a name="view-kubernetes-resources"></a>Zobrazení prostředků Kubernetes

Pokud chcete zobrazit prostředky Kubernetes, přejděte do svého clusteru AKS v Azure Portal. Navigační podokno na levé straně se používá pro přístup k prostředkům. Mezi prostředky patří:

- **Obory názvů** zobrazují obory názvů vašeho clusteru. Filtr v horní části seznamu oborů názvů poskytuje rychlý způsob, jak filtrovat a zobrazit prostředky oboru názvů.
- **Úlohy** zobrazují informace o nasazeních, luskech, sadách replik a sadách démonů nasazených do vašeho clusteru. Níže uvedený snímek obrazovky ukazuje výchozí systémovou lusky v ukázkovém clusteru AKS.
- **Služby a příchozí přenosy** zobrazují všechny prostředky služby a příchozí přenosy v clusteru.

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Kubernetes pod informace zobrazené v Azure Portal." lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>Nasazení aplikace

V tomto příkladu použijeme náš vzorový cluster AKS k nasazení hlasovací aplikace Azure z [rychlého startu AKS][portal-quickstart].

1. Vyberte **Přidat** z libovolného zobrazení prostředků (obor názvů, úlohy nebo služby a příchozí přenosy).
1. Vložte YAML pro hlasovou aplikaci Azure z rychlého startu [AKS][portal-quickstart].
1. V dolní části editoru YAML vyberte **Přidat** a nasaďte aplikaci. 

Po přidání souboru YAML zobrazí prohlížeč prostředků obě služby Kubernetes, které byly vytvořeny: interní služba (Azure-hlasování) a externí službu (Azure-hlasování-přední) pro přístup k aplikaci hlasování Azure. Externí služba obsahuje propojenou externí IP adresu, abyste ji mohli snadno zobrazit v prohlížeči.

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Kubernetes pod informace zobrazené v Azure Portal." lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>Monitorovat přehledy nasazení

AKS clustery s podporou [Azure monitor pro kontejnery][enable-monitor] můžou rychle zobrazit přehledy o nasazení. V zobrazení prostředků Kubernetes můžou uživatelé zobrazit stav živého provozu jednotlivých nasazení, včetně využití CPU a paměti, a také přejít na Azure monitor, kde najdete podrobnější informace. Tady je příklad přehledů nasazení z ukázkového clusteru AKS:

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Kubernetes pod informace zobrazené v Azure Portal." lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>Upravit YAML

Zobrazení prostředků Kubernetes zahrnuje také Editor YAML. Integrovaný editor YAML znamená, že můžete aktualizovat nebo vytvářet služby a nasazení z portálu a okamžitě použít změny.

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="Kubernetes pod informace zobrazené v Azure Portal.":::

Po úpravě YAML se změny aplikují tak, že vyberete **zkontrolovat + Uložit**, potvrďte změny a pak znovu uložíte.

>[!WARNING]
> Provádění přímých změn v produkčním prostředí prostřednictvím uživatelského rozhraní nebo CLI se nedoporučuje, měli byste využít [osvědčené postupy průběžné integrace (CI) a průběžného nasazování (CD)](kubernetes-action.md). Možnosti správy Kubernetes a YAML v portálu Azure jsou vytvořené pro učení a získávání nových nasazení v nastavení pro vývoj a testování.

## <a name="troubleshooting"></a>Řešení potíží

Tato část řeší běžné problémy a postup řešení potíží.

### <a name="unauthorized-access"></a>Neautorizovaný přístup

Chcete-li získat přístup k prostředkům Kubernetes, musíte mít přístup ke clusteru AKS, rozhraní Kubernetes API a objektům Kubernetes. Ujistěte se, že jste buď Správce clusteru, nebo uživatel s příslušnými oprávněními pro přístup ke clusteru AKS. Další informace o zabezpečení clusteru najdete v tématu [Možnosti přístupu a identit pro AKS][concepts-identity].

>[!NOTE]
> Zobrazení prostředků Kubernetes na webu Azure Portal se podporuje jenom u [clusterů s povolenou správou AAD](managed-aad.md) nebo u clusterů, které nejsou s povoleným AAD. Pokud používáte cluster s povoleným systémem AAD, musí mít uživatel nebo identita pro AAD k přístupu k rozhraní Kubernetes API příslušné role nebo role, aby mohli získat přístup k rozhraní API, a to i oprávnění k vyžádání [uživatele `kubeconfig` ](control-kubeconfig-access.md).

### <a name="enable-resource-view"></a>Povolit zobrazení prostředků

Pro existující clustery možná budete muset povolit zobrazení prostředků Kubernetes. Pokud chcete povolit zobrazení prostředků, postupujte podle výzev na portálu pro váš cluster.

:::image type="content" source="media/kubernetes-portal/enable-resource-view.png" alt-text="Kubernetes pod informace zobrazené v Azure Portal." lightbox="media/kubernetes-portal/enable-resource-view.png":::

> [!TIP]
> Funkci AKS pro [**rozsahy IP adres autorizovaných serverem API**](api-server-authorized-ip-ranges.md) lze přidat k omezení přístupu serveru rozhraní API pouze k veřejnému koncovému bodu brány firewall. Další možností pro tyto clustery je aktualizace `--api-server-authorized-ip-ranges` , aby zahrnovaly přístup k místnímu klientskému počítači nebo rozsahu IP adres (ze kterého se právě prohlíží portál). K povolení tohoto přístupu potřebujete veřejnou IPv4 adresu tohoto počítače. Tuto adresu můžete najít pomocí příkazu níže nebo v internetovém prohlížeči pomocí hledání "Co je moje IP adresa".
```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

## <a name="next-steps"></a>Další kroky

Tento článek ukazuje, jak získat přístup k prostředkům Kubernetes pro váš cluster AKS. V tématu [nasazení a manifesty YAML][deployments] získáte hlubší porozumění prostředkům clusteru a souborům YAML, ke kterým se dostanete v prohlížeči prostředků Kubernetes.

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/insights/container-insights-enable-existing-clusters.md
