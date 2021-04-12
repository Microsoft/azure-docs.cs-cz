---
title: Virtuální počítač s Windows – spuštění virtuálního počítače připojit Nejčastější dotazy – Azure
description: Nejčastější dotazy a osvědčené postupy pro používání funkce spustit virtuální počítač při připojení
author: Heidilohr
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0a35d0541358af2f5fac5e4c7486a1be93797922
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445580"
---
# <a name="start-vm-on-connect-faq-preview"></a>Nejčastější dotazy ke spuštění virtuálního počítače (Preview)

> [!IMPORTANT]
> Funkce spustit virtuální počítač v Connect je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek popisuje Nejčastější dotazy týkající se spuštění virtuálního počítače (VM) v rámci funkce Connect (Preview) pro fondy hostitelů virtuálních počítačů s Windows.

## <a name="are-vms-automatically-deallocated-when-a-user-stops-using-them"></a>Oddělují se virtuální počítače automaticky, když je uživatel přestane používat?

No. Budete muset nakonfigurovat další zásady pro odhlášení uživatelů z jejich relací a spuštění skriptů Azure Automation pro uvolnění virtuálních počítačů.

Konfigurace zásad zrušení přidělení:

1. Vzdáleně se připojte k virtuálnímu počítači, pro který chcete zásadu nastavit.

2. Otevřete **Editor Zásady skupiny** a potom v části konfigurace **počítače zásady místního počítače**  >    >  **šablony pro správu**  >  **součásti systému Windows**  >  **Vzdálená plocha**  >  **hostitel relace vzdálené plochy**  >  **Časová omezení relace**.

3. Vyhledejte zásadu, která říká **nastavit časový limit pro odpojené relace**, a pak změňte její hodnotu na **povoleno**.

4. Po povolení této zásady vyberte **ukončit odpojenou relaci**.

>[!NOTE]
>Nezapomeňte nastavit časový limit pro zásadu ukončit odpojenou relaci na hodnotu větší než pět minut. Nízký časový limit může způsobit, že se relace uživatelů ukončí, pokud jejich síť ztratí spojení příliš dlouho, což vede ke ztrátě práce.

Podepisování uživatelů neuvolní své virtuální počítače. Další informace o tom, jak zrušit přidělení virtuálních počítačů, najdete v tématu [spuštění nebo zastavení virtuálních počítačů v době mimo](../automation/automation-solution-vm-management.md)špičku.

## <a name="can-users-turn-off-the-vm-from-their-clients"></a>Můžou uživatelé virtuální počítač vypnout ze svých klientů?

Ano. Uživatelé můžou virtuální počítač vypnout pomocí nabídky Start v rámci své relace stejně jako u fyzického počítače. Vypnutí virtuálního počítače ale neuvolní virtuální počítač. Další informace o tom, jak zrušit přidělení virtuálních počítačů, najdete v tématu [spuštění nebo zastavení virtuálních počítačů v době mimo](../automation/automation-solution-vm-management.md)špičku.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak nakonfigurovat spuštění virtuálního počítače při připojení, najdete v tématu věnovaném [spuštění virtuálního počítače na Connect (Preview)](start-virtual-machine-connect.md).

Pokud máte obecnější otázky k virtuální ploše Windows, přečtěte si naše obecné [Nejčastější dotazy](faq.md).
