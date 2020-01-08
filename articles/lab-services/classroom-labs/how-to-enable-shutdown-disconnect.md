---
title: Povolit vypnutí virtuálních počítačů při odpojení Azure Lab Services | Microsoft Docs
description: Naučte se, jak povolit nebo zakázat automatické vypnutí virtuálních počítačů při odpojení připojení ke vzdálené ploše.
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
ms.openlocfilehash: e615170952ea2987639a0bfc269ad5a1692e1e59
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480798"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Povolit automatické vypnutí virtuálních počítačů při odpojení
V tomto článku se dozvíte, jak můžete povolit nebo zakázat automatické vypnutí virtuálních počítačů s **Windows 10** (šablona nebo student) po odpojení připojení ke vzdálené ploše. Můžete také určit, jak dlouho by měly virtuální počítače čekat na opětovné připojení uživatele, než se automaticky vypíná.

Správce účtu testovacího prostředí může nakonfigurovat toto nastavení pro účet testovacího prostředí, ve kterém vytvoříte Labs. Další informace najdete v tématu [Konfigurace automatického vypnutí virtuálních počítačů při odpojení od účtu testovacího prostředí](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect). Jako vlastník testovacího prostředí můžete nastavení přepsat při vytváření testovacího prostředí nebo po vytvoření testovacího prostředí. 

## <a name="configure-when-creating-a-lab"></a>Konfigurovat při vytváření testovacího prostředí
Na stránce Krok 3 Průvodce vytvořením testovacího prostředí můžete povolit nebo zakázat tuto funkci a také určit, jak dlouho by měl virtuální počítač čekat, než se automaticky vypíná. 

![Konfigurace v době vytváření testovacího prostředí](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Konfigurace po vytvoření testovacího prostředí
Toto nastavení můžete nakonfigurovat na stránce **Nastavení** , jak je znázorněno na následujícím obrázku: 

![Konfigurace po vytvoření testovacího prostředí](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Řídicí panel pro učebn Labs](use-dashboard.md)