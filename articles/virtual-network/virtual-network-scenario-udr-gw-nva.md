---
title: Hybridní připojení s dvoustupňovou aplikací | Dokumenty společnosti Microsoft
description: Zjistěte, jak nasadit virtuální zařízení a UDR k vytvoření vícevrstvého aplikačního prostředí v Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "64575581"
---
# <a name="virtual-appliance-scenario"></a>Scénář virtuálního zařízení
Běžným scénářem mezi většími zákazníky Azure je potřeba poskytnout dvouvrstvou aplikaci vystavenou internetu a zároveň povolit přístup k zadní vrstvě z místního datového centra. Tento dokument vás provede scénářem pomocí uživatelem definovaných tras (UDR), brány VPN a síťových virtuálních zařízení k nasazení dvouvrstvého prostředí, které splňuje následující požadavky:

* Webová aplikace musí být přístupná pouze z veřejného Internetu.
* Webový server hostující aplikaci musí mít přístup k serveru back-endové aplikace.
* Veškerý provoz z Internetu do webové aplikace musí projít virtuálním zařízením brány firewall. Toto virtuální zařízení bude použito pouze pro internetový provoz.
* Veškerý provoz na aplikační server musí projít virtuálním zařízením brány firewall. Toto virtuální zařízení se použije pro přístup k koncovému serveru back-end a přístup přicházející z místní sítě prostřednictvím brány VPN.
* Správci musí být schopni spravovat virtuální zařízení brány firewall ze svých místních počítačů pomocí třetího virtuálního zařízení brány firewall používaného výhradně pro účely správy.

Jedná se o standardní hraniční síť (také známý jako DMZ) scénář s DMZ a chráněné sítě. Takový scénář lze sestavit v Azure pomocí nsGs,firewall virtuální zařízení nebo kombinace obou. Níže uvedená tabulka ukazuje některé výhody a nevýhody mezi sítěmi nsg a firewall virtuálními zařízeními.

|  | Výhody | Nevýhody |
| --- | --- | --- |
| NSG |Žádné náklady. <br/>Integrovaná do Azure RBAC. <br/>Pravidla lze vytvořit v šablonách Azure Resource Manager. |Složitost se může lišit ve větších prostředích. |
| Brána firewall |Plná kontrola nad rovinou dat. <br/>Centrální správa prostřednictvím brány firewall. |Náklady na zařízení firewall. <br/>Není integrován s Azure RBAC. |

Níže uvedené řešení používá virtuální zařízení brány firewall k implementaci scénáře hraniční sítě (DMZ)/chráněné sítě.

## <a name="considerations"></a>Požadavky
Prostředí vysvětlené výše v Azure můžete nasadit pomocí různých funkcí, které jsou dnes k dispozici, a to následovně.

* **Virtuální síť (VNet)**. Virtuální síť Azure funguje podobným způsobem jako místní síť a lze je segmentovat do jedné nebo více podsítí, aby poskytovala izolaci provozu a oddělení problémů.
* **Virtuální zařízení**. Několik partnerů poskytuje virtuální zařízení na Azure Marketplace, která lze použít pro tři výše popsané brány firewall. 
* **Uživatelem definované trasy (UDR).** Tabulky směrování mohou obsahovat UDR používané sítí Azure k řízení toku paketů v rámci virtuální sítě. Tyto směrovací tabulky lze použít pro podsítě. Jednou z nejnovějších funkcí v Azure je možnost použít směrovací tabulku na GatewaySubnet, což poskytuje možnost předávat veškerý provoz přicházející do virtuální sítě Azure z hybridního připojení na virtuální zařízení.
* **Ip forwarding**. Ve výchozím nastavení předávají síťové moduly Azure pakety na karty virtuálního síťového rozhraní (NIC) pouze v případě, že cílová ip adresa paketu odpovídá ip adrese síťové karty. Proto pokud UDR definuje, že paket musí být odeslány do daného virtuálního zařízení, síťový modul Azure by pokles tohoto paketu. Chcete-li zajistit, aby byl paket doručen do virtuálního počítače (v tomto případě virtuálního zařízení), který není skutečným cílem paketu, je třeba povolit předávání IP adres pro virtuální zařízení.
* **Skupiny zabezpečení sítě (NSG).** Následující příklad nepoužívá skupiny nsg, ale můžete použít skupiny sítě nsg aplikované na podsítě nebo síťové karty v tomto řešení k dalšímu filtrování provozu v těchto podsítích a síťových rozhraních.

