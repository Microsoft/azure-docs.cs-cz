---
title: Několik IP adres pro virtuální počítače Azure – portál | Microsoft Docs
description: Zjistěte, jak přiřadit více IP adres k virtuálnímu počítači pomocí Azure Portal | Správce prostředků.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: allensu
ms.openlocfilehash: a0dab64aac8bc5fa68a53fad6cd8e6f6bb4bac03
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217137"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Přiřazení více IP adres k virtuálním počítačům pomocí Azure Portal

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> Tento článek vysvětluje, jak pomocí Azure Portal vytvořit virtuální počítač (VM) pomocí modelu nasazení Azure Resource Manager. K prostředkům vytvořeným prostřednictvím modelu nasazení Classic nelze přiřadit více IP adres. Další informace o modelech nasazení Azure najdete v článku [Principy modelů nasazení](../azure-resource-manager/management/deployment-models.md) .

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Vytvoření virtuálního počítače s několika IP adresami

Pokud chcete vytvořit virtuální počítač s několika IP adresami nebo statickou privátní IP adresou, musíte ho vytvořit pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI. Pokud se chcete dozvědět, jak, klikněte na možnosti PowerShellu nebo rozhraní příkazového řádku v horní části tohoto článku. Můžete vytvořit virtuální počítač s jednou dynamickou privátní IP adresou a (volitelně) jedinou veřejnou IP adresou. Použijte portál podle kroků uvedených v části [Vytvoření virtuálního počítače s Windows](../virtual-machines/windows/quick-create-portal.md) nebo [Vytvoření článků pro virtuální počítače se systémem Linux](../virtual-machines/linux/quick-create-portal.md) . Po vytvoření virtuálního počítače můžete změnit typ IP adresy z možností Dynamic na statickou a přidat další IP adresy pomocí portálu podle kroků v části [Přidání IP adres do virtuálního počítače](#add) v tomto článku.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Přidání IP adres k virtuálnímu počítači

K síťovému rozhraní Azure můžete přidat privátní a veřejné IP adresy, a to provedením následujících kroků. V příkladech v následujících oddílech se předpokládá, že už máte virtuální počítač se třemi konfiguracemi protokolu IP, které jsou popsané ve [scénáři](#scenario), ale není to potřeba.

### <a name="core-steps"></a><a name="coreadd"></a>Základní kroky

1. V případě potřeby vyhledejte Azure Portal https://portal.azure.com a přihlaste se k ní.
2. Na portálu klikněte na **Další služby** > do pole Filtr zadejte *virtuální počítače* a potom klikněte na **virtuální počítače**.
3. V podokně **virtuální počítače** klikněte na virtuální počítač, do kterého chcete přidat IP adresy. Přejděte na kartu **sítě** . na stránce klikněte na **síťové rozhraní** . Jak je znázorněno na obrázku níže: 


    ![Přidání veřejné IP adresy k virtuálnímu počítači](./media/virtual-network-multiple-ip-addresses-portal/figure200319.png)
4. V podokně **síťové rozhraní** klikněte na **Konfigurace protokolu IP**.

5. V podokně, které se zobrazí pro vybraný síťový adaptér, klikněte na **Konfigurace protokolu IP**. Klikněte na tlačítko **Přidat**, proveďte kroky v jedné z následujících částí podle typu IP adresy, kterou chcete přidat, a poté klikněte na tlačítko **OK**. 

### <a name="add-a-private-ip-address"></a>Přidat privátní IP adresu

Chcete-li přidat novou privátní IP adresu, proveďte následující kroky:

1. Proveďte kroky v části [základní kroky](#coreadd) tohoto článku a ujistěte se, že jste v části **Konfigurace protokolu IP** v síťovém rozhraní virtuálního počítače.  Zkontrolujte podsíť zobrazenou jako výchozí (například 10.0.0.0/24).
2. Klikněte na **Přidat**. V podokně **Přidat konfiguraci protokolu IP** , které se zobrazí, vytvořte konfiguraci protokolu IP s názvem *ipconfig-4* s novou *statickou* privátní IP adresou, a to tak, že vydáte nové číslo pro výsledný oktet a kliknete na **OK**.  (Pro podsíť 10.0.0.0/24 by se měla *10.0.0.7* příklad IP adresa.)

    > [!NOTE]
    > Když přidáváte statickou IP adresu, musíte v podsíti, ke které je připojená síťová karta, zadat nepoužitou platnou adresu. Pokud vybraná adresa není k dispozici, portál zobrazí X pro IP adresu a Vy musíte vybrat jinou.

3. Po kliknutí na tlačítko OK se podokno zavře a zobrazí se nová konfigurace protokolu IP. Kliknutím na tlačítko **OK** zavřete podokno **Přidat konfiguraci protokolu IP** .
4. Kliknutím na **Přidat** můžete přidat další konfigurace protokolu IP nebo zavřít všechna otevřená okna a dokončit přidávání IP adres.
5. Doplněním kroků v části [Přidání IP adres do operačního systému](#os-config) virtuálního počítače v tomto článku přidejte privátní IP adresy do operačního systému virtuálního počítače.

### <a name="add-a-public-ip-address"></a>Přidat veřejnou IP adresu

Veřejná IP adresa se přidá pomocí přidružení prostředku veřejné IP adresy k nové konfiguraci protokolu IP nebo existující konfiguraci protokolu IP.

> [!NOTE]
> Veřejné IP adresy mají nominální poplatek. Pokud se chcete dozvědět víc o cenách IP adres, přečtěte si stránku [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses) . Počet veřejných IP adres, které se dají použít v předplatném, je omezený. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
> 

### <a name="create-a-public-ip-address-resource"></a><a name="create-public-ip"></a>Vytvoření prostředku veřejné IP adresy

Veřejná IP adresa je jedno nastavení pro prostředek veřejné IP adresy. Pokud máte prostředek veřejné IP adresy, který není aktuálně přidružený ke konfiguraci protokolu IP, kterou chcete přidružit ke konfiguraci protokolu IP, přeskočte následující postup a proveďte kroky uvedené v jedné z následujících částí, jak budete potřebovat. Pokud nemáte dostupný prostředek veřejné IP adresy, proveďte následující kroky, abyste ho vytvořili:

1. V případě potřeby vyhledejte Azure Portal https://portal.azure.com a přihlaste se k ní.
3. Na portálu klikněte na **vytvořit prostředek**  >  **síť**  >  **Veřejná IP adresa**.
4. V podokně **vytvořit veřejnou IP adresu** , která se zobrazí, zadejte **název**, vyberte typ **přiřazení IP adresy** , **předplatné**, **skupinu prostředků** a **umístění** a pak klikněte na **vytvořit**, jak je znázorněno na následujícím obrázku:

    ![Vytvoření prostředku veřejné IP adresy](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Provedením kroků v jedné z následujících částí přidružte prostředek veřejné IP adresy k konfiguraci protokolu IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Přidružte prostředek veřejné IP adresy k nové konfiguraci protokolu IP.

1. Proveďte kroky v části [základní kroky](#coreadd) tohoto článku.
2. Klikněte na **Přidat**. V podokně **Přidat konfiguraci protokolu IP** , které se zobrazí, vytvořte konfiguraci protokolu IP s názvem *ipconfig-4*. Povolte **veřejnou IP adresu** a vyberte existující dostupný prostředek veřejné IP adresy z podokna **zvolit veřejnou IP adresu** , které se zobrazí.

    Po výběru prostředku veřejné IP adresy klikněte na **OK** a zavře se podokno. Pokud nemáte existující veřejnou IP adresu, můžete ji vytvořit provedením kroků v části [vytvoření prostředku veřejné IP adresy](#create-public-ip) v tomto článku. 

3. Zkontrolujte novou konfiguraci protokolu IP. I když privátní IP adresa nebyla explicitně přiřazena, jedna byla automaticky přiřazena ke konfiguraci protokolu IP, protože všechny konfigurace IP adres musí mít privátní IP adresu.
4. Kliknutím na **Přidat** můžete přidat další konfigurace protokolu IP nebo zavřít všechna otevřená okna a dokončit přidávání IP adres.
5. Dokončete kroky pro operační systém v části [Přidání IP adres do operačního systému](#os-config) virtuálního počítače v tomto článku a přidejte tak privátní IP adresu do operačního systému virtuálního počítače. Nepřidejte veřejnou IP adresu k operačnímu systému.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Přidružte prostředek veřejné IP adresy k existující konfiguraci protokolu IP.

1. Proveďte kroky v části [základní kroky](#coreadd) tohoto článku.
2. Klikněte na konfiguraci protokolu IP, do které chcete přidat prostředek veřejné IP adresy.
3. V podokně IPConfig, které se zobrazí, klikněte na **IP adresa**.
4. V podokně **Zvolte veřejnou IP adresu** , které se zobrazí, vyberte veřejnou IP adresu.
5. Klikněte na **Uložit** a zavřete podokna. Pokud nemáte existující veřejnou IP adresu, můžete ji vytvořit provedením kroků v části [vytvoření prostředku veřejné IP adresy](#create-public-ip) v tomto článku.
3. Zkontrolujte novou konfiguraci protokolu IP.
4. Kliknutím na **Přidat** můžete přidat další konfigurace protokolu IP nebo zavřít všechna otevřená okna a dokončit přidávání IP adres. Nepřidejte veřejnou IP adresu k operačnímu systému.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]