---
title: Sledování zabezpečení v Azure Security Center | Microsoft Docs
description: Tento článek vám pomůže začít pracovat s funkcemi sledování v Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2018
ms.author: terrylan
ms.openlocfilehash: dc362306e20b4d39aa73a552e47cbcbd3037edbd
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063387"
---
# <a name="security-health-monitoring-in-azure-security-center"></a>Sledování stavu zabezpečení v Azure Security Center
Tento článek vám pomůže s využíváním možností v Azure Security Center ke sledování dodržování zásad.

## <a name="what-is-security-health-monitoring"></a>Co je sledování stavu zabezpečení?
Pod pojmem sledování si často představujeme pozorování a čekání, až se stane nějaká událost, abychom na situaci reagovali. Sledování zabezpečení odkazuje na používání proaktivní strategie, kdy se auditují vaše prostředky a identifikují ty systémy, které nesplňují organizační standardy nebo osvědčené postupy.

## <a name="monitoring-security-health"></a>Sledování stavu zabezpečení
Po povolení [zásad zabezpečení](security-center-policies.md) pro prostředky předplatného služba Security Center analyzuje zabezpečení vašich prostředků, aby identifikovala potenciální ohrožení zabezpečení. Informace o konfiguraci vaší sítě jsou k dispozici okamžitě. Získání informací o konfiguraci virtuálních počítačů a počítačů, jako je stav aktualizace zabezpečení nebo konfigurace operačního systému, může trvat hodinu i déle v závislosti na počtu virtuálních počítačů a počítačů, na kterých máte nainstalovaného agenta. V části **Prevence** můžete zobrazit stav zabezpečení svých prostředků a případné problémy. Seznam těchto problémů můžete zobrazit také na dlaždici **Doporučení**.

Další informace o tom, jak používat doporučení, najdete v článku [Implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).

V části **Monitorování stavu prostředků** můžete monitorovat stav zabezpečení svých prostředků. Na následujícím příkladu vidíte, že na dlaždicích každého prostředku (Výpočty a aplikace, Sítě, Zabezpečení dat, Identita a přístup) je celkový počet zjištěných problémů.

