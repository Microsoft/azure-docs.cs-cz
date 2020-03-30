---
title: Konfigurace testovacích účtů ve službě Azure Lab Services | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak vytvořit účet testovacího prostředí, zobrazit všechny účty testovacího prostředí nebo odstranit účet testovacího prostředí ve službě Azure Lab Services.
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
ms.openlocfilehash: fa9dba62b3b58687ec6a2bfc29e8722f7016b679
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284301"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Konfigurace testovacích účtů ve službě Azure Lab Services 
Ve službě Azure Lab Services je testovací účet kontejner pro spravované typy testovacího prostředí, jako jsou testovací prostředí ve třídě. Správce nastaví účet testovacího prostředí pomocí služby Azure Lab Services a poskytuje přístup vlastníkům testovacího prostředí, kteří můžou v účtu vytvářet testovací prostředí. 

Tento článek popisuje, jak provádět následující úkoly: 

- Zadejte rozsah adres pro virtuální chod v testovacím prostředí
- Konfigurace automatického vypnutí virtuálních počítačů při odpojení

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Zadejte rozsah adres pro virtuální chod v testovacím prostředí
Následující postup má kroky k určení rozsahu adres pro virtuální chody v testovacím prostředí. Pokud aktualizujete rozsah, který jste dříve zadali, upravený rozsah adres se vztahuje pouze na virtuální chody, které jsou vytvořeny po provedené změně. 

Zde jsou některá omezení při zadávání rozsahu adres, který byste měli mít na paměti. 

- Předpona musí být menší nebo rovna 23. 
- Pokud virtuální síť je peered na účet testovacího prostředí, zadaný rozsah adres se nemůže překrývat s rozsahem adres z partnerského virtuální sítě.

1. Na stránce **Účet testovacího prostředí** vyberte v levé nabídce **nastavení labs.**
2. Pro pole **Rozsah adresy** zadejte rozsah adres pro virtuální chody, které budou vytvořeny v testovacím prostředí. Rozsah adres by měl být v zápisu beztřídního směrování mezi doménami (CIDR) (příklad: 10.20.0.0/23). Virtuální počítače v testovacím prostředí budou vytvořeny v tomto rozsahu adres.
3. Na panelu nástrojů vyberte **Uložit**. 

    ![Konfigurace rozsahu adres](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Automatické vypnutí virtuálních počítačů při odpojení
Po odpojení připojení ke vzdálené ploše můžete povolit nebo zakázat automatické vypnutí virtuálních počítačů testovacího prostředí systému Windows (šablony nebo studenta). Můžete také určit, jak dlouho virtuální chody by měl čekat na uživatele znovu připojit před automatickým vypnutím.

![Automatické nastavení vypnutí v účtu laboratoře](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Toto nastavení platí pro všechna testovací prostředí vytvořená v účtu testovacího prostředí. Vlastník testovacího prostředí můžete přepsat toto nastavení na úrovni testovacího prostředí. Změna tohoto nastavení v účtu testovacího prostředí ovlivní pouze testovací prostředí, které jsou vytvořeny po provedené změně.

Informace o tom, jak může vlastník testovacího prostředí konfigurovat toto nastavení na úrovni testovacího prostředí, naleznete v [tomto článku](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Umožnění výběru umístění testovacího prostředí pro jeho tvůrce](allow-lab-creator-pick-lab-location.md)
- [Propojení sítě testovacího prostředí s virtuální virtuální sítí druhé strany](how-to-connect-peer-virtual-network.md)
- [Připojení sdílené galerie obrázků k testovacímu prostředí](how-to-attach-detach-shared-image-gallery.md)
- [Přidání uživatele jako vlastníka testovacího prostředí](how-to-add-user-lab-owner.md)
- [Zobrazení nastavení brány firewall pro testovací prostředí](how-to-configure-firewall-settings.md)
