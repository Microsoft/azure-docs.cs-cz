---
title: Nastavení testovacího prostředí s grafickými procesory v Azure Lab Services | Microsoft Docs
description: Přečtěte si, jak nastavit testovací prostředí s virtuálními počítači GPU (Graphics Processing Unit).
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2020
ms.author: nicolela
ms.openlocfilehash: 9f4bc77366aaeaef9c66467e540219763228d317
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450770"
---
# <a name="set-up-a-lab-with-gpu-graphics-processing-unit-virtual-machine-size"></a>Nastavení testovacího prostředí s grafickým procesorem (grafický procesor) velikosti virtuálního počítače
V tomto článku se dozvíte, jak provádět následující úlohy:

- Volba mezi virtualizačními a výpočetními GPU
- Ujistěte se, že jsou nainstalované příslušné ovladače GPU.
- Konfigurace nastavení RDP pro připojení k virtuálnímu počítači GPU (VM)

## <a name="choose-between-virtualization-and-compute-gpu-sizes"></a>Volba mezi virtualizací a výpočetními velikostmi GPU
Na první stránce Průvodce vytvořením testovacího prostředí vyberte **velikost virtuálních počítačů** potřebných pro vaši třídu.  

![Vyberte velikost virtuálního počítače.](../media/how-to-setup-gpu/lab-gpu-selection.png)

Tady máte možnost volby mezi **vizualizací** a **výpočetními** GPU.  Je důležité zvolit vhodný typ GPU na základě softwaru, který budou vaši studenti používat.  

Jak je popsáno v následující tabulce, **Výpočet** velikosti GPU procesorů je určený pro aplikace náročné na výpočetní výkon.  Například [obsáhlý Learning v typu třídy zpracování přirozeného jazyka](./class-type-deep-learning-natural-language-processing.md) ukazuje použití velikosti **malého GPU (COMPUTE)** .  **Výpočetní** procesor je vhodný pro tento typ třídy, protože studenti využívají architektury a nástroje pro hloubkové učení, které poskytuje [Data Science Virtual Machine image](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) , k výuce modelů hloubkového učení s velkými sadami dat.

| Velikost | Cores | Paměť RAM | Description | 
| ---- | ----- | --- | ----------- | 
| Malý grafický procesor (COMPUTE) | <ul><li>6 jader</li><li>56 GB RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Tato velikost nejlépe vyhovuje aplikacím náročným na počítač, jako je umělá a obsáhlá výuka. |

Velikosti **vizualizace** jsou určené pro aplikace náročné na grafiku.  Například [typ třídy SolidWorks Engineering](./class-type-solidworks.md) ukazuje použití velikosti **malého GPU (vizualizace)** .  Grafický procesor **vizualizace** je vhodný pro tento typ třídy, protože studenti komunikují s prostředím SOLIDWORKS ' 3D Computer-s návrhem (CAD) pro modelování a vizualizaci tuhých objektů.

| Velikost | Cores | Paměť RAM | Description | 
| ---- | ----- | --- | ----------- | 
| Malý grafický procesor (vizualizace) | <ul><li>6 jader</li><li>56 GB RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Tato velikost se nejlépe hodí pro vzdálenou vizualizaci, streamování, hraní her a kódování pomocí platforem, jako je OpenGL a DirectX. |
| Střední GPU (vizualizace) | <ul><li>12 jader</li><li>112 GB RAM</li></ul>  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Tato velikost se nejlépe hodí pro vzdálenou vizualizaci, streamování, hraní her a kódování pomocí platforem, jako je OpenGL a DirectX. |

## <a name="ensure-the-appropriate-gpu-drivers-are-installed"></a>Ujistěte se, že jsou nainstalované příslušné ovladače GPU.
Pokud chcete využít možnosti GPU vašich virtuálních počítačů v testovacím prostředí, musíte zajistit, aby byly nainstalované příslušné ovladače GPU.  V Průvodci vytvořením testovacího prostředí Když vyberete velikost virtuálního počítače GPU, je k dispozici možnost **nainstalovat ovladače GPU**.  

![Nainstalovat ovladače GPU](../media/how-to-setup-gpu/lab-gpu-drivers.png)

