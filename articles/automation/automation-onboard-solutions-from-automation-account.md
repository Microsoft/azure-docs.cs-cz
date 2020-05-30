---
title: Povolit Azure Automation Update Management z účtu Automation
description: V tomto článku se dozvíte, jak povolit Update Management z účtu Automation.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: f739134cd066f4dcc7fdf3da16c6db99a54d6265
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204935"
---
# <a name="enable-update-management-from-an-automation-account"></a>Povolení Update Managementu z účtu Automation

Tento článek popisuje, jak můžete účet Automation použít k povolení funkce [Update Management](automation-update-management.md) pro virtuální počítače ve vašem prostředí. Pokud chcete povolit virtuální počítače Azure ve velkém měřítku, musíte povolit existující virtuální počítač pomocí Update Management. 

> [!NOTE]
> Při povolování Update Management jsou podporovány pouze určité oblasti pro propojení pracovního prostoru Log Analytics a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](how-to/region-mappings.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-offering-get-started.md) pro správu počítačů.
* [Virtuální počítač](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure na adrese https://portal.azure.com .

## <a name="enable-update-management"></a>Povolení řešení Update Management

1. Ve svém účtu Automation v části **Správa aktualizací**vyberte **Správa aktualizací** .

2. Vyberte pracovní prostor Log Analytics a účet Automation a kliknutím na **Povolit** povolte Update Management. Dokončení instalace trvá až 15 minut.

    ![Povolení řešení Update Management](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Povolení virtuálních počítačů Azure

1. Ve svém účtu Automation v části **Správa aktualizací**vyberte **Správa aktualizací** .

2. Klikněte na **+ Přidat virtuální počítače Azure** a ze seznamu vyberte jeden nebo víc virtuálních počítačů. Virtuální počítače, které se nedají povolit, jsou zobrazené šedě a nejde je vybrat. Virtuální počítače Azure můžou existovat v libovolné oblasti bez ohledu na umístění vašeho účtu Automation. 

3. Kliknutím na **Povolit** přidejte vybrané virtuální počítače do skupiny počítačů uloženého hledání této funkce.

    ![Povolení virtuálních počítačů Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Povolení virtuálních počítačů mimo Azure

Počítače, které nejsou v Azure, se musí přidat ručně. 

1. Z účtu Automation v části **Správa aktualizací**vyberte **Správa aktualizací** .

2. Klikněte na **Přidat jiný počítač než Azure**. Tato akce otevře nové okno prohlížeče s [pokyny k instalaci a konfiguraci agenta Log Analytics pro systém Windows](../azure-monitor/platform/log-analytics-agent.md) , aby počítač mohl začít vytvářet sestavy Update Management operace. Pokud povolujete počítač, který je aktuálně spravován nástrojem Operations Manager, není vyžadován nový agent a informace o pracovním prostoru jsou zadány do stávajícího agenta.

## <a name="enable-machines-in-the-workspace"></a>Povolit počítače v pracovním prostoru

Ručně nainstalované počítače nebo počítače, které už hlásí do vašeho pracovního prostoru, se musí přidat do Azure Automation, aby Update Management povolené. 

1. Z účtu Automation v části **Správa aktualizací**vyberte **Správa aktualizací** .

2. Vyberte **spravovat počítače**. Tlačítko **spravovat počítače** může být zobrazeno šedě, pokud jste předtím zvolili možnost **Povolit na všech dostupných a budoucích počítačích** .

    ![Uložená hledání](media/automation-onboard-solutions-from-automation-account/managemachines.png)

4. Pokud chcete povolit Update Management pro všechny dostupné počítače, vyberte **Povolit na všech dostupných počítačích** na stránce Správa počítačů. Tato akce zakáže ovládacímu prvku přidat počítače jednotlivě. Tato úloha přidá všechny názvy počítačů, které nahlásí do pracovního prostoru, do uloženého vyhledávacího dotazu skupiny počítačů. Když se tato akce vybere, zakáže se tlačítko **spravovat počítače** .

5. Pokud chcete funkci povolit pro všechny dostupné počítače a budoucí počítače, vyberte **Povolit na všech dostupných a budoucích počítačích**. Tato možnost odstraní uložená hledání a konfigurace oboru z pracovního prostoru a otevře funkci pro všechny počítače Azure a mimo Azure, které vytvářejí sestavy do pracovního prostoru. Když se tato akce vybere, zakáže se trvale tlačítko **spravovat počítače** , protože není k dispozici žádná konfigurace oboru.

6. V případě potřeby můžete konfigurace oboru přidat zpátky tak, že znovu přidáte počáteční uložená hledání. Další informace najdete v tématu [omezení rozsahu nasazení Update Management](automation-scope-configurations-update-management.md).

7. Pokud chcete tuto funkci povolit pro jeden nebo víc počítačů, vyberte **Povolit na vybraných počítačích** a klikněte na **Přidat** vedle každého počítače, abyste funkci povolili. Tato úloha přidá vybrané názvy počítačů do uloženého vyhledávacího dotazu skupiny počítačů pro danou funkci.

## <a name="next-steps"></a>Další kroky

* Pokud chcete použít Update Management pro virtuální počítače, přečtěte si téma [Správa aktualizací a oprav pro virtuální počítače Azure](automation-tutorial-update-management.md).
* Pokud chcete řešit obecné chyby Update Management, přečtěte si téma [řešení potíží s Update Management](troubleshoot/update-management.md).
* Informace o řešení problémů s agentem Windows Update najdete v tématu řešení potíží s [agentem pro Windows Update](troubleshoot/update-agent-issues.md).
* Informace o řešení problémů s agentem aktualizací pro Linux najdete v tématu [řešení potíží s agentem aktualizace pro Linux](troubleshoot/update-agent-issues-linux.md).