![Dlaždice stavu zabezpečení prostředků](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute--apps"></a>Monitorování výpočtů a aplikací
Další informace najdete v článku o [ochraně počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-recommendations.md).

### <a name="monitor-virtual-networks"></a>Monitorování virtuálních sítí
Po kliknutí na dlaždici **Sítě** se otevře okno **Sítě** s dalšími podrobnostmi, jak je znázorněno na následujícím snímku obrazovky:

![Okno Sítě](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>Doporučení pro sítě
Podobně jako u informací o stavu prostředků virtuálních počítačů se tady zobrazí v horní části souhrnný seznam problémů a v dolní části seznam monitorovaných sítí.

Část rozpisu stavu sítí uvádí možné problémy zabezpečení a nabízí [doporučení](security-center-network-recommendations.md). Možné problémy mohou zahrnovat:

* Není nainstalována brána firewall příští generace (NGFW).
* Nejsou povolené skupiny zabezpečení sítě v podsítích.
* Nejsou povolené skupiny zabezpečení sítě ve virtuálních počítačích.
* Omezený externí přístup prostřednictvím veřejného externího koncového bodu.
* Internetové koncové body, které jsou v pořádku

Po kliknutí na doporučení se zobrazí další podrobnosti o doporučení, jak je znázorněno v následujícím příkladu:

![Podrobnosti o doporučení v okně Sítě](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

V tomto příkladu obsahuje okno **Nakonfigurovat chybějící skupiny zabezpečení sítě pro podsítě** seznam podsítí a virtuálních počítačů, kde chybí ochrana skupiny zabezpečení sítě. Pokud kliknete na podsíť, na kterou chcete použít skupinu zabezpečení sítě, zobrazí se okno **Zvolit skupinu zabezpečení sítě**. Tady můžete vybrat nejvhodnější skupinu zabezpečení sítě pro podsíť nebo můžete vytvořit novou skupinu zabezpečení sítě.

#### <a name="internet-facing-endpoints-section"></a>Část internetových koncových bodů
V části **Internetové koncové body** uvidíte virtuální počítače, které jsou aktuálně nakonfigurované s internetovým koncovým bodem, a jejich aktuální stav.

![Virtuální počítače nakonfigurované s internetovým koncovým bodem a stav](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

Tato tabulka obsahuje název koncového bodu, který představuje virtuální počítač, internetovou IP adresu, aktuální stav závažnosti skupiny zabezpečení sítě a bránu firewall příští generace. Tabulka je řazená podle závažnosti:

* Červená (nahoře): Vysoká priorita, mělo by se řešit okamžitě.
* Oranžová: Střední priorita, mělo by se řešit co nejdříve.
* Zelená (poslední): Stav v pořádku.

#### <a name="networking-topology-section"></a>Část topologie sítě
Část **Topologie sítě** obsahuje hierarchické zobrazení prostředků, jak je znázorněno na následujícím snímku obrazovky:

![Hierarchické zobrazení prostředků v části topologie sítě](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

Tato tabulka je řazená podle závažnosti (virtuální počítače a podsítě):

* Červená (nahoře): Vysoká priorita, mělo by se řešit okamžitě.
* Oranžová: Střední priorita, mělo by se řešit co nejdříve.
* Zelená (poslední): Stav v pořádku.

V tomto zobrazení topologie obsahuje první úroveň [virtuální sítě](../virtual-network/virtual-networks-overview.md), [brány virtuální sítě](/vpn-gateway/vpn-gateway-site-to-site-create.md) a [virtuální sítě (klasické)](/virtual-network/virtual-networks-create-vnet-classic-pportal.md). Na druhé úrovni jsou podsítě a na třetí úrovni virtuální počítače, které patří do těchto podsítí. V pravém sloupci je aktuální stav skupiny zabezpečení sítě pro tyto prostředky, jak ukazuje následující příklad:

![Stav skupiny zabezpečení sítě v části topologie sítě](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

Dolní část tohoto okna obsahuje doporučení pro tento virtuální počítač, podobně jako jsme už uváděli dříve. Kliknutím na doporučení získáte další informace nebo použijete potřebný ovládací prvek zabezpečení nebo konfiguraci zabezpečení.

### <a name="monitor-data-security"></a>Monitorování zabezpečení dat

Po kliknutí na **Zabezpečení dat** v části **Prevence** se otevře okno **Datové prostředky** s doporučeními pro SQL a službu Storage. Také obsahuje [doporučení](security-center-sql-service-recommendations.md) pro obecný stav databáze. Další informace o šifrování úložiště najdete v tématu [Povolení šifrování účtu úložiště Azure v Azure Security Center](security-center-enable-encryption-for-storage-account.md).

![Datové prostředky](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

V části **Doporučení SQL** můžete kliknout na jakékoli doporučení a získáte další podrobnosti o další akci pro řešení problému. Následující příklad ukazuje rozbalení doporučení **Auditování databáze a detekce hrozeb u databází SQL**.

![Podrobnosti o doporučení SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Okno **Povolit auditování a detekci hrozeb u databází SQL** obsahuje následující informace:

* Seznam databází SQL
* Server, na kterém jsou umístěné
* Informace o tom, zda bylo toto nastavení zděděno ze serveru nebo zda je v této databázi jedinečné
* Aktuální stav
* Závažnosti problému

Když kliknete na databázi, abyste vyřešili toto doporučení, otevře se okno **Auditování a detekce hrozeb**, jak je znázorněno na následující obrazovce.

![Auditování a detekce hrozeb](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Pokud chcete povolit auditování, vyberte **Zapnuto** pod možností **Auditování**.

### <a name="monitor-identity--access"></a>Monitorování identity a přístupu

Další informace najdete v článku o [monitorování identity a přístupu v Azure Security Center](security-center-identity-access.md).

## <a name="see-also"></a>Viz také
V tomto článku jste zjistili, jak ve službě Azure Security Center používat funkce sledování. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení ve službě Azure Security Center](security-center-policies.md): Zjistěte, jak se v Azure Security Center konfiguruje nastavení zabezpečení.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md): Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md): Zjistěte, jak sledovat stav vašich partnerských řešení.
* [Časté otázky k Azure Security Center](security-center-faq.md): Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení a dodržování předpisů Azure.
