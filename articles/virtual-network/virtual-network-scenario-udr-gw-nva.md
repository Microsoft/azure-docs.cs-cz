---
title: Hybridní připojení s aplikaci na vrstvě 2 | Microsoft Docs
description: Informace o nasazení virtuální zařízení a UDR k vytvoření prostředí vícevrstvé aplikace v Azure
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: jdial
ms.openlocfilehash: 544ba6484b23da425d53594622122b1e18b92359
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2017
ms.locfileid: "23943221"
---
# <a name="virtual-appliance-scenario"></a>Scénář virtuální zařízení
Běžný scénář mezi větší Azure zákazníků se musí zajistit Dvojúrovňová aplikace, přístup k Internetu, při umožňuje přístup k vrstvě zpět z překážek místní datacentra. Tento dokument vás provede procesem scénář používající uživatele definované trasy (UDR), brána sítě VPN a virtuální síťové zařízení k nasazení dvouvrstvé prostředí, které splňuje následující požadavky:

* Webové aplikace musí být přístupné z veřejného Internetu.
* Webový server, který je hostitelem aplikace musí být mít přístup k aplikační server back-end.
* Veškerý provoz z Internetu do webové aplikace musí projít virtuální zařízení brány firewall. Tato virtuální zařízení se použije pro jenom přenosy z Internetu.
* Veškerý provoz směřující do aplikačního serveru musí projít virtuální zařízení brány firewall. Tato virtuální zařízení se použije pro přístup k back-end koncový server a přístup brzo z místní sítě prostřednictvím brány VPN.
* Správci musí mít možnost spravovat virtuální zařízení brány firewall ze své místní počítače, a to pomocí brány firewall třetí virtuální zařízení používané výhradně pro účely správy.

Toto je standardní scénář DMZ s DMZ a chráněné síti. Takový scénář lze sestavit v Azure pomocí skupin Nsg, virtuální zařízení brány firewall nebo jejich kombinaci. Následující tabulka uvádí některé výhody a nevýhody mezi skupiny Nsg a virtuální zařízení brány firewall.

|  | Odborníci na | Nevýhody |
| --- | --- | --- |
| SKUPINA NSG |Bez nákladů. <br/>Integrovaná do Azure RBAC. <br/>Pravidla je možné vytvořit v šablony ARM. |Složitost může lišit v prostředích větší. |
| Brána firewall |Plnou kontrolu nad rovinu data. <br/>Centrální správa prostřednictvím konzoly brány firewall. |Náklady na zařízení brány firewall. <br/>Není integrované s Azure RBAC. |

Následující řešení používá virtuální zařízení brány firewall k implementaci scénáři hraniční sítě nebo chráněné sítě.

## <a name="considerations"></a>Požadavky
Můžete nasadit v prostředí popsané výše v Azure pomocí různých funkcí, které jsou k dispozici dnes, a to takto.

* **Virtuální síť (VNet)**. O virtuální síť Azure funguje podobným způsobem k místní síti a mohou být segmentovány do jedné nebo více podsítí k zajištění izolace přenosů dat a oddělené oblasti zájmu.
* **Virtuální zařízení**. Několik partneři poskytovat virtuálních zařízení v Azure Marketplace, které je možné pro tři brány firewall popsané výše. 
* **Uživatelem definované trasy (UDR)**. Směrovací tabulky mohou obsahovat udr používá Azure sítě k řízení toku paketů v rámci virtuální sítě. Tyto směrovací tabulky lze použít na podsítě. Jedním z nejnovější funkce v Azure je možné aplikovat směrovací tabulku na GatewaySubnet, poskytuje schopnost veškerý provoz přicházející do virtuální sítě Azure ze hybridní připojení k virtuální zařízení.
* **Předávání IP**. Ve výchozím nastavení modul Azure sítě předávat pakety pro virtuální síťové adaptéry (NIC) pouze v případě, že seskupování IP adresa odpovídá paketu cílovou IP adresu. Proto pokud UDR definuje, musí se poslat paket do daného virtuálního zařízení, modul Azure sítě by vyřadit paketu. Chcete-li se ujistit, jestli že paket se doručí na virtuální počítač (v tomto případě virtuální zařízení), který není skutečné cíl paketu, budete muset povolit předávání IP adres pro virtuální zařízení.
* **Skupin zabezpečení (Nsg) sítě**. Následující příklad není nutné používat skupiny Nsg, ale můžete použít skupiny Nsg použije podsítí a síťových karet v tomto řešení Chcete-li dál filtrovat provoz do/z těchto podsítí a síťových karet.

