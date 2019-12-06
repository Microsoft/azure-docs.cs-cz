---
title: Migrace nasazení aktualizace OMS do Azure
description: Tento článek popisuje, jak migrovat existující nasazení aktualizací OMS do Azure.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 07/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 08b3f11f3e44c6580df9942aab2a890115c79ba3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849492"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Migrace nasazení aktualizace OMS do Azure

Portál Operations Management Suite (OMS) je [zastaralý](../azure-monitor/platform/oms-portal-transition.md). Všechny funkce, které byly k dispozici na portálu OMS pro Update Management, jsou k dispozici v Azure Portal. Tento článek poskytuje informace, které potřebujete k migraci na Azure Portal.

## <a name="key-information"></a>Hlavní informace

* Existující nasazení budou fungovat i nadále. Po opětovném vytvoření nasazení v Azure můžete odstranit staré nasazení z OMS.
* Všechny existující funkce v OMS jsou k dispozici v Azure. Další informace o Update Management najdete v tématu [Update Management Overview](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Přístup k Azure Portal

V pracovním prostoru OMS klikněte na **otevřít v Azure**. Tím přejdete do pracovního prostoru Log Analytics, který používá OMS.

![Otevřít v Azure – portál OMS](media/migrate-oms-update-deployments/link-to-azure-portal.png)

V Azure Portal klikněte na **účet Automation** .

![Protokoly služby Azure Monitor](media/migrate-oms-update-deployments/log-analytics.png)

V účtu Automation kliknutím na **Update Management** otevřete Update Management.

![Správa aktualizací](media/migrate-oms-update-deployments/azure-automation.png)

V budoucnu můžete přejít přímo na Azure Portal v části **všechny služby**vyberte **účty Automation** v části **Nástroje pro správu**, vyberte příslušný účet Automation a klikněte na **Update Management**.

## <a name="recreate-existing-deployments"></a>Znovu vytvořit existující nasazení

Všechna nasazení aktualizací vytvořená na portálu OMS obsahují [uložená hledání](../azure-monitor/platform/computer-groups.md) , označované také jako skupina počítačů se stejným názvem, jako má nasazení aktualizace, které existuje. Uložené výsledky hledání obsahují seznam počítačů, které byly naplánovány v nasazení aktualizace.

![Správa aktualizací](media/migrate-oms-update-deployments/oms-deployment.png)

Pokud chcete použít toto existující uložené hledání, postupujte takto:

Pokud chcete vytvořit nové nasazení aktualizace, přejděte na Azure Portal, vyberte účet Automation, který se používá, a klikněte na **Update Management**. Klikněte na **naplánovat nasazení aktualizace**.

![Naplánovat nasazení aktualizace](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Otevře se **nové podokno nasazení aktualizace** . Zadejte hodnoty vlastností popsaných v následující tabulce a pak klikněte na **vytvořit**:

Pro počítače, které se mají aktualizovat, vyberte uložené hledání používané existujícím nasazením OMS.

| Vlastnost | Popis |
| --- | --- |
|Name (Název) |Jedinečný název pro identifikaci nasazení aktualizace. |
|Operační systém| Vyberte **Linux** nebo **Windows**.|
|Počítače k aktualizaci |V rozevíracím seznamu vyberte uložené hledání, importovanou skupinu nebo vyberte možnost počítač a vyberte jednotlivé počítače. Pokud zvolíte možnost **Počítače**, ve sloupci **PŘIPRAVENOST AGENTA AKTUALIZACE** se zobrazí připravenost počítačů.</br> Další informace o různých metodách vytváření skupin počítačů v protokolu Azure Monitor najdete v tématu [skupiny počítačů v protokolech Azure monitor](../azure-monitor/platform/computer-groups.md) |
|Klasifikace aktualizací|Vyberte všechny klasifikace aktualizací, které potřebujete. CentOS tento seznam nepodporuje.|
|Aktualizace k vyloučení|Zadejte aktualizace, které chcete vyloučit. Pro Windows zadejte článek znalostní báze bez předpony **KB** . Pro Linux zadejte název balíčku nebo použijte zástupný znak.  |
|Nastavení plánu|Vyberte čas, kdy se má spustit, a potom pro opakování vyberte buď **jednou** , nebo **opakovanou** . | 
| Časové období údržby |Počet minut, po které se nastaví aktualizace. Hodnota nesmí být kratší než 30 minut nebo více než 6 hodin. |
| Restartovat ovládací prvek| Určuje, jak by se mělo zpracovat restartování.</br>Dostupné možnosti jsou:</br>Restartovat v případě potřeby (výchozí)</br>Vždy restartovat</br>Nikdy nerestartovat</br>Pouze restartovat – nenainstalují se aktualizace|

Kliknutím na **plánovaná nasazení aktualizací** zobrazíte stav nově vytvořeného nasazení aktualizace.

![nové nasazení aktualizace](media/migrate-oms-update-deployments/new-update-deployment.png)

Jak už bylo zmíněno dříve, po konfiguraci nových nasazení prostřednictvím Azure Portal můžete existující nasazení odebrat z portálu OMS.

## <a name="next-steps"></a>Další kroky

Další informace o Update Management v Azure najdete v článku [Update Management](automation-update-management.md)
