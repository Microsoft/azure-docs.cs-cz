---
title: Cluster Azure DC/OS monitorování - Operations Management
description: Monitorování clusteru Azure Container Service DC/OS s analýzy protokolů.
services: container-service
author: keikhara
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: b326e5b686e14cefac4e6376bd3f26787ea1d10d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32164587"
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>Monitorování clusteru Azure Container Service DC/OS s analýzy protokolů

Analýzy protokolů je společnosti Microsoft založená na cloudu IT řešení správy, které pomáhá spravovat a chránit místní a cloudové infrastruktury. Kontejner řešení je řešení v analýzy protokolů, který umožňuje zobrazit inventář kontejneru, výkonu a protokoly na jednom místě. Můžete auditovat, řešení potíží s kontejnery zobrazením protokoly v centrálním umístění a najít aktivní využívání nadbytečné kontejneru na hostiteli.

![](media/container-service-monitoring-oms/image1.png)

Další informace o kontejneru řešení, naleznete [analýzy protokolů řešení kontejneru](../../log-analytics/log-analytics-containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Nastavení analýzy protokolů z universe DC/OS


Tento článek předpokládá, že jste nastavili DC/OS a nasadili jednoduchého webového kontejneru aplikace v clusteru.

### <a name="pre-requisite"></a>Předpoklad
- [Předplatné služby Microsoft Azure](https://azure.microsoft.com/free/) -vám to zdarma.  
- Nastavení pracovního prostoru analýzy protokolu – najdete v části "Krok 3" pod
- [Rozhraní příkazového řádku DC/OS](https://dcos.io/docs/1.8/usage/cli/install/) nainstalována.

1. Na řídicím panelu DC/OS klikněte na Universe a vyhledejte "OMS, jak je uvedeno níže.

![](media/container-service-monitoring-oms/image2.png)

2. Klikněte na **Nainstalovat**. Uvidíte pop až s informacemi a **instalovat balíček** nebo **rozšířený instalace** tlačítko. Když kliknete na tlačítko **rozšířený instalace**, což vede k **vlastnosti konkrétní konfigurace OMS** stránky.

![](media/container-service-monitoring-oms/image3.png)

![](media/container-service-monitoring-oms/image4.png)

3. Zde, zobrazí se výzva k zadání `wsid` (ID pracovního prostoru analýzy protokolů) a `wskey` (primární klíč pro id pracovního prostoru). Chcete-li získat i `wsid` a `wskey` musíte vytvořit účet na webu <https://mms.microsoft.com>.
Postupujte podle kroků pro vytvoření účtu. Po dokončení vytváření účtu, je nutné získat vaše `wsid` a `wskey` kliknutím **nastavení**, pak **připojené zdroje**a potom **servery se systémem Linux**, jak je uvedeno níže.

 ![](media/container-service-monitoring-oms/image5.png)

4. Vyberte počet instancí a klikněte na tlačítko 'Zkontrolovat a nainstalovat'. Obvykle můžete získat počet instancí, které se rovná počtu Virtuálního počítače budete mít v clusteru agenta. Nainstaluje agenta OMS pro Linux jako jednotlivé kontejnery pro každý virtuální počítač, který chce shromažďovat informace o monitorování a informace o protokolování.

## <a name="setting-up-a-simple-oms-dashboard"></a>Nastavení jednoduchý řídicí panel OMS

Po instalaci agenta OMS pro Linux na virtuálních počítačích, dalším krokem je nastavit řídicím panelu OMS. Existují dva způsoby, jak to udělat: OMS portál nebo portál Azure.

### <a name="oms-portal"></a>Portálu OMS 

Přihlaste se k portálu OMS (<https://mms.microsoft.com>) a přejděte na **řešení Galerie**.

![](media/container-service-monitoring-oms/image6.png)

Jakmile jste na **řešení Galerie**, vyberte **kontejnery**.

![](media/container-service-monitoring-oms/image7.png)

Po dokončení výběru řešení kontejneru, zobrazí se na dlaždici na stránce Přehled OMS řídicí panel. Jakmile je indexovaný ingestovaný kontejner dat, zobrazí se dlaždici naplněný informace o řešení dlaždice zobrazení.

![](media/container-service-monitoring-oms/image8.png)

### <a name="azure-portal"></a>Azure Portal 

Přihlášení k portálu Azure v <https://portal.microsoft.com/>. Přejděte na **Marketplace**, vyberte **monitorování + správu** a klikněte na tlačítko **najdete v článku všechny**. Pak zadejte `containers` ve vyhledávání. Zobrazí se "kontejner" ve výsledcích hledání. Vyberte **kontejnery** a klikněte na tlačítko **vytvořit**.

![](media/container-service-monitoring-oms/image9.png)

Po kliknutí na tlačítko **vytvořit**, se zobrazí výzvu k pracovního prostoru. Vyberte pracovní prostor nebo pokud jeden nemáte, vytvořte nový pracovní prostor.

![](media/container-service-monitoring-oms/image10.PNG)

Po dokončení výběru pracovního prostoru, klikněte na tlačítko **vytvořit**.

![](media/container-service-monitoring-oms/image11.png)

Další informace o řešení protokolu analýzy kontejneru, naleznete [analýzy protokolů řešení kontejneru](../../log-analytics/log-analytics-containers.md).

### <a name="how-to-scale-oms-agent-with-acs-dcos"></a>Postup škálování agenta OMS s ACS DC/OS 

V případě, že je potřeba mít nainstalovaný agent OMS souborem uzlu skutečný počet nebo jsou vertikálním navýšení kapacity VMSS přidáním více virtuálních počítačů, můžete tak učinit pomocí příjmu `msoms` služby.

Můžete přejít na Marathon nebo na kartě služeb uživatelského rozhraní DC/OS a škálovat vaše počet uzlů.

![](media/container-service-monitoring-oms/image12.PNG)

To nasadí do dalších uzlů, které ještě nebyly nasadit agenta OMS.

## <a name="uninstall-ms-oms"></a>Odinstalujte MS OMS

Chcete-li odinstalovat MS OMS zadejte následující příkaz:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Dejte nám vědět!
Co funguje? Co je chybějící? Co je potřeba pro to pro vás užitečné? Dejte nám vědět v <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Další postup

 Teď, když jste nastavili analýzy protokolů pro monitorování kontejnerů,[najdete v části řídicího panelu kontejneru](../../log-analytics/log-analytics-containers.md).
