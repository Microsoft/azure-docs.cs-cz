---
title: Ochrana síťových prostředků v Azure Security Center | Microsoft Docs
description: Tento dokument popisuje doporučení v Azure Security Center, která vám pomůžou chránit vaše síťové prostředky Azure a zůstat v souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: v-mohabe
ms.openlocfilehash: 3683550045699a24c27a3226fba2e90a671f80e1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101016"
---
# <a name="protect-your-network-resources-in-azure-security-center"></a>Ochrana síťových prostředků v Azure Security Center
Azure Security Center průběžně analyzuje stav zabezpečení vašich prostředků Azure pro osvědčené postupy zabezpečení sítě. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení, která vás provedou procesem konfigurace potřebných ovládacích prvků k posílení a ochraně vašich prostředků.

Tento článek se zabývá doporučeními, která se vztahují na vaše prostředky Azure z hlediska zabezpečení sítě. Doporučení pro sítě – centrum pro brány firewall nové generace, skupiny zabezpečení sítě, přístup k virtuálnímu počítači JIT přes povolující pravidla příchozího provozu a další. Seznam doporučení k síti a nápravné akce najdete v tématu [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).

> [!NOTE]
> Stránka **síť** vám umožňuje hluboko podrobně do stavu prostředků Azure z hlediska sítě. Mapa sítě a adaptivní síťové ovládací prvky jsou k dispozici pouze pro Azure Security Center úrovně Standard. [Pokud používáte úroveň Free, můžete kliknutím na toto tlačítko **Zobrazit starší síťové sítě** a přijímat doporučení síťových prostředků](#legacy-networking).
>

Stránka **sítě** poskytuje přehled oddílů, které můžete hluboko podrobně, a získat další informace o stavu síťových prostředků:

- Mapa sítě (jenom Azure Security Center úrovně Standard)
- Adaptivní posilování zabezpečení sítě
- Doporučení zabezpečení sítě.
- Okno starší **sítě** (okno předchozí sítě) 
 
![Podokno sítě](./media/security-center-network-recommendations/networking-pane.png)

## <a name="network-map"></a>Mapa sítě
Mapa interaktivní sítě poskytuje grafické zobrazení s překryvy zabezpečení, které vám dává doporučení a přehledy o posílení vašich síťových prostředků. Pomocí mapy můžete zobrazit topologii sítě vašich úloh Azure, připojení mezi virtuálními počítači a podsítěmi a schopnost přejít k podrobnostem z mapy do konkrétních prostředků a doporučení pro tyto prostředky.

Otevření mapy sítě:

1. V Security Center v části hygiena zabezpečení prostředků vyberte **sítě**.
2. V části **Mapa sítě** klikněte na **Zobrazit topologii**.
 
Výchozí zobrazení mapy topologie zobrazuje:

- Předplatná, která jste vybrali v Azure. Mapa podporuje více předplatných.
- Virtuální počítače, podsítě a virtuální sítě typ prostředku pro Správce prostředků (klasické prostředky Azure se nepodporují.)
- Partnerský virtuální sítě
- Pouze prostředky, které mají [doporučení sítě](security-center-recommendations.md) s vysokou nebo střední závažností  
- Internetové prostředky
- Mapa je optimalizovaná pro odběry, které jste vybrali v Azure. Pokud upravíte svůj výběr, mapa se přepočítá a znovu optimalizuje na základě vašeho nového nastavení.  

![Mapa topologie sítě](./media/security-center-network-recommendations/network-map-info.png)

## <a name="understanding-the-network-map"></a>Princip mapy sítě

Mapa sítě může zobrazit vaše prostředky Azure v zobrazení **topologie** a v zobrazení přenosů .

### <a name="the-topology-view"></a>Zobrazení topologie

V zobrazení **topologie** mapy sítě si můžete zobrazit následující přehled o síťových prostředcích:

- Ve vnitřním kruhu vidíte všechny virtuální sítěy v rámci vybraných předplatných, další kruh je všechny podsítě, vnější kroužek je všech virtuálních počítačů.
- Linky spojující prostředky na mapě vám umožní zjistit, které prostředky jsou vzájemně přidružené a jak je vaše síť Azure strukturovaná. 
- Indikátory závažnosti vám umožní rychle získat přehled o tom, které prostředky mají otevřená doporučení od Security Center.
- Kliknutím na kterýkoli z prostředků můžete přejít k podrobnostem a zobrazit podrobnosti o tomto prostředku a jeho doporučeních přímo a v kontextu mapy sítě.  
- Pokud se na mapě zobrazuje příliš mnoho prostředků, Azure Security Center používá vlastní algoritmus k inteligentnímu clusteringu vašich prostředků, zvýrazňování prostředků, které jsou v nejzávažnější stavu, a má doporučení s vysokou závažností. 

Vzhledem k tomu, že je mapa interaktivní a dynamická, na každý uzel lze kliknout a zobrazení se může změnit v závislosti na filtrech:

1. To, co vidíte na mapě sítě, můžete změnit pomocí filtrů v horní části. Mapu můžete zaměřit na:

   -  **Stav zabezpečení**: Mapu můžete filtrovat na základě závažnosti (vysoká, střední, nízká) prostředků Azure.
   - **Doporučení**: Můžete vybrat, které prostředky se zobrazí v závislosti na tom, která doporučení jsou na těchto prostředcích aktivní. Můžete například zobrazit pouze prostředky, pro které Security Center doporučuje povolit skupiny zabezpečení sítě.
   - **Síťové zóny**: Ve výchozím nastavení Mapa zobrazuje jenom internetové prostředky, můžete vybrat taky interní virtuální počítače.
 
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

![Mapa síťových přenosů](./media/security-center-network-recommendations/network-map-traffic.png)

## Starší verze sítě<a name ="legacy-networking"></a>

Pokud nemáte Security Center úroveň Standard, v této části se dozvíte, jak zobrazit bezplatné síťové doporučení.

Pokud chcete získat přístup k těmto informacím, klikněte v okně síť na **Zobrazit starší síťové služby**. 

![Starší verze sítě](./media/security-center-network-recommendations/legacy-networking.png)

### <a name="internet-facing-endpoints-section"></a>Část internetových koncových bodů
V části **internetové koncové body** uvidíte virtuální počítače, které jsou aktuálně nakonfigurované s internetovým koncovým bodem, a jeho stav.

Tato tabulka obsahuje název koncového bodu, internetovou IP adresu a aktuální stav závažnosti skupiny zabezpečení sítě a doporučení pro NGFW. Tabulka je řazená podle závažnosti.

### <a name="networking-topology-section"></a>Část topologie sítě
Část **topologie sítě** obsahuje hierarchické zobrazení prostředků.

Tato tabulka je seřazená podle závažnosti (virtuální počítače a podsítě).

V tomto zobrazení topologie zobrazuje první úroveň virtuální sítě. Druhý displej má podsítě a třetí úroveň zobrazuje virtuální počítače, které patří do těchto podsítí. V pravém sloupci se zobrazuje aktuální stav doporučení skupiny zabezpečení sítě pro tyto prostředky.

Třetí úroveň zobrazuje virtuální počítače, které jsou podobné tomu, co bylo popsáno dříve. Kliknutím na libovolný prostředek můžete získat další informace nebo použít potřebný ovládací prvek zabezpečení nebo konfiguraci.

## <a name="network-recommendations"></a>Doporučení pro síť

|Název doporučení|Popis|severity|Bezpečnostní skóre|Typ prostředku|
|----|----|----|----|----|----|
|Měly by být povolené skupiny zabezpečení sítě na úrovni podsítě.|Povolte skupinám zabezpečení sítě řídit síťový přístup k prostředkům nasazeným v podsítích.|Vysoká/střední|30|Subnet|
|Virtuální počítače by měly být přidružené ke skupině zabezpečení sítě.|Povolte skupinám zabezpečení sítě řízení přístupu k síti virtuálních počítačů.|Vysoká/střední|30|Virtuální počítač|
|Přístup by měl být omezený na povolující skupiny zabezpečení sítě u virtuálních počítačů s přístupem k Internetu.|Tím, že omezíte přístup ke stávajícím pravidlům povolení, posílíte skupiny zabezpečení sítě vašich virtuálních počítačů s přístupem k Internetu.|Vysoká|20|Virtuální počítač|
|Pravidla pro webové aplikace v IaaS skupin zabezpečení sítě by se měla posílit.|Posílit zabezpečení skupiny zabezpečení sítě (NSG) virtuálních počítačů, na kterých běží webové aplikace, s pravidly NSG, která jsou přesná, s ohledem na porty webových aplikací.|Vysoká|20|Virtuální počítač|
|Přístup k App Services by měl být omezený.|Omezte přístup k vašemu App Services změnou konfigurace sítě tak, aby odepřela příchozí provoz z rozsahů, které jsou příliš široké.|Vysoká|10|App Service|
|Porty pro správu by měly být uzavřeny na virtuálních počítačích|Posílení skupiny zabezpečení sítě virtuálních počítačů pro omezení přístupu k portům pro správu.|Vysoká|10|Virtuální počítač|
Měla by být povolená DDoS Protection Standard.|Chraňte virtuální sítě obsahující aplikace s veřejnými IP adresami tím, že povolíte službu DDoS Protection Service Standard. DDoS Protection umožňuje zmírnit snižování objemu sítě a útoků protokolu.|Vysoká|10|Virtuální síť|
|Předávání IP na virtuálním počítači by mělo být zakázané.|Zakažte předávání IP. Když je na síťové kartě virtuálního počítače povolené předávání IP, může tento počítač přijímat přenosy adresované do jiných cílů. Předávání IP adres se vyžaduje jen zřídka (například při použití virtuálního počítače jako síťového virtuálního zařízení), a proto by ho měl zkontrolovat tým zabezpečení sítě.|Střední|10|Virtuální počítač|
|Webová aplikace by měla být přístupná jen přes HTTPS|Povolí přístup pouze HTTPS k webovým aplikacím. Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy.|Střední|20|Webová aplikace|
|Na virtuálních počítačích by se mělo použít řízení přístupu k síti za běhu|Použijte řízení přístupu k virtuálnímu počítači JIT (just-in-time) k trvalému uzamčení přístupu k vybraným portům a povolte oprávněným uživatelům, aby je mohli otevřít přes JIT, jenom po omezené množství času.|Vysoká|20|Virtuální počítač|
|Aplikace Function app by měly být přístupné jenom přes HTTPS|Povolte přístup pouze HTTPS pro aplikace Function App. Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy.|Střední|20|Function App|
|Zabezpečený přenos do účtů úložiště by měl být povolený.|Povolte zabezpečený přenos do účtů úložiště. Zabezpečený přenos je možnost, která vynutí, aby váš účet úložiště přijímal požadavky jenom od zabezpečených připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a službou a chrání přenášená data z útoků síťové vrstvy, jako jsou například útoky prostředníkem, odposlouchávání a napadení relace.|Vysoká|20|Účet úložiště|

## <a name="see-also"></a>Viz také:
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:

* [Ochrana počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-protection.md)
* [Ochrana služby Azure SQL ve službě Azure Security Center](security-center-sql-service-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
