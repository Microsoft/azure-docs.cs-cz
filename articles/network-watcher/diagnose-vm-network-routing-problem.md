---
title: Diagnostika problému se směrováním sítě virtuálního počítače – kurz – Azure Portal | Microsoft Docs
description: V tomto kurzu zjistíte, jak diagnostikovat problém se směrováním sítě virtuálního počítače pomocí funkce dalšího směrování služby Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: ea64c93726c3bc5c5d60f35790bb337333d4d47a
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="tutorial-diagnose-a-virtual-machine-network-routing-problem-using-the-azure-portal"></a>Kurz: Diagnostika problému se směrováním sítě virtuálního počítače pomocí portálu Azure Portal

Když nasadíte virtuální počítač, Azure pro něj vytvoří několik výchozích tras. Můžete vytvořit vlastní trasy a přepsat tak výchozí trasy Azure. Někdy se může stát, že vlastní trasa způsobí, že virtuální počítač nemůže komunikovat s ostatními prostředky. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače
> * Otestování komunikace s adresou URL pomocí funkce dalšího směrování v Network Watcheru
> * Otestování komunikace s IP adresou
> * Diagnostika problému se směrováním a způsoby, jak ho vyřešit

Pokud chcete, můžete problém se směrováním sítě virtuálního počítače diagnostikovat pomocí [Azure CLI](diagnose-vm-network-routing-problem-cli.md) nebo [Azure PowerShellu](diagnose-vm-network-routing-problem-powershell.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Compute** a pak **Windows Server 2016 Datacenter** nebo **Ubuntu Server 17.10 VM**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVm|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vyberte **Vytvořit novou** a zadejte **myResourceGroup**.|
    |Umístění| Vyberte **USA – východ**.|

4. Vyberte velikost virtuálního počítače a pak vyberte **Vybrat**.
5. V části **Nastavení** přijměte všechny výchozí hodnoty a vyberte **OK**.
6. V části **Vytvořit** na kartě **Souhrn** vyberte **Vytvořit** a spusťte nasazování virtuálního počítače. Nasazení virtuálního počítače trvá několik minut. Než budete pokračovat ve zbývajících krocích, počkejte, až virtuální počítač dokončí nasazování.

## <a name="test-network-communication"></a>Test síťové komunikace

Pokud chcete otestovat síťovou komunikaci pomocí Network Watcheru, nejprve ho musíte povolit v alespoň jedné oblasti Azure. Až pak k otestování komunikace použijte funkci dalšího směrování Network Watcheru.

### <a name="enable-network-watcher"></a>Povolení Network Watcheru

Pokud už Network Watcher máte v alespoň jedné oblasti povolený, přeskočte na část [Použití dalšího směrování](#use-next-hop).

1. Na portálu vyberte **Všechny služby**. Do **pole Filtr** zadejte *Network Watcher*. Jakmile se služba**Network Watcher** zobrazí ve výsledcích, vyberte ji.
2. Rozbalte **Oblasti** a pak vedle **USA – východ** vyberte **...**, jak je vidět na obrázku:

    ![Povolení Network Watcheru](./media/diagnose-vm-network-traffic-filtering-problem/enable-network-watcher.png)

3. Vyberte **Povolit Network Watcher**.

### <a name="use-next-hop"></a>Použití dalšího směrování

Azure automaticky vytváří trasy pro výchozí cíle. Můžete vytvořit vlastní trasy, které přepíšou ty výchozí. Někdy můžou vlastní trasy způsobit selhání komunikace. Pomocí funkce dalšího směrování Network Watcheru můžete určit, přes kterou trasu Azure se směruje provoz.

1. Na portálu Azure Portal v části **Network Watcher** vyberte **Další směrování**.
2. Vyberte své předplatné, podle následujícího obrázku zadejte následující hodnoty a potom vyberte **Další směrování**:

    |Nastavení                  |Hodnota                                                   |
    |---------                |---------                                               |
    | Skupina prostředků          | Vyberte myResourceGroup.                                 |
    | Virtuální počítač         | Vyberte myVm.                                            |
    | Síťové rozhraní       | myvm – Název vašeho síťového rozhraní se může lišit.   |
    | Zdrojová IP adresa       | 10.0.0.4                                               |
    | Cílová IP adresa  | 13.107.21.200 – jedna z adres stránky www.bing.com. |

    ![Další směrování](./media/diagnose-vm-network-routing-problem/next-hop.png)

    Za několik sekund vás výsledek informuje, že typ dalšího směrování je **Internet** a že **ID tabulky směrování** je **Trasa systému**. Na základě tohoto výsledku budete vědět, že pro daný cíl existuje platná trasa systému.

3. Změňte **Cílovou IP adresu** na *172.31.0.100* a vyberte znovu **Další směrování**. Vrácený výsledek vás informuje o tom, že **Typ dalšího směrování** je **Žádné** a že **ID tabulky směrování** je taky **Trasa systému**. Tento výsledek říká, že i když existuje trasa systému do cíle, neexistuje žádné další směrování, které by do cíle směrovalo provoz.

## <a name="view-details-of-a-route"></a>Zobrazení podrobností o trase

1. Abyste mohli směrování analyzovat podrobněji, zkontrolujte efektivní trasy síťového rozhraní. Do vyhledávacího pole v horní části portálu zadejte*myvm* (nebo název síťového rozhraní, které jste zkontrolovali). Jakmile se ve výsledcích hledání zobrazí položka **myvm**, vyberte ji.
2. Podle následujícího obrázku v části **PODPORA A ŘEŠENÍ POTÍŽÍ** vyberte **Efektivní trasy**:

    ![Efektivní trasy](./media/diagnose-vm-network-routing-problem/effective-routes.png)

    Když jste provedli test pomocí adresy 13.107.21.200 v části [Použití dalšího směrování](#use-next-hop), ke směrování provozu na danou adresu se použila trasa s předponou adresy 0.0.0.0/0, protože adresu neobsahuje žádná jiná trasa. Standardně se všechny adresy, které se nezadaly v předponě adresy jiné trasy, směrují do internetu.

    Když jste spustili test s adresou 172.31.0.100, výsledek vás informoval o tom, že neexistoval žádný typ dalšího směrování. Jak je vidět na předchozím obrázku, i když existuje výchozí trasa na předponu 172.16.0.0/12, která zahrnuje adresu 172.31.0.100, **TYP DALŠÍHO SMĚROVÁNÍ** je **Žádné**. Azure vytváří výchozí trasu do 172.16.0.0/12, ale neurčuje typ dalšího směrování, dokud k tomu není důvod. Pokud jste přidali například rozsah 172.16.0.0/12 do adresního prostoru virtuální sítě, Azure pro danou trasu změní **TYP DALŠÍHO SMĚROVÁNÍ** na **Virtuální síť**. Při kontrole by se pak jako **TYP DALŠÍHO SMĚROVÁNÍ** zobrazila **Virtuální síť**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup*. Jakmile se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. V části **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** zadejte *myResourceGroup* a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili virtuální počítač a diagnostikovali směrování síťového provozu z daného virtuálního počítače. Dozvěděli jste se, že Azure vytváří několik výchozích tras, a otestovali jste směrování na dva různé cíle. Přečtěte si i další informace o [směrování v Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a o tom, jak [vytvářet vlastní trasy](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

U odchozích připojení virtuálních počítačů můžete navíc pomocí funkce [Řešení potíží s připojením](network-watcher-connectivity-portal.md) v Network Watcheru určit latenci, povolený a zakázaný síťový provoz mezi virtuálním počítačem a koncovým bodem a trasu, která vedla ke koncovému bodu. Naučte se monitorovat komunikaci v průběhu času mezi virtuálním počítačem a koncovým bodem, např. IP adresou nebo adresou URL, pomocí funkce monitorování připojení v Network Watcheru.

> [!div class="nextstepaction"]
> [Monitorování síťového připojení](connection-monitor.md)