---
title: Migrace nasazení aktualizací OMS do Azure
description: Tento článek popisuje, jak migrovat stávající nasazení aktualizací OMS do Azure
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 910f284eedbf50be5b58b6c18f02e50adda35e9a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680002"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Migrace nasazení aktualizací OMS do Azure

Portál OMS (Operations Management Suite) je [již zastaralá](../azure-monitor/platform/oms-portal-transition.md). Všechny funkce, které byly dostupné na portálu OMS pro správu aktualizací, jsou dostupné na webu Azure Portal prostřednictvím protokolů Azure Monitoru. Tento článek obsahuje informace, které potřebujete k migraci na portál Azure.

## <a name="key-information"></a>Klíčové informace

* Stávající nasazení budou nadále fungovat. Po opětovném vytvoření nasazení v Azure můžete odstranit staré nasazení z OMS.
* Všechny existující funkce, které jste měli v OMS, jsou k dispozici v Azure, kde se dozvíte více o správě aktualizací, najdete v [tématu Přehled správy aktualizací](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Přejděte na portál Microsoft Azure

V pracovním prostoru OMS klikněte na **Otevřít v Azure**. Tento výběr přejde do pracovního prostoru Analýzy protokolů, který používá OMS.

![Otevřít v Azure – portál OMS](media/migrate-oms-update-deployments/link-to-azure-portal.png)

Na webu Azure Portal klikněte na **Účet automatizace.**

![Protokoly služby Azure Monitor](media/migrate-oms-update-deployments/log-analytics.png)

V účtu Automation klikněte na **Update Management**.

![Update Management](media/migrate-oms-update-deployments/azure-automation.png)

Na webu Azure Portal vyberte **účty automatizace** v části **Všechny služby**. 

V části **Nástroje pro správu**vyberte příslušný účet automatizace a klepněte na **položku Správa aktualizací**.

## <a name="recreate-existing-deployments"></a>Znovu vytvořit existující nasazení

Všechna nasazení aktualizací vytvořená na portálu OMS mají [uložené hledání](../azure-monitor/platform/computer-groups.md) známé také jako skupina počítačů se stejným názvem jako existující nasazení aktualizace. Uložené hledání obsahuje seznam počítačů, které byly naplánovány v nasazení aktualizace.

![Update Management](media/migrate-oms-update-deployments/oms-deployment.png)

Chcete-li použít toto existující uložené hledání, postupujte takto:

Pokud chcete vytvořit nové nasazení aktualizací, přejděte na portál Azure, vyberte použitý účet automation a klikněte na **Správa aktualizací**. Klepněte na **tlačítko Naplánovat nasazení aktualizace**.

![Naplánovat nasazení aktualizace](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Otevře se podokno Nasazení nové aktualizace. Zadejte hodnoty vlastností popsaných v následující tabulce a klepněte na **vytvořit**:

Pro **počítače aktualizovat**, vyberte uložené hledání používané stávající nasazení OMS.

| Vlastnost | Popis |
| --- | --- |
|Název |Jedinečný název pro identifikaci nasazení aktualizace. |
|Operační systém| Vyberte **Linux** nebo **Windows**.|
|Počítače k aktualizaci |V rozevíracím souboru vyberte uložené hledání, importovaná skupina nebo vyberte možnost Stroj a vyberte jednotlivé počítače. Pokud zvolíte možnost **Počítače**, ve sloupci **PŘIPRAVENOST AGENTA AKTUALIZACE** se zobrazí připravenost počítačů.</br> Informace o různých způsobech vytváření skupin počítačů v protokolech Azure Monitoru najdete [v tématu Skupiny počítačů v protokolech Azure Monitoru.](../azure-monitor/platform/computer-groups.md) |
|Update classifications|Vyberte všechny klasifikace aktualizací, které potřebujete. CentOS nepodporuje to po vybalení z krabice.|
|Aktualizace, které mají být vyloučeny|Zadejte aktualizace, které chcete vyloučit. Pro Windows zadejte článek KB bez předpony **KB.** Pro Linux zadejte název balíčku nebo použijte zástupný znak.  |
|Nastavení plánu|Vyberte čas, kdy chcete začít, a pak vyberte buď **Jednou** nebo **Opakovat** pro opakování. | 
| Okno údržby |Počet minut nastavených pro aktualizace. Hodnota nesmí být kratší než 30 minut nebo více než 6 hodin. |
| Řízení restartování| Určuje, jak by měly být zpracovány restartování.</br>Dostupné možnosti jsou:</br>Restartovat v případě potřeby (výchozí)</br>Vždy restartovat</br>Nikdy nerestartovat</br>Pouze restartovat – nenainstalují se aktualizace|

Kliknutím na **plánované nasazení aktualizací** zobrazíte stav nově vytvořeného nasazení aktualizace.

![nasazení nové aktualizace](media/migrate-oms-update-deployments/new-update-deployment.png)

Jak již bylo zmíněno dříve, jakmile vaše nová nasazení jsou nakonfigurovány prostřednictvím portálu Azure, stávající nasazení lze odebrat z portálu OMS.

## <a name="next-steps"></a>Další kroky

Další informace o správě aktualizací v Azure najdete v [tématu Správa aktualizací](automation-update-management.md).
