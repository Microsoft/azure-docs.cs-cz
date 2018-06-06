---
title: Používat Azure DNS pro privátní domény | Microsoft Docs
description: Přehled privátního DNS hostitelem služby v Microsoft Azure.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: 0ee3b18b7f874c4f6b7b2c9c559aa7e393ad7d8d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700600"
---
# <a name="use-azure-dns-for-private-domains"></a>Použití Azure DNS pro privátní domény
Systému názvů domény nebo DNS, zodpovídá za překladu (nebo vyřešení) název služby na jeho IP adresu. Hostitelská služba domén DNS, Azure DNS poskytuje překlad pomocí infrastruktury Microsoft Azure. Kromě podpora internetových domén DNS, Azure DNS teď také podporuje privátní DNS domény jako funkce preview. 
 
Azure DNS poskytuje spolehlivé, zabezpečené služby DNS pro správu a překládat názvy domén ve virtuální síti bez nutnosti přidat vlastní řešení DNS. Když použijete privátní zóny DNS, můžete použít vlastní názvy vlastních domén namísto názvů Azure, které jsou dnes k dispozici. Pomocí vlastních názvů domén umožňuje přizpůsobit Architektura virtuální sítě podle potřeb vaší organizace. Poskytuje překlad názvů pro virtuální počítače (VM) v rámci virtuální sítě a mezi virtuálními sítěmi. Kromě toho můžete nakonfigurovat názvy zón s rozdělení horizon zobrazení, které umožňuje privátní a veřejné zóny DNS sdílet stejný název.

