---
title: Povolit Azure Automation Change Tracking a inventář z účtu Automation
description: V tomto článku se dozvíte, jak povolit Change Tracking a inventář z účtu Automation.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 302e0cc9b77605afd4012e95fc7c0ab8a22fef3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186312"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Povolení řešení Change Tracking a Inventory z účtu Automation

Tento článek popisuje, jak můžete účet Automation použít k povolení funkce [Change Tracking a inventáře](change-tracking.md) pro virtuální počítače ve vašem prostředí. Pokud chcete povolit virtuální počítače Azure ve velkém měřítku, musíte povolit existující virtuální počítač pomocí Change Tracking a inventáře. 

> [!NOTE]
> Při povolování Change Tracking a inventáře jsou podporovány pouze určité oblasti pro propojení Log Analyticsho pracovního prostoru a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](how-to/region-mappings.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](./index.yml) pro správu počítačů.
* [Virtuální počítač](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure na adrese https://portal.azure.com .

## <a name="enable-change-tracking-and-inventory"></a>Povolení řešení Change Tracking a Inventory

1. Přejděte do svého účtu Automation a v části **Správa konfigurace**vyberte buď **inventarizaci** , nebo **sledování změn** .

2. Vyberte pracovní prostor Log Analytics a účet Automation a kliknutím na **Povolit** povolte Change Tracking a inventář. Dokončení instalace trvá až 15 minut.

    ![Povolení řešení Change Tracking a Inventory](media/automation-enable-changes-from-auto-acct/onboardsolutions.png)

## <a name="enable-azure-vms"></a>Povolení virtuálních počítačů Azure

1. Ve svém účtu Automation v části **Správa konfigurace**vyberte **inventarizace** nebo **sledování změn** .

2. Klikněte na **+ Přidat virtuální počítače Azure** a ze seznamu vyberte jeden nebo víc virtuálních počítačů. Virtuální počítače, které se nedají povolit, jsou zobrazené šedě a nejde je vybrat. Virtuální počítače Azure můžou existovat v libovolné oblasti bez ohledu na umístění vašeho účtu Automation. 

3. Kliknutím na **Povolit** přidejte vybrané virtuální počítače do skupiny počítačů uloženého hledání této funkce. Další informace najdete v tématu [omezení rozsahu nasazení Change Tracking a inventáře](automation-scope-configurations-change-tracking.md).

    ![Povolení virtuálních počítačů Azure](media/automation-enable-changes-from-auto-acct/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Povolení virtuálních počítačů mimo Azure

Počítače, které nejsou v Azure, se musí přidat ručně. 

1. V účtu Automation vyberte v části **Správa konfigurace** **inventarizaci** nebo **sledování změn** .

2. Klikněte na **Přidat jiný počítač než Azure**. Tato akce otevře nové okno prohlížeče s [pokyny k instalaci a konfiguraci agenta Log Analytics pro systém Windows](../azure-monitor/platform/log-analytics-agent.md) , aby počítač mohl začít vytvářet sestavy Change Tracking a operace inventáře. Pokud povolujete počítač, který je aktuálně spravován nástrojem Operations Manager, není vyžadován nový agent a informace o pracovním prostoru jsou zadány do stávajícího agenta.

## <a name="enable-machines-in-the-workspace"></a>Povolit počítače v pracovním prostoru

Ručně nainstalované počítače nebo počítače, které už hlásí do vašeho pracovního prostoru, se musí přidat do Azure Automation Change Tracking a inventáře, který se má povolit. 

1. Ve svém účtu Automation v části **Správa konfigurace**vyberte **inventarizace** nebo **sledování změn** .

2. Vyberte **spravovat počítače**. Tlačítko **spravovat počítače** může být zobrazeno šedě, pokud jste předtím zvolili možnost **Povolit na všech dostupných a budoucích počítačích** .

    ![Uložená hledání](media/automation-enable-changes-from-auto-acct/managemachines.png)

3. Pokud chcete povolit Change Tracking a inventář pro všechny dostupné počítače, vyberte **Povolit na všech dostupných počítačích** na stránce Správa počítačů. Tato akce zakáže ovládacímu prvku přidat počítače jednotlivě. Tato úloha přidá všechny názvy počítačů, které nahlásí do pracovního prostoru, do uloženého vyhledávacího dotazu skupiny počítačů. Když se tato akce vybere, zakáže se tlačítko **spravovat počítače** .

4. Pokud chcete funkci povolit pro všechny dostupné počítače a budoucí počítače, vyberte **Povolit na všech dostupných a budoucích počítačích**. Tato možnost odstraní uložená hledání a konfigurace oboru z pracovního prostoru a otevře funkci pro všechny počítače Azure a mimo Azure, které vytvářejí sestavy do pracovního prostoru. Když se tato akce vybere, zakáže se trvale tlačítko **spravovat počítače** , protože není k dispozici žádná konfigurace oboru.

5. V případě potřeby můžete konfigurace oboru přidat zpátky tak, že znovu přidáte počáteční uložená hledání. Další informace najdete v tématu [omezení rozsahu nasazení Change Tracking a inventáře](automation-scope-configurations-change-tracking.md).

6. Pokud chcete tuto funkci povolit pro jeden nebo víc počítačů, vyberte **Povolit na vybraných počítačích** a klikněte na **Přidat** vedle každého počítače, abyste funkci povolili. Tato úloha přidá vybrané názvy počítačů do uloženého vyhledávacího dotazu skupiny počítačů pro danou funkci.

## <a name="next-steps"></a>Další kroky

* Informace o práci s touto funkcí najdete v tématu [správa Change Tracking a inventáře](change-tracking-file-contents.md).
* Řešení obecných problémů s funkcí najdete v tématu [řešení potíží s Change Tracking a inventářem](troubleshoot/change-tracking.md).
