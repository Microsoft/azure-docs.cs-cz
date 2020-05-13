---
title: Konfigurace účtů testovacího prostředí v Azure Lab Services | Microsoft Docs
description: Tento článek popisuje, jak vytvořit účet testovacího prostředí, Zobrazit všechny účty testovacího prostředí nebo odstranit účet testovacího prostředí v Azure Lab Services.
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
ms.openlocfilehash: 14f66701d3a375807829493f866dcb91b131f2e5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121078"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Konfigurace účtů testovacího prostředí v Azure Lab Services 
V Azure Lab Services účet testovacího prostředí je kontejner pro spravované typy testovacích prostředí, jako je například učeben Labs. Správce nastaví účet testovacího prostředí pomocí Azure Lab Services a poskytne přístup k vlastníkům testovacího prostředí, kteří můžou v účtu vytvářet Labs. 

V tomto článku se dozvíte, jak provádět následující úlohy: 

- Zadejte rozsah adres pro virtuální počítače v testovacím prostředí.
- Konfigurace automatického vypnutí virtuálních počítačů při odpojení

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Zadejte rozsah adres pro virtuální počítače v testovacím prostředí.
Následující postup obsahuje kroky k určení rozsahu adres pro virtuální počítače v testovacím prostředí. Pokud aktualizujete rozsah, který jste předtím zadali, vztahuje se upravený rozsah adres pouze na virtuální počítače, které byly vytvořeny po provedení změny. 

Tady jsou některá omezení, která určují rozsah adres, které byste měli mít na paměti. 

- Předpona musí být menší nebo rovna 23. 
- Pokud je virtuální síť v partnerském vztahu k účtu testovacího prostředí, zadaný rozsah adres se nemůže překrývat s rozsahem adres z partnerské virtuální sítě.

1. Na stránce **účet testovacího prostředí** vyberte v nabídce vlevo možnost **Nastavení Labs** .
2. V poli **Rozsah adres** zadejte rozsah adres pro virtuální počítače, které budou vytvořeny v testovacím prostředí. Rozsah adres by měl být v notaci směrování mezi doménami (například: 10.20.0.0/23). Virtuální počítače v testovacím prostředí se vytvoří v tomto rozsahu adres.

    > [!NOTE]
    > Vlastnost rozsah adres se vztahuje jenom na to, jestli je pro testovací prostředí povolená Partnerská virtuální síť. 
3. Na panelu nástrojů vyberte **Uložit**. 

    ![Konfigurace rozsahu adres](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Automatické vypnutí virtuálních počítačů při odpojení
Po odpojení připojení ke vzdálené ploše můžete povolit nebo zakázat automatické vypnutí virtuálních počítačů s Windows Lab (šablona nebo student). Můžete také určit, jak dlouho by měly virtuální počítače čekat na opětovné připojení uživatele, než se automaticky vypíná.

![Nastavení automatického vypnutí v účtu testovacího prostředí](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Toto nastavení se vztahuje na všechny laboratoře vytvořené v účtu testovacího prostředí. Vlastník testovacího prostředí může toto nastavení přepsat na úrovni testovacího prostředí. Změna tohoto nastavení v účtu testovacího prostředí bude mít vliv jenom na laboratoře, které se vytvoří po provedení změny.

Další informace o tom, jak vlastník testovacího prostředí může nakonfigurovat toto nastavení na úrovni testovacího prostředí, najdete v [tomto článku](how-to-enable-shutdown-disconnect.md) .

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Umožnění výběru umístění testovacího prostředí pro jeho tvůrce](allow-lab-creator-pick-lab-location.md)
- [Připojení sítě testovacího prostředí k partnerské virtuální síti](how-to-connect-peer-virtual-network.md)
- [Připojení Galerie sdílených imagí k testovacímu prostředí](how-to-attach-detach-shared-image-gallery.md)
- [Přidat uživatele jako vlastníka testovacího prostředí](how-to-add-user-lab-owner.md)
- [Zobrazit nastavení brány firewall pro testovací prostředí](how-to-configure-firewall-settings.md)
