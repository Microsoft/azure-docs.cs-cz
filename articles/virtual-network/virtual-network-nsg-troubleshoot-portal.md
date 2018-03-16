---
title: "Řešení potíží s skupin zabezpečení sítě - portál | Microsoft Docs"
description: "Informace o řešení potíží s skupin zabezpečení sítě v modelu nasazení Azure Resource Manager pomocí portálu Azure."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 5998aa0e0b281ead859c24d792c3e175e3cca899
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2018
---
# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Řešení potíží s skupin zabezpečení sítě pomocí portálu Azure
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

Pokud jste nakonfigurovali skupiny zabezpečení sítě (Nsg) ve virtuálním počítači (VM) a dochází problémy s připojením k virtuální počítač, tento článek obsahuje přehled možností diagnostiky pro skupiny Nsg k další řešení.

Skupiny Nsg umožní určit typy provozu s tokem do aplikace a z virtuálních počítačů (VM). Skupiny Nsg můžete použít na podsítě ve virtuální síti Azure (VNet), síťová rozhraní (NIC) nebo obojí. Efektivní pravidla použít na síťový adaptér jsou agregace pravidla, které existují v skupin Nsg použije k síťovému adaptéru a podsíť, ke kterému je připojen k. Pravidla v rámci těchto skupin Nsg můžete někdy vzájemném konfliktu a mít vliv na připojení k síti Virtuálního počítače.  

Můžete zobrazit všechna pravidla efektivní zabezpečení od vaší skupiny Nsg použije na síťové adaptéry Virtuálního počítače. Tento článek ukazuje, jak vyřešit problémy s připojením k virtuální počítač pomocí těchto pravidel v modelu nasazení Azure Resource Manager. Pokud si nejste obeznámeni s virtuální sítí a NSG koncepty, přečtěte si [virtuální síť](virtual-networks-overview.md) a [skupin zabezpečení sítě](virtual-networks-nsg.md) přehled články.

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Řešení potíží s přenosy virtuálních počítačů pomocí efektivní pravidla zabezpečení
Scénář, který následuje je příkladem častých problémů připojení:

Virtuální počítač s názvem *VM1* je součástí podsíť s názvem *Subnet1* v rámci virtuální sítě s názvem *WestUS VNet1*. Pokus o připojení k virtuálnímu počítači pomocí protokolu RDP přes TCP port 3389 selže. Skupiny Nsg jsou použity na síťový adaptér *VM1 NIC1* a podsíť *Subnet1*. Provoz TCP port 3389 je povolený v této skupině související se síťovým rozhraním *VM1 NIC1*, ale příkaz ping TCP a VM1 je port 3389 selže.

Při tomto příkladu používá TCP port 3389, následující postup slouží k určení selhání příchozí a odchozí připojení přes libovolný port.

### <a name="vm"></a>Zobrazení pravidla efektivní zabezpečení pro virtuální počítač
Pomocí následujících kroků pro řešení potíží s skupiny Nsg pro virtuální počítač:

Úplný seznam pravidel efektivní zabezpečení můžete zobrazit na síťový adaptér z virtuálního počítače sám sebe. Můžete také přidat, upravit a odstranit pravidla NSG síťových Adaptérů a podsíť z okna efektivní pravidla, pokud máte oprávnění k provedení těchto operací.

1. Přihlášení k portálu Azure v https://portal.azure.com s účet Azure. Musí mít váš účet přiřazenou *Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action* operace pro síťové rozhraní. Zjistěte, jak přiřadit operations účty, najdete v tématu [vytvářet vlastní role pro řízení přístupu](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. Klikněte na tlačítko **všechny služby**, pak klikněte na tlačítko **virtuální počítače** v seznamu, který se zobrazí.
3. Vyberte virtuální počítač ze seznamu, který se zobrazí řešení a zobrazí se okno virtuálních počítačů s možnostmi.
4. Klikněte na tlačítko **Diagnostikujte & řešení problémů** a pak vyberte častých problémů. V tomto příkladu **nelze se připojit k virtuálnímu počítači s Windows** je vybrána. 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)
5. Kroky se zobrazí pod problému, jak je znázorněno na následujícím obrázku: 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)
   
    Klikněte na tlačítko *pravidel skupiny zabezpečení efektivní* v seznamu doporučené kroky.
