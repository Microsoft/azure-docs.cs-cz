---
title: NP-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače NP-Series.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 61488b88b00206cb78beed4fe773bf9377848701
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861044"
---
# <a name="np-series"></a>NP-Series 
Virtuální počítače NP-Series jsou napájené [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) FPGA pro urychlení úloh, jako je odvozování strojového učení, překódování videa a hledání v databázi & Analytics. Virtuální počítače s řadou NP-Series využívají i procesory Intel Xeon 8171M (Skylake) s veškerou základní rychlostí Turbo 3,2 GHz.

[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): nepodporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): nepodporováno<br>
Podpora generování virtuálních počítačů: generace 1<br>
[Akcelerované síťové služby](../virtual-network/create-vm-accelerated-networking-cli.md): podporované<br>
[Dočasné disky s operačním systémem](ephemeral-os-disks.md): nepodporované <br>
<br>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | FPGA | FPGA paměť: GiB | Max. datových disků | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka:** Jakou verzi Vitis mám použít? 

**A:** Xilinx doporučuje [Vitis 2020,2](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html)


**Otázka:** Potřebuji pro vývoj mého řešení použít virtuální počítače NP? 

**A:** Ne, můžete vyvíjet místní a nasazovat do cloudu. Ujistěte se prosím, že k nasazení na virtuálních počítačích NP budete postupovat podle dokumentace k ověření identity. 

**Otázka:** Jakou verzi XRT mám použít?

Odpověď **:** xrt_202020.2.8.832 

**Otázka:** Jaká je cílová platforma pro nasazení?

**A:** Použijte následující platformy.
- Xilinx-u250-Gen3x16-xdma-Platform-2.1-3_all
- Xilinx-u250-Gen3x16-xdma-validate_2.1 – 3005608.1 

**Otázka:** Jakou platformu mám cílit na vývoj?

Odpověď **:** Xilinx-u250-Gen3x16-xdma-2.1-202010-1-dev_1-2954688_all 

**Otázka:** Jaké jsou podporované operační systémy (operační systémy)? 

**A:** Xilinx a Microsoft ověřili Ubuntu 18,04 LTS a CentOS 7,8.

 Xilinx vytvořil následující image na webu Marketplace, které zjednodušují nasazení těchto virtuálních počítačů. 

[Xilinx Alveo U250 Deployment VM – Ubuntu 18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Xilinx Alveo U250 Deployment VM – CentOS 7,8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

**Otázka:** Můžu nasazovat vlastní virtuální počítače s Ubuntu/CentOS a nainstalovat cílovou platformu XRT/nasazení? 

**Odpověď:** Ano.

**Otázka:** Pokud nasadím vlastní virtuální počítač Ubuntu 18.04, jaké jsou požadované balíčky a kroky?

**A:** [Dokumentace k Xilinx XRT](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf) v jádře 4.1 x za sekundu
       
Nainstalujte následující balíčky.
- xrt_202020 xrt_202020.2.8.832_18.04-amd64-XRT. deb
       
- xrt_202020 xrt_202020.2.8.832_18.04-amd64-Azure. deb
       
- Xilinx-u250-Gen3x16-xdma-Platform-2.1-3_all_18.04. deb. tar. gz
       
- Xilinx-u250-Gen3x16-xdma-validate_2.1-3005608.1_all. deb  

**Otázka:** V Ubuntu se po restartování virtuálního počítače nepovedlo najít moje FPGA (y): 

**A:** Ověřte prosím, že nedošlo k upgradu jádra (uname-a). V takovém případě prosím downgradujte na jádro 4.1 X. 

**Otázka:** Pokud nasadím vlastní virtuální počítač CentOS 7,8, jaké jsou požadované balíčky a kroky?

**A:** Použít verzi jádra: 3.10.0-1160.15.2.el7.x86_64

 Nainstalujte následující balíčky.
   
 - xrt_202020 xrt_202020.2.8.832_7.4.1708-x86_64-XRT. ot./min. 
      
 - xrt_202020 xrt_202020.2.8.832_7.4.1708-x86_64-Azure. ot./min. 
     
 - Xilinx-u250-Gen3x16-xdma-Platform-2.1 -3. webarch. ot./min.. tar. gz 
      
 - Xilinx-u250-Gen3x16-xdma-Validate-2.1 – 3005608.1.... ot./min.  

**Otázka:** Když se spustí xbutil Validate on CentOS, zobrazí se toto upozornění: "Upozornění: verze jádra 3.10.0-1160.15.2.el7.x86_64 není oficiálně podporovaná. 4.18.0-193 je nejnovější podporovaná verze. " 

**A:** To se dá bezpečně ignorovat. 

**Otázka:** Jaké jsou rozdíly mezi virtuálními počítači OnPrem a NP týkajícími se XRT? 

**A:** V Azure podporuje platforma XDMA 2,1 jenom Host_Mem (SB) a DDR – funkce pro uchovávání dat. 

Povolení Host_Mem (SB) (1 GB RAM): sudo xbutil host_mem--Enable--Size 1G 

Zakázání Host_Mem (SB): sudo xbutil host_mem--Disable 

**Otázka:** Můžu spustit příkazy xbmgmt? 

**A:** Ne, na virtuálních počítačích Azure neexistuje žádná podpora správy přímo z virtuálního počítače Azure. 

 **Otázka:** Potřebuji načíst PLP? 

**A:** Ne, PLP se automaticky načte za vás, takže není nutné načítat prostřednictvím příkazů xbmgmt. 

 
**Otázka:** Podporuje Azure různé PLPs? 

**A:** V tuto chvíli ne. Podporujeme jenom PLP, které poskytujeme v balíčcích platforem nasazení. 

**Otázka:** Jak mohu zadat dotaz na PLP informace? 

**A:** Je potřeba spustit dotaz xbutil a podívat se na spodní část. 

**Otázka:** Když vytvořím vlastní virtuální počítač a ručně nasadí XRT, jaké další změny je potřeba udělat? 

**A:** Do/opt/Xilinx/XRT/Setup.sh přidejte položku pro XRT_INI_PATH odkazující na/opt/Xilinx/XRT/xrt.ini

 
Obsah xrt.ini/opt/Xilinx/XRT/by měl obsahovat: <br>
Runtime<br>
ERT = false <br>

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU.](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
