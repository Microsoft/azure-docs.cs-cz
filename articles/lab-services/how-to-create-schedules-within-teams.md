---
title: Vytváření plánů Azure Lab Services v rámci týmů
description: Naučte se vytvářet plány testovacích služeb v rámci týmů.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 202b202f99868875a51d13e95fbcac677246cc05
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042333"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Vytváření a Správa plánů testovacích služeb v rámci týmů

Plány umožňují nakonfigurovat prostředí učebny tak, aby se virtuální počítače v testovacím prostředí automaticky spouštěly a vypnuly v určitou dobu. Můžete definovat Jednorázový plán nebo plán opakování. Následující postupy vám poskytnou kroky pro vytváření a správu plánů pro prostředí učebny: 

Tady je postup, jaký vliv mají plány na virtuální počítače testovacího prostředí: 

- Virtuální počítač šablony není zahrnutý v plánech. 
- Spuštěny jsou pouze přiřazené virtuální počítače. To znamená, že pokud počítač není vyžádán koncovým uživatelem (student), počítač se v naplánovaných hodinách nespustí. 
- Všechny virtuální počítače (bez ohledu na to, jestli je uživatel vyžádal), se zastaví v závislosti na plánu testovacího prostředí. 

> [!IMPORTANT]
> Plánovaná doba spuštění virtuálních počítačů se nepočítá s kvótou přidělenou uživateli. Kvóta je určena pro dobu mimo plánované hodiny, kterou student stráví na virtuálních počítačích. 

Uživatelé můžou plány testovacího prostředí vytvářet, upravovat a odstraňovat v rámci týmů, stejně jako na [webu Labs](https://labs.azure.com). Přečtěte si článek o [vytváření a správě plánů](how-to-create-schedules-within-teams.md).

## <a name="automatic-shutdown-and-disconnect-settings"></a>Nastavení automatického vypnutí a odpojení

Můžete povolit několik funkcí řízení nákladů na automatické vypnutí, abyste proaktivně zabránili dalším nákladům, když se virtuální počítače aktivně nepoužívají. Kombinace následujících tří funkcí automatického vypnutí a odpojení zachytává většinu případů, kdy uživatelé omylem odejdou z virtuálních počítačů, na kterých běží:
 
- Automaticky odpojí uživatele z virtuálních počítačů, které operační systém považuje za nečinné.
- Automaticky vypne virtuální počítače, když se uživatelé odpojí.
- Automaticky vypne virtuální počítače, které jsou spuštěné, ale uživatelé se nepřipojí.

Další podrobnosti najdete v článku [Konfigurace nastavení automatického vypnutí pro testovací prostředí](how-to-enable-shutdown-disconnect.md).

## <a name="next-steps"></a>Další kroky

Viz následující články:

- [Přehled použití Azure Lab Services v rámci týmů](lab-services-within-teams-overview.md)
- [Začněte a vytvořte testovací prostředí v rámci týmů.](how-to-get-started-create-lab-within-teams.md)
- [Správa seznamů uživatelů testovacího prostředí v rámci týmů](how-to-manage-user-lists-within-teams.md)
- [Správa fondu virtuálních počítačů v testovacím prostředí v rámci týmů](how-to-manage-vm-pool-within-teams.md)
- [Přístup k virtuálnímu počítači v rámci týmů – zobrazení studenta](how-to-access-vm-for-students-within-teams.md)
