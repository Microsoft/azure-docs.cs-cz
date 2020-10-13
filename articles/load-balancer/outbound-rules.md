---
title: Odchozí pravidla Azure Load Balancer
description: Tento článek vysvětluje, jak nakonfigurovat odchozí pravidla, která řídí provoz internetového provozu pomocí Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 51810876e3636b7023ce9c9318a071636bb00c4c
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002628"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Odchozí pravidla Azure Load Balancer

Odchozí pravidla umožňují nakonfigurovat odchozí SNAT na úrovni Standard pro vyrovnávání zatížení (Zdrojová síťová adresa). Tato konfigurace umožňuje používat veřejné IP adresy vašeho nástroje pro vyrovnávání zatížení jako proxy.

Tato konfigurace umožňuje:

* Maskování IP adres
* Zjednodušení seznamů povolených.
* Snižuje počet prostředků veřejné IP adresy pro nasazení.

U odchozích pravidel máte úplnou deklarativní kontrolu nad odchozím připojením k Internetu. Odchozí pravidla umožňují škálovat a ladit tuto schopnost vašim konkrétním potřebám. 

Odchozí pravidla se budou dodržovat jenom v případě, že back-end virtuální počítač nemá veřejnou IP adresu na úrovni instance (ILPIP).

![Load Balancer odchozí pravidla](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

U odchozích pravidel můžete explicitně definovat odchozí chování **SNAT** .

Odchozí pravidla umožňují řídit:

* **Které virtuální počítače jsou přeložené na které veřejné IP adresy.**
     * Dvě pravidla byla back-end fondu A používá IP adresu a a B, back-end fond B používá IP adresu C a D.
* **Jak jsou zadány Odchozí porty SNAT.**
     * Back-end fond B je jediným fondem, který umožňuje odchozí připojení, přidělte všem portům SNAT back-end fondu B a žádnému do fondu back-endu a.
* **Které protokoly pro zajištění odchozího překladu pro.**
     * Back-end fond B potřebuje porty UDP pro odchozí připojení. Fond back-endu potřebuje protokol TCP. Udělte porty TCP portům a a UDP do B.
* **Jaká doba se má použít pro časový limit nečinnosti odchozího připojení (4-120 minut).**
     * Pokud máte dlouho běžící připojení s udržením naživu, vyhradte nečinné porty pro dlouhotrvající připojení po dobu až 120 minut. Pro nová připojení se předpokládá, že se zastaralá připojení přebírají a vydávají porty za 4 minuty. 
* **Určuje, zda má být pro časový limit nečinnosti odesláno resetování protokolu TCP.**
     * Když načasování nečinných připojení, pošleme klientovi a serveru TCP RST, aby věděli, že tok je opuštěný?

## <a name="outbound-rule-definition"></a>Definice odchozího pravidla

Odchozí pravidla dodržují stejnou známou syntaxi jako vyrovnávání zatížení a příchozí pravidla **frontend**NAT:  +  **parameters**  +  **back-** Endu parametrů front-endu. 

Odchozí pravidlo konfiguruje odchozí překlad adres (NAT) pro _všechny virtuální počítače identifikované fondem back-end_ pro překlad do _front-endu_.  

_Parametry_ poskytují další jemně odstupňovanou kontrolu nad odchozím algoritmem NAT.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Škálování odchozího překladu adres (NAT) s několika IP adresami

Každá další IP adresa poskytovaná front-endu poskytuje další 64 000 dočasné porty pro nástroj pro vyrovnávání zatížení, které slouží jako porty SNAT. 

Pro plánování rozsáhlých scénářů použijte více IP adres. Pomocí odchozích pravidel můžete zmírnit [vyčerpání SNAT](troubleshoot-outbound-connection.md#snatexhaust). 

Můžete také použít [předponu veřejné IP adresy](https://aka.ms/lbpublicipprefix) přímo s odchozím pravidlem. 

Předpona veřejné IP adresy zvyšuje velikost vašeho nasazení. Předponu můžete přidat do seznamu povolených toků z vašich prostředků Azure. V nástroji pro vyrovnávání zatížení můžete nakonfigurovat konfiguraci protokolu IP front-endu, aby odkazovala na předponu veřejné IP adresy.  

Nástroj pro vyrovnávání zatížení má kontrolu nad předponou veřejné IP adresy. Odchozí pravidlo automaticky použije všechny veřejné IP adresy obsažené v předponě veřejných IP adres pro odchozí připojení. 

Každá IP adresa v rámci předpony veřejných IP adres poskytuje další 64 000 dočasných portů na IP adresu, které nástroj pro vyrovnávání zatížení použije jako porty SNAT.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Časový limit nečinnosti odchozího toku a resetování TCP

Odchozí pravidla poskytují konfigurační parametr pro řízení časového limitu nečinnosti odchozího toku a jejich párování s požadavky vaší aplikace. Výchozí časový limit nečinnosti ve výchozím nastavení na 4 minuty. Další informace najdete v tématu [Konfigurace časových limitů nečinnosti](load-balancer-tcp-idle-timeout.md). 

Výchozím chováním nástroje pro vyrovnávání zatížení je vyřazení toku v tichém režimu, pokud bylo dosaženo odchozího časového limitu nečinnosti. `enableTCPReset`Parametr umožňuje předvídatelné chování aplikace a řízení. Parametr určuje, zda má být odesláno obousměrné resetování TCP (TCP RST) v časovém limitu odchozího nečinnosti. 

Zkontrolujte [časový limit nečinnosti protokolu TCP](https://aka.ms/lbtcpreset) pro podrobnosti, včetně dostupnosti oblasti.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Explicitní zabezpečení a řízení odchozího připojení

Pravidla vyrovnávání zatížení poskytují automatické programování odchozího překladu adres (NAT). Některé scénáře mají výhodu nebo vyžadují, abyste na základě pravidla vyrovnávání zatížení zakázali automatické programování odchozího překladu adres (NAT). Vypnutí prostřednictvím pravidla vám umožní řídit nebo zdokonalovat chování.  

Tento parametr můžete použít dvěma způsoby:

1. Prevence příchozí IP adresy pro odchozí SNAT. Zakáže odchozí SNAT v pravidle vyrovnávání zatížení.
  
2. Vyladění odchozích parametrů **SNAT** pro IP adresu, která se používá pro příchozí a odchozí připojení současně. Aby bylo možné převzít řízení odchozího pravidla, musí být zakázané automatické odchozí NAT. Chcete-li změnit přidělení portu SNAT adresy, která se používá také pro příchozí, `disableOutboundSnat` parametr musí být nastaven na hodnotu true. 

Operace Konfigurace odchozího pravidla se nezdaří, pokud se pokusíte změnit definici IP adresy, která se používá pro příchozí.  Nejdřív zakažte odchozí NAT pravidla vyrovnávání zatížení.

>[!IMPORTANT]
> Pokud nastavíte tento parametr na hodnotu true a nemáte odchozí pravidlo pro definování odchozího připojení, váš virtuální počítač nebude mít odchozí připojení.  Některé operace virtuálního počítače nebo vaší aplikace můžou záviset na dostupnosti odchozího připojení. Ujistěte se, že rozumíte závislostem vašeho scénáře a že se tyto změny považují za důsledky.

V některých případech je pro vytvoření odchozího toku na virtuálním počítači nežádoucí. Může se stát, že budete muset spravovat, které cíle obdrží odchozí toky, nebo které cíle začínají vstupními toky. Pomocí [skupin zabezpečení sítě](../virtual-network/security-overview.md) můžete spravovat cíle, které virtuální počítač dosáhne. Pomocí skupin zabezpečení sítě můžete spravovat, které veřejné cíle začínají příchozí toky.

Když použijete NSG k virtuálnímu počítači s vyrovnáváním zatížení, věnujte pozornost [značkám služby](../virtual-network/security-overview.md#service-tags) a [výchozím pravidlům zabezpečení](../virtual-network/security-overview.md#default-security-rules). 

Ujistěte se, že virtuální počítač může přijímat požadavky na sondu stavu z Azure Load Balancer.

Pokud aplikace NSG blokuje požadavky na test stavu z AZURE_LOADBALANCER výchozí značky, test stavu virtuálního počítače se nepovede a virtuální počítač je označený jako nedostupný. Nástroj pro vyrovnávání zatížení zastaví odesílání nových toků do tohoto virtuálního počítače.

## <a name="limitations"></a>Omezení

- Maximální počet použitelných dočasných portů na IP adresu front-endu je 64 000.
- Rozsah konfigurovatelného odchozího časového limitu nečinnosti je 4 až 120 minut (240 až 7200 sekund).
- Load Balancer nepodporuje protokol ICMP pro odchozí překlad adres (NAT).
- Odchozí pravidla se dají použít jenom u primární konfigurace IP adresy síťového adaptéru.  Nemůžete vytvořit odchozí pravidlo pro sekundární IP adresu virtuálního počítače nebo síťové virtuální zařízení. Podporuje se víc síťových adaptérů.
- Všechny virtuální počítače v rámci **skupiny dostupnosti** se musí přidat do back-endu fondu pro odchozí připojení. 
- Všechny virtuální počítače v rámci **sady škálování virtuálních počítačů** musí být pro odchozí připojení přidané do fondu back-endu.

## <a name="next-steps"></a>Další kroky

- Další informace o [Azure Standard Load Balancer](load-balancer-overview.md)
- Přečtěte si naše [Nejčastější dotazy týkající se Azure Load Balancer](load-balancer-faqs.md)

