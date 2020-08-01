---
title: Povolit Azure Automation Update Management z účtu Automation
description: V tomto článku se dozvíte, jak povolit Update Management z účtu Automation.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 930861c61843c5963c83d8fa6dc1efdce20853f4
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450246"
---
# <a name="enable-update-management-from-an-automation-account"></a>Povolení Update Managementu z účtu Automation

Tento článek popisuje, jak můžete účet Automation použít k povolení funkce [Update Management](update-mgmt-overview.md) pro virtuální počítače ve vašem prostředí. Pokud chcete povolit virtuální počítače Azure ve velkém měřítku, musíte povolit existující virtuální počítač pomocí Update Management.

> [!NOTE]
> Při povolování Update Management jsou podporovány pouze určité oblasti pro propojení pracovního prostoru Log Analytics a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](../index.yml) pro správu počítačů.
* [Virtuální počítač](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na web [Azure Portal](https://portal.azure.com).

## <a name="enable-update-management"></a>Povolení řešení Update Management

1. Ve svém účtu Automation v části **Správa aktualizací**vyberte **Správa aktualizací** .

2. Zvolte pracovní prostor Log Analytics a účet Automation a kliknutím na **Povolit** povolte Update Management. Dokončení instalace trvá až 15 minut.

    ![Povolení řešení Update Management](media/update-mgmt-enable-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Povolení virtuálních počítačů Azure

1. Ve svém účtu Automation v části **Správa aktualizací**vyberte **Správa aktualizací** .

2. Vyberte **+ Přidat virtuální počítače Azure** a ze seznamu vyberte jeden nebo víc virtuálních počítačů. Virtuální počítače, které se nedají povolit, jsou zobrazené šedě a nejde je vybrat. Virtuální počítače Azure můžou existovat v libovolné oblasti bez ohledu na umístění vašeho účtu Automation.

3. Vyberte **Povolit** , pokud chcete přidat vybrané virtuální počítače do skupiny počítačů uložené hledání této funkce.

    ![Povolení virtuálních počítačů Azure](media/update-mgmt-enable-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Povolení virtuálních počítačů mimo Azure

Počítače, které nejsou v Azure, se musí přidat ručně.

1. Z účtu Automation v části **Správa aktualizací**vyberte **Správa aktualizací** .

2. Vyberte **Přidat jiný počítač než Azure**. Tato akce otevře nové okno prohlížeče s [pokyny k instalaci a konfiguraci agenta Log Analytics pro systém Windows](../../azure-monitor/platform/log-analytics-agent.md) , aby mohl počítač začít vytvářet sestavy pro Update Management. Pokud povolíte počítač, který je aktuálně spravovaný pomocí Operations Manager, nový agent se nevyžaduje. Informace o pracovním prostoru jsou přidány do konfigurace agenti.

## <a name="enable-machines-in-the-workspace"></a>Povolit počítače v pracovním prostoru

Ručně nainstalované počítače nebo počítače, které už hlásí do vašeho pracovního prostoru, se musí přidat do Azure Automation, aby Update Management povolené.

1. Z účtu Automation v části **Správa aktualizací**vyberte **Správa aktualizací** .

2. Vyberte **spravovat počítače**. Tlačítko **spravovat počítače** může být zobrazeno šedě, pokud jste předtím zvolili možnost **Povolit na všech dostupných a budoucích počítačích** .

    ![Uložená hledání](media/update-mgmt-enable-automation-account/managemachines.png)

3. Pokud chcete povolit Update Management pro všechny dostupné počítače, vyberte **Povolit na všech dostupných počítačích** na stránce Správa počítačů. Tato akce zakáže ovládacímu prvku přidat počítače jednotlivě. Tato úloha přidá všechny názvy počítačů, které nahlásí do pracovního prostoru, do uloženého vyhledávacího dotazu skupiny počítačů. Když se tato akce vybere, zakáže se tlačítko **spravovat počítače** .

4. Pokud chcete funkci povolit pro všechny dostupné počítače a budoucí počítače, vyberte **Povolit na všech dostupných a budoucích počítačích**. Tato možnost odstraní uložená hledání a konfigurace oboru z pracovního prostoru a otevře funkci pro všechny počítače Azure a mimo Azure, které vytvářejí sestavy do pracovního prostoru. Když se tato akce vybere, zakáže se trvale tlačítko **spravovat počítače** , protože není k dispozici žádná konfigurace oboru.

5. V případě potřeby můžete konfigurace oboru přidat zpátky tak, že znovu přidáte počáteční uložená hledání. Další informace najdete v tématu [omezení rozsahu nasazení Update Management](update-mgmt-scope-configuration.md).

6. Pokud chcete funkci povolit pro jeden nebo víc počítačů, vyberte **Povolit na vybraných počítačích** a vyberte **Přidat** vedle každého počítače. Tato úloha přidá vybrané názvy počítačů do uloženého vyhledávacího dotazu skupiny počítačů pro danou funkci.

## <a name="next-steps"></a>Další kroky

* Pokud chcete použít Update Management pro virtuální počítače, přečtěte si téma [Správa aktualizací a oprav pro vaše virtuální](update-mgmt-manage-updates-for-vm.md)počítače.

* Pokud chcete řešit obecné chyby Update Management, přečtěte si téma [řešení potíží s Update Management](../troubleshoot/update-management.md).