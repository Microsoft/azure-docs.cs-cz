---
title: Několik IP adres pro Azure virtual machines – portál | Dokumentace Microsoftu
description: Zjistěte, jak přiřadit několik IP adres pro virtuální počítač pomocí webu Azure portal | Resource Manager.
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
ms.author: annahar
ms.openlocfilehash: ff6f3444847d9c78836a44ca95f9b00160c29ef4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38630724"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Přiřadit několik IP adres pro virtuální počítače pomocí webu Azure portal

>[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
>
Tento článek vysvětluje, jak vytvořit virtuální počítač (VM) prostřednictvím modelu nasazení Azure Resource Manageru pomocí webu Azure portal. Několik IP adres nelze přiřadit k prostředkům vytvořeným prostřednictvím modelu nasazení classic. Další informace o modelech nasazení Azure najdete v článku [vysvětlení modelů nasazení](../resource-manager-deployment-model.md) článku.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Vytvoření virtuálního počítače s několika IP adresami

Pokud chcete vytvořit virtuální počítač s několika IP adresami nebo statickou privátní IP adresu, musíte vytvořit pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure. Další informace jak, klikněte na možnosti prostředí PowerShell nebo rozhraní příkazového řádku v horní části tohoto článku. Vytvoření virtuálního počítače pomocí jedné dynamickou privátní IP adresu a (volitelně) jedna veřejná IP adresa. Použití portálu podle následujících kroků v [vytvoření virtuálního počítače s Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) nebo [vytvoření virtuálního počítače s Linuxem](../virtual-machines/linux/quick-create-portal.md) článků. Po vytvoření virtuálního počítače můžete změnit typ IP adresy z dynamické na statickou a přidejte další IP adresy pomocí portálu podle následujících kroků v [přidat IP adresy k virtuálnímu počítači](#add) části tohoto článku.

## <a name="add"></a>Přidejte IP adresy virtuálního počítače

Privátní a veřejné IP adresy Azure síťovému rozhraní můžete přidat provedením následujících kroků. Příklady v následujících částech Předpokládejme, že máte již virtuální počítač s tři konfigurace protokolu IP je popsáno v [scénář](#Scenario), ale to není nutné.

### <a name="coreadd"></a>Základní kroky

1. Přejděte na webu Azure portal na https://portal.azure.com a v případě potřeby se přihlaste do ní.
2. Na portálu klikněte na tlačítko **další služby** > typ *virtuálních počítačů* pole filtru, a pak klikněte na **virtuálních počítačů**.
3. V **virtuálních počítačů** podokně, klikněte na virtuální počítač, které chcete přidat IP adres. Klikněte na tlačítko **síťová rozhraní** ve virtuálním počítači, které se zobrazí a pak vyberte síťové rozhraní, které chcete přidat IP adres. V tomto příkladu je znázorněno na následujícím obrázku s názvem síťového adaptéru *myNIC* z virtuálního počítače s názvem *myVM* je vybrán:

    ![Síťové rozhraní](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

4. V podokně se zobrazí pro vybrané síťové rozhraní, klikněte na tlačítko **konfigurací protokolu IP**.

Proveďte kroky v jednom z částí, které následují, na základě typu IP adresu, kterou chcete přidat.

### <a name="add-a-private-ip-address"></a>**Přidejte privátní IP adresy**

Proveďte následující kroky pro přidání nové privátní IP adresy:

1. Proveďte kroky v [základní kroky](#coreadd) části tohoto článku.
2. Klikněte na tlačítko **Add** (Přidat). V **přidat IP adresu konfigurace** podokno, které se zobrazí, vytvořte konfiguraci IP adresy s názvem *IPConfig 4* s *10.0.0.7* jako *statické* privátní IP adresu a pak klikněte na **OK**.

    > [!NOTE]
    > Při přidávání statickou IP adresu, musíte zadat nevyužité, platná adresa v podsíti, ve které je síťové rozhraní připojené k. Pokud není k dispozici adresu, kterou jste vybrali, na portálu se zobrazí X pro IP adresu a je nutné vybrat jiný.

3. Po kliknutí na OK, podokno se zavře a zobrazí nová konfigurace IP uvedené. Klikněte na tlačítko **OK** zavřete **přidat IP adresu konfigurace** podokně.
4. Můžete kliknout na **přidat** přidat další konfigurace protokolu IP, nebo zavřete všechna otevřená okna mohli dokončit přidávání IP adresy.
5. Přidat privátních IP adres do operačního systému virtuálního počítače pomocí kroků v [přidat IP adresy na operační systém virtuálního počítače](#os-config) části tohoto článku.

### <a name="add-a-public-ip-address"></a>Přidání veřejné IP adresy

Veřejná IP adresa se přidá tím, že přidružíte prostředek veřejné IP adresy pro novou konfiguraci protokolu IP nebo existující konfigurace protokolu IP.

> [!NOTE]
> Veřejné IP adresy mají nominální poplatek. Další informace o cenách IP adres, [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses) stránky. Platí omezení na počet veřejné IP adresy, které je možné v rámci předplatného. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-subscription-service-limits.md#networking-limits).
> 

### <a name="create-public-ip"></a>Vytvořte prostředek veřejné IP adresy

Veřejná IP adresa je jedním z nastavení prostředku veřejné IP adresy. Pokud máte prostředek veřejné IP adresy, který není aktuálně přidružen ke konfiguraci IP, kterou chcete přidružit ke konfiguraci IP, přeskočte následující kroky a dokončete kroky v jednom z částí, které následují, podle potřeby. Pokud nemáte k dispozici prostředek veřejné IP adresy, proveďte následující kroky pro jeho vytvoření:

1. Přejděte na webu Azure portal na https://portal.azure.com a v případě potřeby se přihlaste do ní.
3. Na portálu klikněte na tlačítko **vytvořit prostředek** > **sítě** > **veřejnou IP adresu**.
4. V **vytvoření veřejné IP adresy** podokno, které se zobrazí, zadejte **název**vyberte **přiřazení IP adresy** typ, **předplatné**, **Skupiny prostředků**a **umístění**, pak klikněte na tlačítko **vytvořit**, jak je znázorněno na následujícím obrázku:

    ![Vytvořte prostředek veřejné IP adresy](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Proveďte kroky v jednom z následující části pro přidružení prostředek veřejné IP adresy pro konfiguraci IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Přidružte prostředek veřejné IP adresy na novou konfiguraci protokolu IP

1. Proveďte kroky v [základní kroky](#coreadd) části tohoto článku.
2. Klikněte na tlačítko **Add** (Přidat). V **přidat IP adresu konfigurace** podokno, které se zobrazí, vytvořte konfiguraci IP adresy s názvem *IPConfig 4*. Povolit **veřejnou IP adresu** a vyberte existující, k dispozici prostředek veřejné IP adresy z **zvolte veřejnou IP adresu** podokno, které se zobrazí.

    Po výběru prostředku veřejné IP adresy, klikněte na tlačítko **OK** a podokno se zavře. Pokud nemáte stávající veřejnou IP adresu, můžete vytvořit jednu podle postupu uvedeného v [vytvořte prostředek veřejné IP adresy](#create-public-ip) části tohoto článku. 

3. Projděte si novou konfiguraci protokolu IP. I v případě, že privátní IP adresa se přiřazuje explicitně, jeden byl automaticky přiřazen ke konfiguraci IP adresy, protože všechny konfigurace protokolu IP, musí mít privátní IP adresu.
4. Můžete kliknout na **přidat** přidat další konfigurace protokolu IP, nebo zavřete všechna otevřená okna mohli dokončit přidávání IP adresy.
5. Přidejte privátní IP adresu v operačním systému virtuálního počítače pomocí kroků pro váš operační systém v [přidat IP adresy na operační systém virtuálního počítače](#os-config) části tohoto článku. Nepřidávejte veřejnou IP adresu do operačního systému.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Přidružte prostředek veřejné IP adresy do existující konfigurace IP

1. Proveďte kroky v [základní kroky](#coreadd) části tohoto článku.
2. Klikněte na možnost konfigurace protokolu IP, které chcete přidat prostředek veřejné IP adresy na.
3. V podokně IPConfig, který se zobrazí, klikněte na tlačítko **IP adresu**.
4. V **zvolte veřejnou IP adresu** podokno, které se zobrazí, vyberte veřejnou IP adresu.
5. Klikněte na tlačítko **Uložit** a podokna zavřete. Pokud nemáte stávající veřejnou IP adresu, můžete vytvořit jednu podle postupu uvedeného v [vytvořte prostředek veřejné IP adresy](#create-public-ip) části tohoto článku.
3. Projděte si novou konfiguraci protokolu IP.
4. Můžete kliknout na **přidat** přidat další konfigurace protokolu IP, nebo zavřete všechna otevřená okna mohli dokončit přidávání IP adresy. Nepřidávejte veřejnou IP adresu do operačního systému.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
