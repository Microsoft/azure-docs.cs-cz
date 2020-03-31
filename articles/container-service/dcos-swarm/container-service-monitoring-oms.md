---
title: (ZASTARALÉ) Monitorování clusteru Azure DC/OS – správa provozu
description: Monitorujte cluster služby Azure Container Service dc/os pomocí analýzy protokolů.
author: keikhara
ms.service: container-service
ms.topic: conceptual
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 1ab8d1cf3eb38a17f0b3d6c8137e37237498a527
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277326"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(ZASTARALÉ) Monitorování clusteru řadičů domény a operačních služeb Služby Azure Container Service pomocí analýzy protokolů

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics je cloudové řešení pro správu IT od microsoftu, které vám pomůže spravovat a chránit místní a cloudovou infrastrukturu.Kontejnerové řešení je řešení v Log Analytics, které vám pomůže zobrazit inventář kontejnerů, výkon a protokoly v jednom umístění. Můžete auditovat, řešit problémy s kontejnery zobrazením protokolů v centralizovaném umístění a najít hlučné náročné přebytečný kontejner na hostiteli.

![](media/container-service-monitoring-oms/image1.png)

Další informace o řešení kontejneru naleznete v [tématu Analýza protokolu řešení kontejneru](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Nastavení analýzy protokolů z vesmíru DC/OS


Tento článek předpokládá, že jste nastavili řadič domény/operační systém a nasadili jednoduché aplikace webového kontejneru v clusteru.

### <a name="pre-requisite"></a>Předpoklad
- [Předplatné Microsoft Azure](https://azure.microsoft.com/free/) – předplatné můžete získat zdarma.  
- Nastavení pracovního prostoru Analýzy protokolů – viz krok 3 níže
- [NAINSTALOVÁNO CLI DC/OS.](https://docs.mesosphere.com/1.12/cli)

1. Na řídicím panelu DC/OS klikněte na Universe a vyhledejte "OMS", jak je znázorněno níže.

   >[!NOTE]
   >OMS se nyní označuje jako Log Analytics.

   ![](media/container-service-monitoring-oms/image2.png)

2. Klepněte na tlačítko **Instalovat**. Zobrazí se vyskakovací okno s informacemi o verzi a **instalačním balíčkem** nebo **tlačítkem rozšířené instalace.** Po klepnutí na tlačítko **Upřesnit instalaci**, která vás zavede na stránku **vlastností konfigurace specifických pro OMS.**

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Zde budete vyzváni k `wsid` zadání (ID pracovního prostoru `wskey` Analýzy protokolů) a (primární klíč pro ID pracovního prostoru). Chcete-li `wsid` `wskey` získat obojí a musíte <https://mms.microsoft.com>vytvořit účet na adrese .
   Podle pokynů vytvořte účet. Jakmile jste hotovi s vytvářením `wsid` účtu, `wskey` musíte získat a kliknutím na **nastavení**, pak **připojené zdroje**, a pak **Linux Servery**, jak je znázorněno níže.

   ![](media/container-service-monitoring-oms/image5.png)

4. Vyberte požadovaný počet instancí a klikněte na tlačítko Zkontrolovat a nainstalovat. Obvykle budete chtít mít počet instancí rovnající se počtu virtuálních počítačů, které máte v clusteru agenta. Agent Log Analytics pro Linux nainstaluje jako jednotlivé kontejnery na každý virtuální počítač, který chce shromažďovat informace pro monitorování a protokolování informací.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Nastavení jednoduchého řídicího panelu Analýzy protokolů

Po instalaci agenta Log Analytics pro Linux na virtuální počítače, dalším krokem je nastavení řídicího panelu Log Analytics. Řídicí panel můžete nastavit prostřednictvím portálu Azure.

### <a name="azure-portal"></a>portál Azure 

Přihlaste se <https://portal.microsoft.com/>k portálu Azure na adrese . Přejděte na **Marketplace**, vyberte **Sledování + správa** a klikněte na Zobrazit **vše**. Potom `containers` zadejte do hledání. Ve výsledcích hledání se zobrazí "kontejnery". Vyberte **Kontejnery** a klepněte na **tlačítko Vytvořit**.

![](media/container-service-monitoring-oms/image9.png)

Jakmile kliknete na **vytvořit**, požádá vás o pracovní prostor. Vyberte pracovní prostor nebo pokud ho nemáte, vytvořte nový pracovní prostor.

![](media/container-service-monitoring-oms/image10.PNG)

Po výběru pracovního prostoru klikněte na **Vytvořit**.

![](media/container-service-monitoring-oms/image11.png)

Další informace o řešení kontejneru analýzy protokolů naleznete v [analýze protokolů řešení kontejneru](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Jak škálovat agenta Log Analytics pomocí řadiče domény/operačního systémem ACS 

V případě, že potřebujete mít nainstalovaný agent Log Analytics krátký skutečný počet uzlů nebo škálování škálování virtuálního počítače `msoms` sady pomocí přidáním další virtuální počítač, můžete tak učinit škálováním služby.

Můžete buď přejít na Marathon nebo na kartu SLUŽBY ui DC/OS a vertikálně navýšit počet uzlů.

![](media/container-service-monitoring-oms/image12.PNG)

To se nasadí do jiných uzlů, které ještě nenasadily agenta Analýzy protokolů.

## <a name="uninstall-ms-oms"></a>Odinstalace služby MS OMS

Chcete-li odinstalovat službu MS OMS, zadejte následující příkaz:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Dejte nám vědět!!!
Co funguje? Co chybí? Co ještě potřebujete, aby to bylo užitečné pro vás? Dejte nám vědět na <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Další kroky

 Teď, když jste nastavili Log Analytics pro sledování kontejnerů,[podívejte se na řídicí panel kontejneru](../../azure-monitor/insights/containers.md).
