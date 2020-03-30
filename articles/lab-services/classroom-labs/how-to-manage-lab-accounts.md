---
title: Správa testovacích účtů ve službě Azure Lab Services | Dokumenty společnosti Microsoft
description: Přečtěte si, jak vytvořit účet testovacího prostředí, zobrazit všechny účty testovacího prostředí nebo odstranit účet testovacího prostředí v předplatném Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: c82b5d02ab3928eb0472f2a047cdca2238bf0b63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284288"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Správa testovacích účtů ve službě Azure Lab Services 
Ve službě Azure Lab Services je testovací účet kontejner pro spravované typy testovacího prostředí, jako jsou testovací prostředí ve třídě. Správce nastaví účet testovacího prostředí pomocí služby Azure Lab Services a poskytuje přístup vlastníkům testovacího prostředí, kteří můžou v účtu vytvářet testovací prostředí. Tento článek popisuje, jak vytvořit účet testovacího prostředí, zobrazit všechny účty testovacího prostředí nebo odstranit účet testovacího prostředí.

## <a name="create-a-lab-account"></a>Vytvoření účtu testovacího prostředí
Následující kroky ukazují postup vytvoření účtu testovacího prostředí se službou Azure Lab Services pomocí webu Azure Portal. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V levé nabídce vyberte **Všechny služby.** V části **DevOps** vyberte **Účty testovacího** prostředí. Pokud vyberete`*`hvězdičku ( ) vedle **položky Účty laboratoře**, přidá se do části **OBLÍBENÉ položky** v levé nabídce. Od příštího dne vyberte **v** části **OBLÍBENÉ POLOŽKY položku**Účty testovacího prostředí .

    ![Všechny účty > lab služby](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na stránce **Účty testovacího prostředí** vyberte **Přidat** na panelu nástrojů nebo Vytvořit **účet testovacího prostředí** na stránce. 

    ![Výběr možnosti Přidat na stránce Účty testovacího prostředí](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Na kartě **Základy** na stránce **Vytvořit účet testovacího prostředí** proveďte následující akce: 
    1. V části **Lab account name** (Název účtu testovacího prostředí) zadejte název. 
    2. Vyberte **předplatné Azure**, ve kterém chcete účet testovacího prostředí vytvořit.
    3. V části **Skupina prostředků** vyberte **Vytvořit novou** a zadejte název skupiny prostředků.
    4. V části **Umístění** vyberte umístění nebo oblast, ve které chcete účet testovacího prostředí vytvořit.
    5. Pole **Povolit autorovi testovacího prostředí k výběru umístění testovacího prostředí** určete, zda mají tvůrci testovacího prostředí chtít vybrat umístění pro testovací prostředí. Ve výchozím nastavení je tato možnost zakázána. Když je zakázáno, tvůrci testovacího prostředí nelze zadat umístění pro testovací prostředí, které vytvářejí. Laboratoře jsou vytvořeny v nejbližší geografické umístění k účtu testovacího prostředí. Pokud je povoleno, tvůrce testovacího prostředí může vybrat umístění v době vytvoření testovacího prostředí. Další informace naleznete v [tématu Povolit autorovi testovacího prostředí k výběru umístění pro testovací prostředí](allow-lab-creator-pick-lab-location.md). 

        ![Vytvořit účet testovacího prostředí -> základy](../media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Vyberte **Další: Upřesnit** v dolní části stránky přejděte na kartu **Upřesnit** a proveďte následující kroky: 
    1. Vyberte existující **sdílenou galerii obrázků** nebo ji vytvořte. Virtuální počítače šablony můžete uložit do galerie sdílených obrázků, aby ho měli znovu použít ostatní uživatelé. Podrobné informace o galeriích sdílených bitových obrázků najdete [v tématu Použití sdílené galerie obrázků ve službách Azure Lab Services](how-to-use-shared-image-gallery.md).
    2. Určete, zda chcete **automaticky vypnout virtuální počítače se systémem Windows,** když se od nich uživatelé odpojí. Určete, jak dlouho mají virtuální počítače čekat, až se uživatel znovu připojí, než se automaticky vypne. 
    3. Pro **virtuální síť Peer**vyberte virtuální síť pro partnerskou síť (VNet). Laboratoře vytvořené v tomto účtu jsou připojené k vybrané virtuální síti a mají přístup k prostředkům ve vybrané virtuální síti. Další informace najdete [v tématu Připojení virtuální sítě testovacího prostředí k partnerské virtuální síti](how-to-connect-peer-virtual-network.md).    
    8. Zadejte **rozsah adres** pro virtuální chod v testovacím prostředí. Rozsah adres by měl být v zápisu beztřídního směrování mezi doménami (CIDR) (příklad: 10.20.0.0/23). Virtuální počítače v testovacím prostředí budou vytvořeny v tomto rozsahu adres. Další informace najdete [v tématu Určení rozsahu adres pro virtuální chod v testovacím prostředí.](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab)  

        ![Vytvořit účet testovacího prostředí -> Upřesnit](../media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Vyberte **Další: Tagy** v dolní části stránky pro přepnutí na kartu **Značky.** Přidejte všechny značky, které chcete přidružit k účtu testovacího prostředí. Značky jsou dvojice název/hodnota, které umožňují kategorizovat prostředky a zobrazit konsolidovanou fakturaci použitím stejné značky pro více prostředků a skupin prostředků. Další informace najdete [v tématu Použití značek k uspořádání prostředků Azure](../../azure-resource-manager/management/tag-resources.md).

    ![Vytvořit testovací účet -> tagy](../media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Vyberte **Revize + vytvořit** v dolní části této stránky přepnout na kartu Revize + **vytvořit.** 
4. Zkontrolujte souhrnné informace na této stránce a vyberte **vytvořit**. 

    ![Vytvořit testovací účet -> tagy](../media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Počkejte, až bude nasazení dokončeno, rozbalte **Další kroky**a vyberte **Přejít na prostředek,** jak je znázorněno na následujícím obrázku: 

    Můžete také vybrat **ikonu zvonku** na panelu nástrojů (**Oznámení**), potvrdit, že nasazení proběhlo úspěšně, a pak vybrat **přejít na prostředek**. 

    Případně vyberte **Aktualizovat** na stránce **Účty testovacího prostředí** a vyberte účet testovacího prostředí, který jste vytvořili. 

    ![Okno Create a lab account (Vytvořit účet testovacího prostředí)](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Zobrazí se následující stránka **účtu testovacího prostředí**:

    ![Stránka účtu testovacího prostředí](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Zobrazit účty testovacího prostředí
1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V nabídce vyberte **Všechny zdroje.** 
3. Vyberte **testovací účty** pro **typ**. 
    Můžete také filtrovat podle předplatného, skupiny prostředků, umístění a značek. 

    ![Všechny zdroje -> účty lab](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Zobrazení a správa testovacích prostředí v účtu testovacího prostředí

1. Na stránce **Účet testovacího prostředí** vyberte v levé nabídce **všechna testovací prostředí.**

    ![Laboratoře v účtu](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. V účtu se zobrazí **seznam testovacích prostředí** s následujícími informacemi: 
    1. Jméno laboratoře.
    2. Datum vytvoření testovacího prostředí. 
    3. E-mailová adresa uživatele, který vytvořil testovací prostředí. 
    4. Maximální počet uživatelů povolených do testovacího prostředí. 
    5. Stav laboratoře. 
    6. Přiřazení rolí 

## <a name="delete-a-lab-in-the-lab-account"></a>Odstranění testovacího prostředí v účtu testovacího prostředí
Podle pokynů v předchozí části zobrazíte seznam testovacích prostředí v účtu testovacího prostředí.

1. Vyberte **... (tři tečky)** a vyberte **odstranit**. 

    ![Odstranit testovací prostředí – tlačítko](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Vystražovací zprávě vyberte **Ano.** 

    ![Potvrdit odstranění testovacího prostředí](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Odstranění účtu testovacího prostředí
Postupujte podle pokynů z předchozí části, která zobrazuje účty testovacího prostředí v seznamu. K odstranění účtu testovacího prostředí použijte následující pokyny: 

1. Vyberte **účet testovacího prostředí,** který chcete odstranit. 
2. Na panelu nástrojů vyberte **Odstranit.** 

    ![Tlačítko Odstranit účty testovacího prostředí ->](../media/how-to-manage-lab-accounts/delete-button.png)
1. Typ **Ano** pro potvrzení.
1. Vyberte **Odstranit**. 

    ![Odstranit účet testovacího prostředí - potvrzení](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Ke správě účtů testovacího prostředí můžete také použít modul Az.LabServices PowerShell (preview). Další informace naleznete na [domovské stránce Az.LabServices na GitHubu](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Umožnění výběru umístění testovacího prostředí pro jeho tvůrce](allow-lab-creator-pick-lab-location.md)
- [Propojení sítě testovacího prostředí s virtuální virtuální sítí druhé strany](how-to-connect-peer-virtual-network.md)
- [Připojení sdílené galerie obrázků k testovacímu prostředí](how-to-attach-detach-shared-image-gallery.md)
- [Přidání uživatele jako vlastníka testovacího prostředí](how-to-add-user-lab-owner.md)
- [Zobrazení nastavení brány firewall pro testovací prostředí](how-to-configure-firewall-settings.md)
- [Konfigurace dalších nastavení testovacího prostředí](how-to-configure-lab-accounts.md)