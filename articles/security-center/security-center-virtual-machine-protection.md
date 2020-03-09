---
title: Ochrana počítačů a aplikací
description: Tento dokument popisuje doporučení ve službě Security Center, které vám pomůžou chránit virtuální počítače a počítače a webové aplikace a služby App Service Environment.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: bcf92838483fbb6b54802cc0d44cc44ea086d705
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380821"
---
# <a name="protect-your-machines-and-applications"></a>Ochrana počítačů a aplikací
Když Azure Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení, která vás provedou procesem konfigurace potřebných ovládacích prvků k posílení a ochraně vašich prostředků.

Tento článek vysvětluje stránku **COMPUTE a aplikace** v části zabezpečení prostředků Security Center.

Úplný seznam doporučení, která se vám můžou zobrazit na této stránce, najdete v tématu [doporučení k výpočtům a aplikacím](recommendations-reference.md#recs-computeapp).


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Zobrazení zabezpečení prostředků COMPUTE a Apps

[řídicí panel ![Security Center](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

Pokud chcete zobrazit stav prostředků COMPUTE a Apps, vyberte v levém podokně Security Center možnost **compute & aplikace**. K dispozici jsou následující karty:

* **Přehled**: uvádí doporučení pro všechny prostředky výpočtů a aplikací a jejich aktuální stav zabezpečení. 

* [**Virtuální počítače a servery**](#vms-and-computers): uvádí doporučení pro vaše virtuální počítače, počítače a aktuální stav zabezpečení pro každý z nich.

* [**VM Scale Sets**](#vmscale-sets): uvádí doporučení pro vaše sady škálování, 

* [**Cloud Services**](#cloud-services): uvádí doporučení pro webové a pracovní role monitorované nástrojem Security Center

* [**App Services**](#app-services): uvádí doporučení pro vaše aplikační služby App Service Environment a aktuální stav zabezpečení každého

* [**Kontejnery**](#containers): uvádí doporučení pro vaše kontejnery a posouzení zabezpečení jejich konfigurací.

* **Výpočetní prostředky**: uvádí doporučení pro výpočetní prostředky, jako jsou Service Fabric clustery a centra událostí.

### <a name="whats-in-each-tab"></a>Co je na jednotlivých kartách?

Každá karta má více oddílů a v každé části můžete přejít k podrobnostem a zobrazit další podrobnosti o zobrazené položce.

Na každé kartě se také zobrazí doporučení pro relevantní prostředky v monitorovaném prostředí. První sloupec uvádí doporučení, druhý zobrazuje celkový počet ovlivněných prostředků a třetí zobrazuje závažnost problému.

Každé doporučení obsahuje sadu akcí, které můžete provést po výběru. Pokud například vyberete **chybějící aktualizace systému**, zobrazí se počet virtuálních počítačů a počítačů, ve kterých chybí opravy, a závažnost chybějící aktualizace.

> [!NOTE]
> Doporučení zabezpečení jsou stejná jako ta na stránce **doporučení** , ale tady se filtrují na konkrétní vybraný typ prostředku. Další informace o řešení doporučení najdete [v tématu Implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).
>




### <a name="vms-and-computers"></a>Virtuální počítače a servery
Část virtuální počítače a počítače poskytuje přehled všech doporučení týkajících se zabezpečení pro vaše virtuální počítače a počítače. Jsou zahrnuty čtyři typy počítačů:

![Počítač umístěný mimo Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Počítač mimo Azure.

![Virtuální počítač Azure Resource Manageru](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Virtuální počítač Azure Resource Manageru.

![Virtuální počítač Azure Classic](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Virtuální počítač Azure Classic.

![Virtuální počítače uvedené v pracovním prostoru](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Virtuální počítače identifikované pouze z pracovního prostoru, který je součástí zobrazeného předplatného. To zahrnuje virtuální počítače z jiných předplatných, které jsou součástí pracovního prostoru v tomto předplatném, a virtuální počítače, které byly nainstalovány s Operations Manager přímým agentem, a nemají žádné ID prostředku.

Ikona, který se zobrazí pod každým doporučením, pomáhá rychle identifikovat virtuální počítač a počítač, který vyžaduje pozornost a typ doporučení se jedná. Pomocí filtrů můžete také Hledat v seznamu podle **typu prostředku** a podle **závažnosti**.

Pokud chcete přejít k podrobnostem o zabezpečení pro každý virtuální počítač, klikněte na virtuální počítač.
Tady vidíte podrobné informace o zabezpečení pro virtuální počítač nebo počítač. V dolní části vidíte doporučenou akci a závažnost každého problému.

[Cloudové služby ![](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="vmscale-sets"></a>Virtual Machine Scale Sets
Security Center automaticky zjišťuje, zda máte sady škálování a doporučuje nainstalovat Microsoft Monitoring Agent na ně.

Instalace Microsoft Monitoring Agent: 

1. Vyberte doporučení **nainstalovat agenta monitorování do sady škálování virtuálních počítačů**. Zobrazí se seznam nemonitorované sady škálování.

1. Vyberte skupinu škálování, která není v pořádku. Postupujte podle pokynů pro instalaci agenta monitorování pomocí stávajícího naplněné pracovní plochy nebo vytvořte nový. Pokud není nastavená [cenová úroveň](security-center-pricing.md) pracovního prostoru, ujistěte se, že jste ji nastavili.

   ![Instalace MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Pokud chcete nastavit nové sady škálování tak, aby se automaticky nainstalovaly Microsoft Monitoring Agent:
1. Přejděte na Azure Policy a klikněte na **definice**.

1. Vyhledejte **nasazení zásady Log Analytics agenta pro sadu škálování virtuálních počítačů s Windows** a klikněte na něj.

1. Klikněte na **Přiřadit**.

1. Nastavte **Rozsah** a **Log Analytics pracovní prostor** a klikněte na **přiřadit**.

Pokud chcete nastavit všechny existující sady škálování pro instalaci Microsoft Monitoring Agent, v Azure Policy se můžete vrátit k **nápravě** a použít existující zásady na existující sady škálování.





### <a name="cloud-services"></a>Cloudové služby
Pro cloudové služby se vytvoří doporučení, když je verze operačního systému zastaralá.

![Cloud Services](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

V případě, kdy máte doporučení, postupujte podle kroků v doporučení pro aktualizaci operačního systému. Pokud je k dispozici aktualizace, budete mít upozornění (červená nebo oranžová) v závislosti na závažnosti problému. Úplné vysvětlení tohoto doporučení získáte kliknutím na možnost **Aktualizovat verzi operačního systému** ve sloupci **Popis** .






### <a name="app-services"></a>Aplikační služby
Pokud chcete zobrazit informace o App Service, musíte být v Security Center cenové úrovně Standard a povolit App Service v předplatném. Pokyny k povolení této funkce najdete v tématu [ochrana App Service pomocí Azure Security Center](security-center-app-services.md).

V části **App Services**najdete seznam prostředí App Service Environment a shrnutí stavu na základě Security Center prováděného hodnocení.

![Aplikační služby](./media/security-center-virtual-machine-recommendations/app-services.png)

Zobrazují se tři typy aplikačních služeb:

![Prostředí App services](./media/security-center-virtual-machine-recommendations/ase.png) Prostředí App services

![Webová aplikace](./media/security-center-virtual-machine-recommendations/web-app.png) Webová aplikace

![Aplikace – funkce](./media/security-center-virtual-machine-recommendations/function-app.png) Aplikace – funkce

Pokud vyberete webovou aplikaci, otevře se souhrnné zobrazení se třemi kartami:

   - **Doporučení**: na základě posouzení provedených Security Center, která selhala.
   - **Úspěšná vyhodnocení**: seznam hodnocení provedených Security Center, která byla úspěšná.
   - **Nedostupná posouzení**: seznam posouzení, která se nepodařilo spustit z důvodu chyby, nebo doporučení není relevantní pro konkrétní službu App Service.

   V části **doporučení** je seznam doporučení pro vybranou webovou aplikaci a závažnost každého doporučení.

   ![App Services doporučení](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Vyberte doporučení, abyste zobrazili popis doporučení a seznam špatných prostředků, zdravých prostředků a nekontrolovaných prostředků.

   - Sloupec **úspěšné vyhodnocení** zobrazuje seznam předaných vyhodnocení. Závažnost těchto hodnocení je vždy zelená.

   - Vyberte ze seznamu popis posouzení, seznam prostředků není v pořádku a v pořádku a seznam nezkontrolované prostředky předané posouzení. Je na kartě pro prostředky není v pořádku, ale tento seznam je prázdný, vždy od předaný posouzení.





### <a name="containers"></a>Kontejnery

Když otevřete kartu **kontejnery** v závislosti na vašem prostředí, může se zobrazit některý ze tří typů prostředků:

![Hostitel kontejneru](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) Hostitelé kontejneru – virtuální počítače s Docker 

![Služba Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Clustery Azure Kubernetes Service (AKS)

![Registr kontejneru](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Registry Azure Container Registry (ACR) – zobrazené pouze v případě, že jste na cenové úrovni Standard a máte povolenou Azure Container Registry sady prostředků.

Pokyny, jak používat funkce zabezpečení kontejnerů, najdete v tématu [monitorování zabezpečení kontejnerů](monitor-container-security.md).

Výhody Azure Container Registry sady jsou vysvětleny [zde](azure-container-registry-integration.md) .

Výhody sady Kubernetes Services jsou vysvětleny [zde](azure-kubernetes-service-integration.md) .

[karta ![kontejnery](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

Chcete-li zobrazit doporučení pro konkrétní prostředek v seznamu, klikněte na tento prostředek.

#### <a name="visibility-into-container-registries"></a>Viditelnost registrů kontejnerů

Například kliknutím na registr ASC-demo ACR ze seznamu zobrazeného na obrázku výše navedete na tuto stránku s podrobnostmi:

[![doporučení pro konkrétní registr ACR](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>Viditelnost kontejnerů hostovaných na počítačích s IaaS Linux

Po kliknutí na jeden z virtuálních počítačů s Docker se zobrazí stránka s podrobnostmi s informacemi týkajícími se kontejnerů v počítači, jako je například verze Docker a počet imagí spuštěných na hostiteli.

![Doporučení pro virtuální počítač s Docker](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Doporučení týkající se zabezpečení na základě srovnávacího testu CIS pro Docker

Security Center kontroluje konfigurace Dockeru a poskytuje vám vhled do nesprávných konfigurací tím, že poskytuje seznam všech neúspěšných pravidel, která byla posouzena. Security Center poskytuje pokyny, které vám pomůžou tyto problémy rychle vyřešit a ušetřit čas. Security Center nepřetržitě posuzuje konfigurace Dockeru a poskytuje vám jejich nejnovější stav.

![karta kontejner](./media/security-center-container-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>Další kroky
Další informace o doporučeních, která se vztahují na jiné typy prostředků Azure, najdete v následujících článcích:

* [Úplný seznam odkazů na doporučení zabezpečení Azure Security Center](recommendations-reference.md)
* [Monitorování identity a přístupu ve službě Azure Security Center](security-center-identity-access.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
* [Ochrana služby Azure SQL Service v Azure Security Center](security-center-sql-service-recommendations.md)