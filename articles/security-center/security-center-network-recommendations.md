---
title: Ochrana vašich síťových prostředků v Azure Security Center | Dokumentace Microsoftu
description: Tato dokument řeší doporučení ve službě Azure Security Center, které vám pomůžou chránit prostředky Azure, sítě a zůstaňte souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 9/12/2018
ms.author: rkarlin
ms.openlocfilehash: b1f7120b3758e35d818aedbcc3b85feca44f8c33
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129654"
---
# <a name="protect-your-network-resources-in-azure-security-center"></a>Chraňte své prostředky sítě v Azure Security Center
Azure Security Center průběžně analyzuje stav zabezpečení vašich prostředků Azure pro osvědčené postupy zabezpečení sítě. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří se doporučení, která vás provede procesem konfigurace potřebných kontrol k posílení zabezpečení a ochraně vašich prostředků.

Tento článek se zabývá doporučení, které se vztahují k prostředkům Azure z hlediska zabezpečení sítě. Sítě center doporučení týkající se další generace bran firewall, skupiny zabezpečení sítě, pravidla zbytečně povolující příchozí provoz přístupu k virtuálnímu počítači podle potřeby a další. Seznam doporučení pro sítě a nápravné akce, naleznete v tématu [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).

> [!NOTE]
> **Sítě** stránka umožňuje podrobné informace o stavu vašich prostředků Azure z hlediska sítě. Mapování sítě a adaptivní ovládací prvky sítě jsou k dispozici pro Azure Security Center úrovně standard pouze. [Pokud používáte úroveň free, můžete kliknutím na tlačítko **zobrazit starší verze síť** a získejte doporučení k síti prostředků](#legacy-networking).
>

**Sítě** stránka poskytuje přehled částí hluboké můžete věnovat, chcete-li získat další informace o stavu vašich prostředků sítě:

- Mapování sítě (jenom u vrstvy Azure Security Center úrovně Standard)
- Skupina NSG posílení zabezpečení (již brzy. Zaregistrovat verzi preview)
- Doporučení pro zabezpečení sítě.
- Starší verze **sítě** blade (předchozí okno sítě) 
 
![Podokno sítě](./media/security-center-network-recommendations/networking-pane.png)

## <a name="network-map"></a>Mapování sítě
Mapa interaktivní sítě poskytuje grafické zobrazení s zabezpečení překrytí získáte doporučení a přehledy pro posílení zabezpečení síťových prostředků. Pomocí mapy můžete zobrazit síťové topologie vaší úlohy Azure, připojení mezi virtuálními počítači a podsítí a schopnost k podrobnostem z mapy konkrétní prostředky a doporučení pro tyto prostředky.

Chcete-li spustit nástroj Mapa sítě:

1. Ve službě Security Center v části kontrolu zabezpečení prostředků, vyberte **sítě**.
2. V části **mapy sítě** klikněte na tlačítko **naleznete topologii**.
 
Zobrazí se výchozí zobrazení mapy topologie:
- Odběry, které jste vybrali v Azure. Na mapě podporuje několik předplatných.
- Virtuální počítače, podsítí a virtuálních sítí správce prostředků typu prostředku (Azure Classic prostředky nejsou podporovány)
- Jenom prostředky, které mají [sítě doporučení](security-center-recommendations.md) se střední nebo vysokou závažností  
- Internetové různé prostředky
- Mapa je optimalizovaná pro odběry, které jste vybrali v Azure. Pokud změníte svůj výběr, je na mapě přepočítat a znovu optimalizované na základě nového nastavení.  

![Mapa topologie sítě](./media/security-center-network-recommendations/network-map-info.png)

## <a name="understanding-the-network-map"></a>Principy mapování sítě

Mapování sítě můžete zobrazit svých prostředků Azure **topologie** zobrazení a **provoz** zobrazení.

### <a name="the-topology-view"></a>Zobrazení topologie

V **topologie** zobrazení mapy sítě, můžete zobrazit tyto přehledy o vašich síťových prostředků:
- Zobrazí se všechny virtuální sítě v rámci vybraných předplatných vnitřního kroužku, další kruhu je všechny podsítě, vnějšího kroužku se všechny virtuální počítače.
- Čáry spojující prostředky v mapě umožňují zjistit, prostředků, které jsou vzájemně souvisí a strukturování sítí Azure. 
- Indikátory závažnost můžete rychle získat přehled prostředky z nich mít otevřený doporučení ze služby Security Center.
- Můžete kliknout na kterýkoli z prostředků a procházet je zobrazení podrobností o prostředku a jeho doporučení přímo a v místní síti mapování.  
- Pokud existuje příliš mnoho že prostředků se zobrazí na mapě, Azure Security Center používá jeho vlastního algoritmu pro inteligentní cluster vašich prostředků, zvýraznění prostředky, které jsou v nejkritičtější stavy a nejvyšší závažnost doporučení. 

Protože mapy je interaktivní a dynamické, každý uzel je po kliknutí a můžete změnit zobrazení podle filtry:

1. Můžete upravit, co vidíte na mapování sítě pomocí filtrů v horní části. Můžete se soustředit na mapě, podle:
   -  **Stav zabezpečení**: můžete filtrovat na mapě, podle závažnosti (vysoká, střední, nízká) vašich prostředků Azure.
   - **Doporučení**: můžete vybrat, které prostředky se zobrazují podle doporučení, které jsou aktivní na tyto prostředky. Například můžete zobrazit jenom prostředky, pro které Security Center doporučí že povolení skupin zabezpečení sítě.
   - **Sítě zóny**: ve výchozím nastavení, na mapě zobrazí pouze internetové prostředky, můžete vybrat interní virtuální počítače.
 
2. Můžete kliknout na **resetování** v levém horním rohu, kdykoli se vraťte do výchozího stavu z mapy.

Přejdete na prostředek:
1. Když vyberete konkrétní prostředek na mapě, v pravém podokně otevře a poskytuje obecné informace o zdroji, řešení připojená zabezpečení, pokud existuje a doporučení týkající se zdroje. Je stejný typ chování pro každý typ prostředku, který jste vybrali. 
2. Když najedete myší uzel na mapě, zobrazí se obecné informace o zdroji, včetně předplatného, typ prostředku a skupiny prostředků.
3. Použijte odkaz ke zvětšení popis tlačítka a soustředilo mapy v tomto konkrétním uzlu. 
4. K soustředilo mapování z určitého uzlu, oddálení.

### <a name="the-traffic-view"></a>Zobrazení přenosů

**Provoz** zobrazení vám poskytne mapu všechny možné přenosy mezi prostředky. Poskytuje vám vizuální mapy všechna pravidla, že jste nakonfigurovali, která definují prostředků, které mohou komunikovat s kým. To umožňuje zobrazit existující konfigurace skupin zabezpečení sítě také rychle identifikovat možné rizikovou konfigurací v rámci vašich úloh.

### <a name="uncover-unwanted-connections"></a>Odhalte nežádoucí připojení

Síla toto zobrazení je v jeho schopnost zobrazit tyto povolených připojení, spolu s ohrožení zabezpečení, které existují, takže můžete používat tento průřez datům s cílem provést nezbytné posílení zabezpečení na vašich prostředků. 

Například může zjistíte, že by mohla komunikovat dva počítače, které jste nevěděli, že vám umožní lépe izolovat úlohy a podsítě.

### <a name="investigate-resources"></a>Prozkoumat prostředky

Přejdete na prostředek:
1. Když vyberete konkrétní prostředek na mapě, v pravém podokně otevře a poskytuje obecné informace o zdroji, řešení připojená zabezpečení, pokud existuje a doporučení týkající se zdroje. Je stejný typ chování pro každý typ prostředku, který jste vybrali. 
2. Klikněte na tlačítko **provoz** zobrazíte seznam možných odchozí a příchozí přenosy na prostředek – toto je úplný seznam, který může komunikovat s prostředku a který může komunikovat a které protokoly a porty.

**Tato data se odvíjí analýzy skupin zabezpečení sítě, stejně jako pokročilé algoritmy strojového učení, které analyzují víc pravidel, které vám pomohou pochopit jejich crossovers a interakce.** 

![Mapa provozu sítě](./media/security-center-network-recommendations/network-map-traffic.png)

## Starší verze sítě <a name ="legacy-networking"></a>

Pokud nemáte Security Center úrovně Standard, tato část vysvětluje, jak zobrazit doporučení pro síť zdarma.

Chcete-li přistupovat k těmto informacím, v okně sítě, klikněte na tlačítko **zobrazit starší verze síť**. 

![Starší verze sítě](./media/security-center-network-recommendations/legacy-networking.png)

### <a name="internet-facing-endpoints-section"></a>Část internetových koncových bodů
V **internetové koncové body** části uvidíte virtuální počítače, které jsou aktuálně nakonfigurované s internetovým koncovým bodem a jeho stav.

Tato tabulka obsahuje název koncového bodu, Internetovou IP adresu a aktuální stav závažnosti skupiny zabezpečení sítě a NGFW doporučení. Tabulka je řazená podle závažnosti.

### <a name="networking-topology-section"></a>Část topologie sítě
**Topologie sítě** oddíl obsahuje hierarchické zobrazení prostředků.

Tato tabulka je řazená podle závažnosti (virtuální počítače a podsítě).

V tomto zobrazení topologie zobrazí první úroveň virtuální sítě. Druhý zobrazí jsou podsítě a zobrazí na třetí úrovni virtuální počítače, které patří do těchto podsítí. V pravém sloupci zobrazí aktuální stav doporučení pro skupinu zabezpečení sítě pro tyto prostředky.

Na třetí úrovni zobrazí virtuální počítače, který je podobný už uváděli dříve. Můžete kliknout na prostředek pro další informace nebo použijete řízení potřebné zabezpečení nebo konfigurace.

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:

* [Ochrana virtuálních počítačů pomocí Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Ochrana aplikací pomocí Azure Security Center](security-center-application-recommendations.md)
* [Ochrana služby Azure SQL ve službě Azure Security Center](security-center-sql-service-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
