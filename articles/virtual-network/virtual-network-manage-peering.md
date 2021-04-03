---
title: Vytvoření, změna nebo odstranění partnerského vztahu virtuální sítě Azure | Microsoft Docs
description: Vytvoření, změna nebo odstranění partnerského vztahu virtuální sítě. S partnerským vztahem virtuální sítě propojíte virtuální sítě ve stejné oblasti a v oblastech.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: altambaw
ms.openlocfilehash: dc8db3f1eccce2bb85f03d51fcfd1c4113823d49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222662"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Vytvoření, změna nebo odstranění partnerského vztahu virtuální sítě

Přečtěte si, jak vytvořit, změnit nebo odstranit partnerský vztah virtuální sítě. Partnerský vztah virtuálních sítí umožňuje propojit virtuální sítě ve stejné oblasti a oblastech (označované také jako globální partnerský vztah virtuální sítě) prostřednictvím páteřní sítě Azure. Po navázání partnerského vztahu se virtuální sítě pořád spravují jako samostatné prostředky. Pokud s partnerským vztahem mezi virtuálními sítěmi začínáte, můžete o něm získat další informace v tématu [Přehled partnerského vztahu virtuální sítě](virtual-network-peering-overview.md) nebo v tomto [kurzu](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před dokončením kroků v jakékoli části tohoto článku proveďte následující úlohy:

- Pokud ještě nemáte účet Azure, zaregistrujte si [bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.com a přihlaste se pomocí účtu, který má [potřebná oprávnění](#permissions) pro práci s partnerskými vztahy.
- Pokud k dokončení úkolů v tomto článku používáte příkazy prostředí PowerShell, buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShellu z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit `Connect-AzAccount` s účtem, který má [potřebná oprávnění](#permissions) pro práci s partnerským vztahem, a vytvořit tak připojení k Azure.
- Pokud k dokončení úkolů v tomto článku používáte příkazy rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z počítače. Tento kurz vyžaduje Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte ho také spustit `az login` s účtem, který má [potřebná oprávnění](#permissions) pro práci s partnerským vztahem, a vytvořit tak připojení k Azure.

Účet, ke kterému se přihlašujete, nebo se k Azure připojíte pomocí nástroje, musí být přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazená k příslušným akcím uvedeným v [oprávněních](#permissions).

## <a name="create-a-peering"></a>Vytvoření partnerského vztahu

Před vytvořením partnerského vztahu se seznamte s požadavky a omezeními a [potřebnými oprávněními](#permissions).

1. Do vyhledávacího pole v horní části Azure Portal zadejte do vyhledávacího pole *virtuální sítě* . Pokud se ve výsledcích hledání zobrazí **virtuální sítě** , vyberte ji. Nevybírejte možnost **virtuální sítě (klasické)** , pokud se v seznamu objeví, protože nemůžete vytvořit partnerský vztah z virtuální sítě nasazené prostřednictvím modelu nasazení Classic.
2. V seznamu vyberte virtuální síť, pro kterou chcete vytvořit partnerský vztah.
3. V části **Nastavení** vyberte **partnerské vztahy**.
4. Vyberte **+ Přidat**. 
5. <a name="add-peering"></a>Zadejte nebo vyberte hodnoty pro následující nastavení:
    - **Název:** Název partnerského vztahu musí být v rámci virtuální sítě jedinečný.
    - **Model nasazení virtuální sítě:** Vyberte model nasazení, pomocí kterého se nasadila virtuální síť, se kterou chcete vytvořit partnerský vztah.
    - **Znám moje ID prostředku:** Pokud máte přístup pro čtení k virtuální síti, se kterou chcete vytvořit partnerský vztah, nechejte toto políčko nezaškrtnuté. Pokud nemáte oprávnění ke čtení pro virtuální síť nebo předplatné, se kterým chcete vytvořit partnerský vztah, zaškrtněte toto políčko. Zadejte úplné ID prostředku virtuální sítě, se kterou chcete vytvořit partnerský vztah, v poli **ID prostředku** , které se zobrazilo po zaškrtnutí políčka. ID prostředku, které zadáte, musí být pro virtuální síť, která existuje ve stejné nebo [podporované jiné](#requirements-and-constraints) [oblasti](https://azure.microsoft.com/regions) Azure jako tato virtuální síť. Úplné ID prostředku vypadá podobně jako `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>` . ID prostředku pro virtuální síť můžete získat zobrazením vlastností pro virtuální síť. Informace o tom, jak zobrazit vlastnosti pro virtuální síť, najdete v tématu [Správa virtuálních sítí](manage-virtual-network.md#view-virtual-networks-and-settings). Pokud je předplatné přidruženo k jinému Azure Active Directory tenantovi než předplatné s virtuální sítí, ze které vytváříte partnerský vztah, nejprve přidejte uživatele z každého tenanta jako [uživatele typu Host](../active-directory/external-identities/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) v protějším tenantovi.
    - **Předplatné:** Vyberte [odběr](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) virtuální sítě, se kterou chcete vytvořit partnerský vztah. V seznamu je uvedeno jedno nebo více předplatných v závislosti na počtu předplatných, ke kterým má váš účet oprávnění ke čtení. Pokud jste zaškrtli políčko **ID prostředku** , toto nastavení není k dispozici.
    - **Virtuální síť:** Vyberte virtuální síť, se kterou chcete vytvořit partnerský vztah. Můžete vybrat virtuální síť vytvořenou prostřednictvím modelu nasazení Azure. Pokud chcete vybrat virtuální síť v jiné oblasti, je nutné vybrat virtuální síť v [podporované oblasti](#cross-region). Aby se tato virtuální síť mohla zobrazit v seznamu, musíte mít oprávnění ke čtení. Pokud je virtuální síť uvedená, ale je šedá, může to být způsobeno tím, že se adresní prostor pro virtuální síť překrývá s adresním prostorem pro tuto virtuální síť. Pokud se adresní prostory virtuální sítě překrývají, nelze je navázat na partnerský vztah. Pokud jste zaškrtli políčko **ID prostředku** , toto nastavení není k dispozici.
    - **Povolení přístupu k virtuální síti:** Pokud chcete povolit komunikaci mezi dvěma virtuálními sítěmi prostřednictvím výchozího toku, vyberte **povoleno** (výchozí) `VirtualNetwork` . Povolení komunikace mezi virtuálními sítěmi umožňuje vzájemnou komunikaci prostředků připojených k libovolné virtuální síti se stejnou šířkou pásma a latencí, jako kdyby byly připojené ke stejné virtuální síti. Veškerá komunikace mezi prostředky ve dvou virtuálních sítích je přes privátní síť Azure. Značka služby **VirtualNetwork** pro skupiny zabezpečení sítě zahrnuje virtuální síť a partnerský virtuální síť, pokud je toto nastavení **povolené**. (Další informace o značkách služby skupin zabezpečení sítě najdete v tématu [Přehled skupin zabezpečení sítě](./network-security-groups-overview.md#service-tags).) Vyberte **zakázáno** , pokud nechcete, aby se provoz do partnerské virtuální sítě ve výchozím nastavení nasměroval na partnerský vztah. Můžete vybrat **zakázáno** , pokud jste nastavili partnerský vztah k virtuální síti s jinou virtuální sítí, ale občas chcete zakázat výchozí tok přenosů mezi těmito dvěma virtuálními sítěmi. Povolení nebo zakázání je výhodnější než odstranění a opětovné vytváření partnerských vztahů. Pokud je toto nastavení zakázané, přenosy mezi partnerskými virtuálními sítěmi ve výchozím nastavení nesměrují. provoz ale může pořád přesměrovat, pokud je výslovně povolený prostřednictvím pravidla [skupiny zabezpečení sítě](./network-security-groups-overview.md) , které obsahuje příslušné IP adresy nebo skupiny zabezpečení aplikací.
      > [!WARNING]
      > Zakázáním nastavení **Povolení přístupu k virtuální síti** se změní jenom definice značky služby **VirtualNetwork** . Nezabraňuje zcela přenos toků mezi partnerskými připojeními, *jak je* vysvětleno v tomto popisu nastavení.    
    - **Povolení přesměrovaného provozu:** Toto políčko zaškrtněte, pokud chcete, aby byl provoz *předaný* síťovým virtuálním zařízením ve virtuální síti (který nepochází z virtuální sítě) k přenosu do této virtuální sítě prostřednictvím partnerského vztahu. Zvažte například tři virtuální sítě s názvem Spoke1, Spoke2 a hub. Partnerský vztah mezi každou virtuální sítí na paprsku a virtuální sítí rozbočovače existuje, ale partnerské vztahy mezi virtuálními sítěmi paprsků neexistují. Síťové virtuální zařízení je nasazené ve virtuální síti centrální sítě a uživatelsky definované trasy se aplikují na každou virtuální síť paprsků, která směruje provoz mezi podsítěmi přes síťové virtuální zařízení. Pokud toto políčko není zaškrtnuté pro partnerský vztah mezi jednotlivými virtuálními sítěmi a virtuální sítí rozbočovače, přenosy mezi virtuálními sítěmi paprsků nesměrují, protože rozbočovač nepřesměrovává přenos dat mezi virtuálními sítěmi. I když povolením této možnosti povolíte předaný přenos přes partnerský vztah, nevytvoří se žádné trasy definované uživatelem ani síťové virtuální zařízení. Uživatelsky definované trasy a síťová virtuální zařízení se vytvářejí samostatně. Přečtěte si informace o [trasách definovaných uživatelem](virtual-networks-udr-overview.md#user-defined). Toto nastavení nemusíte kontrolovat, pokud je přenos předáván mezi virtuálními sítěmi prostřednictvím VPN Gateway Azure.
    - **Povolení přenosu brány:** Toto políčko zaškrtněte, pokud máte bránu virtuální sítě připojenou k této virtuální síti a chcete, aby přenos přes bránu umožnil provoz z partnerské virtuální sítě. Tato virtuální síť může být například připojená k místní síti přes bránu virtuální sítě. Bránou může být ExpressRoute nebo VPN Gateway. Zaškrtnutím tohoto políčka povolíte přenos z partnerské virtuální sítě do toku přes bránu připojenou k této virtuální síti do místní sítě. Pokud toto políčko zaškrtnete, virtuální síť s partnerským vztahem nemůže mít nakonfigurovanou bránu. Partnerská virtuální síť musí mít zaškrtnuté políčko **používat vzdálené brány** při nastavování partnerského vztahu z druhé virtuální sítě k této virtuální síti. Pokud necháte toto políčko nezaškrtnuté (výchozí nastavení), provoz z partnerské virtuální sítě bude dál přecházet do této virtuální sítě, ale nemůže přesměrovat bránu virtuální sítě připojenou k této virtuální síti. Pokud je partnerský vztah mezi virtuální sítí (Správce prostředků) a virtuální sítí (Classic), musí být brána ve virtuální síti (Správce prostředků).

       Kromě přesměrování provozu do místní sítě může Brána sítě VPN přesměrovat síťový provoz mezi virtuálními sítěmi, které jsou v partnerském vztahu s virtuální sítí, ve které je brána, a nemusíte přitom navzájem navázat partnerský vztah mezi virtuálními sítěmi. Použití brány VPN k přeposílání provozu je užitečné v případě, že chcete používat bránu VPN v centru (viz střed a paprskový příklad popsaný v tématu **Povolení přesměrovaného přenosu**) pro směrování provozu mezi koncovými virtuálními sítěmi, které nejsou navzájem partnerské. Další informace o povolení použití brány pro přenos najdete v tématu [Konfigurace brány VPN pro přenos v partnerském vztahu virtuálních sítí](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tento scénář vyžaduje implementaci uživatelsky definovaných tras, které jako typ dalšího segmentu směrování určují bránu virtuální sítě. Přečtěte si informace o [trasách definovaných uživatelem](virtual-networks-udr-overview.md#user-defined). Bránu VPN můžete zadat jenom jako typ dalšího segmentu směrování v uživatelsky definované trase. bránu ExpressRoute nejde zadat jako typ dalšího segmentu směrování v uživatelsky definované trase.

    - **Používejte vzdálené brány:** Zaškrtněte toto políčko, pokud chcete, aby provoz z této virtuální sítě procházel přes bránu virtuální sítě připojenou k virtuální síti, se kterou jste nacházeli s partnerským vztahem. Například virtuální síť, na kterou se připojujete, má připojenou bránu VPN, která umožňuje komunikaci s místní sítí.  Zaškrtnutím tohoto políčka povolíte přenos z této virtuální sítě přes bránu VPN připojenou k partnerské virtuální síti. Pokud toto políčko zaškrtnete, musí mít připojená virtuální síť připojenou bránu virtuální sítě a musí mít zaškrtnuté políčko **povolovat přenos brány** . Pokud necháte toto políčko nezaškrtnuté (výchozí nastavení), provoz z partnerské virtuální sítě se pořád může přesměrovat do této virtuální sítě, ale nemůže přesměrovat bránu virtuální sítě připojenou k této virtuální síti.
    
      Toto nastavení může mít povolené jenom jeden partnerský vztah pro tuto virtuální síť.

      Vzdálené brány nemůžete použít, pokud už máte ve své virtuální síti nakonfigurovanou bránu. Další informace o používání brány pro přenos najdete v tématu [Konfigurace brány VPN pro přenos v partnerském vztahu virtuálních sítí](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .
        
    > [!NOTE]
    > Pokud používáte bránu Virtual Network k posílání místních přenosů do partnerské virtuální sítě, musí být rozsah IP adres partnerských virtuálních sítí pro místní zařízení VPN nastavený na zajímavou komunikaci. V opačném případě vaše místní prostředky nebudou moci komunikovat s prostředky v partnerské virtuální síti.

6. Vyberte **OK** a přidejte partnerský vztah k vybrané virtuální síti.

Podrobné pokyny pro implementaci partnerských vztahů mezi virtuálními sítěmi v různých předplatných a modelech nasazení najdete v části [Další kroky](#next-steps).

### <a name="commands"></a>Příkazy

- **Azure CLI**: [AZ Network VNet peering Create](/cli/azure/network/vnet/peering)
- **PowerShell**: [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Zobrazení nebo změna nastavení partnerského vztahu

Před změnou partnerského vztahu se seznamte s požadavky a omezeními a [potřebnými oprávněními](#permissions).

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *virtuální sítě* . Pokud se ve výsledcích hledání zobrazí **virtuální sítě** , vyberte ji. Nevybírejte možnost **virtuální sítě (klasické)** , pokud se v seznamu objeví, protože nemůžete vytvořit partnerský vztah z virtuální sítě nasazené prostřednictvím modelu nasazení Classic.
2. V seznamu vyberte virtuální síť, pro kterou chcete změnit nastavení partnerského vztahu.
3. V části **Nastavení** vyberte **partnerské vztahy**.
4. Vyberte partnerský vztah, pro který chcete zobrazit nebo změnit nastavení.
5. Změňte příslušné nastavení. Přečtěte si o možnostech pro každé nastavení v [kroku 5](#add-peering) vytvoření partnerského vztahu.
6. Vyberte **Uložit**.

**Příkazy**

- **Azure CLI**: [AZ Network VNet peering list](/cli/azure/network/vnet/peering) pro výpis partnerských vztahů pro virtuální síť, [AZ Network VNet peering show](/cli/azure/network/vnet/peering) k zobrazení nastavení konkrétního partnerského vztahu a [AZ Network VNet peering Update](/cli/azure/network/vnet/peering) pro změnu nastavení partnerského vztahu. |
- **PowerShell**: [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) pro načtení nastavení partnerského vztahu zobrazení a nastavení [-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) pro změnu nastavení.

## <a name="delete-a-peering"></a>Odstranění partnerského vztahu

Než odstraníte partnerský vztah, ujistěte se, že váš účet má [potřebná oprávnění](#permissions).

Když se partnerský vztah odstraní, provoz z virtuální sítě přestane natékat do partnerské virtuální sítě. Pokud jsou virtuální sítě nasazené prostřednictvím Správce prostředků partnerského vztahu, každá virtuální síť má partnerský vztah k druhé virtuální síti. Když odstranění partnerského vztahu z jedné virtuální sítě zakáže komunikaci mezi virtuálními sítěmi, neodstraní partnerský vztah z jiné virtuální sítě. Stav partnerského vztahu pro partnerský vztah, který existuje v jiné virtuální síti, je **odpojený**. Partnerský vztah nelze znovu vytvořit, dokud znovu nevytvoříte partnerský vztah v první virtuální síti a stav partnerského vztahu pro obě virtuální sítě bude změněn na *připojeno*.

Pokud chcete, aby virtuální sítě mohly někdy komunikovat, ale ne vždy, místo odstranění partnerského vztahu, můžete místo toho nastavit možnost **Povolení přístupu k virtuální síti** jako **zakázané** . Pokud se chcete dozvědět, jak, přečtěte si krok 6 části vytvoření partnerského vztahu v tomto článku. Je možné, že zakážete a povolíte přístup k síti snadněji než odstranění a opětovné vytváření partnerských vztahů.

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *virtuální sítě* . Pokud se ve výsledcích hledání zobrazí **virtuální sítě** , vyberte ji. Nevybírejte možnost **virtuální sítě (klasické)** , pokud se v seznamu objeví, protože nemůžete vytvořit partnerský vztah z virtuální sítě nasazené prostřednictvím modelu nasazení Classic.
2. V seznamu vyberte virtuální síť, pro kterou chcete odstranit partnerský vztah.
3. V části **Nastavení** vyberte **partnerské vztahy**.
4. Na pravé straně partnerského vztahu, který chcete odstranit, vyberte **...**, vyberte **Odstranit** a pak vyberte **Ano** , pokud chcete odstranit partnerský vztah z první virtuální sítě.
5. Dokončete předchozí kroky a odstraňte partnerský vztah z jiné virtuální sítě v partnerském vztahu.

**Příkazy**

- **Azure CLI**: [AZ Network VNet peering Delete](/cli/azure/network/vnet/peering)
- **PowerShell**: [Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Požadavky a omezení

- <a name="cross-region"></a>Můžete vytvořit partnerský vztah virtuálních sítí ve stejné oblasti nebo v různých oblastech. Partnerské vztahy virtuálních sítí v různých oblastech se také označují jako *globální partnerský vztah* virtuální sítě. 
- Při vytváření globálního partnerského vztahu mohou partnerské virtuální sítě existovat v jakékoli oblasti veřejného cloudu Azure nebo v oblasti čínského cloudu nebo v oblastech pro státní Cloud. Nemůžete vytvořit partnerský vztah mezi cloudy. Například virtuální síť ve veřejném cloudu Azure se nedá navázat na síť VNet v cloudu Azure Čína.
- Prostředky v jedné virtuální síti nemůžou komunikovat s front-endovou IP adresou interního nástroje pro vyrovnávání zatížení úrovně Basic ve virtuální síti v globálním partnerském vztahu. Podpora nástroje pro vyrovnávání zatížení úrovně Basic je k dispozici pouze ve stejné oblasti. Podpora nástroje pro vyrovnávání zatížení úrovně Standard je k dispozici pro VNET Peering i globální VNET Peering. Služby, které používají základní nástroj pro vyrovnávání zatížení, který nefunguje přes globální partnerský vztah virtuálních sítí, jsou popsané [tady.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- Můžete použít vzdálené brány nebo povolte přenos brány v globálně partnerských virtuálních sítích a místně partnerských virtuálních sítích.
- Virtuální sítě mohou být ve stejném nebo jiném předplatném. Při vytváření partnerského vztahu virtuálních sítí v různých předplatných je možné oba odběry přidružit ke stejnému nebo jinému klientovi Azure Active Directory. Pokud ještě nemáte tenanta služby AD, můžete si [ho vytvořit](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant).
- Virtuální sítě, u kterých je partnerský uzel, musí mít překrývající se adresní prostory IP adres.
- Po navázání partnerského vztahu virtuální sítě s jinou virtuální sítí nemůžete přidat rozsah adres do nebo odstranit rozsahy adres z adresního prostoru virtuální sítě. Pokud chcete přidat nebo odebrat rozsahy adres, odstraňte partnerský vztah, přidejte nebo odeberte rozsahy adres a potom vytvořte partnerský vztah znovu. Pokud chcete přidat rozsahy adres do nebo odebrat rozsahy adres z virtuálních sítí, přečtěte si téma [Správa virtuálních sítí](manage-virtual-network.md).
- Partnerské vztahy mezi dvěma virtuálními sítěmi nasazenými prostřednictvím Správce prostředků nebo virtuální sítí nasazené prostřednictvím Správce prostředků můžete vytvořit pomocí virtuální sítě nasazené prostřednictvím modelu nasazení Classic. Nemůžete vytvořit partnerské vztahy mezi dvěma virtuálními sítěmi vytvořenými prostřednictvím modelu nasazení Classic. Pokud nejste obeznámeni s modely nasazení Azure, přečtěte si článek [vysvětlení modelů nasazení Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . K propojení dvou virtuálních sítí vytvořených prostřednictvím modelu nasazení Classic můžete použít službu [VPN Gateway](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V).
- Pokud navazujete partnerský vztah mezi dvěma virtuálními sítěmi vytvořenými prostřednictvím Resource Manageru, v partnerském vztahu musí být nakonfigurován partnerský vztah pro každou virtuální síť. Pro stav partnerského vztahu se zobrazí jeden z následujících typů: 
  - *Zahájeno:* Když vytváříte partnerský vztah k druhé virtuální síti z první virtuální sítě, stav partnerského vztahu je *zahájeno*. 
  - *Připojeno:* Při vytváření partnerského vztahu z druhé virtuální sítě k první virtuální síti je stav partnerského vztahu *připojen*. Pokud zobrazíte stav partnerského vztahu pro první virtuální síť, zobrazí se jeho stav změněno z *iniciované* na *připojeno*. Partnerský vztah není úspěšně navázán, dokud není *propojen* stav partnerského vztahu obou partnerských vztahů virtuálních sítí.
- Při vytváření partnerských vztahů mezi virtuálními sítěmi vytvořenými prostřednictvím Správce prostředků s virtuální sítí vytvořenou prostřednictvím modelu nasazení Classic stačí nakonfigurovat partnerský vztah pro virtuální síť nasazenou prostřednictvím Správce prostředků. Nemůžete nakonfigurovat partnerský vztah pro virtuální síť (Classic) nebo mezi dvěma virtuálními sítěmi nasazenými prostřednictvím modelu nasazení Classic. Při vytváření partnerského vztahu z virtuální sítě (Správce prostředků) k virtuální síti (Classic) se stav partnerského vztahu *aktualizuje* a pak se v krátké době změní na *připojeno*.
- Partnerský vztah je mezi dvěma virtuálními sítěmi vytvořen. Partnerské vztahy samy o sobě nejsou přenosné. Pokud vytvoříte partnerské vztahy mezi:
  - VirtualNetwork1 & VirtualNetwork2-VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3-VirtualNetwork2 & VirtualNetwork3


  Mezi VirtualNetwork1 a VirtualNetwork3 se nevztahují žádné partnerské vztahy prostřednictvím VirtualNetwork2. Pokud chcete vytvořit partnerský vztah virtuálních sítí mezi VirtualNetwork1 a VirtualNetwork3, je nutné vytvořit partnerský vztah mezi VirtualNetwork1 a VirtualNetwork3. Mezi VirtualNetwork1 a VirtualNetwork3 se nevztahují žádné partnerské vztahy prostřednictvím VirtualNetwork2. Pokud chcete, aby VirtualNetwork1 a VirtualNetwork3 přímo komunikovaly, musíte vytvořit explicitní partnerský vztah mezi VirtualNetwork1 a VirtualNetwork3 nebo projít síťové virtuální zařízení v síti rozbočovače.  
- V partnerských virtuálních sítích s použitím výchozího překladu názvů Azure nemůžete překládat názvy. Chcete-li přeložit názvy v jiných virtuálních sítích, je nutné použít [Azure DNS pro privátní domény](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo vlastní server DNS. Informace o tom, jak nastavit vlastní server DNS, najdete v tématu [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Prostředky v partnerských virtuálních sítích ve stejné oblasti můžou vzájemně komunikovat se stejnou šířkou pásma a latencí, jako kdyby byly ve stejné virtuální síti. Velikost každého virtuálního počítače ale má svou vlastní maximální šířku pásma sítě. Další informace o maximální šířce pásma pro různé velikosti virtuálních počítačů najdete v tématu velikosti virtuálních počítačů se [systémem Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .
- Virtuální síť může navázat partnerský vztah k jiné virtuální síti a taky se připojit k jiné virtuální síti s bránou virtuální sítě Azure. Když jsou virtuální sítě propojené prostřednictvím partnerského vztahu i brány, provoz mezi virtuálními sítěmi projde konfigurací partnerského vztahu, a ne bránou.
- Klienti VPN typu Point-to-site se musí stáhnout znovu po úspěšném nakonfigurování partnerského vztahu virtuálních sítí, aby bylo zajištěno, že se nové trasy stáhnou do klienta.
- Za příchozí a výchozí přenos dat využívající partnerský vztah virtuálních sítí se účtuje nominální poplatek. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Oprávnění

Účty, které používáte pro práci s partnerským vztahem virtuální sítě, je třeba přiřadit k následujícím rolím:

- [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): pro virtuální síť nasazenou prostřednictvím Správce prostředků.
- [Přispěvatel klasických sítí](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): pro virtuální síť nasazenou prostřednictvím modelu nasazení Classic.

Pokud váš účet není přiřazený k jedné z předchozích rolí, musí být přiřazený k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazená k potřebným akcím z následující tabulky:

| Akce                                                          | Name |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Vyžaduje se vytvoření partnerského vztahu z virtuální sítě A do virtuální sítě B. virtuální síť musí být virtuální síť (Správce prostředků).          |
| Microsoft. Network/virtualNetworks/peer/Action                   | Vyžaduje se k vytvoření partnerského vztahu z virtuální sítě B (Správce prostředků) k virtuální síti.                                                       |
| Microsoft. ClassicNetwork/virtualNetworks/peer/Action                   | Vyžaduje se k vytvoření partnerského vztahu z virtuální sítě B (Classic) k virtuální síti.                                                                |
| Microsoft. Network/virtualNetworks/virtualNetworkPeerings/Read   | Čtení partnerského vztahu virtuální sítě   |
| Microsoft. Network/virtualNetworks/virtualNetworkPeerings/DELETE | Odstranění partnerského vztahu virtuální sítě |

## <a name="next-steps"></a>Další kroky

- Partnerský vztah virtuálních sítí se vytváří mezi virtuálními sítěmi vytvořenými prostřednictvím stejného modelu nebo různých modelů nasazení, které existují ve stejném předplatném nebo různých předplatných. Dokončete kurz pro jeden z následujících scénářů:

  |Model nasazení Azure             | Předplatné  |
  |---------                          |---------|
  |Obě Resource Manager              |[Stejné](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Různé](create-peering-different-subscriptions.md)|
  |Jedna Resource Manager, druhá Classic  |[Stejné](create-peering-different-deployment-models.md)|
  |                                   |[Různé](create-peering-different-deployment-models-subscriptions.md)|

- Zjistěte, jak vytvořit [rozbočovač a uvedenou síťovou topologii](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Vytvoření partnerského vztahu virtuálních sítí pomocí [PowerShellu](powershell-samples.md) nebo ukázkových skriptů [Azure CLI](cli-samples.md) nebo používání [šablon Azure správce prostředků](template-samples.md)
- Vytvoření a přiřazení [definic Azure Policy](./policy-reference.md) pro virtuální sítě