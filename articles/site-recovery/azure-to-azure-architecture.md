---
title: Architektura replikace z Azure do Azure ve službě Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek obsahuje přehled komponent a architektury používané při replikaci virtuálních počítačů Azure mezi oblastmi Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: 20387c325497934ccb2e02188b24a0125f937e00
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078253"
---
# <a name="azure-to-azure-replication-architecture"></a>Architektura replikace z Azure do Azure


Tento článek popisuje architektury používané při replikaci, převzetí služeb při selhání a obnovovat virtuální počítače Azure (VM) mezi oblastmi Azure pomocí [Azure Site Recovery](site-recovery-overview.md) služby.




## <a name="architectural-components"></a>Komponenty architektury

Následující obrázek poskytuje souhrnný přehled prostředí Virtuálního počítače v určité oblasti (v tomto příkladu umístění východní USA). V prostředí virtuálního počítače Azure:
- Aplikace může běžet na virtuálních počítačích se spravovanými disky nebo nespravované disky rozprostřené mezi různými účty úložiště.
- Virtuální počítače mohou být součástí jedné nebo několika podsítí ve virtuální síti.


**Replikace z Azure do Azure**

![prostředí zákazníka](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>Proces replikace

### <a name="step-1"></a>Krok 1

Když povolíte replikaci virtuálního počítače Azure, na následujících odkazech se automaticky vytvoří v cílové oblasti, na základě nastavení zdrojové oblasti. Můžete přizpůsobit nastavení cílové prostředky podle potřeby.

![Povolit replikaci, krok 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Prostředek** | **Podrobnosti**
--- | ---
**Cílová skupina prostředků** | Skupina prostředků, do které patří replikované virtuální počítače po převzetí služeb při selhání. Umístění této skupiny prostředků může být v libovolné oblasti Azure, s výjimkou oblasti Azure, ve které jsou hostované zdrojové virtuální počítače.
**Cílová virtuální síť** | Virtuální síť, ve kterém jsou replikované virtuální počítače umístěné po převzetí služeb při selhání. Vytvoření mapování sítě mezi zdrojovou a cílovou virtuální sítí a naopak.
**Účty úložiště mezipaměti** | Předtím, než se změny zdrojového virtuálního počítače se replikují na cílový účet úložiště, jsou sledovány a odešle na účet úložiště mezipaměti ve zdrojovém umístění. Tento krok zajistí minimální dopad na produkční aplikace běžící na virtuálním počítači.
**Cílové účty úložiště (Pokud zdrojový virtuální počítač nepoužívá spravované disky)**  | Účty úložiště v cílovém umístění, do kterého se data replikují.
** Repliky spravovaných disků (Pokud zdrojový virtuální počítač používá spravované disky) **  | Spravované disky v cílovém umístění, do kterého se data replikují.
**Cílové skupiny dostupnosti**  | Skupiny dostupnosti běžícím replikované virtuální počítače umístěné po převzetí služeb při selhání.

### <a name="step-2"></a>Krok 2

Je-li povolena replikace, rozšíření služby Site Recovery Mobility service se automaticky nainstaluje na virtuálním počítači:

1. Virtuální počítač je registrovaný pomocí Site Recovery.

2. Průběžná replikace je nakonfigurovaná pro virtuální počítač. Datové zápisy na disky virtuálních počítačů průběžně přenášejí do účtu úložiště mezipaměti ve zdrojovém umístění.

   ![Povolit replikaci, krok 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)


 Site Recovery potřebuje nikdy příchozí připojení k virtuálnímu počítači. Jenom odchozí připojení je potřeba na další krok.

 - Site Recovery service adresy URL nebo IP adres
 - Office 365 ověřování adresy URL nebo IP adres
 - Adresy IP účtů úložiště mezipaměti

Pokud povolíte konzistenci napříč několika virtuálními počítači, budou spolu počítače v replikační skupině komunikovat přes port 20004. Ujistěte se, že žádné zařízení brány firewall neblokuje interní komunikaci mezi virtuálními počítači přes port 20004.

> [!IMPORTANT]
Pokud chcete do replikační skupiny zahrnout virtuální počítače s Linuxem, nezapomeňte ručně otevřít odchozí provoz na portu 20004 podle pokynů ke konkrétní verzi Linuxu.

### <a name="step-3"></a>Krok 3

Po průběžnou replikaci probíhá, zápisy na disk okamžitě přenášejí do účtu úložiště mezipaměti. Site Recovery zpracuje data a odesílá je do cílového účtu úložiště nebo repliky spravovaných disků. Až se zpracují data, body obnovení jsou generovány v účtu cílového úložiště každých několik minut.

## <a name="failover-process"></a>Proces převzetí služeb při selhání

Při zahájení převzetí služeb virtuálních počítačů vytvořená v cílovou skupinu prostředků, cílovou virtuální sítí, cílové podsítě a ve skupině dostupnosti cílové nastavovat. Při selhání můžete použít jakýkoli bod obnovení.

![Proces převzetí služeb při selhání](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Další postup

[Rychle replikovat](azure-to-azure-quickstart.md) virtuálního počítače Azure do sekundární oblasti.