Tato možnost je ve výchozím nastavení **povolená** a zajišťuje instalaci *nejnovějších* ovladačů pro typ GPU a image, kterou jste vybrali:
- Když vyberete velikost procesoru **výpočetní** GPU, virtuální počítače testovacího prostředí jsou napájené pomocí GPU [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) .  V takovém případě jsou nainstalovány nejnovější ovladače [CUDA](https://www.nvidia.com/object/io_69526.html) pro zajištění vysoce výkonného výpočetního prostředí.
- Když vyberete velikost GPU pro **vizualizaci** , vaše testovací virtuální počítače jsou napájené technologií GPU [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) a [technologií Grid](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf).  V takovém případě jsou nainstalovány nejnovější ovladače mřížky, aby bylo možné aplikace náročné na grafiku.

> [!IMPORTANT]
> Abyste měli nejlepší uživatelské prostředí s grafickým procesorem pro **vizualizaci** , musíte se ujistit, že jsou nainstalované *oba* ovladače *a* že je GPU povolen přes připojení RDP.  Projděte si postup v části [Povolení připojení GPU přes připojení RDP k virtuálním počítačům s Windows](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms).

### <a name="manually-install-drivers"></a>Ruční instalace ovladačů
Můžete mít scénáře, kdy potřebujete nainstalovat jinou verzi ovladačů, než je nejnovější verze.  Postup v této části ukazuje, jak ručně nainstalovat příslušné ovladače v závislosti na tom, jestli používáte **výpočetní** procesor nebo grafický procesor **vizualizace** .

#### <a name="compute-gpu-drivers"></a>Ovladače pro výpočetní GPU
Pomocí těchto kroků ručně nainstalujte ovladače pro velikost procesoru pro **Výpočet** GPU:
1. Při [vytváření testovacího prostředí](./how-to-manage-classroom-labs.md) **zakažte** nastavení **nainstalovat ovladače GPU** v Průvodci vytvořením testovacího prostředí.
1. Po vytvoření testovacího prostředí se připojte k virtuálnímu počítači šablony a nainstalujte příslušné ovladače.
   ![Stažení ovladačů NVIDIA](../media/how-to-setup-gpu/nvidia-driver-download.png) 
   1. V prohlížeči otevřete [stránku soubory ke stažení ovladačů společnosti NVIDIA](https://www.nvidia.com/Download/index.aspx).
   2. Nastavte **typ produktu** na **Tesla**.
   3. Nastavte **řadu produktů** na **řady k**.
   4. Nastavte **produkt** na **Tesla K80**.
   5. Nastavte **operační systém** podle typu základní image, kterou jste vybrali při vytváření testovacího prostředí.
   6. Nastavte **CUDA Toolkit** na verzi ovladačů CUDA, které potřebujete.
   7. Pro vyhledání ovladačů klikněte na tlačítko **Hledat** .
   8. Kliknutím na **Stáhnout** Stáhněte instalační program.
   9. Spusťte instalační program, aby byly ovladače nainstalované na virtuálním počítači šablony.  
2. Ověřte, jestli jsou ovladače správně nainstalované, podle kroků v části [ověření nainstalovaných ovladačů](how-to-setup-lab-gpu.md#validate-installed-drivers). 
3. Po instalaci ovladačů a dalšího softwaru požadovaného pro vaši třídu můžete kliknout na **publikovat** a vytvořit virtuální počítače studentů.

> [!NOTE]
> Pokud používáte bitovou kopii operačního systému Linux, přečtěte si také následující postup, který nainstaluje ovladače po stažení instalačního programu: [nainstalujte ovladače CUDA v systému Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#install-cuda-drivers-on-n-series-vms).

#### <a name="visualization-gpu-drivers"></a>Ovladače GPU pro vizualizaci
Pomocí těchto kroků ručně nainstalujte ovladače pro **velikosti GPU pro** grafické procesory:
1. Při [vytváření testovacího prostředí](./how-to-manage-classroom-labs.md) **zakažte** nastavení **nainstalovat ovladače GPU** v Průvodci vytvořením testovacího prostředí.
1. Po vytvoření testovacího prostředí se připojte k virtuálnímu počítači šablony a nainstalujte příslušné ovladače.
1. Pomocí následujících kroků nainstalujte ovladače mřížky, které Microsoft poskytuje na virtuálním počítači šablony:
   -  [Ovladače Windows NVIDIA GRID](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)
   -  [Ovladače pro MŘÍŽKu NVIDIA pro Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#nvidia-grid-drivers)
  
1. Restartujte šablonu virtuálního počítače.
1. Ověřte, jestli jsou ovladače správně nainstalované, podle kroků v části [ověření nainstalovaných ovladačů](how-to-setup-lab-gpu.md#validate-installed-drivers).
1. Nakonfigurujte nastavení RDP a povolte GPU pomocí postupu uvedeného v části [Povolení připojení GPU přes připojení RDP k virtuálním počítačům s Windows](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms).
1. Po instalaci ovladačů a dalšího softwaru požadovaného pro vaši třídu můžete kliknout na **publikovat** a vytvořit virtuální počítače studentů.

### <a name="validate-installed-drivers"></a>Ověřit nainstalované ovladače
Postup v této části popisuje, jak ověřit, jestli jsou ovladače GPU správně nainstalované.

#### <a name="windows-images"></a>Bitové kopie systému Windows
1.  Postupujte podle kroků v článku, které ukazují, jak [ověřit instalaci ovladače v systému Windows](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation).
1.  Pokud používáte grafický procesor **vizualizace** , můžete také:
    - Chcete-li zobrazit a upravit nastavení GPU, přejděte k **ovládacímu panelu NVIDIA** .  Chcete-li získat přístup k těmto nastavením, otevřete **Ovládací panely > hardware** a vyberte **ovládací panel NVIDIA**.
      ![Otevřít ovládací panel NVIDIA](../media/how-to-setup-gpu/control-panel-nvidia-settings.png) 
     - Zobrazení výkonu GPU pomocí **Správce úloh**.  Pokud to chcete zobrazit, otevřete **Správce úloh** a vyberte kartu **výkon** . ![ Výkon GPU Správce úloh](../media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > Nastavení **ovládacího panelu NVIDIA** lze použít pouze pro grafické procesory **vizualizace** .  Pokud se pokusíte otevřít **ovládací panel NVIDIA** pro **výpočetní** procesor (GPU), zobrazí se následující chyba: **Nastavení zobrazení NVIDIA nejsou k dispozici.  V tuto chvíli nepoužíváte zobrazení připojené k GPU NVIDIA.**  Podobně jsou informace o výkonu GPU ve **Správci úloh** poskytovány pouze pro grafické procesory **vizualizace** .

#### <a name="linux-images"></a>Image Linux
Postupujte podle kroků v článku, které ukazují, jak [ověřit instalaci ovladače v systému Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#verify-driver-installation).

## <a name="enable-gpu-over-rdp-connection-to-windows-vms"></a>Povolit připojení GPU přes připojení RDP k virtuálním počítačům s Windows
Při použití protokolu RDP (Remote Desktop Protocol) pro připojení k virtuálnímu počítači s Windows, který využívá GPU pro **vizualizaci** , je potřeba provést nějakou dodatečnou konfiguraci, aby se grafický procesor používal pro vykreslování grafiky (v opačném případě se použije CPU).

Níže uvedené kroky je nutné provést na virtuálním počítači šablony.

1. Nejdřív nakonfigurujte nastavení RDP pro použití GPU:

   1. Postupujte podle kroků v tomto článku, které ukazují, jak [nakonfigurovat vykreslování aplikace podporující GPU](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-app-rendering).

   2. Postupujte podle kroků v tomto článku, které ukazují, jak [nakonfigurovat kódování rámců s akcelerovaným grafickým procesorem](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-frame-encoding).

2. Pak ověřte konfiguraci: 

   1. Postupujte podle kroků v tomto článku, které ukazují, jak [ověřit vykreslování aplikace podporující GPU](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-app-rendering).

   2.  Postupujte podle kroků v tomto článku, které ukazují, jak [ověřit kódování rámců s akcelerovaným grafickým procesorem](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-frame-encoding).

3. Nakonec máte nainstalované ovladače a máte nastavení RDP nakonfigurovaná tak, aby používala GPU.  Po nainstalování dalšího softwaru požadovaného pro vaši třídu můžete kliknout na **publikovat** a vytvořit virtuální počítače studentů.  Když se vaši studenti připojí ke svým virtuálním počítačům pomocí protokolu RDP, jejich plocha se vykreslí pomocí GPU svého virtuálního počítače.

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Vytváření a správa testovacích prostředí v učebnách](how-to-manage-classroom-labs.md)
- [Typ třídy SolidWorks počítačového návrhu (CAD)](class-type-solidworks.md)
- [Typ třídy MATLAB](class-type-matlab.md)



