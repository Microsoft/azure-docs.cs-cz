---
title: Více IP adres pro virtuální počítače Azure – portál | Dokumenty společnosti Microsoft
description: Zjistěte, jak přiřadit více IP adres virtuálnímu počítači pomocí portálu Azure | Správce zdrojů.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: anavin
ms.openlocfilehash: a5bb9bb9c584aef8ac79b3c275d01d3c498da843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060605"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Přiřazení více IP adres virtuálním počítačům pomocí portálu Azure

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> Tento článek vysvětluje, jak vytvořit virtuální počítač (VM) prostřednictvím modelu nasazení Azure Resource Manager pomocí portálu Azure. Více ADRES IP nelze přiřadit k prostředkům vytvořeným prostřednictvím klasického modelu nasazení. Další informace o modelech nasazení Azure najdete v článku [Principy modelů nasazení.](../resource-manager-deployment-model.md)

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Vytvoření virtuálního počítače s několika IP adresami

Pokud chcete vytvořit virtuální počítač s více IP adresami nebo statickou privátní IP adresu, musíte ji vytvořit pomocí PowerShellu nebo Azure CLI. Chcete-li se dozvědět, jak, klikněte na možnosti Prostředí PowerShell nebo CLI v horní části tohoto článku. Můžete vytvořit virtuální hospodaříc s jednou dynamickou privátní IP adresou a (volitelně) s jednou veřejnou IP adresou. Pomocí portálu postupujte podle kroků v [článku Vytvoření virtuálního počítače s Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) nebo Vytvoření virtuálního počítače s [Linuxem.](../virtual-machines/linux/quick-create-portal.md) Po vytvoření virtuálního virtuálního počítačů můžete změnit typ IP adresy z dynamické na statickou a přidat další IP adresy pomocí portálu podle kroků v [části Přidat IP adresy do oddílu virtuálního počítačů](#add) v tomto článku.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Přidání IP adres do virtuálního počítačů

Soukromé a veřejné IP adresy můžete přidat do síťového rozhraní Azure provedením následujících kroků. Příklady v následujících částech předpokládají, že už máte virtuální počítače se třemi konfiguracemi IP popsanými ve [scénáři](#scenario), ale není to povinné.

### <a name="core-steps"></a><a name="coreadd"></a>Základní kroky

1. Přejděte na portál https://portal.azure.com Azure na adrese a v případě potřeby se do něj přihlaste.
2. Na portálu klikněte na **Další služby** > do pole filtru zadali *virtuální počítače* a potom klikněte na **Virtuální počítače**.
3. V podokně **Virtuální počítače** klikněte na virtuální počítač, do kterého chcete přidat IP adresy. Přejděte na kartu **Síť.** Na stránce klepněte na **síťové rozhraní.** Jak je znázorněno na obrázku níže: 


    ![Přidání veřejné IP adresy k virtuálnímu počítači](./media/virtual-network-multiple-ip-addresses-portal/figure200319.png)
4. V podokně **síťového rozhraní** klepněte na **konfiguraci protokolu IP**.

5. V podokně, které se zobrazí pro vybranou nic, klepněte na **položku Konfigurace IP**. Klepněte na tlačítko **Přidat**, proveďte kroky v jedné z následujících částí na základě typu adresy IP, kterou chcete přidat, a klepněte na tlačítko **OK**. 

### <a name="add-a-private-ip-address"></a>**Přidání privátní IP adresy**

Chcete-li přidat novou privátní ADRESU IP, postupujte takto:

1. Proveďte kroky v části [Základní kroky](#coreadd) tohoto článku.
2. Klikněte na **Přidat**. V podokně **Přidat konfiguraci IP,** které se zobrazí, vytvořte konfiguraci IP s názvem *IPConfig-4* s *10.0.0.7* jako *statickou privátní* IP adresou a klepněte na tlačítko **OK**.

    > [!NOTE]
    > Při přidávání statické adresy IP je nutné zadat nepoužitou platnou adresu v podsíti, ke které je připojena síťová konta. Pokud vybraná adresa není k dispozici, portál zobrazí pro adresu IP x a musíte vybrat jinou adresu.

3. Po klepnutí na tlačítko OK se podokno zavře a zobrazí se nová konfigurace PROTOKOLU IP. Klepnutím na **tlačítko OK** zavřete **podokno Přidat konfiguraci IP.**
4. Chcete-li přidat další konfigurace IP, můžete klepnout na tlačítko **Přidat** nebo zavřít všechny otevřené listy a dokončit přidávání adres IP.
5. Přidejte privátní IP adresy do operačního systému virtuálního počítačů dokončením kroků v části [Přidání IP adres do oddílu operačního systému virtuálního počítačů](#os-config) v tomto článku.

### <a name="add-a-public-ip-address"></a>Přidání veřejné IP adresy

Veřejná IP adresa je přidána spojením prostředku veřejné IP adresy k nové konfiguraci IP nebo k existující konfiguraci IP.

> [!NOTE]
> Veřejné IP adresy mají nominální poplatek. Další informace o cenách IP adres najdete na stránce [s cenami ip adres.](https://azure.microsoft.com/pricing/details/ip-addresses) Počet veřejných IP adres, které lze použít v předplatném, je omezen. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
> 

### <a name="create-a-public-ip-address-resource"></a><a name="create-public-ip"></a>Vytvoření prostředku veřejné IP adresy

Veřejná IP adresa je jedno nastavení pro prostředek veřejné IP adresy. Pokud máte prostředek veřejné IP adresy, který není aktuálně přidružen ke konfiguraci IP adresy, kterou chcete přidružit ke konfiguraci IP, přeskočte následující kroky a proveďte kroky v jedné z následujících částí podle potřeby. Pokud nemáte k dispozici prostředek veřejné IP adresy, proveďte následující kroky k jeho vytvoření:

1. Přejděte na portál https://portal.azure.com Azure na adrese a v případě potřeby se do něj přihlaste.
3. Na portálu klikněte na vytvořit**Networking** > **veřejnou IP adresu** **sítě prostředků** > .
4. V podokně **Vytvořit veřejnou ADRESU IP,** které se zobrazí, zadejte **název**, vyberte typ **přiřazení IP adresy,** **odběr**, **skupinu prostředků**a **umístění**a klepněte na tlačítko **Vytvořit**, jak je znázorněno na následujícím obrázku:

    ![Vytvoření prostředku veřejné IP adresy](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Proveďte kroky v jedné z následujících částí a přidružte prostředek veřejné IP adresy ke konfiguraci PROTOKOLU IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Přidružení prostředku veřejné IP adresy k nové konfiguraci PROTOKOLU IP

1. Proveďte kroky v části [Základní kroky](#coreadd) tohoto článku.
2. Klikněte na **Přidat**. V zobrazíse podokno **Přidat konfiguraci IP** adresy vytvořte konfiguraci PROTOKOLU IP s názvem *IPConfig-4*. Povolte **veřejnou IP adresu** a vyberte existující dostupný prostředek veřejné IP adresy z podokna Zvolit **veřejnou IP adresu,** které se zobrazí.

    Po výběru prostředku veřejné IP adresy klikněte na **OK** a podokno se zavře. Pokud nemáte existující veřejnou IP adresu, můžete ji vytvořit provedením kroků v části [Vytvořit prostředek veřejné IP adresy](#create-public-ip) v tomto článku. 

3. Zkontrolujte novou konfiguraci PROTOKOLU IP. Přestože privátní IP adresa nebyla explicitně přiřazena, byla automaticky přiřazena ke konfiguraci IP adresy, protože všechny konfigurace IP adres musí mít privátní IP adresu.
4. Chcete-li přidat další konfigurace IP, můžete klepnout na tlačítko **Přidat** nebo zavřít všechny otevřené listy a dokončit přidávání adres IP.
5. Přidejte privátní IP adresu do operačního systému virtuálního počítače dokončením kroků pro váš operační systém v části [Přidat IP adresy do operačního systému virtuálního počítače](#os-config) v tomto článku. Nepřidávejte veřejnou IP adresu do operačního systému.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Přidružení prostředku veřejné IP adresy k existující konfiguraci PROTOKOLU IP

1. Proveďte kroky v části [Základní kroky](#coreadd) tohoto článku.
2. Klikněte na konfiguraci IP adresy, do které chcete přidat prostředek veřejné IP adresy.
3. V podokně IPConfig, které se zobrazí, klepněte na **adresu IP**.
4. V podokně **Zvolit veřejnou IP adresu,** které se zobrazí, vyberte veřejnou IP adresu.
5. Klikněte na **Uložit** a podokna se zavřou. Pokud nemáte existující veřejnou IP adresu, můžete ji vytvořit provedením kroků v části [Vytvořit prostředek veřejné IP adresy](#create-public-ip) v tomto článku.
3. Zkontrolujte novou konfiguraci PROTOKOLU IP.
4. Chcete-li přidat další konfigurace IP, můžete klepnout na tlačítko **Přidat** nebo zavřít všechny otevřené listy a dokončit přidávání adres IP. Nepřidávejte veřejnou IP adresu do operačního systému.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
