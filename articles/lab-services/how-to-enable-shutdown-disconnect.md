---
title: Povolit vypnutí virtuálních počítačů při odpojení Azure Lab Services | Microsoft Docs
description: Naučte se, jak povolit nebo zakázat automatické vypnutí virtuálních počítačů při odpojení připojení ke vzdálené ploše.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0c5c22d5e4a9d66413e37cce095f5497915bd122
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445708"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Povolit automatické vypnutí virtuálních počítačů při odpojení
V tomto článku se dozvíte, jak můžete povolit nebo zakázat automatické vypnutí virtuálních počítačů s **Windows 10** (šablona nebo student) po odpojení připojení ke vzdálené ploše. Můžete také určit, jak dlouho by měly virtuální počítače čekat na opětovné připojení uživatele, než se automaticky vypíná.

Správce účtu testovacího prostředí může nakonfigurovat toto nastavení pro účet testovacího prostředí, ve kterém vytvoříte Labs. Další informace najdete v tématu [Konfigurace automatického vypnutí virtuálních počítačů při odpojení od účtu testovacího prostředí](how-to-configure-lab-accounts.md). Jako vlastník testovacího prostředí můžete nastavení přepsat při vytváření testovacího prostředí nebo po vytvoření testovacího prostředí. 

## <a name="configure-when-creating-a-lab"></a>Konfigurovat při vytváření testovacího prostředí
Na stránce Krok 3 Průvodce vytvořením testovacího prostředí můžete povolit nebo zakázat tuto funkci a také určit, jak dlouho by měl virtuální počítač čekat, než se automaticky vypíná. 

![Konfigurace v době vytváření testovacího prostředí](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Konfigurace po vytvoření testovacího prostředí
Toto nastavení můžete nakonfigurovat na stránce **Nastavení** , jak je znázorněno na následujícím obrázku: 

![Konfigurace po vytvoření testovacího prostředí](./media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Pokud jste před odpojením relace RDP k VIRTUÁLNÍmu počítači vypnuli operační systém Windows (OS) na virtuálním počítači, funkce Automatické vypnutí nebude správně fungovat.  

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Řídicí panel pro učebn Labs](use-dashboard.md)