6. **Získat efektivní zabezpečení pravidla** okno se zobrazí, jak je znázorněno na následujícím obrázku:
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)
   
    Všimněte si následující části obrázku:
   
   * **Obor:** nastavena na *VM1*, virtuální počítač vybrali v kroku 3.
   * **Síťové rozhraní:** *VM1 NIC1* je vybrána. Virtuální počítač může mít více síťových rozhraní (NIC). Každý síťový adaptér může mít jedinečnou efektivní pravidla. Při řešení potíží, budete potřebovat k zobrazení pravidel efektivní zabezpečení pro každý síťový adaptér.
   * **Přidružené skupiny Nsg:** skupiny Nsg můžete použít pro síťový adaptér i síťový adaptér je připojený k podsíti. Na obrázku skupinu NSG použilo síťový adaptér a, který je připojený k podsíti. Kliknutím na názvy NSG přímo upravit pravidla v skupin Nsg.
   * **Karta VM1 nsg:** seznam pravidel, které jsou zobrazené na obrázku je NSG použije na síťový adaptér. Několik výchozích pravidel vytvářejí Azure vždy, když je vytvořena skupina NSG. Nelze odstranit výchozí pravidla, ale je možné přepsat pravidla s vyšší prioritou. Další informace o výchozích pravidel, přečtěte si [NSG přehled](virtual-networks-nsg.md#default-rules) článku.
   * **CÍLOVÝ sloupec:** některé pravidla mají text ve sloupci jiné mají předpony adres. Text je název výchozí značky použijí pravidlo zabezpečení, jakmile byla vytvořena. Zadané značky jsou poskytované systémem identifikátory, které představují více předpony. Vybrat pravidlo s značkou, jako například *AllowInternetOutBound*, uvádí předpony v **předpony adres** okno.
   * **Stáhnout:** seznamu pravidel příslušného může trvat dlouho. Soubor .csv pravidel pro offline analýzu můžete stáhnout kliknutím **Stáhnout** a ukládání souboru.
   * **AllowRDP** příchozí pravidlo: Toto pravidlo umožňuje připojení RDP k virtuálnímu počítači.
7. Klikněte **Subnet1 NSG** zobrazíte efektivní pravidla z NSG použije na podsíť, jak je znázorněno na následujícím obrázku: 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)
   
    Upozornění *denyRDP* **příchozí** pravidlo. Příchozí pravidla použít na podsítě se vyhodnocují dříve, než pravidla použít na síťové rozhraní. Vzhledem k tomu, že pravidlo Odepřít se použije na podsíť, požadavek na připojení k TCP 3389 nezdaří, protože nikdy vyhodnotí pravidla povolit v síťový adaptér. 
   
    *DenyRDP* pravidlo je důvod, proč se nedaří připojení RDP. Jeho odebráním by měla potíže vyřešit.
   
   > [!NOTE]
   > Pokud síťový adaptér přidružený virtuální počítač není ve spuštěném stavu, nebo skupin Nsg nebyly použity na síťový adaptér nebo podsíť, se nezobrazí žádná pravidla.
   > 
   > 
8. Chcete-li upravit pravidla NSG, klikněte na tlačítko *Subnet1 NSG* v **přidružené skupiny Nsg** části.
   Tím se otevře **Subnet1 NSG** okno. Pravidla lze přímo upravit kliknutím na **příchozí pravidla zabezpečení**.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)
9. Po odebrání *denyRDP* příchozí pravidlo v **Subnet1 NSG** a přidání *allowRDP* pravidla, účinného pravidla seznamu vypadá jako na následujícím obrázku:
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)
   
    Potvrďte, že otevřete otevřením připojení ke vzdálené ploše k virtuálnímu počítači nebo pomocí nástroje Pspingu TCP port 3389. Další informace o Pspingu načtením [stránky pro stažení Pspingu](https://technet.microsoft.com/sysinternals/psping.aspx).

### <a name="nic"></a>Zobrazení pravidla efektivní zabezpečení pro rozhraní sítě
Pokud vaše tok přenosů dat virtuálního počítače je ovlivněn pro konkrétní síťové karty, můžete zobrazit úplný seznam efektivní pravidla pro síťový adaptér z daného kontextu rozhraní sítě pomocí následujících kroků:

1. Přihlášení k portálu Azure na https://portal.azure.com.
2. Klikněte na tlačítko **všechny služby**, pak klikněte na tlačítko **síťových rozhraní** v seznamu, který se zobrazí.
3. Vyberte síťové rozhraní. Na následujícím obrázku s názvem síťový adaptér *VM1 NIC1* je vybrána.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)
   
    Všimněte si, že **oboru** je nastaven na síťové rozhraní vybrána. Další informace o další informace zobrazené, přečtěte si kroku 6 **odstraňování skupin Nsg pro virtuální počítač** tohoto článku.
   
   > [!NOTE]
   > Pokud skupina NSG je odebrán z rozhraní sítě, podsítě NSG je stále platná na daný síťový adaptér. V takovém případě by výstup zobrazit pouze pravidla z podsítě NSG. Pravidla se zobrazí, pouze pokud síťový adaptér je připojen k virtuálnímu počítači.
   > 
   > 
4. Můžete přímo upravit pravidla pro skupiny Nsg přidružená síťový adaptér a podsítě. Další informace, jak číst kroku 8 tohoto **zobrazit pravidla efektivní zabezpečení pro virtuální počítač** tohoto článku.

