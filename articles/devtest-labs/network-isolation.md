---
title: Izolace sítě v Azure DevTest Labs
description: Přečtěte si o izolaci sítě v Azure DevTest Labs.
ms.topic: article
ms.date: 08/25/2020
ms.openlocfilehash: 28bfa048a8a6666deb58a8ecfff38779c91d95b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96352928"
---
# <a name="network-isolation-in-devtest-labs"></a>Izolace sítě v DevTest Labs

[Virtuální síť Azure](../virtual-network/virtual-networks-overview.md) funguje jako hranice zabezpečení a izoluje prostředky Azure od veřejného Internetu. K místní síti se můžete připojit taky přes virtuální síť Azure, abyste se mohli bezpečně připojit k prostředkům Prem. V DevTest Labs můžete zvolit [izolaci všech virtuálních počítačů a prostředí testovacího](devtest-lab-configure-vnet.md) prostředí [do vaší sítě](connect-environment-lab-virtual-network.md) , abyste zajistili, že se prostředky testovacího prostředí řídí zásadami sítě organizace. 

Jako vlastník testovacího prostředí můžete také zvolit kompletní izolaci testovacího prostředí, což znamená souběžné izolování virtuálních počítačů a prostředí do vybrané sítě. můžete také izolovat účet úložiště testovacího prostředí a trezory klíčů vytvořené v rámci předplatného. Tento článek vás provede vytvořením izolovaného testovacího prostředí sítě. 

Přečtěte si také následující články:

- [Jak DevTest Labs používá účet úložiště testovacího prostředí](encrypt-storage.md)
- [Jak DevTest Labs používá trezory klíčů](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> Izolace sítě se v současné době podporuje jenom pro vytváření nových cvičení.

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>Postup povolení izolace sítě během vytváření testovacího prostředí

1. Při vytváření testovacího prostředí přejít na kartu **síť** .
1. Můžete buď vybrat **výchozí** síť, kterou bude laboratoř vytvářet, nebo vybrat existující síť z rozevíracího seznamu. Budete moct vybrat jenom sítě, které jsou ve stejné oblasti a předplatném jako testovací prostředí. 

    > [!div class="mx-imgBorder"]
    > ![Vytvoření testovacího prostředí](./media/network-isolation/create-lab.png)
1. Vyberte podsíť.

    > [!div class="mx-imgBorder"]
    > ![Vytvoření podsítě](./media/network-isolation/create-lab-subnet.png)
1. Pokud se rozhodnete izolovat prostředky testovacího prostředí (účet úložiště testovacího prostředí a trezor klíčů) do výchozí sítě, nebude potřeba žádná další akce a testovací prostředí bude zpracovávat izolaci prostředků, které budou předány dál.
 
    > [!div class="mx-imgBorder"]
    > ![Izolace sítě](./media/network-isolation/isolate-lab-resources.png)
1. Pokud se rozhodnete izolovat prostředky testovacího prostředí (účet úložiště testovacího prostředí a trezor klíčů) do existující sítě, kterou jste vybrali, musíte provést následující kroky po vytvoření testovacího prostředí, abyste zajistili, že testovací prostředí bude i nadále fungovat v izolovaném režimu. 
 
    > [!div class="mx-imgBorder"]
    > ![Izolovat znovu zdroj](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > Vlastník testovacího prostředí musí tento postup provést před konfigurací nebo vytvořením prostředků v testovacím prostředí.

### <a name="steps-to-follow-post-lab-creation"></a>Kroky pro vytvoření následného testovacího prostředí

1. Na domovské stránce testovacího prostředí vyberte **skupinu prostředků** na stránce **Přehled** . Měla by se zobrazit stránka **skupiny prostředků** pro skupinu prostředků, která obsahuje testovací prostředí. 
 
   > [!div class="mx-imgBorder"]
   > ![Laboratoř contoso](./media/network-isolation/contoso-lab.png)
1. Vyberte účet Azure Storage testovacího prostředí. Konvence pojmenování pro účet úložiště testovacího prostředí je:<*labNameWithoutInvalidCharacters* > *<číslici*>. Například pokud je název testovacího prostředí contosolab, může být název účtu úložiště acontosolab1234.
 
   > [!div class="mx-imgBorder"]
   > ![Test společnosti Contoso](./media/network-isolation/contoso-test.png)
1. V účtu úložiště přejděte na brány firewall a virtuální sítě a ujistěte se, že je zaškrtnuté políčko pro přístup k tomuto účtu úložiště použít důvěryhodné služby Microsoftu. Vzhledem k tomu, že [DevTest Labs je důvěryhodná služba Microsoftu](../storage/common/storage-network-security.md#trusted-microsoft-services), tato možnost umožní testovacímu prostředí fungovat normálně v izolovaném režimu sítě. 

   > [!div class="mx-imgBorder"]
   > ![Brány firewall pro laboratoř contoso](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. Potom klikněte na **+ Přidat existující virtuální síť**, vyberte virtuální síť a podsíť, kterou jste vybrali při vytváření testovacího prostředí, a klikněte na **Povolit**. 

   > [!div class="mx-imgBorder"]
   > ![Contoso moje virtuální síť](./media/network-isolation/contoso-lab-my-vnet.png)
5.  Po úspěšném povolení koncového bodu služby pro vybranou virtuální síť klikněte na **Přidat**. 

   > [!div class="mx-imgBorder"]
   > ![Add](./media/network-isolation/contoso-firewall-add.png)
 
V takovém případě úložiště Azure umožní příchozí připojení z přidané virtuální sítě a povolit testovacímu prostředí úspěšné fungování v izolovaném režimu sítě. 

Můžete také zvolit automatizaci těchto kroků a nakonfigurovat toto nastavení pro více cvičení. 

[Další informace o správě výchozích pravidel přístupu k síti pro Azure Storage pomocí PowerShellu a rozhraní příkazového řádku](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#powershell)

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>Při používání testovacího prostředí v izolovaném režimu sítě si pamatujte na to, co je potřeba.

### <a name="accessing-labs-storage-account-outside-the-lab"></a>Přístup k účtu úložiště testovacího prostředí mimo testovací prostředí 

V rámci síťového izolovaného prostředí pro akce, jako je nahrání VHD do účtu úložiště testovacího prostředí pro vytváření vlastních imagí z, bude vlastník testovacího prostředí muset explicitně povolit přístup k účtu úložiště z povoleného koncového bodu. Můžete to udělat tak, že vytvoříte virtuální počítač a účet úložiště testovacího prostředí pro zabezpečený přístup z tohoto virtuálního počítače. 

[Další informace o přístupu k účtu úložiště soukromě z virtuálního počítače](../private-link/tutorial-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>Exportují se data o využití z testovacího prostředí. 

V rámci síťového izolovaného prostředí pro [Export osobních údajů o využití pro testovací prostředí](personal-data-delete-export.md)bude vlastník testovacího prostředí explicitně muset poskytnout účet úložiště a vygenerovat v rámci účtu objekt BLOB pro ukládání dat. 

Pokud není zadaný účet úložiště, tato operace se v izolovaném režimu v síti nezdařila, protože účet úložiště testovacího prostředí není dostupný pro testovací prostředí pro případ, že ho zákazník neposkytl bez účtu úložiště. 

[Další informace o exportu dat využití testovacího prostředí v zadaném účtu úložiště](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>Další kroky

[Automatické vytváření a úpravy testovacích prostředí pomocí šablon Azure Resource Manager a PowerShellu](devtest-lab-use-arm-and-powershell-for-lab-resources.md)