---
title: Povolit Azure Automation Update Management z účtu Automation
description: V tomto článku se dozvíte, jak povolit Update Management z účtu Automation.
services: automation
ms.date: 10/15/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 81b46bd1e30efff81748389ef62c46410479fb4b
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206624"
---
# <a name="enable-update-management-from-an-automation-account"></a>Povolení Update Managementu z účtu Automation

Tento článek popisuje, jak můžete účet Automation použít k povolení funkce [Update Management](update-mgmt-overview.md) pro virtuální počítače ve vašem prostředí, včetně počítačů nebo serverů zaregistrovaných u [serverů s podporou ARC Azure](../../azure-arc/servers/overview.md) (Preview). Pokud chcete povolit virtuální počítače Azure ve velkém měřítku, musíte povolit existující virtuální počítač Azure pomocí Update Management.

> [!NOTE]
> Při povolování Update Management jsou podporovány pouze určité oblasti pro propojení pracovního prostoru Log Analytics a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](../index.yml) pro správu počítačů.
* [Virtuální počítač Azure](../../virtual-machines/windows/quick-create-portal.md)nebo virtuální počítač nebo server zaregistrovaný u serverů s povoleným ARC (Preview). Virtuální počítače nebo servery mimo Azure musí mít nainstalovaného [agenta Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) pro Windows nebo Linux a vytváření sestav k pracovnímu prostoru, který je propojený s účtem Automation Update Management je povolený v. Agent se dá nainstalovat na servery s podporou ARC nasazením [rozšíření virtuálního počítače azure Log Analytics](../../azure-arc/servers/manage-vm-extensions.md) pomocí Azure ARC.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

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

3. Pokud chcete povolit Update Management pro všechny dostupné počítače, které se hlásí do pracovního prostoru, vyberte **Povolit na všech dostupných počítačích** na stránce Správa počítačů. Tato akce zakáže ovládacímu prvku přidat počítače jednotlivě a přidá všechny počítače, které nastavují sestavy do pracovního prostoru, do uloženého vyhledávacího dotazu skupiny počítačů `MicrosoftDefaultComputerGroup` . Když se tato akce vybere, zakáže se možnost **spravovat počítače** .

4. Pokud chcete funkci povolit pro všechny dostupné počítače a budoucí počítače, vyberte **Povolit na všech dostupných a budoucích počítačích**. Tato možnost odstraní uloženou konfiguraci hledání a rozsahu z pracovního prostoru a umožní, aby funkce zahrnovala všechny počítače s Azure a mimo Azure, které aktuálně nebo v budoucnu, nahlásily do pracovního prostoru. Když se tato akce vybere, zakáže možnost **spravovat počítače** trvale, protože není dostupná žádná konfigurace oboru.

    > [!NOTE]
    > Vzhledem k tomu, že tato možnost odstraní uloženou konfiguraci hledání a rozsahu v rámci Log Analytics, je důležité před výběrem této možnosti odebrat všechna zámky pro odstranění v pracovním prostoru Log Analytics. Pokud to neuděláte, možnost odebrání konfigurací neproběhne a je nutné je odebrat ručně.

5. V případě potřeby můžete konfiguraci oboru přidat zpátky tak, že znovu přidáte počáteční uložený vyhledávací dotaz. Další informace najdete v tématu [omezení rozsahu nasazení Update Management](update-mgmt-scope-configuration.md).

6. Pokud chcete funkci povolit pro jeden nebo víc počítačů, vyberte **Povolit na vybraných počítačích** a vyberte **Přidat** vedle každého počítače. Tato úloha přidá vybrané názvy počítačů do uloženého vyhledávacího dotazu skupiny počítačů pro danou funkci.

## <a name="next-steps"></a>Další kroky

* Pokud chcete použít Update Management pro virtuální počítače, přečtěte si téma [Správa aktualizací a oprav pro vaše virtuální](update-mgmt-manage-updates-for-vm.md)počítače.

* Pokud už nepotřebujete spravovat virtuální počítače nebo servery s Update Management, přečtěte si téma [Odebrání virtuálních počítačů z Update Management](update-mgmt-remove-vms.md).

* Pokud chcete řešit obecné chyby Update Management, přečtěte si téma [řešení potíží s Update Management](../troubleshoot/update-management.md).