## <a name="nsg"></a>Zobrazení pravidla efektivní zabezpečení pro skupinu zabezpečení sítě (NSG)
Při úpravě pravidla NSG, můžete zkontrolovat dopad pravidla přidávané na konkrétním virtuálním počítači. Můžete zobrazit úplný seznam pravidel efektivní zabezpečení pro všechny síťové adaptéry, které daný NSG se použije, aniž by bylo nutné přepnout kontext z okna dané skupiny NSG. Chcete-li vyřešit efektivní pravidel ve skupině NGS, proveďte následující kroky:

1. Přihlášení k portálu Azure na https://portal.azure.com.
2. Klikněte na tlačítko **všechny služby**, pak klikněte na tlačítko **skupin zabezpečení sítě** v seznamu, který se zobrazí.
3. Vyberte skupinu NSG. Na následujícím obrázku nebyla vybrána skupina NSG s názvem VM1 nsg.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)
   
    Všimněte si následující části na předchozím obrázku:
   
   * **Obor:** nastavena na NSG vybrané.
   * **Virtuální počítač:** při NSG se použije na podsíť, bude použita na všech síťových rozhraní, které jsou připojené ke všem virtuálním počítačům, které jsou připojené k podsíti. Tento seznam obsahuje tato skupina NSG se použije pro všechny virtuální počítače. Žádné virtuální počítače můžete vybrat ze seznamu.
     
     > [!NOTE]
     > Pokud skupina NSG se použije k jenom podsíť prázdný, virtuální počítače nebude v seznamu uveden. Pokud skupina NSG se použije na síťový adaptér, který není přidružený virtuální počítač, nebude uvedené také tyto síťové adaptéry. 
     > 
     > 
   * **Síťové rozhraní:** virtuální počítač může mít více síťových rozhraní. Můžete vybrat rozhraní sítě připojen k vybrané virtuálnímu počítači.
   * **AssociatedNSGs:** kdykoli, síťový adaptér může mít až dvě efektivní Nsg, jeden použity na síťový adaptér a jiné podsíti. I když oboru je vybrán jako VM1-nsg, pokud má síťový adaptér podsíť efektivní NSG, výstup bude zobrazovat i skupiny Nsg.
4. Můžete přímo upravit pravidla pro skupiny zabezpečení sítě spojená s síťový adaptér nebo podsíť. Další informace, jak číst kroku 8 tohoto **zobrazit pravidla efektivní zabezpečení pro virtuální počítač** tohoto článku.

Další informace o další informace zobrazené, přečtěte si kroku 6 **zobrazit pravidla efektivní zabezpečení pro virtuální počítač** tohoto článku.

> [!NOTE]
> I když podsítě a síťový adaptér mohou mít pouze jednu skupinu NSG na ně použity, může být přidružena k několika síťových adaptérů a více podsítí skupinu NSG.
> 
> 

## <a name="considerations"></a>Požadavky
Při řešení potíží s připojením problémy, zvažte následující body:

* Výchozí pravidla NSG se blokují příchozí přístup z Internetu a pouze povolit virtuální síť příchozí přenosy. Pravidla musí být přidané do povolit příchozí přístup z Internetu, podle potřeby.
* Pokud neexistují žádná pravidla zabezpečení NSG způsobuje připojení k síti Virtuálního počítače k selhání, může být problém z důvodu:
  * Software brány firewall běžící v rámci operačního systému Virtuálního počítače
  * Trasy nakonfigurované pro virtuální zařízení nebo místní provoz. Internetové přenosy můžete přesměrovat do místního prostřednictvím vynucené tunelování. Připojení RDP/SSH z Internetu virtuální počítač nemusí fungovat s tímto nastavením, v závislosti na tom, jak místní síťový hardware zpracovává tento provoz. Pro čtení [řešení potíží s trasy](virtual-network-routes-troubleshoot-powershell.md) článku se dozvíte, jak k diagnostikování problémů trasy, které může být zpomalovat toku provozu a odhlásit z virtuálního počítače. 
* Pokud máte peered virtuálních sítí, ve výchozím nastavení, se automaticky rozšíří VIRTUAL_NETWORK značky pro zahrnutí předpony peered virtuální sítě. Tyto předpony v můžete zobrazit **ExpandedAddressPrefix** seznamu, vyřešte všechny problémy související s konektivitou partnerského vztahu virtuální sítě. 
* Efektivní zabezpečení pravidla se zobrazují pouze pokud je skupina NSG přidružená Virtuálního počítače síťový adaptér a nebo podsíť. 
* Pokud neexistují žádné skupiny Nsg přidružená síťový adaptér nebo podsíť a jestli máte veřejné IP adresy přiřazené k virtuálnímu počítači, budou všechny porty otevřené pro příchozí a odchozí přístup. Pokud virtuální počítač má veřejnou IP adresu, použijete skupiny Nsg na síťový adaptér nebo podsíť důrazně doporučujeme.

