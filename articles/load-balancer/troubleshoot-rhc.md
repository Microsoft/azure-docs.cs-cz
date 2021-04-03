---
title: Řešení problémů s dostupností Azure Load Balancer prostředků, front-endu a dostupnosti back-endu
description: Použijte dostupné metriky k diagnostice vašich degradované nebo nedostupné služby Azure Standard Load Balancer.
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2020
ms.author: errobin
ms.openlocfilehash: 3acaaba86c9a546a0bd45b5386287908168d50d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97955616"
---
# <a name="troubleshoot-resource-health-and-inbound-availability-issues"></a>Řešení potíží se stavem prostředků a problémy s příchozí dostupností 

Tento článek je průvodcem pro zkoumání problémů, které mají vliv na dostupnost prostředků služby Vyrovnávání zatížení front-endu a back-endu. 

K určení stavu nástroje pro vyrovnávání zatížení se používá Resource Health Check (RHC) pro Load Balancer. Analyzuje metriku dostupnosti cesty k datům v intervalu **2 minut** , aby zjistila, jestli jsou k dispozici koncové body vyrovnávání zatížení, IP adresy front-endu a portů front-endu s pravidly vyrovnávání zatížení.

Následující tabulka popisuje logiku RHC, která slouží k určení stavu nástroje pro vyrovnávání zatížení.

| Stav prostředku | Description |
| --- | --- |
| K dispozici | Váš prostředek standardního nástroje pro vyrovnávání zatížení je v pořádku a dostupný. |
| Snížený výkon | Váš standardní nástroj pro vyrovnávání zatížení má platformy nebo uživatelem iniciované události, které mají vliv na výkon. Metrika dostupnosti cesty k datům hlásila stav mezi 25 % a 90 % po dobu alespoň dvou minut. Dosáhnete středně silného dopadu na výkon. 
| Neaktivní | Váš prostředek standardního nástroje pro vyrovnávání zatížení není v pořádku. Metrika dostupnosti DataPath ohlásila méně než 25% stavu minimálně pro dvě minuty. Pro příchozí připojení budete mít výrazný dopad na výkon nebo nedostatečná dostupnost. Mohou existovat události uživatele nebo platformy, které způsobují nedostupnost. |
| Neznámý | Stav prostředku pro prostředek standardního nástroje pro vyrovnávání zatížení se ještě neaktualizoval nebo nepřijal informace o dostupnosti cesty k datům za posledních 10 minut. Tento stav by měl být přechodný a jakmile se přijmou data, měl by odrážet správný stav. |


## <a name="about-the-metrics-well-use"></a>O metrikách, které budeme používat
Tyto dvě metriky, které se mají použít, jsou stav *dostupnosti cesty k datům* a *sonda stavu* a je důležité pochopit jejich význam pro odvození správných přehledů. 

## <a name="data-path-availability"></a>Dostupnost cesty k datům
Metrika dostupnosti dat je vygenerována při každém 25 sekundách na všech portech front-end s nakonfigurovanými pravidly pro vyrovnávání zatížení a příchozím NAT. Tento test TCP se pak bude směrovat do kterékoli z těchto back-endové instance v pořádku. Pokud služba obdrží odpověď na příkaz k odeslání odpovědi, je považována za úspěšnou a součet metriky se jednou bude opakovat, pokud selže. Počet této metriky je 1/100 z celkového počtu minut TCP Tests na zkušební období. Proto chceme vzít v úvahu průměr, který zobrazí průměrnou část součtu/počet za časové období. Metrika dostupnosti datových cest agregovaná podle průměru, proto poskytuje procentuální míru úspěšnosti pro příkazy TCP testu TCP na front-endu IP: port pro každé z vašich pravidel pro vyrovnávání zatížení a příchozí překlad adres (NAT).

## <a name="health-probe-status"></a>Stav sondy stavu
Metrika stavu sondy stavu je vygenerována pomocí testu paměti protokolu definovaného v testu stavu. Tento test se pošle do každé instance ve fondu back-end a na portu definovaném v testu stavu. V případě sond HTTP a HTTPS vyžaduje úspěšný příkaz k odeslání odpovědi HTTP 200 OK, zatímco s sondami TCP jsou všechny odpovědi považovány za úspěšné. Po sobě jdoucí úspěchy nebo chyby každé sondy pak určí, jestli je instance back-endu v pořádku a dokáže přijímat přenosy pro pravidla vyrovnávání zatížení, ke kterým je fond back-endu přiřazený. Podobně jako dostupnost cesty k datům používáme průměrnou agregaci, která nám oznamuje průměrný počet úspěšných a celkových příkazů testu dostupnosti během intervalu vzorkování. Tato hodnota stavu sondy stavu udává stav back-endu v izolaci od vašeho nástroje pro vyrovnávání zatížení, a to tak, že se vaše instance back-endu vystavuje bez posílání provozu

>[!IMPORTANT]
>Stav sondy stavu se vzorkuje po jedné minutě. To může vést k menším výkyvům v jiné ustálené hodnotě. Například pokud existují dvě instance back-endu, jedna sonda a jedna sonda, služba sondy stavu může zachytit 7 vzorků pro bezproblémovou instanci a 6 pro instanci, která není v pořádku. To povede k dřív ustálené hodnotě 50, která se zobrazí jako 46,15 po dobu jednoho minutového intervalu. 

