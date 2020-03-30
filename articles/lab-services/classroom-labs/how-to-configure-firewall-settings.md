---
title: Nastavení brány firewall pro Azure Lab Services
description: Zjistěte, jak určit veřejnou IP adresu a rozsah čísel portů virtuálních počítačů v testovacím prostředí, aby bylo možné přidat informace do pravidel brány firewall.
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443447"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Nastavení brány firewall pro Azure Lab Services

Každá organizace nebo škola vytvoří vlastní síť způsobem, který nejlépe vyhovuje jejich potřebám.  Někdy to zahrnuje nastavení pravidel brány firewall, která blokují připojení rdp (RDP) nebo Secure Shell (Ssh) k počítačům mimo vlastní síť.  Vzhledem k tomu, že azure lab služby běží ve veřejném cloudu, některé další konfigurace možná potřeba umožnit studentům přístup k jejich virtuálnípočítač při připojování ze sítě areálu.

Každé testovací prostředí používá jednu veřejnou IP adresu a více portů.  Všechny virtuální počítače, šablony virtuálních počítačů a studentských virtuálních počítačů, bude používat tuto veřejnou IP adresu.  Veřejná IP adresa se po celou dobu životnosti laboratoře nezmění.  Každý virtuální virtuální ms však bude mít jiné číslo portu.  Čísla portů se pohybují od 49152 do 65535.  Kombinace veřejné IP adresy a čísla portu se používá k připojení instruktora a studentů ke správnému virtuálnímu virtuálnímu účtu.  Tento článek se bude zabývat tím, jak najít konkrétní veřejnou IP adresu používanou testovacím prostředím.  Tyto informace lze použít k aktualizaci příchozích a odchozích pravidel brány firewall, aby studenti měli přístup ke svým virtuálním počítačem.

>[!IMPORTANT]
>Každé testovací prostředí bude mít jinou veřejnou IP adresu.

## <a name="find-public-ip-for-a-lab"></a>Vyhledání veřejné IP adresy pro testovací prostředí

Veřejné IP adresy pro každé testovací prostředí jsou **uvedeny** na stránce Všechny testovací prostředí v testovacím prostředí služby Lab.  Pokyny, jak najít všechny **testovací prostředí,** naleznete v tématu [správa testovacích prostředí v účtu testovacího prostředí](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account).  

> [!div class="mx-imgBorder"]
> ![Stránka Všechna laboratoře](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Pokud počítač šablony pro vaši testovací prostředí ještě není publikovaný, veřejná IP adresa se nezobrazí.

## <a name="conclusion"></a>Závěr

Teď známe veřejnou IP adresu laboratoře.  Příchozí a odchozí pravidla lze vytvořit pro bránu firewall organizace pro veřejnou IP adresu a rozsah portů 49152-65535.  Po aktualizaci pravidel mohou studenti přistupovat ke svým virtuálním počítačům, aniž by přístup blokovala síťová brána firewall.

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Umožnění výběru umístění testovacího prostředí pro jeho tvůrce](allow-lab-creator-pick-lab-location.md)
- [Propojení sítě testovacího prostředí s virtuální virtuální sítí druhé strany](how-to-connect-peer-virtual-network.md)
- [Připojení sdílené galerie obrázků k testovacímu prostředí](how-to-attach-detach-shared-image-gallery.md)
- [Přidání uživatele jako vlastníka testovacího prostředí](how-to-add-user-lab-owner.md)
- [Zobrazení nastavení brány firewall pro testovací prostředí](how-to-configure-firewall-settings.md)
- [Konfigurace dalších nastavení testovacího prostředí](how-to-configure-lab-accounts.md)
