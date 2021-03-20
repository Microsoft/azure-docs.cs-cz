---
title: Hybridní připojení se 2 úrovněmi aplikace | Microsoft Docs
description: Naučte se nasazovat virtuální zařízení a UDR a vytvořit vícevrstvé prostředí aplikací v Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: carmonm
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: kumud
ms.openlocfilehash: 1d2dde4e77a39b114f721cd6d2be250141984e7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86231705"
---
# <a name="virtual-appliance-scenario"></a>Scénář virtuálního zařízení
Běžným scénářem v rámci většího zákazníka Azure je nutnost poskytovat dvě vrstvené aplikace, které jsou zpřístupněné pro Internet, a současně umožnit přístup k back-vrstvám z místního datacentra. Tento dokument vás provede scénářem, který využívá trasy definované uživatelem (UDR), VPN Gateway a síťová virtuální zařízení k nasazení dvou vícevrstvých prostředí, která splňují následující požadavky:

* Webová aplikace musí být přístupná jenom z veřejného Internetu.
* Webový server, který je hostitelem aplikace, musí být schopný získat přístup k back-endovému aplikačnímu serveru.
* Veškerý provoz z Internetu do webové aplikace musí projít virtuálním zařízením brány firewall. Toto virtuální zařízení se bude používat jenom pro internetový provoz.
* Veškerý provoz směřující do aplikačního serveru musí projít virtuálním zařízením brány firewall. Toto virtuální zařízení se bude používat pro přístup k back-end back-endu serveru a přístup k němu z místní sítě prostřednictvím VPN Gateway.
* Správci musí být schopni spravovat virtuální zařízení brány firewall ze svých místních počítačů pomocí třetí virtuální brány firewall používané výhradně pro účely správy.

Toto je standardní hraniční síť (označovaná také jako DMZ) s DMZ a chráněnou sítí. Tento scénář je možné vytvořit v Azure pomocí skupin zabezpečení sítě, virtuálními zařízeními brány firewall nebo kombinací obou. V následující tabulce jsou uvedeny některé z specialistů a nevýhody mezi virtuálními zařízeními skupin zabezpečení sítě a firewallem.

|  | Výhody | Nevýhody |
| --- | --- | --- |
| **NSG** |Žádné náklady. <br/>Integrováno do Azure RBAC. <br/>Pravidla lze vytvořit v Azure Resource Manager šablonách. |Složitost se může ve větších prostředích lišit. |
| **Brána firewall** |Plné řízení roviny dat <br/>Centrální správa prostřednictvím konzoly brány firewall. |Náklady na zařízení brány firewall. <br/>Není integrováno do Azure RBAC. |

Řešení níže používá virtuální zařízení brány firewall k implementaci scénáře sítě/Protected hraniční sítě (DMZ).

## <a name="considerations"></a>Požadavky
Prostředí můžete nasadit v Azure podrobněji pomocí různých funkcí dostupných v současnosti, a to následujícím způsobem.

* **Virtuální síť (VNet)** . Virtuální síť Azure funguje podobně jako místní síť a dá se rozdělit do jedné nebo víc podsítí, aby poskytovala izolaci provozu a oddělení obav.
* **Virtuální zařízení**. Několik partnerů poskytuje virtuální zařízení v Azure Marketplace, která se dají použít pro tři výše popsané brány firewall. 
* **Trasy definované uživatelem (udr)**. Směrovací tabulky můžou obsahovat udr, které používají sítě Azure k řízení toku paketů v rámci virtuální sítě. Tyto směrovací tabulky lze použít pro podsítě. Jednou z nejnovějších funkcí v Azure je možnost použít pro GatewaySubnet směrovací tabulku, která poskytuje schopnost předávat veškerý provoz přicházející do virtuální sítě Azure z hybridního připojení k virtuálnímu zařízení.
* **Předávání IP**. Ve výchozím nastavení předávají pakety síťové služby Azure do síťových karet (nic) pouze v případě, že cílová IP adresa paketu odpovídá IP adrese síťového rozhraní. Proto pokud UDR definuje, že se paket musí odeslat do daného virtuálního zařízení, síťový modul Azure tento paket vynechá. Aby se zajistilo doručení paketu do virtuálního počítače (v tomto případě virtuální zařízení), které není skutečným cílem pro daný paket, musíte povolit předávání IP pro virtuální zařízení.
* **Skupiny zabezpečení sítě (skupin zabezpečení sítě)**. Následující příklad nepoužívá skupin zabezpečení sítě, ale můžete použít skupin zabezpečení sítě, které se použijí pro podsítě nebo síťové karty v tomto řešení k dalšímu filtrování provozu v a z těchto podsítí a síťových adaptérů.