![Připojení pomocí protokolu IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

V tomto příkladu je odběr, který obsahuje následující:

* 2 skupin prostředků, není vidět v diagramu. 
  * **ONPREMRG**. Obsahuje všechny prostředky potřebné k simulaci do místní sítě.
  * **AZURERG**. Obsahuje všechny prostředky potřebné pro prostředí Azure virtuální sítě. 
* Virtuální síť s názvem **onpremvnet** napodobovat místního datového centra segmentované, jak je uvedeno dále.
  * **onpremsn1**. Podsíť obsahující virtuálního počítače (VM) se systémem Ubuntu tak, aby napodoboval na místním serveru.
  * **onpremsn2**. Podsíť obsahující virtuálního počítače s Ubuntu tak, aby napodoboval místním počítači použít správce.
* Neexistuje jeden virtuální zařízení brány firewall s názvem **OPFW** na **onpremvnet** používá k udržení tunel ke **azurevnet**.
* Virtuální síť s názvem **azurevnet** segmentované, jak je uvedeno dále.
  * **azsn1**. Externí brány firewall podsítě používané výhradně pro externí brány firewall. Všechny přenosy z Internetu se mají prostřednictvím této podsíti. Tato podsíť obsahuje pouze síťový adaptér propojené s externí brány firewall.
  * **azsn2**. Podsítě front end, který je hostitelem virtuálního počítače s jako webový server, které mají být přístupné z Internetu.
  * **azsn3**. Back-end podsíť, který je hostitelem virtuálního počítače s back-end serveru aplikací, které mají být přístupné pro front-end webový server.
  * **azsn4**. Podsíť správy používat výhradně k zajištění správy přístupu pro všechny virtuální zařízení brány firewall. Tato podsíť obsahuje jenom síťovou kartu pro každý virtuální zařízení brány firewall používá v řešení.
  * **GatewaySubnet**. Azure hybridní připojení podsíť potřebné pro ExpressRoute a VPN Gateway a tím zajistit připojení mezi virtuálními sítěmi Azure a další sítě. 
* Existují 3 virtuálních zařízení brány firewall v **azurevnet** sítě. 
  * **AZF1**. Externí brány firewall zveřejněné do veřejného Internetu pomocí prostředek veřejné IP adresy v Azure. Je potřeba zajistit, že máte šablonu z Marketplace, nebo přímo od dodavatele zařízení, že zřizuje o 3-NIC virtuální zařízení.
  * **AZF2**. Vnitřní brána firewall použít k řízení provozu mezi **azsn2** a **azsn3**. Toto je také 3-NIC virtuální zařízení.
  * **AZF3**. Brány firewall správy přístupné správcům z místního datového centra a připojený k podsíti správy použít ke správě všech zařízení brány firewall. Můžete najít 2-NIC šablony virtuální zařízení na webu Marketplace, nebo požádejte jeden přímo od dodavatele zařízení.

## <a name="user-defined-routing-udr"></a>Uživatelem definované směrování (UDR)
Každá podsíť v Azure může být propojený UDR tabulku používá k definování jak inicioval provoz v, že se směruje podsítě. Pokud jsou definovány žádné udr, Azure používá výchozí trasy umožňující přenos mají být předány z jedné podsítě. Abyste lépe pochopili udr, navštivte [co jsou trasy definované uživatelem a předávání IP](virtual-networks-udr-overview.md).

K zajištění komunikace probíhá prostřednictvím zařízení správné brány firewall, založené na poslední požadavku výše, musíte vytvořit následující trasy tabulka obsahující udr v **azurevnet**.

### <a name="azgwudr"></a>azgwudr
V tomto scénáři se použije pouze provoz z místního do Azure ke správě bránu firewall připojením k **AZF3**, a že komunikace musí procházet přes bránu firewall interní **AZF2**. Proto je nutné v pouze jednoho z nich **GatewaySubnet** jak je uvedeno níže.

| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Umožňuje místní provoz dosáhnout brány firewall správy **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Umožňuje přenos na back-end podsíť hostování aplikační server prostřednictvím **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Umožňuje všechny ostatní přenosy ho směrovat pomocí **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Umožňuje provoz **azsn2** k toku ze serveru aplikace na webovém serveru prostřednictvím **AZF2** |

Je také nutné vytvořit směrovací tabulky pro podsítě **onpremvnet** tak, aby napodoboval místního datového centra.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Umožňuje provoz **onpremsn2** prostřednictvím **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Umožňuje přenos zálohovány podsíti v Azure prostřednictvím **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Umožňuje provoz **onpremsn1** prostřednictvím **OPFW** |

## <a name="ip-forwarding"></a>Předávání IP
UDR a předávání IP adres jsou funkce, které můžete použít v kombinaci umožňující virtuální zařízení, který se má použít k řízení toku provozu ve službě Azure VNet.  Virtuální zařízení není nic jiného než virtuální počítač, na kterém běží aplikace sloužící k určitému zpracování síťového provozu, jako je například brána firewall nebo zařízení NAT.

Tento virtuální počítač virtuálního zařízení musí být schopný přijímat příchozí provoz, který mu není adresovaný. Pokud chcete virtuálnímu počítači povolit přijímání dat adresovaných jiným cílům, je nutné, abyste mu povolili předávání IP. Toto je nastavení Azure, nikoli nastavení hostovaného operačního systému. Virtuální zařízení se pořád potřeba spustit nějaký typ aplikace zpracovávat příchozí provoz a přesměrovávala je správně.

Další informace o předávání IP adres, navštivte [co jsou trasy definované uživatelem a předávání IP](virtual-networks-udr-overview.md).

Jako příklad Představte si, že máte následující nastavení v Azure virtuální sítě:

* Podsíť **onpremsn1** obsahuje virtuální počítač s názvem **onpremvm1**.
* Podsíť **onpremsn2** obsahuje virtuální počítač s názvem **onpremvm2**.
* Virtuální zařízení s názvem **OPFW** je připojený k **onpremsn1** a **onpremsn2**.
* Trasy definované uživatelem propojené s **onpremsn1** Určuje, že všechny přenosy do **onpremsn2** zaslána **OPFW**.

Na tento příkaz, pokud **onpremvm1** pokusí navázat spojení se **onpremvm2**, se použije UDR a bude odeslán provoz **OPFW** jako další segment. Mějte na paměti, že nedojde ke změně cílové skutečných paketech, stále zobrazuje **onpremvm2** je cílem. 

Bez povoleno pro předávání IP **OPFW**, logice Azure virtuální sítě budou vyřadit paketů, jelikož umožňuje pakety k odeslání do virtuálního počítače, pokud je cílem paketu IP adresu Virtuálního počítače.

S předávání IP adres předá logice virtuální síť Azure pakety do OPFW, aniž byste museli měnit jeho původní cílovou adresu. **OPFW** musí zpracování paketů a určení toho, co s nimi dělat.

Pro výše uvedené scénář fungovat, je nutné povolit předávání IP adres na síťových adaptérů pro **OPFW**, **AZF1**, **AZF2**, a **AZF3** , který se používá pro směrování (všechny síťové adaptéry s výjimkou těch, propojit k podsíti management). 

## <a name="firewall-rules"></a>Pravidla brány firewall
Jak je popsáno výše, předávání IP adres pouze zajistí, že pakety odesílají na virtuální zařízení. Vaše zařízení je ještě potřeba rozhodnout, jak tyto pakety. Ve scénáři výše budete muset vytvořit ve vašem zařízení následující pravidla:

### <a name="opfw"></a>OPFW
OPFW představuje místní zařízení se systémem obsahující následující pravidla:

* **Trasy**: veškerý provoz do 10.0.0.0/16 (**azurevnet**) prostřednictvím tunelu, musí se poslat **ONPREMAZURE**.
* **Zásady**: Povolí všechny obousměrnou komunikaci mezi **port2** a **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
AZF1 představuje Azure virtuální zařízení, která obsahuje následující pravidla:

* **Zásady**: Povolí všechny obousměrnou komunikaci mezi **port1** a **port2**.

### <a name="azf2"></a>AZF2
AZF2 představuje Azure virtuální zařízení, která obsahuje následující pravidla:

* **Trasy**: veškerý provoz do 10.0.0.0/16 (**onpremvnet**) musí být poslaly k bráně Azure IP adresa (např. 10.0.0.1) **port1**.
* **Zásady**: Povolí všechny obousměrnou komunikaci mezi **port1** a **port2**.

## <a name="network-security-groups-nsgs"></a>Skupiny zabezpečení sítě (Nsg)
V tomto scénáři nejsou používány skupiny Nsg. Ale může použít skupiny Nsg na každé podsíti omezit příchozí a odchozí provoz. Například je možné aplikovat následující pravidla NSG na externí FW podsíť.

**Příchozí**

* Povolit všechny přenosy TCP na portu 80 pro žádné virtuální počítače v podsíti z Internetu.
* Odepřít všechny ostatní přenosy z Internetu.

**Odchozí**

* Odepřít veškerý provoz do Internetu.

## <a name="high-level-steps"></a>Kroky vysoké úrovně
Pokud chcete nasadit tento scénář, postupujte následujícím způsobem nejvyšší úrovni.

1. Přihlaste se k předplatnému Azure.
2. Pokud chcete nasadit virtuální sítě tak, aby napodoboval do místní sítě, zřízení prostředků, které jsou součástí **ONPREMRG**.
3. Zřízení prostředků, které jsou součástí **AZURERG**.
4. Zřídit tunelové propojení z **onpremvnet** k **azurevnet**.
5. Po zřízení jsou všechny prostředky, přihlaste se k **onpremvm2** a příkazem ping otestovat 10.0.3.101 k testování připojení mezi **onpremsn2** a **azsn3**.

