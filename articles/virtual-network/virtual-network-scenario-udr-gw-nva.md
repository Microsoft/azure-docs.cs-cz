---
title: Hybridní připojení se 2vrstvou aplikaci | Dokumentace Microsoftu
description: Zjistěte, jak nasadit virtuální zařízení a uživatelem definovaná TRASA k vytvoření prostředí vícevrstvou aplikaci v Azure
services: virtual-network
documentationcenter: na
author: KumudD
manager: carmonm
editor: tysonn
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: kumud
ms.openlocfilehash: 1bdc485dfb352144e8a8d0fb75965cbb78288e2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64575581"
---
# <a name="virtual-appliance-scenario"></a>Scénář virtuálního zařízení
Běžný scénář mezi větších zákazníků Azure je potřeba poskytnout aplikaci dvouvrstvém přístupný z Internetu, zároveň vám umožní přístup na úroveň zpět z místního datacentra. Tento dokument vás provede scénář použití uživatelem definované trasy (UDR), bránu sítě VPN a síťových virtuálních zařízení pro nasazení dvouvrstvé prostředí, které splňuje následující požadavky:

* Webová aplikace musí být přístupné z veřejného Internetu.
* Webový server, který je hostitelem aplikace musí být mít přístup k back-endový aplikační server.
* Veškerý provoz z Internetu do webové aplikace musí procházet přes virtuální zařízení brány firewall. Tato virtuální zařízení se použije pro jenom přenosy z Internetu.
* Veškerý provoz směřující do aplikačního serveru musí procházet přes virtuální zařízení brány firewall. Tato virtuální zařízení se použije pro přístup k back-end serveru a přístup pocházející z místní sítě prostřednictvím brány VPN.
* Správci musí mít možnost spravovat virtuální zařízení brány firewall ze své místní počítače, pomocí brány firewall třetí virtuální zařízení používá výhradně pro účely správy.

Toto je scénář standardní hraniční sítě (knowns také jako DMZ) DMZ a chráněná síťová. Takový scénář lze sestavit v Azure pomocí skupin zabezpečení sítě, virtuální zařízení brány firewall nebo kombinaci obojího. Následující tabulka ukazuje některé výhody a nevýhody mezi skupiny zabezpečení sítě a virtuální zařízení brány firewall.

|  | V oblasti IT | Nevýhody |
| --- | --- | --- |
| NSG |Bez poplatků. <br/>Integrovaná v Azure RBAC. <br/>Pravidla můžete vytvořit v šablonách Azure Resource Manageru. |Složitost tak můžete pozměnit ve větších prostředí. |
| Brána firewall |Plnou kontrolu nad rovina dat. <br/>Centrální správa prostřednictvím konzoly brány firewall. |Náklady na zařízení brány firewall. <br/>Není součástí Azure RBAC. |

Následující řešení používá virtuální zařízení brány firewall k implementaci hraniční síti (DMZ) / chráněné scénáři.

## <a name="considerations"></a>Požadavky
Můžete nasadit prostředí je vysvětleno výše v Azure ještě dnes, následujícím způsobem pomocí různých funkcí, které jsou k dispozici.

* **Virtuální síť (VNet)** . Virtuální síť Azure funguje podobně jako ochrana čipem v místní síti a je možné segmentovat do jedné nebo několika podsítí pro zajištění izolace provozu a oddělení oblastí zájmu.
* **Virtuální zařízení**. Několik partneři poskytovat virtuální zařízení na webu Azure Marketplace, který lze použít pro tři brány firewall, je popsáno výše. 
* **Uživatelem definované trasy (UDR)** . Směrovací tabulky mohou obsahovat používá sítě Azure k řízení toku paketů ve virtuální síti trasy definované uživatelem. Tyto směrovacích tabulek můžete použít na podsítě. Jednou z nejnovějších funkcí v Azure je možnost použití směrovací tabulky podsítě GatewaySubnet, veškerý provoz přicházející do virtuální sítě Azure z hybridní připojení do virtuálního zařízení možnost, že.
* **Předávání IP**. Ve výchozím nastavení modulu Azure sítě přesměrovávat pakety pro virtuální síťové karty (síťové adaptéry) pouze v případě, že paketů cílová IP adresa shoduje s adresou IP adresy NIC. Proto pokud trasu UDR definuje, paket se musí odeslat na danou virtuální zařízení, modul Azure sítě by vyřadit paketu. Chcete-li zajistit, že paket se doručí na virtuální počítač (v tomto případě virtuální zařízení), který není na skutečné místo určení paketu, budete muset povolit předávání IP adres pro virtuální zařízení.
* **Skupiny zabezpečení sítě (Nsg)** . Následující příklad nevyužívá skupin nsg, ale můžete použít skupiny zabezpečení sítě u podsítí a/nebo síťové karty v tomto řešení Chcete-li dál filtrovat provoz do a z těchto podsítí a síťových karet.