## <a name="diagnose-degraded-and-unavailable-load-balancers"></a>Diagnostika degradované a nedostupné nástroje pro vyrovnávání zatížení
Jak je uvedeno v článku o [stavu prostředků](load-balancer-standard-diagnostics.md#resource-health-status), je nástroj pro vyrovnávání zatížení, který se zobrazuje mezi 25% a 90% dostupností cesty k datům, a nedostupný Nástroj pro vyrovnávání zatížení je o jednu, která má méně než 25% dostupnost cesty k datům, po dobu dvou minut. Tyto stejné kroky je možné provést k prozkoumání chyby, která se zobrazí v jakémkoli upozornění na stav testu stavu nebo v výstrahách dostupnosti dat, která jste nakonfigurovali. Podíváme se na případ, kde jsme kontrolovali náš stav prostředků, a zjistili jsme, že náš nástroj pro vyrovnávání zatížení nebude k dispozici s dostupnou datovou cestou 0% – naše služba je mimo provoz.

Nejdřív přejdeme na podrobné zobrazení metrik našeho okna nástroje pro vyrovnávání zatížení. Můžete to provést prostřednictvím okna prostředku nástroje pro vyrovnávání zatížení nebo odkazu ve zprávě o stavu prostředku.  Dále přejdeme na kartu dostupnosti front-endu a back-endu a Prohlédněte si třicet minutové okno časového období, kdy došlo k degradování nebo nedostupnosti stavu. Pokud uvidíme dostupnost cesty k datům na 0%, víme, že existuje problém, který brání provozu pro všechna naše pravidla vyrovnávání zatížení a příchozího překladu adres (NAT) a může zjistit, jak dlouho tento dopad uplynul. 

Další místo, co potřebujeme vyhledat, je naše metrika stavu sondy stavu, aby bylo možné zjistit, jestli je naše cesta k datům nedostupná, protože neexistují žádné zdravé back-endové instance pro poskytování provozu. Pokud máme pro všechna naše pravidla vyrovnávání zatížení a příchozí back-end jednu instanci back-endu v pořádku, víme, že není naše konfigurace, která by způsobila nedostupnost našich datových cest. Tento scénář indikuje problémy s platformou Azure, ale zřídka se Fret, pokud je nenajdete jako automatizovaná výstraha se pošle týmu, aby se rychle vyřešily všechny problémy s platformou.

## <a name="diagnose-health-probe-failures"></a>Diagnostika selhání sondy stavu
Řekněme, že zkontrolujeme náš stav sondy stavu a zjistíme, že se všechny instance zobrazují jako chybné. V tomto hledání se vysvětluje, proč je naše cesta k datům nedostupná, protože provoz nikde. K vyloučení běžných chyb konfigurace byste pak měli projít následující kontrolní seznam:
* Podívejte se na využití procesoru pro vaše prostředky, abyste zkontrolovali, jestli jsou vaše instance ve skutečnosti v pořádku.
  * Můžete zaškrtnout toto 
* Pokud používáte kontrolu sondy HTTP nebo HTTPS, pokud je aplikace v pořádku a reaguje
  * Ověřte, jestli je tato funkce přímo přístupná k aplikacím prostřednictvím privátní IP adresy nebo veřejné IP adresy na úrovni instance přidružené k vaší back-endové instanci.
* Zkontrolujte skupiny zabezpečení sítě použité pro naše back-end prostředky. Zajistěte, aby neexistovala žádná pravidla s vyšší prioritou než AllowAzureLoadBalancerInBound, která by blokovala sondu stavu.
  * To můžete provést tak, že navštívíte své virtuální počítače back-end nebo Virtual Machine Scale Sets
  * Pokud najdete tento problém NSG, přesuňte stávající pravidlo povolení nebo vytvořte nové pravidlo s vysokou prioritou, které povolí přenos AzureLoadBalancer.
* Ověřte operační systém. Ujistěte se, že vaše virtuální počítače naslouchají na portu sondy, a zkontrolujte jejich pravidla brány firewall operačního systému, abyste zajistili, že neblokují přenos sondy pocházející z IP adresy 168.63.129.16.
  * Naslouchací porty můžete kontrolovat spuštěním příkazu netstat-a pomocí příkazového řádku Windows nebo pomocí příkazu netstat-l v terminálu Linux.
* Neumísťujte virtuální počítač brány firewall síťové virtuální zařízení do back-endu nástroje pro vyrovnávání zatížení, použijte [trasy definované uživatelem](../virtual-network/virtual-networks-udr-overview.md#user-defined) ke směrování provozu do back-endu instancí přes bránu firewall.
* Ujistěte se, že používáte správný protokol, pokud použijete protokol HTTP k testování portu naslouchajícího pro aplikaci jiného typu než HTTP. test selže

Pokud jste prošli obsahem tohoto kontrolního seznamu a pořád hledáte chyby sondy stavu, může dojít ke vzácným problémům s platformou, které mají vliv na službu sondy pro vaše instance. V tomto případě má Azure zpátky a do našeho týmu se pošle automatizovaná výstraha, která rychle vyřeší všechny problémy s platformou.

## <a name="next-steps"></a>Další kroky

* [Další informace o testu stavu Azure Load Balancer](load-balancer-custom-probe-overview.md)
* [Další informace o metrikách Azure Load Balancer](load-balancer-standard-diagnostics.md)
