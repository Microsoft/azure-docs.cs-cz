---
title: Ochrana síťových prostředků v Azure Security Center
description: Tento dokument popisuje doporučení v Azure Security Center, která vám pomůžou chránit vaše síťové prostředky Azure a zůstat v souladu se zásadami zabezpečení.
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
ms.openlocfilehash: 28d0d7b14c18a1d98be1872b1555930b2e1650c3
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342446"
---
# <a name="protect-your-network-resources"></a>Ochrana síťových prostředků
Azure Security Center průběžně analyzuje stav zabezpečení vašich prostředků Azure pro osvědčené postupy zabezpečení sítě. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení, která vás provedou procesem konfigurace potřebných ovládacích prvků k posílení a ochraně vašich prostředků.

Úplný seznam doporučení pro sítě najdete v tématu [doporučení pro sítě](recommendations-reference.md#recs-network).

Tento článek se zabývá doporučeními, která se vztahují na vaše prostředky Azure z hlediska zabezpečení sítě. Doporučení pro sítě – centrum pro brány firewall nové generace, skupiny zabezpečení sítě, přístup k virtuálnímu počítači JIT, který má nadlimitně povolující pravidla příchozího provozu a další. Seznam doporučení k síti a nápravné akce najdete v tématu [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).

Mezi **síťové** funkce Security Center patří: 

- Mapa sítě (vyžaduje Azure Defender)
- [Adaptivní posílení zabezpečení sítě](security-center-adaptive-network-hardening.md) (vyžaduje Azure Defender)
- Doporučení pro zabezpečení sítě
 
## <a name="view-your-networking-resources-and-their-recommendations"></a>Zobrazení síťových prostředků a jejich doporučení

Na [stránce inventář](asset-inventory.md)prostředků použijte filtr typ prostředku k výběru síťových prostředků, které chcete prozkoumat:

:::image type="content" source="./media/security-center-network-recommendations/network-filters-inventory.png" alt-text="Typy síťových prostředků inventáře prostředků" lightbox="./media/security-center-network-recommendations/network-filters-inventory.png":::


## <a name="network-map"></a>Mapa sítě

Mapa interaktivní sítě poskytuje grafické zobrazení s překryvy zabezpečení, které vám dává doporučení a přehledy o posílení vašich síťových prostředků. Pomocí mapy můžete zobrazit topologii sítě vašich úloh Azure, připojení mezi virtuálními počítači a podsítěmi a schopnost přejít k podrobnostem z mapy do konkrétních prostředků a doporučení pro tyto prostředky.

Otevření mapy sítě:

1. V nabídce Security Center otevřete řídicí panel Azure Defender a vyberte **Mapa sítě**.

    :::image type="content" source="./media/security-center-network-recommendations/opening-network-map.png" alt-text="Typy síťových prostředků inventáře prostředků" lightbox="./media/security-center-network-recommendations/opening-network-map.png":::

1. Vyberte nabídku **vrstvy** a zvolte možnost **topologie**.
 
Výchozí zobrazení mapy topologie zobrazuje:

- Předplatná, která jste vybrali v Azure. Mapa podporuje více předplatných.
- Virtuální počítače, podsítě a virtuální sítě typ prostředku pro Správce prostředků (klasické prostředky Azure se nepodporují.)
- Partnerský virtuální sítě
- Pouze prostředky, které mají [doporučení sítě](security-center-recommendations.md) s vysokou nebo střední závažností  
- Internetové prostředky
- Mapa je optimalizovaná pro odběry, které jste vybrali v Azure. Pokud upravíte svůj výběr, mapa se přepočítá a znovu optimalizuje na základě vašeho nového nastavení.  

[![Mapa topologie sítě](./media/security-center-network-recommendations/network-map-info.png)](./media/security-center-network-recommendations/network-map-info.png#lightbox)

## <a name="understanding-the-network-map"></a>Princip mapy sítě

Mapa sítě může zobrazit vaše prostředky Azure v zobrazení **topologie** a v zobrazení **přenosů** . 

### <a name="the-topology-view"></a>Zobrazení topologie

V zobrazení **topologie** mapy sítě si můžete zobrazit následující přehled o síťových prostředcích:

- Ve vnitřním kruhu vidíte všechny virtuální sítěy v rámci vybraných předplatných, další kruh je všechny podsítě, vnější kroužek je všech virtuálních počítačů.
- Linky spojující prostředky na mapě vám umožní zjistit, které prostředky jsou vzájemně přidružené a jak je vaše síť Azure strukturovaná. 
- Indikátory závažnosti vám umožní rychle získat přehled o tom, které prostředky mají otevřená doporučení od Security Center.
- Kliknutím na kterýkoli z prostředků můžete přejít k podrobnostem a zobrazit podrobnosti o tomto prostředku a jeho doporučeních přímo a v kontextu mapy sítě.  
- Pokud se na mapě zobrazuje příliš mnoho prostředků, Azure Security Center používá vlastní algoritmus k inteligentnímu clusteringu vašich prostředků, zvýrazňování prostředků, které jsou v nejzávažnější stavu, a má doporučení s vysokou závažností. 

Vzhledem k tomu, že je mapa interaktivní a dynamická, na každý uzel lze kliknout a zobrazení se může změnit v závislosti na filtrech:

1. To, co vidíte na mapě sítě, můžete změnit pomocí filtrů v horní části. Mapu můžete zaměřit na:

   -  **Stav zabezpečení**: mapu můžete filtrovat podle závažnosti (vysoká, střední, nízká) vašich prostředků Azure.
   - **Doporučení**: můžete vybrat, které prostředky se zobrazí v závislosti na tom, která doporučení jsou na těchto prostředcích aktivní. Můžete například zobrazit pouze prostředky, pro které Security Center doporučuje povolit skupiny zabezpečení sítě.
   - **Síťové zóny**: ve výchozím nastavení se na mapě zobrazí jenom internetové prostředky, můžete vybrat taky interní virtuální počítače.
 
2. Kliknutím na **resetovat** v levém horním rohu můžete kdykoli vrátit mapu do výchozího stavu.

Přechod k podrobnostem prostředku:

1. Když na mapě vyberete konkrétní prostředek, otevře se pravé podokno a zobrazí se obecné informace o prostředku, připojená řešení zabezpečení, pokud existují, a doporučení relevantních pro daný prostředek. Je to stejný typ chování pro každý vybraný typ prostředku. 
2. Když najedete myší na uzel v mapě, můžete zobrazit obecné informace o prostředku, včetně předplatného, typu prostředku a skupiny prostředků.
3. Pomocí tohoto odkazu se přiblížíte k popisu tlačítka a přesměrujte mapu na daném konkrétním uzlu. 
4. Chcete-li znovu umístit mapu z konkrétního uzlu, zmenšete ji.

### <a name="the-traffic-view"></a>Zobrazení přenosů

Zobrazení **přenosů** poskytuje mapu veškerého možného provozu mezi prostředky. Zobrazí se vám vizuální mapa všech nakonfigurovaných pravidel definujících, které prostředky mohou komunikovat s kým. To vám umožní zobrazit existující konfiguraci skupin zabezpečení sítě a rychle identifikovat možné rizikové konfigurace v rámci vašich úloh.

### <a name="uncover-unwanted-connections"></a>Odkrýt nechtěné připojení

Síla tohoto zobrazení je ve své schopnosti, aby vám ukázala, že tato povolená připojení jsou spojená s ohroženými chybami zabezpečení, takže můžete použít tuto průřezovou část dat k provádění nezbytných posílení zabezpečení vašich prostředků. 

Můžete například zjistit, že se dva počítače, které nevíte, můžou komunikovat, a umožnit vám tak lepší izolaci úloh a podsítí.

### <a name="investigate-resources"></a>Prozkoumat prostředky

Přechod k podrobnostem prostředku:

1. Když na mapě vyberete konkrétní prostředek, otevře se pravé podokno a zobrazí se obecné informace o prostředku, připojená řešení zabezpečení, pokud existují, a doporučení relevantních pro daný prostředek. Je to stejný typ chování pro každý vybraný typ prostředku. 
2. Kliknutím na **provoz** zobrazíte seznam možných odchozích a příchozích přenosů v prostředku – jedná se o úplný seznam, který může komunikovat s prostředkem a na základě jakých protokolů a portů může komunikovat. Když například vyberete virtuální počítač, zobrazí se všechny virtuální počítače, se kterými může komunikovat, a když vyberete podsíť, zobrazí se všechny podsítě, se kterými může komunikovat.

**Tato data vycházejí z analýzy skupin zabezpečení sítě a pokročilých algoritmů strojového učení, které analyzují více pravidel, aby bylo možné pochopit jejich křížení a interakce.** 

[![Mapa síťových přenosů](./media/security-center-network-recommendations/network-map-traffic.png)](./media/security-center-network-recommendations/network-map-traffic.png#lightbox)


## <a name="next-steps"></a>Další kroky

Další informace o doporučeních, která se vztahují na jiné typy prostředků Azure, najdete v následujících tématech:

- [Ochrana počítačů a aplikací ve službě Azure Security Center](./asset-inventory.md)