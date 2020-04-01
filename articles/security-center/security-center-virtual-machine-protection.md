---
title: Chraňte své stroje a aplikace
description: Tento dokument řeší doporučení v Centru zabezpečení, která vám pomohou chránit vaše virtuální počítače a počítače a webové aplikace a prostředí služby App Service.
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
ms.openlocfilehash: 93dde2e873a00303dcb8563caed4d56dbf11cc12
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435634"
---
# <a name="protect-your-machines-and-applications"></a>Chraňte své stroje a aplikace
Když Azure Security Center identifikuje potenciální slabá místa zabezpečení, vytvoří doporučení, která vás provedou procesem konfigurace potřebných ovládacích prvků pro posílení a ochranu vašich prostředků.

Tento článek vysvětluje **výpočetní a apps** stránku security center zabezpečení části.

Úplný seznam doporučení, která se můžou zobrazit na této stránce, najdete v [tématu Doporučení pro výpočetní výkony a aplikace](recommendations-reference.md#recs-computeapp).


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Zobrazení zabezpečení prostředků pro výpočetní prostředky a aplikace

[![Řídicí panel Security Center](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

Pokud chcete zobrazit stav prostředků výpočetních prostředků a aplikací, v levém podokně v Centru zabezpečení vyberte **Výpočetní & aplikace**. K dispozici jsou následující karty:

* **Přehled**: uvádí doporučení pro všechny výpočetní prostředky a prostředky aplikací, stejně jako jejich aktuální stav zabezpečení 

* [**Virtuální počítače a servery:**](#vms-and-computers)uvádí doporučení pro vaše virtuální počítače, počítače a aktuální stav zabezpečení každého

* [**Škálovací sady virtuálních zařízení:**](#vmscale-sets)uvádí doporučení pro vaše škálovací sady, 

* [**Cloudové služby**](#cloud-services): uvádí doporučení pro vaše webové a pracovní role monitorované Security Center

* [**Služby aplikace**](#app-services): uvádí doporučení pro prostředí služby App service a aktuální stav zabezpečení každého

* [**Kontejnery**](#containers): uvádí doporučení pro vaše kontejnery a posouzení bezpečnosti jejich konfigurací

* **Výpočetní prostředky:** uvádí doporučení pro výpočetní prostředky, jako jsou clustery Service Fabric a centra událostí

### <a name="whats-in-each-tab"></a>Co je v každé záložce?

Každá karta obsahuje více oddílů a v každé části můžete přejít k podrobnostem a zobrazit další podrobnosti o zobrazené položce.

Na každé kartě se také zobrazí doporučení pro příslušné prostředky ve sledovaném prostředí. V prvním sloupci je uvedeno doporučení, druhý zobrazuje celkový počet ovlivněných prostředků a třetí zobrazuje závažnost problému.

Každé doporučení obsahuje sadu akcí, které můžete provést po jeho výběru. Pokud například vyberete **chybějící aktualizace systému**, zobrazí se počet virtuálních počítačů a počítačů, kterým chybí opravy, a závažnost chybějící aktualizace.

> [!NOTE]
> Doporučení zabezpečení jsou stejná jako doporučení na stránce **Doporučení,** ale zde jsou filtrována podle konkrétního typu prostředku, který jste vybrali. Další informace o řešení doporučení najdete [v tématu Implementace doporučení zabezpečení v Centru zabezpečení Azure](security-center-recommendations.md).
>




### <a name="vms-and-servers"></a><a name="vms-and-computers"></a>Virtuální servery a servery
V části Virtuální počítače a počítače poskytuje přehled všech doporučení zabezpečení pro vaše virtuální počítače a počítače. Zahrnuty jsou čtyři typy strojů:

![Počítač umístěný mimo Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Počítač mimo Azure.

![Virtuální počítač Správce prostředků Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Virtuální počítač Azure Resource Manager.

![Virtuální počítač Azure Classic](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Virtuální počítač Azure Classic.

![Virtuální společnosti identifikované z pracovního prostoru](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Virtuální počítače identifikované pouze z pracovního prostoru, který je součástí zobrazeného předplatného. To zahrnuje virtuální chod z jiných předplatných, které sestavy do pracovního prostoru v tomto předplatném a virtuálních stránek, které byly nainstalovány s operačním správcem přímého agenta a nemají žádné ID prostředku.

Ikona, která se zobrazí pod každé doporučení vám pomůže rychle identifikovat virtuální počítač a počítač, který potřebuje pozornost a typ doporučení. Pomocí filtrů můžete také prohledávat seznam podle **typu prostředku** a **podle závažnosti**.

Pokud chcete přejít k podrobnostem doporučení zabezpečení pro každý virtuální počítač, klikněte na virtuální počítač.
Tady se zobrazí podrobnosti o zabezpečení virtuálního počítače nebo počítače. V dolní části můžete vidět doporučenou akci a závažnost každého problému.

[![Cloudové služby](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="virtual-machine-scale-sets"></a><a name="vmscale-sets"></a>Škálovací sady virtuálních počítačů
Security Center automaticky zjistí, zda máte škálovací sady a doporučuje nainstalovat agenta Log Analytics na ně.

Instalace agenta Analýzy protokolů: 

1. Vyberte doporučení **Instalace agenta monitorování ve škálovací sadě virtuálních strojů**. Získáte seznam nesledovaných škálovacích sad.

1. Vyberte sadu měřítka není v pořádku. Podle pokynů nainstalujte agenta monitorování pomocí existujícího naplněného pracovního prostoru nebo vytvořte nový. Nezapomeňte nastavit [cenovou úroveň](security-center-pricing.md) pracovního prostoru, pokud není nastavená.

   ![Instalace MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Nastavení nových škálovacích sad pro automatickou instalaci agenta Analýzy protokolů:
1. Přejděte na **Zásady**Azure a klikněte na Definice .

1. Vyhledejte zásadu **Nasazení agenta Analýzy protokolů pro škálovací sady virtuálních strojů windows** a klikněte na něj.

1. Klikněte na **Přiřadit**.

1. Nastavte pracovní prostor **Obor** a **Analýza protokolů** a klepněte na **tlačítko Přiřadit**.

Pokud chcete nastavit všechny existující škálovací sady pro instalaci agenta Log Analytics, v zásadách Azure přejděte na **nápravu** a použijte existující zásady pro existující škálovací sady.





### <a name="cloud-services"></a><a name="cloud-services"></a>Cloudové služby
Pro cloudové služby je doporučení vytvořeno, když je verze operačního systému zastaralá.

![Cloud Services](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Ve scénáři, kde máte doporučení, postupujte podle kroků v doporučení aktualizovat operační systém. Pokud je k dispozici aktualizace, budete mít výstrahu (červená nebo oranžová - v závislosti na závažnosti problému). Úplné vysvětlení tohoto doporučení zobrazíte klepnutím na tlačítko **Aktualizovat verzi operačního systému** ve sloupci **POPIS.**






### <a name="app-services"></a><a name="app-services"></a>Služby aplikací
Chcete-li zobrazit informace o službě App Service, musíte být na standardní cenové úrovni Security Center a povolit službu App Service ve vašem předplatném. Pokyny k povolení této funkce najdete v [tématu Ochrana služby App Service pomocí Azure Security Center](security-center-app-services.md).

V části **App Services**najdete seznam prostředí služby App service a souhrn stavu na základě provedeného Centra zabezpečení hodnocení.

![Služby aplikací](./media/security-center-virtual-machine-recommendations/app-services.png)

Jsou zobrazeny tři typy aplikačních služeb:

![Prostředí služeb aplikace](./media/security-center-virtual-machine-recommendations/ase.png) Prostředí služeb aplikace

![Webová aplikace](./media/security-center-virtual-machine-recommendations/web-app.png) Webová aplikace

![Aplikace funkcí](./media/security-center-virtual-machine-recommendations/function-app.png) Aplikace funkcí

Pokud vyberete webovou aplikaci, otevře se souhrnné zobrazení se třemi kartami:

   - **Doporučení**: na základě hodnocení provedených centrem zabezpečení, která se nezdařila.
   - **Prošel hodnocení**: seznam hodnocení provedených Security Center, který prošel.
   - **Nedostupná hodnocení**: seznam hodnocení, která se nepodařilo spustit z důvodu chyby nebo doporučení není relevantní pro konkrétní službu aplikace

   V části **Doporučení** je seznam doporučení pro vybranou webovou aplikaci a závažnost každého doporučení.

   ![Doporučení pro služby App Services](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Výběrem doporučení zobrazíte popis doporučení a seznam prostředků v pořádku, prostředků v pořádku a nenaskenovaných prostředků.

   - Sloupec **Předané hodnocení** zobrazuje seznam předaných hodnocení. Závažnost těchto hodnocení je vždy zelená.

   - Vyberte předané hodnocení ze seznamu pro popis hodnocení, seznam prostředků není v pořádku a v pořádku a seznam nenaskenovaných prostředků. K dispozici je karta pro prostředky není v pořádku, ale tento seznam je vždy prázdný, protože posouzení předáno.





### <a name="containers"></a><a name="containers"></a>Kontejnery

Když otevřete kartu **Kontejnery,** v závislosti na vašem prostředí se může zobrazit některý ze tří typů prostředků:

![Hostitel kontejneru](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) Hostitelé kontejnerů – virtuální ms spuštěné v dockeru 

![Kubernetes servis](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Clustery služby Azure Kubernetes Service (AKS)

![Registr kontejneru](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Registry Azure Container Registry (ACR) – zobrazí se jenom když jste na standardní cenové úrovni a když máte povolenou sadu Azure Container Registry.

Pokyny k používání funkcí zabezpečení kontejneru naleznete [v tématu Sledování zabezpečení kontejnerů](monitor-container-security.md).

Výhody balíčku Azure Container Registry jsou vysvětleny [zde](azure-container-registry-integration.md)

Výhody balíčku Kubernetes Services jsou vysvětleny [zde](azure-kubernetes-service-integration.md)

[![Karta Kontejnery](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

Chcete-li zobrazit doporučení pro konkrétní zdroj v seznamu, klepněte na tento zdroj.

#### <a name="visibility-into-container-registries"></a>Přehled o registrech kontejnerů

Například klepnutí na registr Asc-demo ACR ze seznamu zobrazeného na obrázku výše vede k této stránce podrobností:

[![Doporučení pro konkrétní registr ACR](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>Přehled o kontejnerech hostovaných na počítačích IaaS Linux

Když kliknete na jeden z virtuálních počítačů spuštěných v dockeru, zobrazí se stránka podrobností s informacemi souvisejícími s kontejnery v počítači, jako je například verze Dockeru a počet bitových kopií spuštěných na hostiteli.

![Doporučení pro virtuální hosti s dockerem](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Doporučení zabezpečení založená na srovnávacím testu CIS pro Docker

Security Center kontroluje konfigurace Dockeru a poskytuje vám vhled do nesprávných konfigurací tím, že poskytuje seznam všech neúspěšných pravidel, která byla posouzena. Centrum zabezpečení obsahuje pokyny, které vám pomohou tyto problémy rychle vyřešit a ušetřit čas. Security Center nepřetržitě posuzuje konfigurace Dockeru a poskytuje vám jejich nejnovější stav.

![karta kontejner](./media/security-center-virtual-machine-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>Další kroky
Další informace o doporučeních, která platí pro jiné typy prostředků Azure, najdete v následujících článcích:

* [Úplný referenční seznam doporučení zabezpečení Azure Security Center](recommendations-reference.md)
* [Monitorování identity a přístupu v Azure Security Center](security-center-identity-access.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
* [Ochrana služby Azure SQL v Azure Security Center](security-center-sql-service-recommendations.md)