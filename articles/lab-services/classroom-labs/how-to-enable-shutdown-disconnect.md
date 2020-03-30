---
title: Povolit vypnutí virtuálních počítačů při odpojení služby Azure Lab Services | Dokumenty společnosti Microsoft
description: Přečtěte si, jak povolit nebo zakázat automatické vypnutí virtuálních počítačů, když je odpojené připojení ke vzdálené ploše.
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
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: 68a27a325a0ef02c6eeea9867a21ba0e24ab5321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117131"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Povolit automatické vypnutí virtuálních počítačů při odpojení
Tento článek ukazuje, jak můžete povolit nebo zakázat automatické vypnutí virtuálních počítačů **testovacího prostředí windows 10** (šablona nebo student) po odpojení připojení ke vzdálené ploše. Můžete také určit, jak dlouho virtuální chody by měl čekat na uživatele znovu připojit před automatickým vypnutím.

Správce účtu testovacího prostředí může nakonfigurovat toto nastavení pro účet testovacího prostředí, ve kterém vytvoříte testovací prostředí. Další informace [najdete v tématu Konfigurace automatického vypnutí virtuálních počítačů při odpojení účtu testovacího prostředí](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect). Jako vlastník testovacího prostředí můžete přepsat nastavení při vytváření testovacího prostředí nebo po vytvoření testovacího prostředí. 

## <a name="configure-when-creating-a-lab"></a>Konfigurace při vytváření testovacího prostředí
Na stránce kroku 3 průvodce vytvořením testovacího prostředí můžete tuto funkci povolit nebo zakázat a také určit, jak dlouho má virtuální počítač čekat, než se uživatel znovu připojí, než se automaticky vypne. 

![Konfigurace v době vytvoření testovacího prostředí](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Konfigurovat po vytvoření testovacího prostředí
Toto nastavení můžete nakonfigurovat na stránce **Nastavení,** jak je znázorněno na následujícím obrázku: 

![Konfigurovat po vytvoření testovacího prostředí](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Pokud vypnete operační systém Windows (OS) na virtuálním počítači před odpojením relace RDP k virtuálnímu počítači, funkce automatického vypnutí nebude fungovat správně.  

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Řídicí panel pro laboratoře ve třídě](use-dashboard.md)