---
title: Co jsou řešení pro provoz Oracle WebLogic serveru ve službě Azure Kubernetes
description: Naučte se, jak spustit Oracle WebLogic Server ve službě Azure Kubernetes.
author: rezar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 10/28/2020
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: cf44ad8d81656248329d993d86e9922dc4985258
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486670"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>Co jsou řešení pro provoz Oracle WebLogic serveru ve službě Azure Kubernetes?

Tato stránka popisuje řešení pro spouštění Oracle WebLogic serveru (WLS) ve službě Azure Kubernetes (AKS). Tato řešení jsou společně vyvinutá a podporovaná Oracle a Microsoftem.

Je také možné spustit WebLogic Server v Azure Virtual Machines. Tato řešení jsou popsaná v [tomto článku společnosti Microsoft](./oracle-weblogic.md).

WebLogic Server je přední aplikační server Java, který spouští některé z nejdůležitějších podnikových aplikací v jazyce Java po celém světě. WebLogic Server tvoří základ middlewaru pro sadu Oracle Software Suite. Společnosti Oracle a Microsoft se zavazují, že zákazníkům serveru WebLogic umožňují volbu a flexibilitu při spouštění úloh v Azure jako Špičková cloudová platforma.

## <a name="wls-on-aks-certified-and-supported"></a>Na serveru AKS certifieded and supporteded
Služba WebLogic Server je certifikována Oracle a společností Microsoft, aby správně fungovala v AKS. WebLogic Server na řešeních AKS se zaměřuje na to, aby bylo možné snadno spustit vaše kontejnerové a Orchestrované aplikace v jazyce Java EE v infrastruktuře Docker a Kubernetes. Řešení se zaměřují na spolehlivost, škálovatelnost, možnosti správy a podniková podpora.

Clustery serveru WebLogic jsou plně povolené pro spouštění na Kubernetes prostřednictvím operátoru WebLogic Kubernetes (v tomto případě se tady říká "operator"). Operátor sleduje standardní vzorek operátoru Kubernetes. Zjednodušuje správu a provoz domén WebLogic a nasazení na Kubernetes tím, že automatizuje jinak ruční úlohy a přidává další funkce pro provozní spolehlivost. Operátor podporuje Oracle WebLogic Server 12c, infrastrukturu middlewaru pro Oracle Fusion 12c a novější. Otestovali jsme oficiální image Docker pro WebLogic server 12.2.1.3 a 12.2.1.4 s operátorem. Podrobnosti o operátorovi najdete v [oficiální dokumentaci od Oracle](https://oracle.github.io/weblogic-kubernetes-operator/).

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>Doprovodné materiály, skripty a ukázky pro WLS v AKS
Kromě certifikace WebLogic serveru na AKS, Oracle a Microsoft společně poskytují podrobné pokyny, skripty a ukázky pro provozování serveru WebLogic Server v AKS. Doprovodné materiály jsou součástí ukázky služby Azure Kubernetes v [dokumentaci k operátoru](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/). Pokyny jsou zaměřené na to, že se AKS nasazení v produkčním serveru WebLogic na nasazení. Doprovodné materiály využívají oficiální image Docker serveru WebLogic, které poskytuje Oracle. Převzetí služeb při selhání se dosahuje prostřednictvím souborů Azure přidaných prostřednictvím deklarací trvalého objemu Kubernetes. Služba Vyrovnávání zatížení Azure je podporovaná, když se zřídí pomocí služby Kubernetes typu "vyrovnávání zatížení". Doprovodné materiály vám umožní vysoký stupeň konfigurace a přizpůsobení.

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="K nasazení WebLogic serveru na AKS můžete použít ukázkové skripty.":::

Návod aktuálně předpokládá nasazení domény mimo bitovou kopii Docker a použití standardních imagí Docker od Oracle. Přidáme pokyny pro povolení vlastních imagí s vaší doménou v imagi Docker. Další možnosti snadného použití a integrace služeb Azure jsou v budoucnu možné, a to prostřednictvím nabídek na webu Marketplace: zrcadlení Oracle WebLogic Server v Azure Virtual Machines řešení.

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