---
title: Vytvoření, změna nebo odstranění partnerského vztahu virtuální sítí Azure | Dokumentace Microsoftu
description: Informace o vytvoření, změna nebo odstranění partnerského vztahu virtuálních sítí.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: jdial;anavin
ms.openlocfilehash: 068ebfb9bdb7874a5596adaa7133ba270853f983
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434117"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Vytvoření, změna nebo odstranění partnerského vztahu virtuálních sítí

Informace o vytvoření, změna nebo odstranění partnerského vztahu virtuálních sítí. Partnerské vztahy virtuálních sítí můžete propojit virtuální sítě ve stejné oblasti a oblastmi (označované také jako globální VNet Peering) prostřednictvím páteřní sítě Azure. Po navázání partnerského vztahu, virtuálních sítí se pořád spravují jako samostatné prostředky. Pokud začínáte partnerský vztah virtuální sítě, další informace o řezu [přehled partnerských vztahů virtuálních sítí](virtual-network-peering-overview.md) nebo provedením [kurzu](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Před zahájením

Před dokončením kroků v jakékoli části tohoto článku, proveďte následující úkoly:

- Pokud ještě nemáte účet Azure, zaregistrujte si [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí účtu, který má [potřebná oprávnění](#permissions) pro práci s partnerské vztahy.
- Pokud používáte příkazy prostředí PowerShell k dokončení úkolů v tomto článku, buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/powershell), nebo pomocí prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 5.7.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Pokud používáte PowerShell místně, musíte také spustit `Connect-AzureRmAccount` pomocí účtu, který má [potřebná oprávnění](#permissions) pro práci s partnery, vytvořte připojení k Azure.
- Pokud k dokončení úkolů v tomto článku pomocí příkazů rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/bash), nebo pomocí rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje použití Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` pomocí účtu, který má [potřebná oprávnění](#permissions) pro práci s partnery, vytvořte připojení k Azure.

Účet přihlásit nebo připojit k Azure, musíte být přiřazeni k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přiřazené příslušné akce uvedené v [oprávnění ](#permissions).

## <a name="create-a-peering"></a>Vytvoření partnerského vztahu

Před vytvořením partnerského vztahu, seznamte se s [požadavky a omezení](#requirements-and-contstraints) a [potřebná oprávnění](#permissions).

1. Do vyhledávacího pole v horní části webu Azure portal, zadejte *virtuálních sítí* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ji. Nesmí být zvolen **virtuální sítě (klasické)** Pokud se zobrazí v seznamu, nelze vytvoříte partnerské připojení z virtuální sítě nasazené pomocí modelu nasazení classic.
2. Vyberte v seznamu, který chcete vytvořit partnerský vztah virtuální sítě.
3. Seznam virtuálních sítí vyberte virtuální síť, kterou chcete vytvořit partnerský vztah.
4. V části **nastavení**vyberte **partnerské vztahy**.
5. Vyberte **+ Přidat**. 
6. <a name="add-peering"></a>Zadejte nebo vyberte hodnoty pro následující nastavení:
    - **Jméno:** Název partnerského vztahu musí být jedinečný v rámci virtuální sítě.
    - **Model nasazení virtuální sítě:** Vyberte které model nasazení, které chcete vytvořit partnerský vztah virtuální sítě nasazené prostřednictvím.
    - **Znám svoje ID prostředku:** Pokud máte přístup pro čtení k virtuální síti, kterou chcete vytvořit partnerský vztah, nechte toto políčko nezaškrtnuté. Pokud nemáte přístup pro čtení k virtuální síti nebo předplatné, které chcete vytvořit partnerský vztah, zaškrtněte toto políčko. Zadejte úplné ID prostředku virtuální sítě, kterou chcete vytvořit partnerský vztah v **ID prostředku** poli zobrazeném po zaškrtnuté políčko. Prostředků zadáte ID musí být pro virtuální síť, která existuje ve stejném, nebo [nepodporuje různé](#requirements-and-constraints) Azure [oblasti](https://azure.microsoft.com/regions) jako tato virtuální síť. Úplné ID prostředku vypadá podobně jako /subscriptions/<Id>/providers/Microsoft.Network/virtualNetworks/ < virtual-network-name > /resourceGroups/ < resource-group-name >. ID prostředku pro virtuální síť můžete získat zobrazením vlastností pro virtuální síť. Zjistěte, jak zobrazit vlastnosti pro virtuální síť, najdete v článku [Správa virtuálních sítí](manage-virtual-network.md#view-virtual-networks-and-settings). Pokud předplatné na jiného tenanta Azure Active Directory než předplatné s virtuální sítí vytváření partnerských vztahů ze souvisí, z každého tenanta jako nejprve přidat uživatele [uživatele typu Host](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) v opačné tenanta.
    - **Předplatné:** Vyberte [předplatné](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) ve virtuální síti, kterou chcete vytvořit partnerský vztah. Jeden nebo více odběrů jsou uvedeny v závislosti na tom, kolik předplatných má váš účet přístup pro čtení. Pokud jste zaškrtli možnost **ID prostředku** zaškrtávací políčko, toto nastavení není k dispozici.
    - **Virtuální síť:** Vyberte virtuální síť, kterou chcete vytvořit partnerský vztah. Můžete vybrat virtuální síť vytvořena prostřednictvím obou modelů nasazení Azure. Pokud chcete vybrat virtuální síť v jiné oblasti, musíte vybrat virtuální sítě [podporované oblasti](#cross-region). Musíte mít přístup pro čtení k virtuální síti, aby se nezobrazí v seznamu. Pokud virtuální síť je uvedené, ale nejde aktivovat, pravděpodobně protože adresní prostor virtuální sítě se překrývá s adresním prostorem pro tuto virtuální síť. Pokud virtuální síť adresní prostory překrývají, jsou nejde navázat partnerský vztah. Pokud jste zaškrtli možnost **ID prostředku** zaškrtávací políčko, toto nastavení není k dispozici.
    - **Povolit přístup k virtuální síti:** Vyberte **povoleno** (výchozí), pokud chcete povolit komunikaci mezi dvěma virtuálními sítěmi. Povolení komunikace mezi virtuálními sítěmi umožní prostředkům připojeným k obou virtuálních sítích komunikovat mezi sebou stejnou šířku pásma a čekací doba jakoby byly připojené ke stejné virtuální síti. Veškerá komunikace mezi prostředky ve dvou virtuálních sítích je v Azure privátní síti. **VirtualNetwork** značka služby pro skupiny zabezpečení sítě zahrnuje virtuální síť a partnerské virtuální síti. Další informace o značkách služby skupiny zabezpečení sítě najdete v tématu [přehled skupin zabezpečení sítě](security-overview.md#service-tags). Vyberte **zakázané** Pokud nechcete, aby provoz směrovat do partnerské virtuální síti. Můžete vybrat **zakázané** Pokud jste partnerský vztah virtuální sítě s jinou virtuální sítí, ale někdy chcete zakázat toku provozu mezi dvěma virtuálními sítěmi. Může se stát, že povolení/zákazu je pohodlnější než odstraníte a znovu vytvoříte partnerské vztahy. Pokud je toto nastavení zakázáno, tok nebude provoz mezi partnerskými virtuálními sítěmi.
    - **Povolit přesměrovaný přenos:** Zaškrtnutím tohoto políčka Povolit provoz *předané* podle síťové virtuální zařízení ve virtuální síti (která neměli pocházejí z virtuální sítě) do služby flow k této virtuální síti přes partnerské vztahy. Představte si třeba tři virtuální sítě s názvem Spoke1 Spoke2 a rozbočovače. Partnerský vztah mezi virtuální síť každého paprsku a centrální virtuální síti existuje, ale neexistují partnerských vztahů mezi virtuálními sítěmi paprsků. Síťové virtuální zařízení je nasazena v centrální virtuální síti a trasy definované uživatelem se použijí pro virtuální síť každého paprsku, která směrovat provoz mezi podsítěmi přes síťové virtuální zařízení. Pokud je toto políčko není zaškrtnuto pro partnerský vztah mezi virtuální síť každého paprsku a centrální virtuální síti, přenos není tok mezi virtuálními sítěmi paprsků protože centra je přesměrování provozu mezi virtuálními sítěmi. Když povolíte tuto funkci umožňuje přesměrované přenosy přes partnerský vztah, nevytvoří všechny trasy definované uživatelem nebo síťových virtuálních zařízení. Uživatelem definované trasy a síťových virtuálních zařízení se vytvářejí zvlášť. Další informace o [trasy definované uživatelem](virtual-networks-udr-overview.md#user-defined). Nemusíte kontrolovat toto nastavení, pokud přenosy mezi virtuálními sítěmi pomocí služby Azure VPN Gateway předávaly.
    - **Povolit průchod bránou:** Toto políčko zaškrtněte, pokud máte bránu virtuální sítě připojené k této virtuální síti a chcete povolit provoz z partnerské virtuální síti, které jsou předávány přes bránu. Tato virtuální síť může být například připojené k místní síti přes bránu virtuální sítě. Může jí být služby ExpressRoute nebo VPN gateway. Zaškrtnutím tohoto políčka umožní provoz z partnerské virtuální síti tok přes bránu připojit tuto virtuální síť k místní síti. Pokud zaškrtnete toto políčko, navázaným partnerským vztahem virtuální síť nemůže mít nakonfigurovanou bránu. Partnerské virtuální síti musí mít **používat vzdálené brány** zaškrtnutí políčka při nastavování partnerského připojení z druhé virtuální sítě do této virtuální sítě. Necháte-li toto není zaškrtnuto políčko (výchozí), provoz z partnerské virtuální síti stále toky do této virtuální síti, ale nelze procházet skrz bránu virtuální sítě připojené k této virtuální síti. Pokud partnerský vztah mezi virtuální sítí (Resource Manager) a virtuální síť (classic), musí být brána virtuální sítě (Resource Manager). Tuto možnost nelze povolit, pokud máte partnerské vztahy virtuálních sítí v různých oblastech.
    
        Kromě předávají provoz směřující do místní sítě, můžete bránu VPN směrovat síťový provoz mezi virtuálními sítěmi, které jsou v partnerském vztahu s virtuální sítí, brána se nachází v, aniž by bylo potřeba vytvořit partnerský vztah mezi sebou virtuální sítě. Přes bránu VPN směrovat provoz je užitečná, pokud chcete používat bránu sítě VPN v rozbočovači (podívejte se na střed a paprsek příklad popsaných **povolit přesměrovaný přenos**) směrovat provoz mezi virtuálními sítěmi paprsků, které nejsou virtuální sítě partnerský vztah mezi sebou. Další informace o povolení použití brány pro průchod najdete v tématu [konfigurovat bránu VPN k přenosu v partnerského vztahu virtuálních sítí](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tento scénář vyžaduje implementace trasy definované uživatelem, které jako typ dalšího segmentu směrování zadat bránu virtuální sítě. Další informace o [trasy definované uživatelem](virtual-networks-udr-overview.md#user-defined). Bránu sítě VPN lze zadat pouze jako typ dalšího segmentu směrování v uživatelsky definované trase, bránu ExpressRoute nelze zadat jako typ dalšího segmentu směrování v uživatelsky definované trase. Tuto možnost nelze povolit, pokud máte partnerské vztahy virtuálních sítí v různých oblastech.

    - **Používat vzdálené brány:** Zaškrtněte toto políčko, aby přijímaly provoz z této virtuální sítě probíhá přes bránu virtuální sítě připojené k virtuální síti, se partnerský vztah. Například virtuální síť, kterou jste partnerský vztah má bránu VPN připojený, která umožňuje komunikaci s místní sítí.  Zaškrtnutím tohoto políčka umožní provoz z této virtuální sítě k toku prostřednictvím brány VPN, které jsou připojené k partnerské virtuální síti. Pokud zaškrtnete toto políčko, musí mít bránu virtuální sítě připojené k němu partnerské virtuální síti a musí mít **povolit průchod bránou** zaškrtnutí políčka. Pokud necháte toto není zaškrtnuto políčko (výchozí), provoz z partnerské virtuální síti může stále probíhat k této virtuální síti, ale nemůže flow přes bránu virtuální sítě připojen na tento virtuální sítě. 
    Pouze jeden partnerský vztah pro tuto virtuální síť může mít toto nastavení povolené.

        Pokud už máte nakonfigurovanou ve vaší virtuální síti bránu, nemůže používat vzdálené brány. Tuto možnost nelze povolit, pokud máte partnerské vztahy virtuálních sítí v různých oblastech. Další informace o použití brány pro přenosu, naleznete v tématu [konfigurovat bránu VPN k přenosu v partnerského vztahu virtuálních sítí](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

7. Vyberte **OK** se přidat partnerský vztah virtuální sítě, které jste vybrali.

Podrobné pokyny k implementaci partnerský vztah mezi virtuálními sítěmi v různých předplatných a modelech nasazení najdete v tématu [další kroky](#next-steps). 


### <a name="commands"></a>Příkazy

- **Azure CLI**: [vytvořit partnerský vztah virtuální sítě az sítě](/cli/azure/network/vnet/peering#create)
- **PowerShell**: [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Zobrazit nebo změnit nastavení partnerského vztahu

Před změnou, partnerský vztah, seznamte se s [požadavky a omezení](#requirements-and-contstraints) a [potřebná oprávnění](#permissions).

1. Do vyhledávacího pole v horní části portálu zadejte *virtuálních sítí* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ji. Nesmí být zvolen **virtuální sítě (klasické)** Pokud se zobrazí v seznamu, nelze vytvoříte partnerské připojení z virtuální sítě nasazené pomocí modelu nasazení classic.
2. V seznamu, který chcete změnit nastavení partnerského vztahu pro výběr virtuální sítě.
3. Seznam virtuálních sítí vyberte virtuální síť, kterou chcete změnit nastavení partnerského vztahu pro.
4. V části **nastavení**vyberte **partnerské vztahy**.
5. Vyberte partnerské vztahy, které chcete zobrazit nebo změnit nastavení pro.
6. Změňte odpovídající nastavení. Přečtěte si o možnostech pro každé nastavení v [kroku 6](#add-peering) vytvořit partnerský vztah. 
7. Vyberte **Uložit**.

**Příkazy**

- **Azure CLI**: [az sítě vnet partnerského vztahu seznam](/cli/azure/network/vnet/peering) na seznam partnerských vztahů pro virtuální síť, [az sítě zobrazit partnerského vztahu virtuálních sítí](/cli/azure/network/vnet/peering#az_network_vnet_peering_show) zobrazit nastavení pro konkrétní partnerského vztahu a [az network aktualizace partnerského vztahu virtuálních sítí](/cli/azure/network/vnet/peering#az_network_vnet_peering_update) Chcete-li změnit nastavení partnerského vztahu. |
- **PowerShell**: [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) načíst zobrazení nastavení partnerského vztahu a [Set-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/set-azurermvirtualnetworkpeering) Chcete-li změnit nastavení.

## <a name="delete-a-peering"></a>Odstranit partnerský vztah

Před odstraňuje se partnerský vztah, ujistěte, že má váš účet [potřebná oprávnění](#permissions).

Při odstranění partnerského vztahu, provoz z virtuální sítě už vede do partnerské virtuální síti. Po vytvoření partnerského vztahu virtuální sítě nasazené prostřednictvím Resource Manageru, každá virtuální síť má partnerský vztah k jiné virtuální síti. I když odstraňuje se partnerský vztah z jedné virtuální sítě zakáže komunikaci mezi virtuálními sítěmi, ale neodstraníte partnerského připojení z druhé virtuální sítě. Stav partnerského vztahu pro partnerský vztah, který existuje ve virtuální síti je **odpojeno**. Nelze znovu vytvořit partnerský vztah dokud je znovu vytvořit partnerský vztah v první virtuální sítě a stav partnerského vztahu pro obě virtuální sítě změny *připojeno*. 

Pokud chcete, aby virtuální sítě ke komunikaci v některých případech, ale ne vždy místo odstraňuje se partnerský vztah, můžete nastavit **povolit přístup k virtuální síti** nastavení **zakázané** místo toho. Další informace, jak číst kroku 6 postupu [vytvoření partnerského vztahu](#create-peering) části tohoto článku. Může se stát zakázání a povolení přístupu k síti je jednodušší než odstranili a znovu vytvořili partnerské vztahy.

1. Do vyhledávacího pole v horní části portálu zadejte *virtuálních sítí* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ji. Nesmí být zvolen **virtuální sítě (klasické)** Pokud se zobrazí v seznamu, nelze vytvoříte partnerské připojení z virtuální sítě nasazené pomocí modelu nasazení classic.
2. Vyberte v seznamu, který chcete odstranit partnerský vztah virtuální sítě.
3. Seznam virtuálních sítí vyberte virtuální síť, kterou chcete odstranit partnerský vztah.
4. V části **nastavení**vyberte **partnerské vztahy**.
5. Na pravé straně partnerského vztahu chcete odstranit, vyberte **...** vyberte **odstranit**a pak vyberte **Ano** odstranění partnerského vztahu z první virtuální sítě.
6. Dokončete předchozí kroky pro odstranění partnerského vztahu z druhé virtuální sítě v partnerském vztahu.

**Příkazy**

- **Azure CLI**: [az sítě vnet partnerského vztahu delete](/cli/azure/network/vnet/peering#az_network_vnet_peering_delete)
- **PowerShell**: [Remove-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/remove-azurermvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Požadavky a omezení 

- <a name="cross-region"></a>Po vytvoření partnerského vztahu virtuálních sítí ve stejné oblasti nebo v různých oblastech. Vytvoření partnerského vztahu virtuálních sítí v různých oblastech se také označuje jako *globální partnerský vztah*. 
- Při vytváření globální partnerský vztah, mohou existovat partnerských virtuálních sítích v libovolné oblasti Azure veřejného cloudu, ale ne v národních cloudů Azure. Může pouze vytvořit partnerský vztah virtuálních sítí ve stejné oblasti v národních cloudech.
- Prostředky v jedné virtuální sítě nemůže komunikovat s front-endovou IP adresu Azure interního nástroje load balancer v globálním partnerském vztahu virtuální sítě. Nástroje pro vyrovnávání zatížení a prostředky, které s ním komunikují, musí být ve virtuální síti ve stejné oblasti. Pokud partnerských virtuálních sítích se ale ve stejné oblasti, můžete prostředky v obou virtuálních sítích komunikovat s front-endovou IP adresu Azure interního nástroje load balancer v obou virtuálních sítích v partnerském vztahu.
- Nelze používat vzdálené brány nebo povolit průchod bránou v globálním partnerském vztahu virtuálních sítí. Pokud chcete používat vzdálené brány nebo povolit průchod bránou, musí být partnerských virtuálních sítích ve stejné oblasti.
- Virtuální sítě může být ve stejném ani jiném předplatném. Po vytvoření partnerského vztahu virtuálních sítí v různých předplatných, dá se přidružit k stejného nebo jiného tenanta Azure Active Directory oběma předplatným. Pokud ještě nemáte klient služby AD, můžete rychle [vytvořit](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant). Podpora pro vytvoření partnerského vztahu mezi virtuálními sítěmi z předplatných přidružených k různým tenantům Azure Active Directory není k dispozici na portálu. Můžete použít rozhraní příkazového řádku, Powershellu nebo šablony.
- Virtuální sítě, které můžete vytvořit partnerský vztah musí mít překrývat adresní prostory IP adres.
- Nelze přidat rozsahy adres do nebo odstranit rozsahy adres z adresního prostoru virtuální sítě, jakmile se virtuální síť má partnerský vztah s jinou virtuální sítí. Chcete-li přidat nebo odebrat rozsahy adres, odstranit partnerský vztah, přidat nebo odebrat rozsahy adres, pak znovu vytvořte partnerský vztah. Přidat rozsahy adres k nebo odebrat rozsahy adres virtuální sítě, najdete v článku [Správa virtuálních sítí](manage-virtual-network.md).
- Můžete vytvořit partnerský vztah dvou virtuálních sítí, které jsou nasazené prostřednictvím Resource Manageru nebo virtuální sítě nasazené prostřednictvím Resource Manageru pomocí virtuální sítě nasazené pomocí modelu nasazení classic. Nejde vytvořit partnerský vztah dvou virtuálních sítí vytvořených prostřednictvím modelu nasazení classic. Pokud nejste obeznámeni s modely nasazení Azure, přečtěte si [vysvětlení modelů nasazení Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) článku. K propojení dvou virtuálních sítí vytvořených prostřednictvím modelu nasazení Classic můžete použít službu [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V).
- Pokud navazujete partnerský vztah mezi dvěma virtuálními sítěmi vytvořenými prostřednictvím Resource Manageru, v partnerském vztahu musí být nakonfigurován partnerský vztah pro každou virtuální síť. Se zobrazí jedna z následujících typů pro stav partnerského vztahu: 
    - *Zahájeno:* Když vytvoříte partnerské připojení k druhé virtuální sítě z první virtuální sítě, stav partnerského vztahu je *iniciováno*. 
    - *Připojeno:* Když vytvoříte partnerské připojení z druhé virtuální sítě k první virtuální sítě, stav partnerského vztahu je *připojeno*. Pokud zobrazíte stav partnerského vztahu pro první virtuální sítě, zobrazí se její stav se změnil z *iniciováno* k *připojeno*. Partnerský vztah není úspěšně navázán, dokud stav partnerského vztahu pro obě partnerské vztahy virtuálních sítí je *připojeno*.
- Při vytváření partnerského vztahu virtuální sítě vytvořené pomocí Resource Manageru s virtuální sítí vytvořenou prostřednictvím modelu nasazení classic, je pouze nakonfigurovat partnerský vztah virtuální sítě, která je nasazena prostřednictvím Resource Manageru. Nejde nakonfigurovat partnerský vztah virtuální sítě (classic), nebo mezi dvě virtuální sítě nasazené pomocí modelu nasazení classic. Když vytvoříte partnerské připojení z virtuální sítě (Resource Manager) k virtuální síti (Classic), stav partnerského vztahu je *aktualizace*, pak se brzy změní na *připojeno*.
- Mezi dvěma virtuálními sítěmi se naváže partnerský vztah. Partnerské vztahy jsou nepřenosné. Pokud vytvoříte partnerské vztahy mezi:
    - VirtualNetwork1 & VirtualNetwork2
    - VirtualNetwork2 & VirtualNetwork3

  Neexistuje žádné partnerský vztah mezi VirtualNetwork1 a VirtualNetwork3 prostřednictvím VirtualNetwork2. Pokud chcete vytvořit partnerský vztah mezi VirtualNetwork1 a VirtualNetwork3, budete muset vytvořit partnerský vztah mezi VirtualNetwork1 a VirtualNetwork3.
- Nelze přeložit názvy v partnerských virtuálních sítích pomocí výchozí rozlišování názvů Azure. K překladu názvů v dalším virtuálním sítím, je nutné použít [Azure DNS pro privátní domény](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo vlastního serveru DNS. Další informace o nastavení serveru DNS, najdete v článku [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Prostředky v partnerských virtuálních sítích ve stejné oblasti můžete komunikovat mezi sebou na šířku pásma a latencí jako kdyby byly ve stejné virtuální síti. Jednotlivé velikosti virtuálního počítače však má svou vlastní maximální šířku pásma sítě. Další informace o maximální šířce pásma pro různých velikostí virtuálních počítačů najdete v tématu [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) velikosti virtuálních počítačů.
- Virtuální síť může být v partnerském vztahu s jinou virtuální sítí a také být připojené k jiné virtuální síti s bránou virtuální sítě Azure. Pokud virtuální sítě jsou propojené prostřednictvím partnerského vztahu a bránu, provoz mezi virtuálními sítěmi prochází konfigurace partnerského vztahu, nikoli brány.
- Klienti VPN Point-to-Site musí znova stáhnout po vytvoření partnerského vztahu virtuální sítě byl úspěšně nakonfigurován na Ujistěte se, že nové postupy se stáhnou do klienta.
- Za příchozí a výchozí přenos dat využívající partnerský vztah virtuálních sítí se účtuje nominální poplatek. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Oprávnění

Tyto účty, které můžete použít pro práci se partnerský vztah virtuální sítě musí mít přiřazenou následující role:

- [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): Pro virtuální síť nasazena prostřednictvím Resource Manageru.
- [Přispěvatel klasických sítí](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): Pro virtuální sítě nasazené pomocí modelu nasazení classic.

Pokud váš účet nemá přiřazenou k jednomu z předchozích rolí, musíte být přiřazeni k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , který je přiřazen nezbytné akce z následující tabulky:

| Akce | Název |
|---|---|
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Potřebný k vytvoření partnerského vztahu virtuální sítě A virtuální sítí b virtuální síti A musí být virtuální síť (Resource Manager)                            |
| Microsoft.Network/virtualNetworks/peer/action                   | Potřebný k vytvoření partnerského vztahu virtuální síť B (Resource Manager) k virtuální síti A                                                                                |
| Microsoft.ClassicNetwork/virtualNetworks/peer                   | Potřebný k vytvoření partnerského vztahu virtuální síť B (classic) k virtuální síti A                                                                                    |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Přečtěte si partnerského vztahu virtuálních sítí   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Odstranění partnerského vztahu virtuálních sítí |

## <a name="next-steps"></a>Další postup

* Partnerský vztah virtuálních sítí se vytváří mezi virtuálními sítěmi vytvořenými prostřednictvím stejného modelu nebo různých modelů nasazení, které existují ve stejném předplatném nebo různých předplatných. Dokončete kurz pro jeden z následujících scénářů:

    |Model nasazení Azure             | Předplatné  |
    |---------                          |---------|
    |Obě Resource Manager              |[Stejné](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Různé](create-peering-different-subscriptions.md)|
    |Jedna Resource Manager, druhá Classic  |[Stejné](create-peering-different-deployment-models.md)|
    |                                   |[Různé](create-peering-different-deployment-models-subscriptions.md)|

* Zjistěte, jak vytvořit [rozbočovač a uvedenou síťovou topologii](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Vytvoření partnerského vztahu virtuálních sítí pomocí [PowerShell](powershell-samples.md) nebo [rozhraní příkazového řádku Azure](cli-samples.md) ukázkové skripty nebo pomocí Azure [šablon Resource Manageru](template-samples.md)
* Vytvoření a použití [Azure policy](policy-samples.md) pro virtuální sítě