![Připojení protokolem IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

V tomto příkladu je k dispozici předplatné, které obsahuje následující:

* 2 skupiny prostředků, které nejsou zobrazené v diagramu. 
  * **ONPREMRG**. Obsahuje všechny prostředky nezbytné pro simulaci místní sítě.
  * **AZURERG**. Obsahuje všechny prostředky nezbytné pro prostředí Azure Virtual Network. 
* Virtuální síť s názvem **onpremvnet** , která se používá k napodobení místního datového centra, jak je uvedeno níže.
  * **onpremsn1**. Podsíť obsahující virtuální počítač se systémem Ubuntu, aby napodoboval místní server.
  * **onpremsn2**. Podsíť obsahující virtuální počítač se systémem Ubuntu pro napodobení místního počítače, který používá správce.
* K dispozici je jedno virtuální zařízení brány firewall s názvem **OPFW** v **onpremvnet** , které slouží k údržbě tunelu na **azurevnet**.
* Virtuální síť s názvem **azurevnet** segmentovaná, jak je uvedeno níže.
  * **azsn1**. Externí podsíť brány firewall, která se používá výhradně pro externí bránu firewall. Veškerý internetový provoz bude přicházet přes tuto podsíť. Tato podsíť obsahuje jenom síťovou kartu propojenou s externí bránou firewall.
  * **azsn2**. Front-end, který hostuje virtuální počítač běžící jako webový server, ke kterému se bude přicházet z Internetu.
  * **azsn3**. Podsíť back-endu hostující virtuální počítač, na kterém běží aplikační server back-end, ke kterému se bude přicházet prostřednictvím webového serveru front-end.
  * **azsn4**. Podsíť pro správu, která se používá výhradně k poskytnutí přístupu pro správu ke všem virtuálním zařízením brány firewall. Tato podsíť obsahuje jenom síťovou kartu pro každé virtuální zařízení brány firewall použité v řešení.
  * **Podsíť brány:** Podsíť hybridního připojení Azure, která se vyžaduje pro ExpressRoute a VPN Gateway, aby poskytovala připojení mezi virtuální sítě Azure a dalšími sítěmi. 
* V **azurevnet** síti se nachází 3 virtuální zařízení brány firewall. 
  * **AZF1**. Externí brána firewall vystavená veřejnému Internetu pomocí prostředku veřejné IP adresy v Azure. Musíte mít jistotu, že máte šablonu z webu Marketplace nebo přímo od dodavatele zařízení, která zřídí virtuální zařízení se třemi síťovými kartami.
  * **AZF2**. Interní brána firewall používaná k řízení provozu mezi **azsn2** a **azsn3**. Toto je také virtuální zařízení se třemi kartami.
  * **AZF3**. Brána firewall pro správu přístupná správcům z místního datového centra a připojená k podsíti pro správu, která se používá ke správě všech zařízení brány firewall. Na webu Marketplace můžete najít šablony virtuálních zařízení se dvěma síťovými kartami nebo si ji vyžádat přímo od dodavatele zařízení.

## <a name="user-defined-routing-udr"></a>Směrování definované uživatelem (UDR)
Každá podsíť v Azure se dá propojit s tabulkou UDR, která slouží k definování způsobu směrování provozu v této podsíti. Pokud nejsou definované žádné udr, Azure použije výchozí trasy, které umožní tok provozu z jedné podsítě do jiné. Abyste lépe pochopili udr, přečtěte si téma [co jsou uživatelsky definované trasy a předávání IP](virtual-networks-udr-overview.md).

Aby bylo zajištěno, že komunikace probíhá přes správné zařízení brány firewall na základě posledního požadavku výše, je třeba vytvořit následující směrovací tabulku obsahující udr v **azurevnet**.

### <a name="azgwudr"></a>azgwudr
V tomto scénáři se k řízení bran firewall pomocí připojení k **AZF3** použije jediný přenos z místního prostředí do Azure a tento provoz musí projít interní bránou firewall **AZF2**. Proto je v **GatewaySubnet** nutná pouze jedna trasa, jak je znázorněno níže.

| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Umožňuje místní provoz pro přístup k bráně firewall pro správu **AZF3** . |

### <a name="azsn2udr"></a>azsn2udr
| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Umožňuje provoz do podsítě back-endu hostující aplikační server prostřednictvím **AZF2** . |
| 0.0.0.0/0 |10.0.2.10 |Povolí směrování všech ostatních přenosů přes **AZF1** . |

### <a name="azsn3udr"></a>azsn3udr
| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Umožňuje přenosu dat do **azsn2** z aplikačního serveru do webserveru prostřednictvím **AZF2** . |

Pro podsítě v **onpremvnet** je také potřeba vytvořit směrovací tabulky, které napodobují místní datacentrum.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Povoluje přenos do **onpremsn2** prostřednictvím **OPFW** . |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Cíl | Další směrování | Vysvětlení |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Povoluje provoz do back-mailové podsítě v Azure prostřednictvím **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Povoluje přenos do **onpremsn1** prostřednictvím **OPFW** . |

## <a name="ip-forwarding"></a>Předávání IP
UDR a předávání IP jsou funkce, které můžete použít v kombinaci k umožnění použití virtuálních zařízení k řízení toku provozu ve virtuální síti Azure.  Virtuální zařízení není nic jiného než virtuální počítač, na kterém běží aplikace sloužící k určitému zpracování síťového provozu, jako je například brána firewall nebo zařízení NAT.

Tento virtuální počítač virtuálního zařízení musí být schopný přijímat příchozí provoz, který mu není adresovaný. Pokud chcete virtuálnímu počítači povolit přijímání dat adresovaných jiným cílům, je nutné, abyste mu povolili předávání IP. Toto je nastavení Azure, nikoli nastavení hostovaného operačního systému. Virtuální zařízení ještě potřebuje spustit nějaký typ aplikace pro zpracování příchozího provozu a správně ho směrovat.

Další informace o předávání IP adres najdete v [informacích o trasách definovaných uživatelem a předávání IP](virtual-networks-udr-overview.md).

Představte si například, že máte ve virtuální síti Azure následující nastavení:

* **Onpremsn1** podsítě obsahuje virtuální počítač s názvem **onpremvm1**.
* **Onpremsn2** podsítě obsahuje virtuální počítač s názvem **onpremvm2**.
* Virtuální zařízení s názvem **OPFW** je připojené k **onpremsn1** a **onpremsn2**.
* Uživatelem definovaná trasa propojená s **onpremsn1** určuje, že všechny přenosy do **onpremsn2** musí být odesílány do **OPFW**.

V tomto okamžiku se v případě, že se **onpremvm1** pokusí navázat spojení s **onpremvm2**, se použije udr a provoz se pošle **OPFW** jako další segment směrování. Mějte na paměti, že skutečný cíl paketu se nemění, stále říká, že **onpremvm2** je cílem. 

Bez povoleného předávání IP pro **OPFW** vynechá logika virtuálních sítí Azure pakety, protože povoluje odesílání paketů do virtuálního počítače jenom v případě, že je IP adresa virtuálního počítače cílová pro daný paket.

Díky předávání IP je logika virtuální sítě Azure předávat pakety do OPFW beze změny původní cílové adresy. **OPFW** musí pakety zpracovávat a určit, co s nimi dělat.

Aby výše uvedený scénář fungoval, musíte povolit předávání IP na síťových kartách pro **OPFW**, **AZF1**, **AZF2** a **AZF3** , které se používají pro směrování (všechny síťové karty kromě těch, které jsou propojené s podsítí pro správu). 

## <a name="firewall-rules"></a>Pravidla brány firewall
Jak je popsáno výše, předávání IP zajišťuje pouze odeslání paketů do virtuálních zařízení. Vaše zařízení pořád potřebuje rozhodnout, co s těmito pakety udělat. Ve výše uvedeném scénáři budete muset ve svých zařízeních vytvořit tato pravidla:

### <a name="opfw"></a>OPFW
OPFW představuje místní zařízení, které obsahuje následující pravidla:

* **Trasa**: veškerý provoz do 10.0.0.0/16 (**azurevnet**) se musí odesílat prostřednictvím tunelového **ONPREMAZURE**.
* **Zásada**: povolí veškerý obousměrný provoz mezi **PORT2** a **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
AZF1 představuje virtuální zařízení Azure obsahující následující pravidla:

* **Zásada**: povolí veškerý obousměrný provoz mezi **PORT1** a **PORT2**.

### <a name="azf2"></a>AZF2
AZF2 představuje virtuální zařízení Azure obsahující následující pravidla:

* **Trasa**: veškerý provoz do 10.0.0.0/16 (**onpremvnet**) musí být odeslán do IP adresy brány Azure (tj. 10.0.0.1) prostřednictvím **PORT1**.
* **Zásada**: povolí veškerý obousměrný provoz mezi **PORT1** a **PORT2**.

## <a name="network-security-groups-nsgs"></a>Skupiny zabezpečení sítě (skupin zabezpečení sítě)
V tomto scénáři se skupin zabezpečení sítě nepoužívají. Můžete ale použít skupin zabezpečení sítě pro každou podsíť a omezit tak příchozí a odchozí provoz. Například můžete použít následující pravidla NSG pro externí podsíť FW.

**Příchozí**

* Povolte veškerý provoz TCP z Internetu na port 80 na jakémkoli virtuálním počítači v podsíti.
* Zakažte všechny ostatní přenosy z Internetu.

**Odesílaná**

* Odepřít veškerý provoz do Internetu.

## <a name="high-level-steps"></a>Kroky vysoké úrovně
Pokud chcete tento scénář nasadit, postupujte podle následujících kroků na nejvyšší úrovni.

1. Přihlaste se k předplatnému Azure.
2. Pokud chcete nasadit virtuální síť, která bude napodobovat místní síť, zřiďte prostředky, které jsou součástí **ONPREMRG**.
3. Zřídí prostředky, které jsou součástí **AZURERG**.
4. Zřízení tunelu z **onpremvnet** na **azurevnet**.
5. Po zřízení všech prostředků se přihlaste k **onpremvm2** a otestujte 10.0.3.101 a otestujte připojení mezi **onpremsn2** a **azsn3**.

