---
title: Co jsou řešení pro provoz Oracle WebLogic serveru ve službě Azure Kubernetes
description: Naučte se, jak spustit Oracle WebLogic Server ve službě Azure Kubernetes.
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 02/23/2021
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: ac9f81fbde33bdd10bc8374a566a4f2ba83fc253
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669024"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>Co jsou řešení pro provoz Oracle WebLogic serveru ve službě Azure Kubernetes?

Tato stránka popisuje řešení pro spouštění Oracle WebLogic serveru (WLS) ve službě Azure Kubernetes (AKS). Tato řešení jsou společně vyvinutá a podporovaná Oracle a Microsoftem.

Je také možné spustit WebLogic Server v Azure Virtual Machines. Tato řešení jsou popsaná v [tomto článku společnosti Microsoft](./oracle-weblogic.md).

WebLogic Server je přední aplikační server Java, který spouští některé z nejdůležitějších podnikových aplikací v jazyce Java po celém světě. WebLogic Server tvoří základ middlewaru pro sadu Oracle Software Suite. Společnosti Oracle a Microsoft se zavazují, že zákazníkům serveru WebLogic umožňují volbu a flexibilitu při spouštění úloh v Azure jako Špičková cloudová platforma.

## <a name="wls-on-aks-certified-and-supported"></a>Na serveru AKS certifieded and supporteded
Služba WebLogic Server je certifikována Oracle a společností Microsoft, aby správně fungovala v AKS. WebLogic Server na řešeních AKS se zaměřuje na to, aby bylo možné snadno spustit vaše kontejnerové a Orchestrované aplikace v jazyce Java v infrastruktuře Docker a Kubernetes. Řešení se zaměřují na spolehlivost, škálovatelnost, možnosti správy a podniková podpora.

Clustery serveru WebLogic jsou plně povolené pro spouštění na Kubernetes prostřednictvím operátoru WebLogic Kubernetes (v tomto případě se tady říká "operator"). Operátor sleduje standardní vzorek operátoru Kubernetes. Zjednodušuje správu a provoz domén WebLogic a nasazení na Kubernetes tím, že automatizuje jinak ruční úlohy a přidává další funkce pro provozní spolehlivost. Operátor podporuje Oracle WebLogic Server 12c, infrastrukturu middlewaru pro Oracle Fusion 12c a novější. Otestovali jsme oficiální image Docker pro WebLogic server 12.2.1.3 a 12.2.1.4 s operátorem. Podrobnosti o operátorovi najdete v [oficiální dokumentaci od Oracle](https://oracle.github.io/weblogic-kubernetes-operator/).

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>Doprovodné materiály, skripty a ukázky pro WLS v AKS
Kromě certifikace WebLogic serveru na AKS, Oracle a Microsoft společně poskytují podrobné pokyny, skripty a ukázky pro provozování serveru WebLogic Server v AKS. Doprovodné materiály jsou součástí ukázky služby Azure Kubernetes v [dokumentaci k operátoru](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/). Pokyny jsou zaměřené na to, že se AKS nasazení v produkčním serveru WebLogic na nasazení. Doprovodné materiály využívají oficiální image Docker serveru WebLogic, které poskytuje Oracle. Převzetí služeb při selhání se dosahuje prostřednictvím souborů Azure přidaných prostřednictvím deklarací trvalého objemu Kubernetes. Služba Vyrovnávání zatížení Azure je podporovaná, když se zřídí pomocí služby Kubernetes typu "vyrovnávání zatížení". Azure Container Registry (ACR) se podporuje pro nasazení domén serveru WLS do vlastních imagí Docker. Doprovodné materiály vám umožní vysoký stupeň konfigurace a přizpůsobení.

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="K nasazení WebLogic serveru na AKS můžete použít ukázkové skripty.":::

Řešení zahrnují dva způsoby nasazení domén serveru WLS do AKS. Domény se dají nasadit přímo na Kubernetes trvalé svazky. Tato možnost nasazení je dobrá, pokud chcete migrovat na AKS, ale přesto chcete spravovat službu WLS pomocí konzoly pro správu nebo nástroje WebLogic Scripting (WLST). Možnost také umožňuje přesunout se do AKS bez nutnosti vývoje Docker. Kubernetes nativní způsob nasazení domén serveru WLS na AKS je vytvoření vlastních imagí Docker na základě oficiálních imagí z nástroje Oracle Container Registry, publikování vlastních imagí do ACR a nasazení domény do AKS pomocí operátoru. Tato možnost v řešení také umožňuje aktualizovat doménu prostřednictvím Kubernetes ConfigMaps po dokončení nasazení.

Další možnosti snadného použití a integrace služeb Azure jsou v budoucnu možné, a to prostřednictvím nabídek na webu Marketplace: zrcadlení Oracle WebLogic Server v Azure Virtual Machines řešení.

_Tato řešení jsou vlastními licencemi_. Předpokládají, že už máte příslušné licence pro Oracle a máte řádně licenci na spuštění nabídek v Azure.

_Pokud vás zajímá, jak úzce pracovat na scénářích migrace pomocí technického týmu, který vyvíjí Tato řešení, vyplňte [Tento krátký průzkum](https://aka.ms/wls-on-azure-survey) a uveďte kontaktní údaje_. Správci programu, architekti a technici budou brzy k dispozici a zahájí se uzavírání spolupráce. Možnost spolupracovat na scénáři migrace je zadarmo, zatímco řešení se nacházejí v aktivním počátečním vývoji.

## <a name="deployment-architectures"></a>Architektury nasazení

Řešení pro provoz Oracle WebLogic serveru ve službě Azure Kubernetes umožní široké spektrum architektur nasazení připraveného pro produkční prostředí s relativním náběh/s.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-aks.png" alt-text="V AKS jsou povolená složitá nasazení serveru WebLogic.":::

Kromě toho, že řešení poskytují zákazníkům úplnou flexibilitu pro přizpůsobení jejich nasazení. Zákazníci s nasazováním aplikací nejspíš budou integrovat další prostředky Azure s jejich nasazeními. Zákazníkům se doporučuje poskytnout zpětnou vazbu do průzkumu a dále tak vylepšit řešení.

## <a name="next-steps"></a>Další kroky

Prozkoumejte spuštění Oracle WebLogic serveru ve službě Azure Kubernetes.

> [!div class="nextstepaction"]
> [Doprovodné materiály, skripty a ukázky pro provozování služby WLS na AKS](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)

> [!div class="nextstepaction"]
> [WebLogic Kubernetes – operátor](https://oracle.github.io/weblogic-kubernetes-operator/)
