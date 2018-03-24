---
title: Pomocí Azure DNS pro privátní domény | Microsoft Docs
description: Přehled privátní DNS, který je hostitelem služby v Microsoft Azure.
services: dns
documentationcenter: na
author: KumudD
manager: jennoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: 7f1bd8cdcab7bdd61b3f006acf6090c53db8eda6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="using-azure-dns-for-private-domains"></a>Pomocí Azure DNS pro privátní domény
Systému názvů domény nebo DNS, zodpovídá za překladu (nebo vyřešení) název služby na jeho IP adresu. Azure DNS je hostitelská služba domén DNS poskytnutí překladu názvů pomocí infrastruktury Microsoft Azure.  Kromě internetových domén DNS Azure DNS teď také podporuje privátní DNS domény jako funkce preview.  
 
Azure DNS poskytuje spolehlivé, zabezpečené služby DNS pro správu a překládat názvy domén ve virtuální síti, aniž by bylo nutné přidat vlastní řešení DNS. Zóny DNS privátní umožňují použít vlastní názvy vlastních domén namísto názvů Azure, které jsou dnes k dispozici.  Pomocí vlastních názvů domén umožňuje přizpůsobit Architektura virtuální sítě podle potřeb vaší organizace. Poskytuje překlad názvů pro virtuální počítače v rámci virtuální sítě a mezi virtuálními sítěmi. Kromě toho můžete nakonfigurovat názvy zón s rozdělení horizon zobrazení – povolení, privátní a veřejné zóny DNS sdílet stejný název.