![Připojení IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

V tomto příkladu je předplatné, které obsahuje následující:

* 2 skupiny prostředků, které nejsou zobrazeny v diagramu. 
  * **ONPREMRG**. Obsahuje všechny prostředky potřebné k simulaci místní sítě.
  * **AZURERG**. Obsahuje všechny prostředky potřebné pro prostředí virtuální sítě Azure. 
* Virtuální síť s názvem **onpremvnet** slouží k napodobení místní datové centrum segmentované, jak je uvedeno níže.
  * **onpremsn1**. Podsíť obsahující virtuální počítač (VM) se systémem Ubuntu napodobovat místní server.
  * **onpremsn2**. Podsíť obsahující virtuální počítač se systémem Ubuntu, který napodobuje místní počítač používaný správcem.
* Existuje jeden virtuální zařízení brány firewall s názvem **OPFW** na **onpremvnet** slouží k údržbě tunelového propojení **azurevnet**.
* Virtuální síť s názvem **azurevnet** segmentované, jak je uvedeno níže.
  * **azsn1**. Externí podsíť brány firewall používaná výhradně pro externí bránu firewall. Veškerý internetový provoz bude procházet prostřednictvím této podsítě. Tato podsíť obsahuje pouze síť ovou kartu propojenou s externí bránou firewall.
  * **azsn2**. Podsíť front-endu hostující virtuální hovirtuální ho disponiál jako webový server, ke kterému bude přístup náležející z Internetu.
  * **azsn3**. Podsíť back-endu hostující virtuální server se serverem back-endaplikace, ke kterému bude přistupovat webový server front-endu.
  * **azsn4**. Podsíť pro správu používanou výhradně k poskytování přístupu ke správě všech virtuálních zařízení brány firewall. Tato podsíť obsahuje pouze síťovou kartu pro každé virtuální zařízení brány firewall používané v řešení.
  * **GatewaySubnet**. Podsíť hybridního připojení Azure, která je vyžadována pro bránu ExpressRoute a BRÁNA VPN, aby poskytovala připojení mezi virtuálními sítěmi Azure a dalšími sítěmi. 
* V síti **azurevnet** jsou 3 virtuální zařízení brány firewall. 
  * **AZF1**. Externí brána firewall vystavená veřejnému Internetu pomocí prostředku veřejné IP adresy v Azure. Musíte zajistit, že máte šablonu z marketplace nebo přímo od dodavatele zařízení, která zřazuje virtuální zařízení 3-NIC.
  * **AZF2**. Interní brána firewall používaná k řízení provozu mezi **azsn2** a **azsn3**. Toto je také virtuální zařízení 3-NIC.
  * **AZF3**. Brána firewall pro správu přístupná správcům z místního datového centra a připojená k podsíti pro správu, která slouží ke správě všech zařízení brány firewall. Šablony virtuálních zařízení 2-NIC najdete na webu Marketplace nebo si je můžete vyžádat přímo od dodavatele zařízení.

## <a name="user-defined-routing-udr"></a>Uživatelem definované směrování (UDR)
Každá podsíť v Azure může být propojena s tabulkou UDR, která slouží k definování způsobu směrování přenosů iniciovaných v této podsíti. Pokud nejsou definovány žádné UDR, Azure používá výchozí trasy k povolení přenosu z jedné podsítě do druhé. Chcete-li lépe porozumět udr, navštivte informace o tom, [jaké jsou uživatelem definované trasy a předávání IP](virtual-networks-udr-overview.md)adres .

Chcete-li zajistit komunikaci prostřednictvím správného zařízení brány firewall, na základě posledního požadavku výše, je třeba vytvořit následující tabulku tras obsahující UDR v **azurevnetu**.

### <a name="azgwudr"></a>azgwudr
V tomto scénáři pouze provoz, který proudí z místního do Azure se použije ke správě firewallů připojením k **AZF3**a že provoz musí projít vnitřní bránou firewall, **AZF2**. Proto je v **síti GatewaySubnet** nutná pouze jedna trasa, jak je znázorněno níže.

| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Umožňuje místnímu provozu dosáhnout brány firewall pro správu **AZF3.** |

### <a name="azsn2udr"></a>azsn2udr
| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Umožňuje přenos do podsítě back-end, která hostuje aplikační server prostřednictvím **AZF2.** |
| 0.0.0.0/0 |10.0.2.10 |Umožňuje směrování veškerého ostatního provozu přes **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Umožňuje přenos **na azsn2** toku z aplikačního serveru na webový server přes **AZF2** |

Je také potřeba vytvořit tabulky tras pro podsítě v **onpremvnet** napodobovat místní datové centrum.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Umožňuje provoz **na onpremsn2** přes **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Umožňuje provoz do zálohované podsítě v Azure prostřednictvím **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Umožňuje provoz **na onpremsn1** přes **OPFW** |

## <a name="ip-forwarding"></a>Předávání IP
Předávání UDR a IP jsou funkce, které můžete použít v kombinaci k povolení virtuálních zařízení, které mají být použity k řízení toku provozu ve virtuální síti Azure.  Virtuální zařízení není nic jiného než virtuální počítač, na kterém běží aplikace sloužící k určitému zpracování síťového provozu, jako je například brána firewall nebo zařízení NAT.

Tento virtuální počítač virtuálního zařízení musí být schopný přijímat příchozí provoz, který mu není adresovaný. Pokud chcete virtuálnímu počítači povolit přijímání dat adresovaných jiným cílům, je nutné, abyste mu povolili předávání IP. Toto je nastavení Azure, nikoli nastavení hostovaného operačního systému. Vaše virtuální zařízení stále potřebuje spustit nějaký typ aplikace pro zpracování příchozího provozu a odpovídajícím způsobem směrovat.

Další informace o předávání IP adres, navštivte [What are User Defined Routes and IP Forwarding](virtual-networks-udr-overview.md).

Představte si například, že máte ve virtuální síti Azure následující nastavení:

* Podsíť **onpremsn1** obsahuje virtuální počítač s názvem **onpremvm1**.
* Podsíť **onpremsn2** obsahuje virtuální počítač s názvem **onpremvm2**.
* Virtuální zařízení s názvem **OPFW** je připojen k **onpremsn1** a **onpremsn2**.
* Uživatelem definovaná trasa propojená s **onpremsn1** určuje, že veškerý provoz **na onpremsn2** musí být odeslán **opfw**.

V tomto okamžiku, pokud **onpremvm1** pokusí navázat spojení s **onpremvm2**, UDR bude použit a provoz bude odeslána **OPFW** jako další směrování. Mějte na paměti, že skutečný cíl paketu se nemění, stále říká, **že onpremvm2** je cíl. 

Bez ip forwarding u **OPFW**, logika virtuální sítě Azure bude vyřadit pakety, protože umožňuje pouze pakety, které mají být odeslány do virtuálního počítače, pokud IP adresa virtuálního počítače je cíl paketu.

S IP forwarding logiky virtuální sítě Azure předá pakety OPFW, aniž by se změnila původní cílová adresa. **OPFW** musí zpracovávat pakety a určit, co s nimi dělat.

Aby výše uvedený scénář fungoval, musíte povolit předávání IP adres na síťových listech pro **OPFW**, **AZF1**, **AZF2**a **AZF3,** které se používají pro směrování (všechny síťové karty s výjimkou těch, které jsou propojeny s podsítí pro správu). 

## <a name="firewall-rules"></a>Pravidla brány firewall
Jak je popsáno výše, předávání IP pouze zajišťuje, že pakety jsou odesílány do virtuálních zařízení. Váš spotřebič se stále musí rozhodnout, co s těmito pakety udělá. Ve výše uvedeném scénáři budete muset ve svých spotřebičích vytvořit následující pravidla:

### <a name="opfw"></a>OPFW
OPFW představuje místní zařízení obsahující následující pravidla:

* **Trasa**: Veškerý provoz na 10.0.0.0/16 **(azurevnet**) musí být odeslán tunelem **ONPREMAZURE**.
* **Zásady**: Povolit veškerý obousměrný provoz mezi **port2** a **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
AZF1 představuje virtuální zařízení Azure obsahující následující pravidla:

* **Zásady**: Povolit veškerý obousměrný provoz mezi **port1** a **port2**.

### <a name="azf2"></a>AZF2
AZF2 představuje virtuální zařízení Azure obsahující následující pravidla:

* **Trasa**: Veškerý provoz na 10.0.0.0/16 **(onpremvnet**) musí být odeslán na IP adresu brány Azure (tj. 10.0.0.1) přes **port1**.
* **Zásady**: Povolit veškerý obousměrný provoz mezi **port1** a **port2**.

## <a name="network-security-groups-nsgs"></a>Skupiny zabezpečení sítě (NSG)
V tomto scénáři nejsou používány nsg. Skupiny nsg však můžete použít pro každou podsíť a omezit příchozí a odchozí provoz. Můžete například použít následující pravidla skupiny nsg pro externí podsíť FW.

**Příchozí**

* Povolit veškerý přenos TCP z Internetu na port 80 na libovolném virtuálním počítači v podsíti.
* Odepřít všechny ostatní přenosy z Internetu.

**Odchozí**

* Odepřít veškerý provoz na Internetu.

## <a name="high-level-steps"></a>Kroky na vysoké úrovni
Chcete-li nasadit tento scénář, postupujte podle kroků na vysoké úrovni níže.

1. Přihlaste se ke svému předplatnému Azure.
2. Pokud chcete nasadit virtuální síť napodobovat místní sítě, zřídit prostředky, které jsou součástí **ONPREMRG**.
3. Zřídit prostředky, které jsou součástí **AZURERG**.
4. Zřídit tunel z **onpremvnet** do **azurevnet**.
5. Po zřízení všech prostředků se přihlaste k **onpremvm2** a ping 10.0.3.101 k testování připojení mezi **onpremsn2** a **azsn3**.

