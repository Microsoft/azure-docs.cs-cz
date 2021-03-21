---
title: Postup vytvoření testovacího prostředí se sdíleným prostředkem | Azure Lab Services
description: Naučte se, jak vytvořit testovací prostředí, které vyžaduje prostředek sdílený mezi studenty.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: d4bf4127dc163bb5f034e077b84664828374ba87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94647966"
---
# <a name="how-to-create-a-lab-with-a-shared-resource-in-azure-lab-services"></a>Postup vytvoření testovacího prostředí se sdíleným prostředkem v Azure Lab Services

Při vytváření testovacího prostředí učebny může někdy docházet k nějakým prostředkům, které je potřeba sdílet mezi všemi studenty v testovacím prostředí.  Máte například licenční server nebo SQL Server pro třídu databáze.  Tento článek pojednává o krocích pro povolení sdíleného prostředku pro testovací prostředí.  Budeme také mluvit o tom, jak omezit přístup k tomuto sdílenému prostředku.

## <a name="architecture"></a>Architektura

Jak je znázorněno na následujícím obrázku, budeme mít účet testovacího prostředí s testovacím prostředím.  Účet testovacího prostředí bude mít nastavení partnerského vztahu virtuální sítě, aby byla virtuální síť pro testovací prostředí připojená k síti sdíleného prostředku.  V diagramu níže jsou dvě virtuální sítě s rozsahy IP adres, které se nepřekrývají.  Tyto rozsahy IP adres jsou jenom ukázkové rozsahy.  Všimněte si také, že virtuální síť sdíleného prostředku je ve stejném předplatném jako účet testovacího prostředí.

![Služby testovacího prostředí s architekturou sdílených prostředků](./media/how-to-create-a-lab-with-shared-resource/shared-resource-architecture.png)

## <a name="setup-shared-resource"></a>Nastavení sdíleného prostředku

Před vytvořením testovacího prostředí musí být vytvořená virtuální síť pro sdílený prostředek.  Další informace o tom, jak vytvořit virtuální síť, najdete v tématu [vytvoření virtuální sítě](../virtual-network/quick-create-portal.md).  Plánování rozsahů virtuální sítě, aby se nepřekrývaly s IP adresou testovacích počítačů, je důležité.  Další informace o plánování sítě najdete v článku věnovaném plánování [virtuálních sítí](../virtual-network/virtual-network-vnet-plan-design-arm.md) . V našem příkladu je sdílený prostředek ve virtuální síti s rozsahem 10.2.0.0/16.  Pokud jste to ještě neudělali, [vytvořte podsíť](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) pro uložení sdíleného prostředku.  V tomto příkladu používáme rozsah 10.2.0.0/24, ale rozsah se může lišit v závislosti na potřebách vaší sítě.

Sdílený prostředek může být software spuštěný ve virtuálním počítači nebo v poskytnuté službě Azure. Sdílený prostředek by měl být k dispozici prostřednictvím privátní IP adresy.  Když sdílený prostředek zpřístupníte jenom prostřednictvím soukromé IP adresy, omezíte přístup k tomuto sdílenému prostředku.

Diagram taky zobrazuje skupinu zabezpečení sítě (NSG), která se dá použít k omezení provozu z virtuálního počítače studenta.  Můžete například napsat pravidlo zabezpečení, které zajistí, že provoz z IP adres virtuálního počítače studenta může přistupovat jenom k jednomu sdílenému prostředku a nic jiného.  Další informace o nastavení pravidel zabezpečení najdete v tématu [Správa skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md#work-with-security-rules). Pokud chcete omezit přístup ke sdílenému prostředku do konkrétního testovacího prostředí, Získejte IP adresu pro testovací prostředí z [Nastavení testovacího prostředí z účtu testovacího prostředí](manage-labs.md#view-labs-in-a-lab-account) a nastavte příchozí pravidlo povolující přístup jenom z této IP adresy.  Nezapomeňte pro tuto IP adresu povolit porty 49152 až 65535.  Volitelně můžete najít soukromou IP adresu virtuálních počítačů studenta pomocí [stránky fond virtuálních počítačů](how-to-set-virtual-machine-passwords.md).

Pokud je váš sdílený prostředek virtuálním počítačem Azure, který spouští potřebný software, budete možná muset upravit výchozí pravidla brány firewall pro virtuální počítač.

### <a name="tips-for-shared-resources---license-server"></a>Tipy pro sdílené prostředky – licenční server
Jednou z častých sdílených prostředků je licenční server. tady je několik tipů, jak to provést s nastavením.
#### <a name="server-region"></a>Oblast serveru
Licenční server bude nutné připojit k virtuální síti, která je v rámci tohoto testovacího prostředí, takže licenční server musí být umístěn ve stejné oblasti jako účet testovacího prostředí.

#### <a name="static-private-ip-and-mac-address"></a>Statická privátní IP adresa a adresa MAC
Ve výchozím nastavení mají virtuální počítače dynamickou privátní IP adresu, [než nakonfigurujete veškerý software nastavený na statickou IP](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)adresu. Tím se nastaví privátní IP adresa a adresa MAC, které se budou statické.  

#### <a name="control-access"></a>Řízení přístupu
Řízení přístupu k licenčnímu serveru je klíč.  Jakmile bude mít virtuální počítač přístup k instalaci, bude potřeba i pro údržbu, řešení potíží a aktualizaci.  Tady je několik různých způsobů, jak to provést.
- [Nastavení přístupu JIT (just in time) v rámci Azure Security Center.](../security-center/security-center-just-in-time.md?tabs=jit-config-asc%252cjit-request-asc)
- [Nastavení skupiny zabezpečení sítě pro omezení přístupu.](../virtual-network/network-security-groups-overview.md)
- [Nastavením bastionu povolíte zabezpečený přístup k licenčnímu serveru.](https://azure.microsoft.com/services/azure-bastion/)

## <a name="lab-account"></a>Účet testovacího prostředí

Pokud chcete použít sdílený prostředek, musí být účet testovacího prostředí nastavený tak, aby používal [partnerský virtuální síť](how-to-connect-peer-virtual-network.md).  V tomto případě budeme navázat partnerský vztah k virtuální síti, která obsahuje sdílený prostředek.

>[!WARNING]
>Testovací prostředí pro vaši třídu se musí vytvořit, **až** bude účet testovacího prostředí připojen k virtuální síti sdíleného prostředku.  
Počítač šablony

Po navázání účtu testovacího prostředí do virtuální sítě by měl mít počítač s šablonou teď přístup ke sdílenému prostředku.  Možná budete muset aktualizovat pravidla brány firewall v závislosti na sdíleném prostředku, ke kterému se přistupoval.