---
title: Ochrana síťových prostředků v Azure Security Center
description: Tento dokument řeší doporučení v Azure Security Center, které vám pomohou chránit vaše síťové prostředky Azure a zůstat v souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: memildin
ms.openlocfilehash: 792b95b120f67afcd360730acbd783a3071388b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77431481"
---
# <a name="protect-your-network-resources"></a>Ochrana síťových prostředků
Azure Security Center průběžně analyzuje stav zabezpečení vašich prostředků Azure pro osvědčené postupy zabezpečení sítě. Když Security Center identifikuje potenciální slabá místa zabezpečení, vytvoří doporučení, která vás provedou procesem konfigurace potřebných ovládacích prvků pro posílení a ochranu vašich prostředků.

Tento článek vysvětluje stránku **Síť** v části Zabezpečení prostředků centra zabezpečení.

Úplný seznam doporučení pro vytváření sítí naleznete v tématu [Networking recommendations](recommendations-reference.md#recs-network).

Tento článek řeší doporučení, která se vztahují na prostředky Azure z hlediska zabezpečení sítě. Doporučení pro sítě se soustřeďují kolem firewallů nové generace, skupin zabezpečení sítě, přístupu k virtuálním účtům JIT, příliš tolerantních pravidel příchozího provozu a dalších. Seznam doporučení pro sítě a nápravných akcí najdete [v tématu Správa doporučení zabezpečení v Centru zabezpečení Azure](security-center-recommendations.md).

> [!NOTE]
> Stránka **Síť** umožňuje podrobné prolomení stavu prostředků Azure z hlediska sítě. Mapa sítě a adaptivní síťové ovládací prvky jsou k dispozici pouze pro standardní úroveň Azure Security Center. [Pokud používáte bezplatnou úroveň, můžete klepnutím na tlačítko **zobrazit starší síť** a přijímat doporučení síťových prostředků](#legacy-networking).
>

Stránka **Síť** poskytuje přehled oddílů, do kterých se můžete hlouběji ponořit, abyste získali další informace o stavu síťových prostředků:

- Mapa sítě (pouze úroveň Azure Security Center Standard)
- Adaptivní posilování zabezpečení sítě
- Doporučení zabezpečení sítě.
- Rozhraní Legacy **Networking** Blade (předchozí síťové okno) 
 
[![Podokno Síť](./media/security-center-network-recommendations/networking-pane.png)](./media/security-center-network-recommendations/networking-pane.png#lightbox)

## <a name="network-map"></a>Mapa sítě
Interaktivní mapa sítě poskytuje grafické zobrazení s překryvy zabezpečení, které poskytují doporučení a přehledy pro posílení zabezpečení síťových prostředků. Pomocí mapy můžete zobrazit topologii sítě úloh Azure, připojení mezi virtuálními počítači a podsítěmi a možnost přejít z mapy na konkrétní prostředky a doporučení pro tyto prostředky.

Otevření mapy Sítě:

1. V Centru zabezpečení vyberte v části Hygiena zabezpečení prostředků **položku Networking**.
2. V části **Mapa sítě** klikněte na **Zobrazit topologii**.
 
Výchozí zobrazení mapy topologie se zobrazí:

- Předplatná, která jste vybrali v Azure. Mapa podporuje více odběrů.
- Virtuální počítače, podsítě a virtuální sítě typu prostředků Správce prostředků (klasické prostředky Azure nejsou podporované)
- Partnerské virtuální sítě
- Pouze prostředky, které mají [doporučení sítě](security-center-recommendations.md) s vysokou nebo střední závažností  
- Internetové prostředky
- Mapa je optimalizovaná pro předplatná, která jste vybrali v Azure. Pokud změníte výběr, mapa se přepočítá a přepracuje na základě nového nastavení.  

[![Mapa topologie sítě](./media/security-center-network-recommendations/network-map-info.png)](./media/security-center-network-recommendations/network-map-info.png#lightbox)

## <a name="understanding-the-network-map"></a>Principy mapy sítě

Mapa síť vám může zobrazit prostředky Azure v zobrazení **Topologie** a **Zobrazení provozu.** 

### <a name="the-topology-view"></a>Zobrazení topologie

V zobrazení **Topologie** na mapě sítě můžete zobrazit následující přehledy o síťových prostředcích:

- Ve vnitřním kruhu můžete vidět všechny virtuální sítě v rámci vybraných odběrů, další kruh jsou všechny podsítě, vnější kruh jsou všechny virtuální počítače.
- Řádky spojující prostředky na mapě vás naštěhnou, které prostředky jsou vzájemně spojené a jak je vaše síť Azure strukturovaná. 
- Pomocí indikátorů závažnosti můžete rychle získat přehled o tom, které prostředky mají otevřená doporučení z Centra zabezpečení.
- Můžete kliknout na některý z prostředků k podrobnostem a zobrazit podrobnosti o tomto prostředku a jeho doporučení přímo a v kontextu mapy sítě.  
- Pokud je na mapě zobrazeno příliš mnoho prostředků, Azure Security Center používá svůj vlastní algoritmus k inteligentnímu clusteru vašich prostředků, zvýraznění prostředků, které jsou v nejkritičtějším stavu a mají doporučení s nejvyšší závažností. 

Vzhledem k tomu, že mapa je interaktivní a dynamická, lze na každý uzel kliknout a zobrazení se může měnit na základě filtrů:

1. To, co vidíte na mapě sítě, můžete upravit pomocí filtrů nahoře. Mapu můžete zaměřit na základě:

   -  **Stav zabezpečení**: Mapu můžete filtrovat na základě závažnosti (vysoká, střední, nízká) prostředků Azure.
   - **Doporučení**: Můžete vybrat, které prostředky se zobrazí na základě doporučení, která jsou aktivní na těchto prostředcích. Můžete například zobrazit pouze prostředky, pro které Centrum zabezpečení doporučuje povolit skupiny zabezpečení sítě.
   - **Síťové zóny**: Ve výchozím nastavení se na mapě zobrazují pouze internetové prostředky, můžete také vybrat interní virtuální aplikace.
 
2. Chcete-li mapu kdykoli vrátit do výchozího stavu, můžete kdykoli klepnout na tlačítko **Obnovit** v levém horním rohu.

Postup přechodu k podrobnostem o prostředku:

1. Když vyberete konkrétní prostředek na mapě, otevře se pravé podokno a poskytne obecné informace o prostředku, připojených řešenízabezpečení, pokud existují, a doporučení týkající se prostředku. Jedná se o stejný typ chování pro každý typ prostředku, který vyberete. 
2. Když najedete na uzel v mapě, můžete zobrazit obecné informace o prostředku, včetně předplatného, typu prostředku a skupiny prostředků.
3. Pomocí odkazu můžete přiblížit špičku nástroje a znovu zaostřit mapu na daný uzel. 
4. Chcete-li mapu znovu zaostřit od určitého uzlu, oddálit ji.

### <a name="the-traffic-view"></a>Zobrazení Dopravní

Zobrazení **Dopravní** poskytuje mapu veškerého možného provozu mezi vašimi zdroji. To poskytuje vizuální mapu všech pravidel, která jste nakonfigurovali a která definují, které prostředky mohou komunikovat s kým. To umožňuje zobrazit existující konfiguraci skupin zabezpečení sítě a rychle identifikovat možné rizikové konfigurace v rámci úloh.

### <a name="uncover-unwanted-connections"></a>Odhalte nežádoucí připojení

Síla tohoto zobrazení je v jeho schopnost zobrazit tato povolená připojení spolu s existujícími chybami zabezpečení, takže můžete použít tento průřez dat k provedení potřebné posílení zabezpečení na vaše prostředky. 

Můžete například zjistit dva počítače, o kterých jste nevěděli, že mohou komunikovat, což vám umožní lépe izolovat úlohy a podsítě.

### <a name="investigate-resources"></a>Prozkoumání zdrojů

Postup přechodu k podrobnostem o prostředku:

1. Když vyberete konkrétní prostředek na mapě, otevře se pravé podokno a poskytne obecné informace o prostředku, připojených řešenízabezpečení, pokud existují, a doporučení týkající se prostředku. Jedná se o stejný typ chování pro každý typ prostředku, který vyberete. 
2. Kliknutím na **položku Přenos** zobrazíte seznam možných odchozích a příchozích přenosů na prostředku – toto je úplný seznam toho, kdo může s zdrojem komunikovat a s kým může komunikovat a přes který protokoly a porty. Například když vyberete virtuální hod, zobrazí se všechny virtuální ho mohou komunikovat a když vyberete podsíť, zobrazí se všechny podsítě, se kterými může komunikovat.

**Tato data jsou založena na analýze skupin zabezpečení sítě a pokročilých algoritmů strojového učení, které analyzují více pravidel, aby porozuměly jejich přechodům a interakcím.** 

[![Mapa síťového provozu](./media/security-center-network-recommendations/network-map-traffic.png)](./media/security-center-network-recommendations/network-map-traffic.png#lightbox)


## <a name="legacy-networking"></a>Starší sítě<a name ="legacy-networking"></a>

Pokud nemáte úroveň Standard Centra zabezpečení, tato část vysvětluje, jak zobrazit bezplatná doporučení pro vytváření sítí.

Chcete-li získat přístup k těmto informacím, klepněte v okně Síť na **tlačítko Zobrazit starší síť**. 

[![Starší sítě](./media/security-center-network-recommendations/legacy-networking.png)](./media/security-center-network-recommendations/legacy-networking.png#lightbox)

### <a name="internet-facing-endpoints-section"></a>Část internetových koncových bodů
V části **koncové body směřující k Internetu** uvidíte virtuální počítače, které jsou aktuálně nakonfigurované s koncovým bodem směřujícím k Internetu a jeho stavem.

Tato tabulka má název koncového bodu, internetovou adresu IP a aktuální stav závažnosti skupiny zabezpečení sítě a doporučení NGFW. Tabulka je seřazena podle závažnosti.

### <a name="networking-topology-section"></a>Část topologie sítě
Sekce **Topologie sítě** má hierarchické zobrazení zdrojů.

Tato tabulka je seřazena (virtuální počítače a podsítě) podle závažnosti.

V tomto zobrazení topologie zobrazí první úroveň virtuální sítě. Druhá zobrazí podsítě a třetí úroveň zobrazí virtuální počítače, které patří do těchto podsítí. V pravém sloupci je uveden aktuální stav doporučení skupiny zabezpečení sítě pro tyto prostředky.

Třetí úroveň zobrazuje virtuální počítače, což je podobné tomu, co je popsáno výše. Kliknutím na libovolný prostředek se dozvíte více nebo použijete požadovaný ovládací prvek zabezpečení nebo konfiguraci.

## <a name="next-steps"></a>Další kroky

Další informace o doporučeních, která platí pro jiné typy prostředků Azure, najdete v následujících tématech:

* [Ochrana počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-protection.md)
* [Ochrana služby Azure SQL v Azure Security Center](security-center-sql-service-recommendations.md)