![Přehled systému DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="benefits"></a>Výhody

* **Eliminuje nutnost pro vlastní řešení DNS.** Mnoho zákazníků vytvořili vlastní DNS řešení pro správu zón DNS ve své virtuální síti.  Správa zóny DNS lze nyní provést pomocí nativní infrastrukturu Azure, která odebere zatížení vytváření a správě vlastních řešení DNS.

* **Použijte všechny běžné typy záznamů DNS.**  Azure DNS podporuje záznamy A, AAAA, CNAME, MX, NS, PTR, SOA, SRV a TXT.

* **Správa záznamů automatické název hostitele.** Společně s hostování svoje vlastní záznamy DNS, Azure automaticky udržuje záznamy název hostitele pro virtuální počítače v zadané virtuální sítě.  To umožňuje optimalizovat názvy domén, které můžete použít bez nutnosti vytvářet vlastní řešení DNS nebo upravit aplikaci.

* **Rozlišení názvu hostitele mezi virtuálními sítěmi.** Na rozdíl od názvů hostitelů Azure může být privátní zóny DNS sdílena mezi virtuálními sítěmi.  Tato funkce zjednodušuje scénáře napříč sítí a službu zjišťování například partnerský vztah virtuální sítě.

* **Známých nástrojů a činnost koncového uživatele.** K redukovat křivku, používá tato nová nabídka již zavedené nástroje Azure DNS (PowerShell, šablony Resource Manageru, REST API).

* **Rozdělení horizon DNS podporují.** Azure DNS vám umožní vytvořit zóny se stejným názvem, který odkazující na různých odpovědi z virtuální sítě a z veřejného Internetu.  Typický scénář v nástroji rozdělení horizon DNS je poskytnout vyhrazené verzi služby pro použití ve virtuální síti.

* **K dispozici ve všech oblastech Azure.** Zóny DNS privátní Azure je dostupná v všech oblastech Azure ve veřejném Azure cloudu. 


## <a name="capabilities"></a>Možnosti 
* Automatická registrace virtuálních počítačů z jedné virtuální sítě propojené s privátní zóny jako registrace virtuální sítě. Virtuální počítače bude registrovaný (Přidat) k zóně privátní jako zaznamenává odkazující na jejich privátní IP adresy. Kromě toho když virtuální počítač v registrace virtuální sítě se odstranila, Azure také automaticky odebere odpovídající záznam DNS z propojené privátní zóny. Všimněte si, že registrace virtuálních sítí také ve výchozím nastavení fungovat jako řešení virtuálních sítí v, že bude fungovat překlad názvů DNS pro zónu ze všech virtuálních počítačů v rámci virtuální sítě registrace. 
* Předat dál překlad názvů DNS, které jsou podporovány v rámci virtuální sítě, které jsou propojeny s privátní zóny jako řešení virtuální sítě. Pro překlad DNS cross virtuální síť, neexistuje žádné explicitní závislost, virtuální sítě se peered mezi sebou. Však mohou zákazníci chtějí partnerský uzel virtuálních sítí s dalšími scénáři (např: přenos HTTP).
* Zpětné vyhledávání DNS jsou podporovány v rámci oboru virtuální sítě. Pro privátní IP Adresou v rámci ve virtuální síti přiřazené k privátní zóny zpětného vyhledávání DNS vrátí plně kvalifikovaný název domény, který zahrnuje název hostitele nebo záznamu, stejně jako název zóny jako příponu. 


## <a name="limitations"></a>Omezení
* 1 virtuální sítě registrace za soukromé zóny
* Až 10 řešení virtuální sítě na privátní zóny
* Dané virtuální síti lze propojit jen na jednu zónu privátní jako virtuální síť registrace
* Dané virtuální síti může být propojený až 10 privátní zóny jako řešení virtuální sítě
* Pokud je zadán virtuální síť registrace, záznamy DNS pro virtuální počítače z této virtuální sítě, které jsou registrovány k zóně privátní nebude viditelná a získat z prostředí Powershell nebo rozhraní příkazového řádku nebo rozhraní API, ale záznamy virtuálních počítačů jsou skutečně zaregistrované a vyřešit úspěšně
* Zpětné DNS bude fungovat pouze pro adresní prostor privátní IP ve virtuální síti registrace
* Reverse DNS pro privátní IP adresu, která není registrována v zóně privátní (např: privátní IP pro virtuální počítač ve virtuální síti, který bude propojen jako řešení virtuální sítě na zónu privátní) vrátí "internal.cloudapp.net" jako přípona DNS, ale tato přípona nebude možné přeložit.   
* Virtuální síť musí být prázdná (tj. žádné záznamy virtuálních počítačů) při počátečním (tj. prvním) propojení na zónu privátní jako registraci nebo řešení virtuální síť. Virtuální síť pak lze však není prázdná pro budoucí propojování jako virtuální síť registraci nebo řešení, k jiné privátní zóny. 
* V tomto okamžiku podmíněné předávání není podporován, například pro povolení rozlišení mezi Azure a místní sítě. Dokumentace na tom, jak zákazníci můžou realizovat tento scénář prostřednictvím jiným mechanismem, najdete v tématu [překlad názvů pro virtuální počítače a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

Také doporučujeme také přečíst na [– nejčastější dotazy](./dns-faq.md#private-dns) pro některé běžné otázky a odpovědi na privátní zóny v Azure DNS, včetně konkrétní chování DNS řešení a registrace můžete očekávat, že pro určité typy operací. 


## <a name="pricing"></a>Ceny

Zóny DNS privátní je zdarma verzi public Preview. Při obecné dostupnosti si tato funkce bude používat na základě využití cenové model podobná existující Azure DNS nabídky. 


## <a name="next-steps"></a>Další postup

Naučte se vytvářet privátní zóny v Azure DNS pomocí [prostředí PowerShell](./private-dns-getstarted-powershell.md) nebo [rozhraní příkazového řádku](./private-dns-getstarted-cli.md).

Přečíst na některé běžné scénáře [privátní zóny scénáře](./private-dns-scenarios.md) , může být dosaženo privátní zón v Azure DNS.

Přečíst na [– nejčastější dotazy](./dns-faq.md#private-dns) pro některé běžné otázky a odpovědi na privátní zóny v Azure DNS, včetně konkrétní chování můžete očekávat, že pro určité typy operací. 

Další informace o zóny DNS a záznamy, navštivte stránky: [DNS zóny a zaznamenává přehled](dns-zones-records.md).

Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.