![Připojení pomocí protokolu IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

V tomto příkladu je předplatné, které obsahuje následující:

* 2 skupin prostředků, není vidět v diagramu. 
  * **ONPREMRG**. Obsahuje všechny prostředky potřebné pro simulaci v místní síti.
  * **AZURERG**. Obsahuje všechny prostředky potřebné pro prostředí Azure virtuální sítě. 
* Virtuální síť s názvem **onpremvnet** napodobovat místním datovým centrem segmentované, jak je uvedeno níže.
  * **onpremsn1**. Podsíť obsahující virtuální počítač (VM) s Ubuntu tak, aby napodoboval na místním serveru.
  * **onpremsn2**. Podsíť obsahující virtuální počítač s Ubuntu tak, aby napodoboval v místním počítači použít správce.
* Existuje jedna virtuální zařízení brány firewall s názvem **OPFW** na **onpremvnet** používá k udržení tunel ke **azurevnet**.
* Virtuální síť s názvem **azurevnet** segmentované, jak je uvedeno níže.
  * **azsn1**. Externí bránu firewall podsítě používané výhradně pro externí bránu firewall. Všechny přenosy z Internetu bude mít přes tuto podsíť. Tato podsíť obsahuje pouze propojené s externí bránu firewall síťové rozhraní.
  * **azsn2**. Podsítě front end, který je hostitelem virtuálního počítače s jako webový server, který bude přístupný z Internetu.
  * **azsn3**. Podsíť back-endu, který je hostitelem virtuálního počítače s back-endový aplikační server, kterou budou přistupovat front-end webový server.
  * **azsn4**. Podsítě pro správu používá výhradně k poskytování řízení přístupu na všechna virtuální zařízení brány firewall. Tato podsíť obsahuje pouze síťovou kartu pro každý virtuální zařízení brány firewall používané v řešení.
  * **GatewaySubnet**. Azure hybrid připojení podsíť vyžaduje a tím zajistit připojení mezi virtuálními sítěmi Azure a další sítě pro ExpressRoute a VPN Gateway. 
* Existují 3 virtuální zařízení brány firewall v **azurevnet** sítě. 
  * **AZF1**. Externí bránu firewall vystavena do veřejného Internetu s použitím prostředek veřejné IP adresy v Azure. Je potřeba zajistit, že máte šablonu z webu Marketplace, nebo přímo ze zařízení od dodavatele, že ustanovení o 3-NIC virtuální zařízení.
  * **AZF2**. Vnitřní brána firewall používá k řízení provozu mezi **azsn2** a **azsn3**. Toto je také 3-NIC virtuálního zařízení.
  * **AZF3**. Správa brány firewall přístupné správcům z místního datového centra a připojený k podsíti správy používá ke správě všech zařízení brány firewall. Můžete najít 2 síťovými Kartami šablony virtuálního zařízení na webu Marketplace, nebo můžete požádat přímo ze svého dodavatele zařízení.

## <a name="user-defined-routing-udr"></a>Uživatelem definované směrování (UDR)
Každá podsíť v Azure může být propojené s tabulkou uživatelem definovaná TRASA sloužících k definování, jak inicioval provoz v tom, že podsíť prochází. Pokud jsou definované žádné trasy definované uživatelem, Azure využívá výchozí trasy pro povolení provozu tok z jedné podsítě do druhé. Abyste lépe pochopili trasy definované uživatelem, navštivte [co jsou trasy definované uživatelem a předávání IP adres](virtual-networks-udr-overview.md).

K zajištění komunikace probíhá přes toto zařízení správný brány firewall založené na poslední požadavku výše, je potřeba vytvořit následující směrovací tabulku obsahující trasy definované uživatelem v **azurevnet**.

### <a name="azgwudr"></a>azgwudr
V tomto scénáři se použije pouze provoz z místního Azure spravovat bránu firewall propojíte s **AZF3**, a provoz musí projít interní bránu firewall, **AZF2**. Proto je nutné v pouze jednu trasu **GatewaySubnet** jak je znázorněno níže.

| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Umožňuje místní přenosy k dosažení brány firewall správy **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Umožňuje přenos pro podsíť back-endu, který je hostitelem aplikace serveru prostřednictvím **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Povolí veškerý ostatní provoz směrovat přes **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Umožňuje přenos **azsn2** do služby flow z aplikačního serveru na webovém serveru prostřednictvím **AZF2** |

Je také potřeba vytvořit směrovací tabulky pro podsítě **onpremvnet** tak, aby napodoboval místního datového centra.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Umožňuje přenos **onpremsn2** prostřednictvím **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Umožňuje přenos zajištěnou podsítě v Azure prostřednictvím **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Umožňuje přenos **onpremsn1** prostřednictvím **OPFW** |

## <a name="ip-forwarding"></a>Předávání IP
Směrování definovaného uživatelem a předávání IP adres jsou funkce, které můžete použít v kombinaci, aby virtuální zařízení se použije k řízení toku provozu ve virtuální síti Azure.  Virtuální zařízení není nic jiného než virtuální počítač, na kterém běží aplikace sloužící k určitému zpracování síťového provozu, jako je například brána firewall nebo zařízení NAT.

Tento virtuální počítač virtuálního zařízení musí být schopný přijímat příchozí provoz, který mu není adresovaný. Pokud chcete virtuálnímu počítači povolit přijímání dat adresovaných jiným cílům, je nutné, abyste mu povolili předávání IP. Toto je nastavení Azure, nikoli nastavení hostovaného operačního systému. Virtuální zařízení je stále potřeba spustit některé typu aplikace ke zpracování příchozí provoz a směrovat odpovídajícím způsobem.

Další informace o předávání IP adres najdete v tématu [co jsou trasy definované uživatelem a předávání IP adres](virtual-networks-udr-overview.md).

Jako příklad Představte si, že máte následující nastavení ve virtuální síti Azure:

* Podsíť **onpremsn1** obsahuje virtuální počítač s názvem **onpremvm1**.
* Podsíť **onpremsn2** obsahuje virtuální počítač s názvem **onpremvm2**.
* Virtuální zařízení s názvem **OPFW** je připojen k **onpremsn1** a **onpremsn2**.
* Uživatelem definovaná trasa propojené s **onpremsn1** Určuje, že všechny provoz do **onpremsn2** se musí odeslat na **OPFW**.

Na tento příkaz, pokud **onpremvm1** pokusí navázat připojení s **onpremvm2**, bude použita uživatelem definovaná TRASA a provoz se odešlou do **OPFW** jako další segment směrování. Mějte na paměti, že nedojde ke změně cíle skutečných paketech, stále stavu **onpremvm2** je cílem. 

Bez předávání IP adres povolený pro **OPFW**, Azure virtuálních sítí logiky zahodí pakety, jelikož umožňuje pouze pakety k odeslání do virtuálního počítače, pokud IP adresu Virtuálního počítače je cílem paketu.

S předávání IP adres virtuální sítě Azure logic předává pakety OPFW, beze změny jeho původní cílovou adresu. **OPFW** musí zpracovat paketů a určení toho, co s nimi dělat.

Pro výše uvedeném scénáři Pokud chcete pracovat, je nutné povolit předávání IP adres na síťové adaptéry pro **OPFW**, **AZF1**, **AZF2**, a **AZF3** , která se používají pro směrování (všechny síťové adaptéry s výjimkou těch, které jsou propojeny s podsítě pro správu). 

## <a name="firewall-rules"></a>Pravidla brány firewall
Jak je popsáno výše, předávání IP adres pouze zajistí, že pakety odesílají na virtuální zařízení. Vaše zařízení je stále potřeba rozhodněte, jak se tyto pakety. Ve výše uvedeném scénáři budete muset vytvořit v svoje zařízení následující pravidla:

### <a name="opfw"></a>OPFW
OPFW představuje místní zařízení, které obsahuje následující pravidla:

* **trasa**: Veškerý provoz směrem k 10.0.0.0/16 (**azurevnet**) pomocí tunelového připojení se musí odeslat **ONPREMAZURE**.
* **Zásady**: Povolení veškerého provozu obousměrné mezi **port2** a **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
AZF1 představuje Azure virtuálního zařízení, který obsahuje následující pravidla:

* **Zásady**: Povolení veškerého provozu obousměrné mezi **port1** a **port2**.

### <a name="azf2"></a>AZF2
AZF2 představuje Azure virtuálního zařízení, který obsahuje následující pravidla:

* **trasa**: Veškerý provoz směrem k 10.0.0.0/16 (**onpremvnet**) se musí odeslat na Azure gateway IP adresa (např. 10.0.0.1) prostřednictvím **port1**.
* **Zásady**: Povolení veškerého provozu obousměrné mezi **port1** a **port2**.

## <a name="network-security-groups-nsgs"></a>Skupiny zabezpečení sítě (Nsg)
V tomto scénáři se používá skupiny zabezpečení sítě. Můžete však použít skupiny zabezpečení sítě ke každé podsíti omezit příchozí a odchozí provoz. Například můžete použít následující pravidla skupiny zabezpečení sítě k podsíti externí FW.

**příchozí**

* Povolí veškerý provoz TCP na port 80 na všech virtuálních počítačů v podsíti z Internetu.
* Odepřít veškerý ostatní provoz z Internetu.

**Odchozí**

* Odepřít veškerý provoz do Internetu.

## <a name="high-level-steps"></a>Postup vysoké úrovně
K nasazení tohoto scénáře, postupujte podle níže uvedené kroky na nejvyšší úrovni.

1. Přihlaste se ke svému předplatnému Azure.
2. Pokud chcete nasadit virtuální síť tak, aby napodoboval v místní síti, zřizovat prostředky, které jsou součástí **ONPREMRG**.
3. Zřízení prostředků, které jsou součástí **AZURERG**.
4. Zřízení tunelového propojení z **onpremvnet** k **azurevnet**.
5. Po zřízení jsou všechny prostředky, přihlaste se k **onpremvm2** a odešlete zprávu ping 10.0.3.101 k testování připojení mezi **onpremsn2** a **azsn3**.

