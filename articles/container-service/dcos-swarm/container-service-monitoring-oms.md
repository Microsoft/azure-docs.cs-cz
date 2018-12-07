---
title: (NEPOUŽÍVANÉ) Monitorování clusteru Azure DC/OS – řízení provozu
description: Monitorování clusteru služby Azure Container Service DC/OS pomocí Log Analytics.
services: container-service
author: keikhara
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: b5c1596066b02d5ad4f59ed553408d263acc825c
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993576"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(NEPOUŽÍVANÉ) Monitorování clusteru služby Azure Container Service DC/OS pomocí Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics je od Microsoftu cloudové řešení pro správu IT, která pomáhá spravovat a chránit místní a cloudovou infrastrukturu. Řešení kontejnerů je řešení ve službě Log Analytics, který umožňuje zobrazit inventář kontejneru, výkonu a protokolů na jednom místě. Můžete auditovat, řešení potíží s kontejnery zobrazením protokolů v centrálním umístění a najít hlučného využívání nadbytečné kontejneru na hostiteli.

![](media/container-service-monitoring-oms/image1.png)

Další informace o řešení kontejnerů najdete v článku [kontejneru řešení Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Nastavení Log Analytics z DC/OS universe


Tento článek předpokládá, že jste nastavili DC/OS a nasazení jednoduché webové aplikace typu kontejner v clusteru.

### <a name="pre-requisite"></a>Předpoklad
- [Předplatné Microsoft Azure](https://azure.microsoft.com/free/) – můžete získat předplatné zdarma.  
- Nastavení pracovního prostoru analýzy protokolu – viz "Krok 3" níže
- [Rozhraní příkazového řádku DC/OS](https://dcos.io/docs/1.8/usage/cli/install/) nainstalované.

1. Na řídicím panelu DC/OS klikněte na světě a vyhledejte položku "OMS", jak je znázorněno níže.

   >[!NOTE]
   >OMS se teď označuje jako Log Analytics.

 ![](media/container-service-monitoring-oms/image2.png)

2. Klikněte na **Nainstalovat**. Zobrazí se automaticky otevírané okno s informací o verzi a **instalovat balíček** nebo **rozšířené instalace** tlačítko. Po kliknutí na **rozšířené instalace**, což povede k **vlastnosti konkrétní konfigurace OMS** stránky.

 ![](media/container-service-monitoring-oms/image3.png)

 ![](media/container-service-monitoring-oms/image4.png)

3. Tady, zobrazí se výzva k zadání `wsid` (ID pracovního prostoru Log Analytics) a `wskey` (primární klíč pro ID pracovního prostoru). Chcete-li získat i `wsid` a `wskey` budete muset vytvořit účet na webu <https://mms.microsoft.com>.
Postupujte podle kroků k vytvoření účtu. Po dokončení vytváření účtu, je třeba získat vaše `wsid` a `wskey` kliknutím **nastavení**, pak **připojené zdroje**a potom **servery s Linuxem**, jak je znázorněno níže.

 ![](media/container-service-monitoring-oms/image5.png)

4. Vyberte počet instancí a klikněte na tlačítko "Zkontrolovat a nainstalovat". Obvykle můžete mít počet instancí rovná počtu virtuálních počítačů máte ve vašem clusteru agenta. Nainstaluje agenta log Analytics pro Linux jako jednotlivých kontejnerů na každém virtuálním počítači, který je chce shromažďovat informace o monitorování a protokolování.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Vytvoření jednoduchého řídicího panelu Log Analytics

Po instalaci agenta Log Analytics pro Linux na virtuálních počítačích, dalším krokem je nastavení řídicího panelu Log Analytics. Můžete nastavit řídicí panel webu Azure portal.

### <a name="azure-portal"></a>portál Azure 

Přihlaste se k webu Azure portal v <https://portal.microsoft.com/>. Přejděte na **Marketplace**vyberte **monitorování a správa** a klikněte na tlačítko **najdete v článku všechny**. Zadejte `containers` v hledání. Zobrazí se "kontejnery" ve výsledcích hledání. Vyberte **kontejnery** a klikněte na tlačítko **vytvořit**.

![](media/container-service-monitoring-oms/image9.png)

Po kliknutí na **vytvořit**, budete dotázáni, můžete pro váš pracovní prostor. Vyberte pracovní prostor nebo pokud nemáte, vytvořte nový pracovní prostor.

![](media/container-service-monitoring-oms/image10.PNG)

Po výběru pracovního prostoru, klikněte na tlačítko **vytvořit**.

![](media/container-service-monitoring-oms/image11.png)

Další informace o řešení kontejnerů Log Analytics najdete [kontejneru řešení Log Analytics](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Jak škálovat agenta Log Analytics s ACS DC/OS 

V případě, je potřeba mít nainstalovány agenta Log Analytics nemá skutečný počet nebo jsou vertikálním navýšení kapacity škálovací sady tak, že přidáte další virtuální počítač virtuálních počítačů, můžete tak učinit díky škálování `msoms` služby.

Můžete přejít na Marathon nebo na kartě služeb uživatelského rozhraní DC/OS a vertikálně navýšit kapacitu vašeho počet uzlů.

![](media/container-service-monitoring-oms/image12.PNG)

To se nasadí do dalších uzlů, které zatím nenainstalovali agenta Log Analytics.

## <a name="uninstall-ms-oms"></a>Odinstalace MS OMS

Chcete-li odinstalovat MS OMS zadejte následující příkaz:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Dejte nám vědět!
Co funguje? Co chybí? Co je potřeba pro to pro vás užitečné? Dejte nám vědět v <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Další postup

 Teď, když jste nastavili Log Analytics pro monitorování kontejnerů,[zobrazit řídicí panel kontejneru](../../azure-monitor/insights/containers.md).
