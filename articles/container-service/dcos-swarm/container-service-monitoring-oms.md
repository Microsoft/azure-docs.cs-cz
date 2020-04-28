---
title: ZASTARALÉ Monitorování clusteru Azure DC/OS – Správa operací
description: Monitorujte Azure Container Service cluster DC/OS pomocí Log Analytics.
author: keikhara
ms.service: container-service
ms.topic: conceptual
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 1ab8d1cf3eb38a17f0b3d6c8137e37237498a527
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277326"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>ZASTARALÉ Monitorování Azure Container Service clusteru DC/OS pomocí Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics je cloudové řešení pro správu IT od Microsoftu, které pomáhá spravovat a chránit místní i cloudovou infrastrukturu.Řešení kontejneru je řešení v Log Analytics, které vám pomůže zobrazit inventář kontejnerů, výkon a protokoly v jednom umístění. Můžete auditovat, řešit potíže s kontejnery zobrazením protokolů v centralizovaném umístění a zjistit vysokou úroveň nenáročného kontejneru na hostiteli.

![](media/container-service-monitoring-oms/image1.png)

Další informace o řešení kontejnerů naleznete v tématu [řešení kontejneru Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Nastavení Log Analytics z universu DC/OS


V tomto článku se předpokládá, že jste nastavili DC/OS a nasadili jste jednoduché aplikace webového kontejneru v clusteru.

### <a name="pre-requisite"></a>Předpoklad
- [Předplatné Microsoft Azure](https://azure.microsoft.com/free/) – můžete si zdarma získat předplatné.  
- Nastavení Log Analytics pracovního prostoru – viz krok 3 níže
- Rozhraní příkazového [řádku DC/OS](https://docs.mesosphere.com/1.12/cli) je nainstalováno.

1. Na řídicím panelu DC/OS klikněte na Universe a vyhledejte OMS, jak vidíte níže.

   >[!NOTE]
   >OMS se teď označuje jako Log Analytics.

   ![](media/container-service-monitoring-oms/image2.png)

2. Klikněte na **nainstalovat**. Zobrazí se automaticky otevírané okno s informacemi o verzi a **instalačním balíčkem** nebo **pokročilou instalací** . Po kliknutí na možnost **Pokročilá instalace**se zobrazí stránka **vlastností konfigurace specifické pro OMS** .

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Tady budete požádáni o zadání `wsid` (ID pracovního prostoru Log Analytics) a `wskey` (primární klíč pro ID pracovního prostoru). Chcete-li `wsid` získat `wskey` obojí a potřebujete vytvořit účet na adrese <https://mms.microsoft.com>.
   Použijte postup vytvoření účtu. Až budete s vytvářením účtu hotovi, `wsid` budete muset získat a `wskey` kliknout na **Nastavení**, **připojené zdroje**a pak na **servery Linux**, jak vidíte níže.

   ![](media/container-service-monitoring-oms/image5.png)

4. Vyberte požadovaný počet instancí a klikněte na tlačítko zkontrolovat a nainstalovat. Obvykle budete chtít, aby počet instancí byl stejný jako počet virtuálních počítačů v clusteru agenta. Agent Log Analytics pro Linux se nainstaluje jako samostatné kontejnery na každý virtuální počítač, který chce shromažďovat informace pro informace o monitorování a protokolování.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Nastavení jednoduchého řídicího panelu Log Analytics

Po instalaci agenta Log Analytics pro Linux na virtuálních počítačích je dalším krokem nastavení řídicího panelu Log Analytics. Řídicí panel můžete nastavit prostřednictvím Azure Portal.

### <a name="azure-portal"></a>portál Azure 

Přihlaste se k <https://portal.microsoft.com/>Azure Portal na. Přejděte na **Marketplace**, vyberte **monitorování + Správa** a klikněte na **Zobrazit vše**. Pak zadejte `containers` hledání. Ve výsledcích hledání se zobrazí "kontejnery". Vyberte **kontejnery** a klikněte na **vytvořit**.

![](media/container-service-monitoring-oms/image9.png)

Po kliknutí na **vytvořit**se zobrazí dotaz na váš pracovní prostor. Vyberte svůj pracovní prostor, nebo pokud ho nemáte, vytvořte nový pracovní prostor.

![](media/container-service-monitoring-oms/image10.PNG)

Po výběru pracovního prostoru klikněte na **vytvořit**.

![](media/container-service-monitoring-oms/image11.png)

Další informace o Log Analyticsovém řešení kontejnerů najdete v [Log Analytics řešení kontejneru](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Jak škálovat agenta Log Analytics pomocí služby ACS DC/OS 

V případě, že je potřeba mít nainstalovaný agent Log Analytics krátkým počtem uzlů nebo škálovat sadu virtuálních počítačů přidáním více virtuálních počítačů, můžete tak učinit škálováním `msoms` služby.

Můžete buď přejít na kartu služby Marathon nebo uživatelské rozhraní DC/OS a škálovat počet uzlů.

![](media/container-service-monitoring-oms/image12.PNG)

Tato akce se nasadí do jiných uzlů, které ještě neimplementovaly agenta Log Analytics.

## <a name="uninstall-ms-oms"></a>Odinstalace MS OMS

Chcete-li odinstalovat program MS OMS, zadejte následující příkaz:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Dejte nám prosím jistotu!!!
Co funguje? Co chybí? Co dalšího potřebujete, aby to bylo pro vás užitečné? Dejte nám prosím informace na adrese <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Další kroky

 Teď, když jste nastavili Log Analytics pro monitorování vašich kontejnerů, se[podívejte na svůj řídicí panel kontejneru](../../azure-monitor/insights/containers.md).
