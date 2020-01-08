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
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: b7e5b0286cdd15834b84e4fd3e619c6555054823
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552997"
---
# <a name="protect-your-machines-and-applications"></a>Ochrana počítačů a aplikací
Když Azure Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení, která vás provedou procesem konfigurace potřebných ovládacích prvků k posílení a ochraně vašich prostředků.

Tento článek vysvětluje stránku **COMPUTE a aplikace** v části zabezpečení prostředků Security Center.

Úplný seznam doporučení, která se vám můžou zobrazit na této stránce, najdete v tématu [doporučení pro data a úložiště](recommendations-reference.md#recs-computeapp).


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Zobrazení zabezpečení prostředků COMPUTE a Apps

[řídicí panel ![Security Center](./media/security-center-virtual-machine-recommendations/overview.png)](./media/security-center-virtual-machine-recommendations/overview.png#lightbox)

Pokud chcete zobrazit stav vašich výpočetních a aplikací prostředků, vyberte v části **prostředky** na bočním panelu Security Center **výpočetní & aplikace** . K dispozici jsou následující karty:

* **Přehled**: uvádí doporučení pro všechny prostředky výpočtů a aplikací a jejich aktuální stav zabezpečení. 

* [**Virtuální počítače a počítače**](#vms-and-computers): uvádí doporučení pro vaše virtuální počítače, počítače a aktuální stav zabezpečení pro každý z nich.

* [**VM Scale Sets**](#vmscale-sets): uvádí doporučení pro vaše sady škálování, 

* [**Cloud Services**](#cloud-services): uvádí doporučení pro webové a pracovní role monitorované pomocí Security Center

* [**App Services**](#app-services): uvádí doporučení pro vaše aplikační služby App Service Environment a aktuální stav zabezpečení každého

* **Kontejnery**: uvádí doporučení pro vaše kontejnery a posouzení zabezpečení jejich konfigurací.

* **Výpočetní prostředky**: uvádí doporučení pro výpočetní prostředky, jako jsou Service Fabric clustery a centra událostí.

### <a name="whats-in-each-tab"></a>Co je na jednotlivých kartách?

Každá karta má více oddílů a v každé části můžete přejít k podrobnostem a zobrazit další podrobnosti o zobrazené položce.

Na každé kartě se také zobrazí doporučení pro relevantní prostředky v monitorovaném prostředí. První sloupec uvádí doporučení, druhý zobrazuje celkový počet ovlivněných prostředků a třetí zobrazuje závažnost problému.

Každé doporučení obsahuje sadu akcí, které můžete provést po výběru. Pokud například vyberete **chybějící aktualizace systému**, zobrazí se počet virtuálních počítačů a počítačů, ve kterých chybí opravy, a závažnost chybějící aktualizace.

> [!NOTE]
> Doporučení zabezpečení jsou stejná jako ta na stránce **doporučení** , ale tady se filtrují na konkrétní vybraný typ prostředku. Další informace o řešení doporučení najdete [v tématu Implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).
>

### <a name="vms-and-computers"></a>Virtuální počítače a počítače
Část virtuální počítače a počítače poskytuje přehled všech doporučení týkajících se zabezpečení pro vaše virtuální počítače a počítače. Jsou zahrnuty čtyři typy počítačů:

![Počítač umístěný mimo Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Počítač mimo Azure.

![Virtuální počítač Azure Resource Manageru](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Virtuální počítač Azure Resource Manageru.

![Virtuální počítač Azure Classic](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Virtuální počítač Azure Classic.

![Virtuální počítače uvedené v pracovním prostoru](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Virtuální počítače identifikované pouze z pracovního prostoru, který je součástí zobrazeného předplatného. To zahrnuje virtuální počítače z jiných předplatných, které jsou součástí pracovního prostoru v tomto předplatném, a virtuální počítače, které byly nainstalovány s Operations Manager přímým agentem, a nemají žádné ID prostředku.

Ikona, který se zobrazí pod každým doporučením, pomáhá rychle identifikovat virtuální počítač a počítač, který vyžaduje pozornost a typ doporučení se jedná. Pomocí filtrů můžete také Hledat v seznamu podle **typu prostředku** a podle **závažnosti**.

Pokud chcete přejít k podrobnostem o zabezpečení pro každý virtuální počítač, klikněte na virtuální počítač.
Tady vidíte podrobné informace o zabezpečení pro virtuální počítač nebo počítač. V dolní části vidíte doporučenou akci a závažnost každého problému.

[Cloudové služby ![](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)

### <a name="cloud-services"></a>Cloudové služby
Pro cloudové služby se vytvoří doporučení, když je verze operačního systému zastaralá.

![Cloudové služby](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

V případě, kdy máte doporučení, postupujte podle kroků v doporučení pro aktualizaci operačního systému. Pokud je k dispozici aktualizace, budete mít upozornění (červená nebo oranžová) v závislosti na závažnosti problému. Úplné vysvětlení tohoto doporučení získáte kliknutím na možnost **Aktualizovat verzi operačního systému** ve sloupci **Popis** .

### <a name="app-services"></a>Aplikační služby
Pokud chcete zobrazit informace o App Service, musíte být v Security Center cenové úrovně Standard a povolit App Service v předplatném. Pokyny k povolení této funkce najdete v tématu [ochrana App Service pomocí Azure Security Center](security-center-app-services.md).


V části **App services**najdete seznam vaší služby App service Environment a provést shrnutí stavu na základě posouzení Security Center.

![Aplikační služby](./media/security-center-virtual-machine-recommendations/app-services.png)

Zobrazují se tři typy aplikačních služeb:

![Prostředí App services](./media/security-center-virtual-machine-recommendations/ase.png) Prostředí App services

![Webová aplikace](./media/security-center-virtual-machine-recommendations/web-app.png) Webová aplikace

![Aplikace – funkce](./media/security-center-virtual-machine-recommendations/function-app.png) Aplikace – funkce

Pokud vyberete webovou aplikaci, otevře se souhrnné zobrazení se třemi kartami:

   - **Doporučení**: na základě posouzení provedených Security Center, která selhala.
   - **Předaný posouzení**: seznam posouzení, které provádí služba Security Center, který předává.
   - **Nedostupná posouzení**: seznam vyhodnocení, které se nepovedlo spustit kvůli chybě nebo doporučení není relevantní pro konkrétní službu App service

   V části **doporučení** se seznam doporučení pro vybranou webovou aplikaci a závažnost jednotlivých doporučení.

   ![App Services doporučení](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Vyberte doporučení, abyste zobrazili popis doporučení a seznam špatných prostředků, zdravých prostředků a nekontrolovaných prostředků.

   - Sloupec **úspěšné vyhodnocení** zobrazuje seznam předaných vyhodnocení. Závažnost těchto hodnocení je vždy zelená.

   - Vyberte ze seznamu popis posouzení, seznam prostředků není v pořádku a v pořádku a seznam nezkontrolované prostředky předané posouzení. Je na kartě pro prostředky není v pořádku, ale tento seznam je prázdný, vždy od předaný posouzení.

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


## <a name="next-steps"></a>Další kroky
Další informace o doporučeních, která se vztahují na jiné typy prostředků Azure, najdete v následujících článcích:

* [Úplný seznam odkazů na doporučení zabezpečení Azure Security Center](recommendations-reference.md)
* [Monitorování identity a přístupu ve službě Azure Security Center](security-center-identity-access.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
* [Ochrana služby Azure SQL ve službě Azure Security Center](security-center-sql-service-recommendations.md)