![Přehled systému DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="benefits"></a>Výhody

Azure DNS poskytuje následující výhody:

* **Eliminuje nutnost pro vlastní řešení DNS**. Mnoho zákazníků vytvořili vlastní DNS řešení pro správu zón DNS ve své virtuální síti. Teď můžete provádět správu zóny DNS pomocí nativní infrastrukturu Azure, která odebere zatížení při vytváření a správě vlastních řešení DNS.

* **Použít všechny běžné typy záznamů DNS**. Azure DNS podporuje záznamy A, AAAA, CNAME, MX, NS, PTR, SOA, SRV a TXT.

* **Správa záznamů automatické hostname**. Společně s hostování svoje vlastní záznamy DNS, Azure automaticky udržuje záznamy název hostitele pro virtuální počítače v zadané virtuální sítě. V tomto scénáři můžete optimalizovat názvy domén, které můžete použít bez nutnosti vytvářet vlastní řešení DNS nebo upravovat aplikace.

* **Rozlišení názvu hostitele mezi virtuálními sítěmi**. Na rozdíl od názvů hostitelů Azure může být privátní zóny DNS sdílena mezi virtuálními sítěmi. Tato funkce zjednodušuje mezi sítě a zjišťování služby scénářích, třeba partnerský vztah virtuální sítě.

* **Známých nástrojů a činnost koncového uživatele**. K redukovat křivku, používá tato nová nabídka zavedené nástroje Azure DNS (prostředí PowerShell, šablony Azure Resource Manager a rozhraní REST API).

* **Rozdělení horizon DNS podporují**. Pomocí Azure DNS můžete vytvořit zóny se stejným názvem, které odkazují na různých odpovědi z virtuální sítě a z veřejného Internetu. Typický scénář v nástroji rozdělení horizon DNS je poskytnout vyhrazené verzi služby pro použití ve virtuální síti.

* **K dispozici ve všech oblastech Azure**. Funkce privátní zón Azure DNS je dostupná v všech oblastech Azure ve veřejném cloudu Azure. 


## <a name="capabilities"></a>Možnosti

Azure DNS poskytuje následující možnosti:
 
* **Automatická registrace virtuálních počítačů z jedné virtuální sítě propojené zónu privátní jako virtuální síť registrace**. Virtuální počítače jsou registrovaná (Přidat) k zóně privátní jako zaznamenává odkazující na jejich privátních IP adres. Pokud virtuální počítač v registrace je odstranit virtuální síť, Azure také automaticky odebere odpovídající DNS záznamů z propojené privátní zóny. 

  > [!NOTE]
  > Ve výchozím nastavení registraci virtuálních sítí slouží také jako řešení virtuálních sítí v tom smyslu, že funguje překlad názvů DNS pro zónu ze všech virtuálních počítačů v rámci virtuální sítě registrace. 

* **Přední rozlišení serveru DNS je podporovaná ve virtuálních sítích, které jsou propojeny s privátní zóny jako řešení virtuální sítě**. Pro překlad DNS cross virtuální síť neexistuje žádná explicitní závislost tak, že virtuální sítě se kterými mají partnerský mezi sebou. Zákazníci mohou však chtějí partnerský uzel virtuálních sítí pro další scénáře (třeba přenos HTTP).

* **Zpětné vyhledávání DNS je podporována v rámci oboru virtuální sítě**. Pro privátní IP adresu ve virtuální síti přiřazené k privátní zóny zpětného vyhledávání DNS vrátí plně kvalifikovaný název domény, který zahrnuje název hostitele nebo záznamu, stejně jako název zóny jako příponu. 


## <a name="limitations"></a>Omezení

Azure DNS se vztahují následující omezení:

* Jeden privátní zóny je povoleno pouze jedna registrace virtuální sítě.
* Až 10 řešení jsou povoleny virtuální sítě na privátní zóny.
* Konkrétní virtuální síť může být propojený jenom jeden privátní zóny jako registrace virtuální sítě.
* Konkrétní virtuální síť může být propojený až 10 privátní zóny jako řešení virtuální sítě.
* Pokud je zadán virtuální síť registrace, záznamy DNS pro virtuální počítače z této virtuální sítě, které jsou registrovány k zóně privátní nejsou viditelná nebo získat z prostředí Azure Powershell a rozhraní API Správce Azure CLI, ale záznamy virtuálních počítačů jsou skutečně zaregistrované a bude úspěšně vyřešte.
* Zpětné DNS platí jenom pro adresní prostor privátní IP ve virtuální síti registrace.
* Reverse DNS pro privátní IP adresa, která není registrován v privátní zóny (například privátní IP adresu pro virtuální počítač ve virtuální síti, který bude propojen jako virtuální síť řešení privátního zóny) vrátí *internal.cloudapp.net* jako příponu DNS. Tato přípona však není možné přeložit. 
* Virtuální síť musí být prázdná (tj, virtuálního počítače neexistují žádné záznamy) při ho původně (to znamená, první) odkazy na zónu privátní jako registraci nebo řešení virtuální sítě. Virtuální síť pak lze však není prázdná pro budoucí propojování jako virtuální síť registraci nebo řešení, k jiné privátní zóny. 
* V současné době nepodporuje podmíněné předávání (například pro povolení rozlišení mezi Azure a místní sítí). Informace o tom, jak zákazníci můžou realizovat tento scénář pomocí jiných mechanismů najdete v tématu [překlad názvů pro virtuální počítače a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Pro běžné otázky a odpovědi týkající se privátní zóny v Azure DNS, včetně konkrétní chování DNS řešení a registrace můžete očekávat určité typy operací, najdete v části [– nejčastější dotazy](./dns-faq.md#private-dns).  


## <a name="pricing"></a>Ceny

Funkce soukromé zóny DNS je zdarma verzi public Preview. Při obecné dostupnosti bude funkci nabízejí na základě využití cenový model podobná existující Azure DNS nabídky. 


## <a name="next-steps"></a>Další postup

Naučte se vytvářet privátní zóny v Azure DNS pomocí [prostředí Azure PowerShell](./private-dns-getstarted-powershell.md) nebo [rozhraní příkazového řádku Azure](./private-dns-getstarted-cli.md).

Přečtěte si informace o některé běžné [privátní zóny scénáře](./private-dns-scenarios.md) , může být dosaženo privátní zón v Azure DNS.

Pro běžné otázky a odpovědi týkající se privátní zóny v Azure DNS, včetně konkrétní chování můžete očekávat určité typy operací, najdete v části [– nejčastější dotazy](./dns-faq.md#private-dns). 

Další informace o zóny DNS a záznamy, navštivte stránky [DNS zóny a zaznamenává přehled](dns-zones-records.md).

Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure. 

