---
title: Vytvoření, změna nebo odstranění partnerského vztahu virtuální sítě Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak vytvořit, změnit nebo odstranit partnerský vztah virtuální sítě.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: anavin
ms.openlocfilehash: 97acac61d0397a4e13fb64d39a6aba92e4de2afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123312"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Vytvoření, změna nebo odstranění partnerského vztahu virtuální sítě

Přečtěte si, jak vytvořit, změnit nebo odstranit partnerský vztah virtuální sítě. Partnerský vztah virtuální sítě umožňuje připojit virtuální sítě ve stejné oblasti a napříč oblastmi (označované také jako globální partnerský vztah virtuální sítě) prostřednictvím páteřní sítě Azure. Po partnerské síti jsou virtuální sítě stále spravovány jako samostatné prostředky. Pokud s partnerským vztahem virtuální chýše tečte, můžete se o něm dozvědět víc v [přehledu partnerského vztahu virtuální sítě](virtual-network-peering-overview.md) nebo dokončením [kurzu](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před dokončením kroků v libovolné části tohoto článku proveďte následující úkoly:

- Pokud ještě nemáte účet Azure, zaregistrujte si [bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí účtu, který má [potřebná oprávnění](#permissions) pro práci s partnerskými partnery.
- Pokud pomocí příkazů Prostředí PowerShell k dokončení úloh v tomto článku, buď spustit příkazy v [Prostředí Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte Prostředí PowerShell místně, musíte `Connect-AzAccount` také spustit s účtem, který má [potřebná oprávnění](#permissions) pro práci s partnerským vztahem, k vytvoření připojení s Azure.
- Pokud pomocí příkazů rozhraní Příkazového řádku Azure (CLI) k dokončení úloh v tomto článku, buď spustit příkazy v [prostředí Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také `az login` spustit s účtem, který má [potřebná oprávnění](#permissions) pro práci s partnerským vztahem, k vytvoření připojení s Azure.

Účet, ke kterému se přihlásíte nebo se s ním připojíte, musí být přiřazen k roli [síťového přispěvatele](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní roli,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) které jsou přiřazeny příslušné akce uvedené v [části Oprávnění](#permissions).

## <a name="create-a-peering"></a>Vytvoření partnerského vztahu

Před vytvořením partnerského vztahu se seznamte s požadavky a omezeními a [nezbytnými oprávněními](#permissions).

1. Do vyhledávacího pole v horní části portálu Azure zadejte do vyhledávacího pole *virtuální sítě.* Když se **virtuální sítě** zobrazí ve výsledcích hledání, vyberte je. Nevybírejte **virtuální sítě (klasické),** pokud se zobrazí v seznamu, protože nelze vytvořit partnerský vztah z virtuální sítě nasazené prostřednictvím modelu klasického nasazení.
2. Vyberte virtuální síť v seznamu, pro který chcete vytvořit partnerský vztah.
3. V části **NASTAVENÍ**vyberte **Peerings**.
4. Vyberte **+ Přidat**. 
5. <a name="add-peering"></a>Zadejte nebo vyberte hodnoty pro následující nastavení:
    - **Název:** Název partnerského vztahu musí být jedinečný v rámci virtuální sítě.
    - **Model nasazení virtuální sítě:** Vyberte, který model nasazení virtuální síť, se kterou chcete spolupracovat, byl nasazen.
    - **Znám své ID zdroje:** Pokud máte přístup pro čtení do virtuální sítě, se kterou chcete spolupracovat, ponechte toto políčko nezaškrtnuté. Pokud nemáte přístup pro čtení do virtuální sítě nebo předplatného, se kterým chcete spolupracovat, zaškrtněte toto políčko. Do pole **ID prostředku,** které se zobrazilo, když jste toto políčko zaškrtli, zadejte úplné ID prostředku virtuální sítě, se kterou chcete partnerské zařízení. ID prostředku, které zadáte, musí být pro virtuální síť, která existuje ve stejné nebo [podporované jiné](#requirements-and-constraints) [oblasti](https://azure.microsoft.com/regions) Azure jako tato virtuální síť. Úplné ID prostředku vypadá `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>`podobně jako . ID prostředku pro virtuální síť můžete získat zobrazením vlastností virtuální sítě. Informace o tom, jak zobrazit vlastnosti virtuální sítě, najdete v [tématu Správa virtuálních sítí](manage-virtual-network.md#view-virtual-networks-and-settings). Pokud je předplatné přidružené k jinému tenantovi služby Azure Active Directory než k předplatnému s virtuální sítí, ze které vytváříte partnerský vztah, nejprve přidejte uživatele z každého klienta jako [uživatele typu Host](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) v opačném tenantovi.
    - **Předplatné:** Vyberte [předplatné](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) virtuální sítě, se kterou chcete spolupracovat. V seznamu je uvedeno jedno nebo více předplatných v závislosti na počtu předplatných, ke kterých má váš účet přístup pro čtení. Pokud jste zaškrtli políčko **ID prostředku,** toto nastavení není k dispozici.
    - **Virtuální síť:** Vyberte virtuální síť, se kterou chcete spolupracovat. Můžete vybrat virtuální síť vytvořenou buď pomocí modelu nasazení Azure. Pokud chcete vybrat virtuální síť v jiné oblasti, musíte vybrat virtuální síť v [podporované oblasti](#cross-region). Musíte mít přístup pro čtení do virtuální sítě, aby byla viditelná v seznamu. Pokud je virtuální síť v seznamu uvedena, ale zobrazená, může to být proto, že adresní prostor pro virtuální síť se překrývá s adresním prostorem pro tuto virtuální síť. Pokud se adresní prostory virtuální sítě překrývají, nelze je peered. Pokud jste zaškrtli políčko **ID prostředku,** toto nastavení není k dispozici.
    - **Povolit přístup k virtuální síti:** Pokud chcete povolit komunikaci mezi dvěma virtuálními sítěmi, vyberte **možnost Povoleno** (výchozí). Povolení komunikace mezi virtuálními sítěmi umožňuje prostředkům připojeným k jedné virtuální síti vzájemnou komunikaci se stejnou šířkou pásma a latencí, jako kdyby byly připojeny ke stejné virtuální síti. Veškerá komunikace mezi prostředky ve dvou virtuálních sítích probíhá přes privátní síť Azure. Značka služby **VirtualNetwork** pro skupiny zabezpečení sítě zahrnuje virtuální síť a virtuální síť partnerského vztahu. Další informace o značkách služby skupiny zabezpečení sítě naleznete v [tématu Přehled skupin zabezpečení sítě](security-overview.md#service-tags). Pokud nechcete, aby se provoz do partnerské virtuální sítě tokoval, vyberte **Zakázáno.** Můžete vybrat **Zakázáno,** pokud jste peered virtuální síť s jinou virtuální sítí, ale občas chcete zakázat tok provozu mezi dvěma virtuálními sítěmi. Povolení nebo zakázání je pro vás pohodlnější než odstranění a opětovné vytvoření partnerských stran. Pokud je toto nastavení zakázáno, provoz netokuje mezi partnerskými virtuálními sítěmi.
    - **Povolit předávaný provoz:** Zaškrtnutím tohoto políčka povolíte, aby provoz *předaný* síťovým virtuálním zařízením ve virtuální síti (která nepochází z virtuální sítě) tok do této virtuální sítě prostřednictvím partnerského vztahu. Zvažte například tři virtuální sítě s názvem Spoke1, Spoke2 a Hub. Mezi jednotlivými virtuálními sítěmi pro paprsky a virtuální sítí Hub existuje partnerský vztah, ale mezi virtuálními sítěmi pro paprsky neexistují partnerské servery. Virtuální síťové zařízení se nasazuje ve virtuální síti Hub a pro každou virtuální síť s paprsky se použijí uživatelem definované trasy, které směrují provoz mezi podsítěmi prostřednictvím virtuálního síťového zařízení. Pokud toto políčko není zaškrtnuto pro partnerský vztah mezi každou virtuální sítí s paprsky a virtuální sítí rozbočovače, provoz neprobíhá mezi virtuálními sítěmi s paprsky, protože rozbočovač nepředává přenosy mezi virtuálními sítěmi. Při povolení této funkce umožňuje předávaný provoz prostřednictvím partnerského vztahu, nevytváří žádné uživatelem definované trasy nebo síťová virtuální zařízení. Uživatelem definované trasy a síťová virtuální zařízení jsou vytvářeny samostatně. Informace o [uživatelem definovaných trasách](virtual-networks-udr-overview.md#user-defined). Toto nastavení nemusíte kontrolovat, pokud se přenos y předávají mezi virtuálními sítěmi prostřednictvím brány Azure VPN.
    - **Povolit tranzit brány:** Toto políčko zaškrtněte, pokud máte bránu virtuální sítě připojenou k této virtuální síti a chcete povolit tok provozu z partnerské virtuální sítě. Tato virtuální síť může být například připojena k místní síti prostřednictvím brány virtuální sítě. Bránou může být brána ExpressRoute nebo VPN. Zaškrtnutí tohoto políčka umožňuje přenosy z partnerské virtuální sítě tok přes bránu připojenou k této virtuální síti do místní sítě. Pokud zaškrtnete toto políčko, virtuální síť s partnerským vztahem nemůže mít nakonfigurovanou bránu. Při nastavování partnerského vztahu z jiné virtuální sítě do této virtuální sítě musí být zaškrtnuto políčko **Použít vzdálené brány.** Pokud toto políčko ponecháte nezaškrtnuté (výchozí), provoz z partnerské virtuální sítě stále proudí do této virtuální sítě, ale nemůže tok přes bránu virtuální sítě připojené k této virtuální síti. Pokud je partnerský vztah mezi virtuální sítí (Správce prostředků) a virtuální sítí (klasickou), brána musí být ve virtuální síti (Resource Manager).

       Kromě předávání přenosů do místní sítě může brána VPN předávat síťový provoz mezi virtuálními sítěmi, které jsou partnerské společnosti s virtuální sítí, ve které se brána nachází, aniž by virtuální sítě musely být vzájemně propojeny. Použití brány VPN k předávání provozu je užitečné, když chcete použít bránu VPN v rozbočovači (viz příklad rozbočovače a paprsku popsaný pro **Povolit přeposílaný provoz**) virtuální sítě pro směrování provozu mezi virtuálními sítěmi s paprsky, které nejsou vzájemně propojeny. Další informace o povolení používání brány pro přenos najdete v [tématu Konfigurace brány VPN pro přenos v partnerské síti virtuální sítě](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tento scénář vyžaduje implementaci uživatelem definovaných tras, které určují bránu virtuální sítě jako další typ směrování. Informace o [uživatelem definovaných trasách](virtual-networks-udr-overview.md#user-defined). Bránu VPN můžete zadat pouze jako další typ směrování v uživatelem definované trase, bránu ExpressRoute nelze zadat jako další typ směrování v uživatelem definované trase.

    - **Používejte vzdálené brány:** Zaškrtnutím tohoto políčka povolíte přenosy z této virtuální sítě toku přes bránu virtuální sítě připojenou k virtuální síti, se kterou jste partnerovitou. Například virtuální síť, se kterou partnerská síť má připojenou bránu VPN, která umožňuje komunikaci s místní sítí.  Zaškrtnutí tohoto políčka umožňuje přenosy z této virtuální sítě tok přes bránu VPN připojené k partnerské virtuální síti. Pokud toto políčko zaškrtnete, partnerská virtuální síť musí mít k němu připojenou bránu virtuální sítě a musí mít zaškrtnuté políčko **Povolit přenos brány.** Pokud toto políčko ponecháte nezaškrtnuté (výchozí), provoz z partnerské virtuální sítě může stále tok do této virtuální sítě, ale nemůže tok přes bránu virtuální sítě připojené k této virtuální síti.
    
      Toto nastavení může být povoleno pouze v jednom partnerském vztahu pro tuto virtuální síť.

      Vzdálené brány nelze použít, pokud už máte bránu nakonfigurovanou ve virtuální síti. Další informace o používání brány pro přenos najdete v [tématu Konfigurace brány VPN pro přenos v partnerské síti virtuální sítě.](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        
    > [!NOTE]
    > Pokud používáte bránu virtuální sítě k přenositetelně odesílat místní provoz do partnerské sítě, rozsah IP virtuální sítě pro místní zařízení VPN musí být nastavenna na "zajímavý" provoz. V opačném případě vaše místní prostředky nebudou moct komunikovat s prostředky v partnerské virtuální síti.

6. Výběrem **možnosti OK** přidáte partnerský vztah do vybrané virtuální sítě.

Podrobné pokyny pro implementaci partnerského vztahu mezi virtuálními sítěmi v různých předplatných a modelech nasazení naleznete [v dalších krocích](#next-steps).

### <a name="commands"></a>Příkazy

- **Azure CLI:** vytvoření [partnerského vztahu síťové sítě az](/cli/azure/network/vnet/peering)
- **Prostředí PowerShell:** [Přidání virtuálního partnera sítě Az](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Zobrazení nebo změna nastavení partnerského vztahu

Před změnou partnerského vztahu se seznamte s požadavky a omezeními a [nezbytnými oprávněními](#permissions).

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *virtuální sítě.* Když se **virtuální sítě** zobrazí ve výsledcích hledání, vyberte je. Nevybírejte **virtuální sítě (klasické),** pokud se zobrazí v seznamu, protože nelze vytvořit partnerský vztah z virtuální sítě nasazené prostřednictvím modelu klasického nasazení.
2. Vseznamu, pro který chcete změnit nastavení partnerského vztahu, vyberte virtuální síť.
3. V části **NASTAVENÍ**vyberte **Peerings**.
4. Vyberte partnerský vztah, pro který chcete zobrazit nebo změnit nastavení.
5. Změňte příslušné nastavení. Přečtěte si o možnostech pro každé nastavení v [kroku 5](#add-peering) vytvoření partnerského vztahu.
6. Vyberte **Uložit**.

**Příkazy**

- **Azure CLI:** [seznam partnerského vztahu sítě az](/cli/azure/network/vnet/peering) seznam seznam partnerských uživatelů pro virtuální síť, [az síť partnerský vztah zobrazit](/cli/azure/network/vnet/peering) nastavení pro konkrétní partnerský vztah a aktualizace [partnerského vztahu sítě AZ](/cli/azure/network/vnet/peering) změnit nastavení partnerského vztahu.|
- **PowerShell**: [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) pro načtení nastavení partnerského vztahu zobrazení a [Set-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) pro změnu nastavení.

## <a name="delete-a-peering"></a>Odstranění partnerského vztahu

Před odstraněním partnerského vztahu se ujistěte, že váš účet má [potřebná oprávnění](#permissions).

Při odstranění partnerského vztahu, provoz z virtuální sítě již toky do partnerského vztahu virtuální sítě. Když virtuální sítě nasazené prostřednictvím Správce prostředků jsou partnerský vztah, každá virtuální síť má partnerský vztah k jiné virtuální síti. Při odstranění partnerského vztahu z jedné virtuální sítě zakáže komunikaci mezi virtuálními sítěmi, neodstraní partnerský vztah z jiné virtuální sítě. Stav partnerského vztahu pro partnerský vztah, který existuje v jiné virtuální síti, je **Odpojeno**. Partnerský vztah nelze znovu vytvořit, dokud znovu nevytvoříte partnerský vztah v první virtuální síti a stav partnerského vztahu pro obě virtuální sítě se změní na *Připojeno*.

Pokud chcete, aby virtuální sítě někdy komunikovaly, ale ne vždy, místo toho, abyste zrušili partnerský vztah, můžete místo toho nastavit nastavení **Povolit přístup virtuální sítě** na **Zakázáno.** Chcete-li se dozvědět, jak, přečtěte si krok 6 vytvoření partnerského vztahu části tohoto článku. Může být zakázání a povolení přístupu k síti jednodušší než odstranění a opětovné vytvoření partnerských stran.

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *virtuální sítě.* Když se **virtuální sítě** zobrazí ve výsledcích hledání, vyberte je. Nevybírejte **virtuální sítě (klasické),** pokud se zobrazí v seznamu, protože nelze vytvořit partnerský vztah z virtuální sítě nasazené prostřednictvím modelu klasického nasazení.
2. Vyberte virtuální síť v seznamu, pro který chcete odstranit partnerský vztah.
3. V části **NASTAVENÍ**vyberte **Peerings**.
4. Na pravé straně partnerského vztahu, který chcete odstranit, vyberte **...**, vyberte **Odstranit**, a pak vyberte **Ano,** chcete-li partnerský vztah odstranit z první virtuální sítě.
5. Dokončete předchozí kroky k odstranění partnerského vztahu z jiné virtuální sítě v partnerské síti.

**Příkazy**

- **Azure CLI:** [odstranění partnerského vztahu síťové sítě az](/cli/azure/network/vnet/peering)
- **Prostředí PowerShell**: [Odebrání virtuálního partnera sítě AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Požadavky a omezení

- <a name="cross-region"></a>Virtuální sítě můžete partnerských ve stejné oblasti nebo v různých oblastech. Partnerský vztah virtuálních sítí v různých oblastech se také označuje jako *globální partnerský vztah virtuální sítě*. 
- Při vytváření globálního partnerského vztahu mohou virtuální sítě partnerského vztahu existovat v libovolné oblasti veřejného cloudu Azure nebo v oblastech cloudu v Číně nebo v cloudových oblastech s vládou. Nelze peer přes mraky. Například virtuální síť ve veřejném cloudu Azure nelze peered na virtuální síť v azure číně cloudu.
- Prostředky v jedné virtuální síti nemůžou komunikovat s front-endovou IP adresou interního nástroje pro vyrovnávání zatížení úrovně Basic ve virtuální síti v globálním partnerském vztahu. Podpora nástroje pro vyrovnávání zatížení úrovně Basic je k dispozici pouze ve stejné oblasti. Podpora nástroje pro vyrovnávání zatížení úrovně Standard je k dispozici pro VNET Peering i globální VNET Peering. Zde jsou zdokumentovány služby, které používají základní vyrovnávání zatížení, které nebudou fungovat přes globální partnerský vztah virtuální [sítě.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- Můžete použít vzdálené brány nebo povolit přechod brány v globálně partnerských virtuálních sítích a místně partnerských virtuálních sítích.
- Virtuální sítě může být ve stejné nebo různé odběry. Když se účastníte partnerských virtuálních sítí v různých předplatných, obě předplatná mohou být přidružena ke stejnému nebo jinému tenantovi služby Azure Active Directory. Pokud ještě nemáte klienta služby AD, můžete [ho vytvořit](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant). Podpora partnerského vztahu napříč virtuálními sítěmi od předplatných přidružených k různým klientům Služby Azure Active Directory není na portálu dostupná. Můžete použít CLI, PowerShell nebo šablony.
- Virtuální sítě, které partnerská partnerská sítě musí mít nepřekrývající se adresní prostory IP.
- Po vytvoření partnerského vztahu s jinou virtuální sítí nelze přidat rozsahy adres ani je odstranit z adresního prostoru virtuální sítě. Chcete-li přidat nebo odebrat rozsahy adres, odstraňte partnerský vztah, přidejte nebo odeberte rozsahy adres a potom partnerský vztah znovu vytvořte. Pokud chcete do virtuálních sítí přidat rozsahy adres nebo je odebrat, přečtěte si informace [o tom, jak spravovat virtuální sítě](manage-virtual-network.md).
- Můžete peer dvě virtuální sítě nasazené prostřednictvím Správce prostředků nebo virtuální sítě nasazené prostřednictvím Správce prostředků s virtuální síť nasazenou prostřednictvím klasického modelu nasazení. Nelze peer dvě virtuální sítě vytvořené prostřednictvím modelu klasické nasazení. Pokud nejste obeznámeni s modely nasazení Azure, přečtěte si článek [Porozumět modelům nasazení Azure.](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) K propojení dvou virtuálních sítí vytvořených prostřednictvím modelu nasazení Classic můžete použít službu [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V).
- Pokud navazujete partnerský vztah mezi dvěma virtuálními sítěmi vytvořenými prostřednictvím Resource Manageru, v partnerském vztahu musí být nakonfigurován partnerský vztah pro každou virtuální síť. Pro stav partnerského vztahu se zobrazí jeden z následujících typů: 
  - *Zahájeno:* Při vytváření partnerského vztahu do druhé virtuální sítě z první virtuální sítě je stav partnerského vztahu *Inicializován*. 
  - *Připojeno:* Když vytvoříte partnerský vztah z druhé virtuální sítě do první virtuální sítě, jeho stav partnerského vztahu je *Připojeno*. Pokud zobrazíte stav partnerského vztahu pro první virtuální síť, zobrazí se jeho stav změněn z *Zahájeno* na *Připojeno*. Partnerský vztah není úspěšně vytvořen, dokud není připojen stav partnerského vztahu pro oba partnerské *servery*virtuální sítě .
- Při partnerovém vztahu virtuální sítě vytvořené prostřednictvím Správce prostředků s virtuální sítí vytvořenou prostřednictvím klasického modelu nasazení nakonfigurujete pouze partnerský vztah pro virtuální síť nasazenou prostřednictvím Správce prostředků. Partnerský vztah nelze konfigurovat pro virtuální síť (klasickou) nebo mezi dvěma virtuálními sítěmi nasazenými prostřednictvím klasického modelu nasazení. Když vytvoříte partnerský vztah z virtuální sítě (Správce prostředků) do virtuální sítě (Classic), stav partnerského vztahu je *Aktualizace*, pak se brzy změní na *Připojeno*.
- Partnerský vztah je vytvořen mezi dvěma virtuálními sítěmi. Partnerských stran nejsou přenosné. Pokud vytváříte partnerské partnerské vztahy mezi:
  - VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3

  Neexistuje žádný partnerský vztah mezi VirtualNetwork1 a VirtualNetwork3 prostřednictvím VirtualNetwork2. Pokud chcete vytvořit partnerský vztah virtuální sítě mezi virtualnetwork1 a VirtualNetwork3, budete muset vytvořit partnerský vztah mezi VirtualNetwork1 a VirtualNetwork3.
- Názvy ve virtuálních sítích partnerského vztahu nelze přeložit pomocí výchozího překladu názvů Azure. Chcete-li přeložit názvy v jiných virtuálních sítích, musíte použít [Azure DNS pro privátní domény](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo vlastní server DNS. Informace o tom, jak nastavit vlastní server DNS, najdete v [tématu Překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Prostředky ve virtuálních sítích s partnerským partnerem ve stejné oblasti spolu mohou komunikovat se stejnou šířkou pásma a latencí, jako kdyby byly ve stejné virtuální síti. Každá velikost virtuálního počítače má však svou vlastní maximální šířku pásma sítě. Další informace o maximální šířce pásma sítě pro různé velikosti virtuálních strojů najdete v tématu Velikosti virtuálních počítačů [se systémem Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linux.](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Virtuální síť může být partnerský vztah k jiné virtuální síti a také připojenk jiné virtuální síti s bránou virtuální sítě Azure. Když jsou virtuální sítě připojené prostřednictvím partnerského vztahu i brány, přenosmezi virtuálními sítěmi protéká konfigurací partnerského vztahu, nikoli bránou.
- Po úspěšném nakonfigurování partnerského vztahu virtuální sítě na server site musí být klienti VPN znovu staženi, aby bylo zajištěno, že se nové trasy stáhnou do klienta.
- Za příchozí a výchozí přenos dat využívající partnerský vztah virtuálních sítí se účtuje nominální poplatek. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Oprávnění

Účty, které používáte pro práci s partnerským vztahem virtuální sítě, musí být přiřazeny k následujícím rolím:

- [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): Pro virtuální síť nasazenou prostřednictvím Správce prostředků.
- [Klasický síťový přispěvatel:](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)Pro virtuální síť nasazenou prostřednictvím klasického modelu nasazení.

Pokud váš účet není přiřazen k jedné z předchozích rolí, musí být přiřazen [k vlastní roli,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) které jsou přiřazeny potřebné akce z následující tabulky:

| Akce                                                          | Name (Název) |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Vyžadováno pro vytvoření partnerského vztahu z virtuální sítě A do virtuální sítě B. Virtuální síť A musí být virtuální síť (Resource Manager)          |
| Microsoft.Network/virtualNetworks/peer/action                   | Vyžadováno pro vytvoření partnerského vztahu z virtuální sítě B (Resource Manager) do virtuální sítě A                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | Vyžadováno vytvoření partnerského vztahu z virtuální sítě B (klasické) do virtuální sítě A                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Čtení partnerského vztahu ve virtuální síti   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Odstranění partnerského vztahu virtuální sítě |

## <a name="next-steps"></a>Další kroky

- Partnerský vztah virtuálních sítí se vytváří mezi virtuálními sítěmi vytvořenými prostřednictvím stejného modelu nebo různých modelů nasazení, které existují ve stejném předplatném nebo různých předplatných. Dokončete kurz pro jeden z následujících scénářů:

  |Model nasazení Azure             | Předplatné  |
  |---------                          |---------|
  |Obě Resource Manager              |[Stejné](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Různé](create-peering-different-subscriptions.md)|
  |Jedna Resource Manager, druhá Classic  |[Stejné](create-peering-different-deployment-models.md)|
  |                                   |[Různé](create-peering-different-deployment-models-subscriptions.md)|

- Zjistěte, jak vytvořit [rozbočovač a uvedenou síťovou topologii](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Vytvoření partnerského vztahu virtuální sítě pomocí ukázkových skriptů [PowerShellu](powershell-samples.md) nebo [Azure CLI](cli-samples.md) nebo pomocí šablon Azure [Resource Manageru](template-samples.md)
- Vytvoření a použití [zásad Azure](policy-samples.md) pro virtuální sítě
