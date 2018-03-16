---
title: "Řešení potíží s trasy – portál | Microsoft Docs"
description: "Informace o řešení potíží s trasy v modelu nasazení Azure Resource Manager pomocí portálu Azure."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: bdd8b6dc-02fb-4057-bb23-8289caa9de89
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: e0b835f4cbad9855bfb7ddccf2d9bf5b4bf88231
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2018
---
# <a name="troubleshoot-routes-using-the-azure-portal"></a>Řešení potíží s postupy pomocí portálu Azure
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-routes-troubleshoot-portal.md)
> * [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
>
>

Pokud dochází k problémům se síťovým připojením do nebo z vaší virtuální počítač Azure (VM), tras, které mohou ovlivňovat vaše datové přenosy virtuálních počítačů. Tento článek obsahuje přehled možností diagnostiky pro směrování, aby mohl pomoct řešit problémy s další.

Směrovací tabulky jsou spojeny s podsítěmi a platí na všech síťových rozhraní (NIC) v této podsíti. Každé rozhraní sítě můžete použít následující typy trasy:

* **Systémové trasy:** ve výchozím nastavení, má každá podsíť vytvořená ve virtuální síti Azure (VNet) systému směrovací tabulky, které umožňují místní provoz VNet, místní provoz prostřednictvím brány sítě VPN a přenosy z Internetu. Systémové trasy existovat také pro peered virtuální sítě.
* **Trasy protokolu BGP:** rozšíří na rozhraní sítě prostřednictvím ExpressRoute nebo VPN připojení site-to-site. Další informace o směrování protokolu BGP načtením [protokol BGP s bránami VPN](../vpn-gateway/vpn-gateway-bgp-overview.md) a [přehled ExpressRoute](../expressroute/expressroute-introduction.md) články.
* **Trasy definované uživatelem (UDR):** Pokud používáte virtuální zařízení sítě nebo jsou vynucené tunelování provozu do místní sítě prostřednictvím sítě site-to-site VPN, může mít trasy definované uživatelem (udr) přidružené tabulky tras podsítě. Pokud si nejste obeznámeni s udr, přečtěte si [trasy definované uživatelem](virtual-networks-udr-overview.md#user-defined) článku.

S různými tras, které lze použít k síťovému rozhraní může být obtížné určit, které agregační trasy jsou platné. Pomoc při řešení potíží s připojení k síti virtuálních počítačů, můžete zobrazit všechny efektivní trasy pro rozhraní sítě v modelu nasazení Azure Resource Manager.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>Řešení potíží s přenosy virtuálních počítačů pomocí efektivní trasy
Tento článek používá následující scénář jako příklad k ukazují, jak vyřešit efektivní trasy pro síťové rozhraní:

Virtuální počítač (*VM1*) připojené k virtuální síti (*VNet1*, předpona: 10.9.0.0/16) nepodaří připojit k VM(VM3) ve virtuální síti nově peered (*VNet3*, předpony 10.10.0.0/16). Neexistují žádné udr nebo BGP směruje u VM1 NIC1 síťové rozhraní připojené k virtuálnímu počítači, se použijí jenom systémové trasy.

Tento článek vysvětluje, jak a zjistěte příčinu selhání připojení pomocí funkce efektivní trasy v modelu nasazení správou prostředků Azure.
Tento příklad používá jenom systémové trasy, stejný postup slouží k určení selhání příchozí a odchozí připojení přes jakýkoli typ trasy.

> [!NOTE]
> Pokud virtuální počítač má více než jeden síťový adaptér připojený, zkontrolujte efektivní trasy pro jednotlivé síťové adaptéry diagnostikovat problémy s připojením k síti do a z virtuálního počítače.
>
>

### <a name="view-effective-routes-for-a-virtual-machine"></a>Zobrazit účinné postupy pro virtuální počítač
Agregace tras, které se použijí k virtuálnímu počítači najdete proveďte následující kroky:

1. Přihlášení k portálu Azure na https://portal.azure.com. Musí mít váš účet přiřazenou *Microsoft.Network/networkInterfaces/effectiveRouteTable/action* operace pro síťové rozhraní. Zjistěte, jak přiřadit operations účty, najdete v tématu [vytvářet vlastní role pro řízení přístupu](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. Klikněte na tlačítko **všechny služby**, pak klikněte na tlačítko **virtuální počítače** v seznamu, který se zobrazí.
3. Vyberte virtuální počítač ze seznamu, který se zobrazí řešení a zobrazí se okno virtuálních počítačů s možnostmi.
4. Klikněte na tlačítko **Diagnostikujte & řešení problémů** a pak vyberte častých problémů. V tomto příkladu **nelze se připojit k virtuálnímu počítači s Windows** je vybrána.

    ![](./media/virtual-network-routes-troubleshoot-portal/image1.png)
5. Kroky se zobrazí pod problému, jak je znázorněno na následujícím obrázku:

    ![](./media/virtual-network-routes-troubleshoot-portal/image2.png)

    Klikněte na tlačítko *efektivní trasy* v seznamu doporučené kroky.
6. **Efektivní trasy** okno se zobrazí, jak je znázorněno na následujícím obrázku:

    ![](./media/virtual-network-routes-troubleshoot-portal/image3.png)

    Pokud virtuální počítač má pouze jeden síťový adaptér, je standardně vybraná. Pokud máte více než jeden síťový adaptér, vyberte síťový adaptér, pro který chcete zobrazit efektivní trasy.

   > [!NOTE]
   > Pokud síťový adaptér přidružený virtuální počítač není ve spuštěném stavu, efektivní směrování se nezobrazí. Na portálu se zobrazí pouze prvních 200 efektivní trasy. Úplný seznam, klikněte na tlačítko **Stáhnout**. Dále můžete filtrovat podle výsledků soubor stažený .csv.
   >
   >

    Všimněte si následující výstup:

   * **Zdroj**: Určuje typ směrování. Systémové trasy se zobrazují jako *výchozí*, udr se zobrazují jako *uživatele* a brány trasy (statické nebo BGP) se zobrazují jako *Brána VPN*.
   * **Stav**: označuje stav efektivní trasy. Možné hodnoty jsou *Active* nebo *neplatný*.
   * **Adres**: Určuje předpona adresy efektivní trasy v notaci CIDR.
   * **nextHopType**: označuje dalšího přechodu pro danou trasu. Možné hodnoty jsou *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*, nebo *Null*. Hodnota *Null* pro **nextHopType** UDR může znamenat neplatná. Například pokud **nextHopType** je *VirtualAppliance* a virtuální zařízení sítě virtuálního počítače není zřízený nebo spuštěném stavu. Pokud **nextHopType** je *Brána VPN* a neexistuje žádná brána zřízený/běží v dané virtuální síti, může zneplatní trasy.
7. Neexistuje žádná trasa uvedené *WestUS VNET3* virtuální síť (předponu 10.10.0.0/16) z *WestUS VNet1* (předpony 10.9.0.0/16) na obrázku v předchozím kroku. Na následujícím obrázku je odkaz partnerského vztahu v *odpojeno* stavu:

    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    Obousměrná odkaz pro partnerského vztahu se přeruší, která vysvětluje, proč VM1 se nemohl připojit k VM3 v *WestUS VNet3* virtuální sítě.
8. Následující obrázek znázorňuje trasy po vytvoření partnerského vztahu propojení obousměrný:

    ![](./media/virtual-network-routes-troubleshoot-portal/image5.png)

Více řešení potíží scénáře pro vyhodnocení vynucené tunelování a směrování, přečtěte si [aspekty](virtual-network-routes-troubleshoot-portal.md#considerations) tohoto článku.

### <a name="view-effective-routes-for-a-network-interface"></a>Zobrazit účinné postupy pro rozhraní sítě
Pokud pro konkrétní síťové rozhraní (NIC) je ovlivněn tok přenosu sítě, můžete zobrazit úplný seznam efektivní trasy přímo na síťový adaptér. Pokud chcete zobrazit agregační tras, které se použijí pro síťový adaptér, proveďte následující kroky:

1. Přihlášení k portálu Azure na https://portal.azure.com.
2. Klikněte na tlačítko **všechny služby**, pak klikněte na tlačítko **síťová rozhraní**
3. Pro název síťového adaptéru v seznamu Hledat, nebo ho vyberte v seznamu, který se zobrazí. V tomto příkladu **VM1 NIC1** je vybrána.
4. Vyberte **efektivní trasy** v **síťové rozhraní** okno, jak je znázorněno na následujícím obrázku:

       ![](./media/virtual-network-routes-troubleshoot-portal/image6.png)

    **Oboru** výchozí hodnoty pro vybrané síťové rozhraní.

      ![](./media/virtual-network-routes-troubleshoot-portal/image7.png)

### <a name="view-effective-routes-for-a-route-table"></a>Zobrazení efektivní trasy pro směrovací tabulku
Při úpravě trasy definované uživatelem (udr) ve směrovací tabulce, můžete zkontrolovat dopad trasy přidávané na konkrétním virtuálním počítači. Směrovací tabulka se dá přidružit libovolný počet podsítí. Teď si můžete zobrazit všechny efektivní trasy pro všechny síťové adaptéry, které daný směrovací tabulka se aplikují, bez nutnosti můžete přepnout kontext z okna tabulky dané trasy.

V tomto příkladu UDR (*UDRoute*) je zadána ve směrovací tabulce (*UDRouteTable*). Tato trasa odesílá všechny internetové přenosy od *Subnet1* v *WestUS VNet1* virtuální sítě přes virtuální síťové zařízení (hodnocení chyb zabezpečení), v *Podsíť2* z stejnou virtuální síť. Trasy, která je znázorněno na následujícím obrázku:

![](./media/virtual-network-routes-troubleshoot-portal/image8.png)

Pokud chcete zobrazit agregační trasy pro směrovací tabulku, proveďte následující kroky:

1. Přihlášení k portálu Azure na https://portal.azure.com.
2. Klikněte na tlačítko **všechny služby**, pak klikněte na tlačítko **směrovacích tabulek**
3. Vyhledejte v seznamu směrovací tabulka, které chcete zobrazit agregační trasy pro a vyberte ho. V tomto příkladu **UDRouteTable** je vybrána. Zobrazí se okno pro vybraný směrovací tabulka, jak je znázorněno na následujícím obrázku:

    ![](./media/virtual-network-routes-troubleshoot-portal/image9.png)
4. Vyberte **efektivní trasy** v **směrovací tabulku** okno. **Oboru** nastavena do směrovací tabulky, které jste vybrali.
5. Směrovací tabulka je použít pro více podsítí. Vyberte **podsíť** chcete zkontrolovat ze seznamu. V tomto příkladu **Subnet1** je vybrána.
6. Vyberte **síťové rozhraní**. Jsou uvedeny všechny síťové adaptéry připojené k vybrané podsíti. V tomto příkladu **VM1 NIC1** je vybrána.

    ![](./media/virtual-network-routes-troubleshoot-portal/image10.png)

   > [!NOTE]
   > Pokud síťový adaptér není přidružen spuštěný virtuální počítač, jsou zobrazeny žádné efektivní trasy.
   >
   >

## <a name="considerations"></a>Požadavky
Pokud kontrola seznam tras vrátí mějte na paměti několik akcí:

* Směrování je založena na nejdelší shody předpony (LPM) mezi udr, směrování protokolu BGP a systému. Pokud existuje víc tras se stejnou shodou LPM, pak trasa se vybere na základě původu v následujícím pořadí:

  * Trasa definovaná uživatelem
  * Trasa protokolu BGP
  * Trasy systému (výchozí)

    S efektivní směrování uvidí jenom účinné postupy, které jsou založené na všechny dostupné trasy shodou LPM. Ukazuje, jak se ve skutečnosti vyhodnocují trasy pro daný síťový adaptér, díky tomu mnohem snazší řešení potíží s konkrétní trasy, které mohou ovlivňovat připojení z virtuálního počítače.
* Pokud máte udr a odesílání provozu pro virtuální síťové zařízení (hodnocení chyb zabezpečení), s *VirtualAppliance* jako **nextHopType**, ujistěte se, zda je na hodnocení chyb zabezpečení, který provoz přijímá povolena předávání IP nebo dojde ke ztrátě paketů.
* Pokud vynuceném tunelovém propojení je povoleno, veškerý odchozí internetový provoz se směruje na místní. Připojení RDP/SSH z Internetu do virtuální počítač nemusí fungovat s tímto nastavením, v závislosti na tom, jak místní zpracovává tento provoz.
  Můžete povolit vynucené tunelování:
  * Pokud připojení site-to-site VPN pomocí nastavení trasy definované uživatelem (UDR) s nextHopType jako brány sítě VPN
  * Pokud je výchozí trasy inzerované přes protokol BGP
* Pro virtuální síť partnerského vztahu provoz fungovala správně, systémová trasa s **nextHopType** *VNetPeering* peered VNet předponu rozsahu, musí existovat. Pokud tyto trasy neexistuje a partnerského vztahu propojení virtuální sítě vypadá v pořádku:
  * Počkejte několik sekund a opakovat, pokud se jedná o nově vytvořeným partnerského vztahu odkaz. Někdy trvá déle šířil trasy na všech síťových rozhraní v podsíti.
  * Skupina zabezpečení sítě (NSG) pravidla, které mohou ovlivňovat tok provozu. Další informace najdete v tématu [odstraňování skupin zabezpečení sítě](virtual-network-nsg-troubleshoot-portal.md) článku.
