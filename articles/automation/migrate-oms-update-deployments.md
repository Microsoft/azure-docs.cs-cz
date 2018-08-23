---
title: Proveďte migraci vašich nasazení aktualizace OMS do Azure
description: Tento článek popisuje, jak migrovat vaše stávající nasazení aktualizací OMS do Azure
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 07/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d0b380aa6046daa235098516a8c93d3ba72533a6
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "42054424"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Proveďte migraci vašich nasazení aktualizace OMS do Azure

Na portálu Operations Management Suite (OMS), která má být [zastaralé](../log-analytics/log-analytics-oms-portal-transition.md). Všechny funkce, která byla dostupná na portálu OMS Update Management je k dispozici na webu Azure Portal. Tento článek obsahuje informace, které potřebujete k migraci na webu Azure portal.

## <a name="key-information"></a>Informace o klíči

* Existující nasazení budou i nadále fungovat. Jakmile znovu vytvořit nasazení v Azure můžete odstranit staré nasazení z OMS.
* Všechny existující funkce, které jste měli v OMS jsou dostupné v Azure, další informace o správě aktualizací najdete v tématu [přehled správy aktualizací](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Přístup k webu Azure portal

Z pracovního prostoru OMS, klikněte na tlačítko **Open in Azure**. Umožňuje přejít do pracovního prostoru Log Analytics, která používá OMS.

![Otevřít v Azure – portál OMS](media/migrate-oms-update-deployments/link-to-azure-portal.png)

Na webu Azure Portal, klikněte na tlačítko **účtu Automation**

![Log Analytics](media/migrate-oms-update-deployments/log-analytics.png)

Ve vašem účtu Automation, klikněte na tlačítko **Update Management** otevřete správu aktualizací.

![Update Management](media/migrate-oms-update-deployments/azure-automation.png)

V budoucnu můžete přejít přímo na webu Azure portal, v části **všechny služby**vyberte **účty Automation** pod **nástroje pro správu**, vyberte příslušné služby Automation Účet a klikněte na tlačítko **Update Management**.

## <a name="recreate-existing-deployments"></a>Znovu vytvořit existující nasazení

Máte všechna nasazení aktualizací, které jsou vytvořené na portálu OMS [uložené výsledky hledání](../log-analytics/log-analytics-computer-groups.md) označované také jako skupinu počítačů, se stejným názvem jako nasazení aktualizace, která existuje. Uložené výsledky hledání obsahuje seznam počítačů, které bylo naplánováno v nasazení aktualizací.

![Update Management](media/migrate-oms-update-deployments/oms-deployment.png)

Pokud chcete použít tuto existující uložené výsledky hledání, postupujte podle těchto kroků:

Chcete-li vytvořit nové aktualizace nasazení, přejděte na portál Azure Portal, vyberte účet Automation, který se používá a klikněte na tlačítko **Update Management**. Klikněte na tlačítko **naplánovat nasazení aktualizací**.

![Naplánovat nasazení aktualizace](media/migrate-oms-update-deployments/schedule-update-deployment.png)

**Nové nasazení aktualizace** se otevře podokno. Zadejte hodnoty vlastností popsaných v následující tabulce a potom klikněte na tlačítko **vytvořit**:

Pro počítače, které chcete aktualizovat, vyberte uložené výsledky hledání používá existující nasazení OMS.

| Vlastnost | Popis |
| --- | --- |
|Název |Jedinečný název pro identifikaci nasazení aktualizace. |
|Operační systém| Vyberte **Linux** nebo **Windows**.|
|Počítače k aktualizaci |Vyberte uložená hledání, importované skupiny, nebo vybrat počítač z rozevíracího seznamu a vyberte jednotlivé počítače. Pokud se rozhodnete **počítače**, připravenosti na počítači se zobrazí v **připravenost agenta aktualizací** sloupce.</br> Další informace o různých metod vytváření skupiny počítačů v Log Analytics najdete v tématu [skupiny počítačů v Log Analytics](../log-analytics/log-analytics-computer-groups.md) |
|Klasifikace aktualizací|Vyberte všechny klasifikace aktualizací, které potřebujete. CentOS nepodporuje to úprav.|
|Aktualizace k vyloučení|Zadejte aktualizace k vyloučení. Pro Windows, zadejte článku znalostní BÁZE bez **KB** předponu. Pro Linux zadejte název balíčku nebo použít zástupný znak.  |
|Nastavení plánu|Vyberte čas spuštění a pak vyberte buď **jednou** nebo **periodický** opakování.|| Časové období údržby |Počet minut pro aktualizace. Hodnota nemůže být menší než 30 minut nebo déle než 6 hodin. |
| Časové období údržby |Počet minut pro aktualizace. Hodnota nemůže být menší než 30 minut a maximálně 6 hodin |
| Restartovat ovládacího prvku| Ohraničujícím zpracování restartování počítače.</br>Dostupné možnosti jsou:</br>Restartování v případě potřeby (výchozí)</br>Vždy restartovat</br>Nikdy restartování</br>Pouze restartovat – nebude instalace aktualizace|

Klikněte na tlačítko **naplánovaná nasazení aktualizací** zobrazíte stav nasazení aktualizace nově vytvořený.

![nové nasazení aktualizace](media/migrate-oms-update-deployments/new-update-deployment.png)

Jak je uvedeno nahoře, jakmile se vaše nová nasazení jsou nakonfigurované na webu Azure portal, můžete existující nasazení odebere z portálu OMS.

## <a name="next-steps"></a>Další postup

Další informace o správě aktualizací v Azure najdete v tématu [Update Management](automation-update-management.md)