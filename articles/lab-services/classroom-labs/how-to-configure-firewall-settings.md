---
title: Nastavení brány firewall pro Azure Lab Services
description: Přečtěte si, jak zjistit veřejnou IP adresu a rozsah čísel portů virtuálních počítačů v testovacím prostředí, aby bylo možné přidat informace do pravidel brány firewall.
author: emaher
ms.author: enewman
ms.date: 12/12/2019
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: da1614e4a3e02ed91ef2d3c59ac4eb3eac0dcc7c
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692768"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Nastavení brány firewall pro Azure Lab Services

Každá organizace nebo škola nastaví svou vlastní síť způsobem, který nejlépe vyhovuje jejich potřebám.  V některých případech zahrnuje nastavení pravidel brány firewall blokujících připojení protokol RDP (Remote Desktop Protocol) (RDP) nebo Secure Shell (SSH) k počítačům mimo svou vlastní síť.  Vzhledem k tomu, že Azure Lab Services běží ve veřejném cloudu, může být potřeba některá další konfigurace, která studentům umožní přístup k virtuálnímu počítači při připojování ze sítě areálu.

Každé testovací prostředí používá jednu veřejnou IP adresu a několik portů.  Tato veřejná IP adresa se použije u všech virtuálních počítačů, virtuálních počítačů šablon i studentů.  Veřejná IP adresa se po dobu životnosti testovacího prostředí nezmění.  Každý virtuální počítač ale bude mít jiné číslo portu.  Čísla portů jsou v rozsahu od 49152 do 65535.  Kombinace veřejné IP adresy a čísla portu se používá pro připojení instruktorů a studentů ke správnému virtuálnímu počítači.  Tento článek popisuje, jak najít konkrétní veřejnou IP adresu, kterou používá testovací prostředí.  Tyto informace se dají použít k aktualizaci příchozích a odchozích pravidel brány firewall, aby studenti mohli přistupovat ke svým virtuálním počítačům.

>[!IMPORTANT]
>Každé testovací prostředí bude mít jinou veřejnou IP adresu.

## <a name="find-public-ip-for-a-lab"></a>Vyhledání veřejné IP adresy pro testovací prostředí

Veřejné IP adresy pro každé testovací prostředí jsou uvedené v okně **všechny laboratoře** účtu testovacího prostředí služby Lab Services.  Návod, jak najít okno **všechny laboratoře** , najdete v tématu [Správa cvičení v účtu testovacího prostředí](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account).  

> [!div class="mx-imgBorder"]
> okno ![všech cvičení](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Veřejná IP adresa se nezobrazí, pokud ještě není publikovaný počítač šablony pro testovací prostředí.

## <a name="conclusion"></a>Závěr

Nyní víme o veřejné IP adrese pro testovací prostředí.  Příchozí a odchozí pravidla je možné vytvořit pro bránu firewall organizace pro veřejnou IP adresu a rozsah portů 49152-65535.  Po aktualizaci pravidel můžou studenti přistupovat ke svým virtuálním počítačům bez blokování přístupu přes bránu firewall sítě.
