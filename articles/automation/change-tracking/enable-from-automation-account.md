---
title: Povolit Azure Automation Change Tracking a inventář z účtu Automation
description: V tomto článku se dozvíte, jak povolit Change Tracking a inventář z účtu Automation.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 32fb95c88d632cc2c51cd2390f0244e9c1927051
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585898"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Povolení řešení Change Tracking a Inventory z účtu Automation

Tento článek popisuje, jak můžete účet Automation použít k povolení [Change Tracking a inventáře](overview.md) pro virtuální počítače ve vašem prostředí. Pokud chcete povolit virtuální počítače Azure ve velkém měřítku, musíte povolit existující virtuální počítač pomocí Change Tracking a inventáře.

> [!NOTE]
> Při povolování Change Tracking a inventáře jsou podporovány pouze určité oblasti pro propojení Log Analyticsho pracovního prostoru a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](../automation-security-overview.md) pro správu počítačů.
* [Virtuální počítač](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure na adrese https://portal.azure.com .

## <a name="enable-change-tracking-and-inventory"></a>Povolení řešení Change Tracking a Inventory

1. Přejděte do svého účtu Automation a v části **Správa konfigurace** vyberte buď **inventarizaci** , nebo **sledování změn** .

2. Vyberte pracovní prostor Log Analytics a účet Automation a kliknutím na **Povolit** povolte Change Tracking a inventář. Dokončení instalace trvá až 15 minut.

    ![Povolení řešení Change Tracking a Inventory](media/enable-from-automation-account/enable-feature.png)

## <a name="enable-azure-vms"></a>Povolení virtuálních počítačů Azure

1. Ve svém účtu Automation v části **Správa konfigurace** vyberte **inventarizace** nebo **sledování změn** .

2. Klikněte na **+ Přidat virtuální počítače Azure** a ze seznamu vyberte jeden nebo víc virtuálních počítačů. Virtuální počítače, které se nedají povolit, jsou zobrazené šedě a nejde je vybrat. Virtuální počítače Azure můžou existovat v libovolné oblasti bez ohledu na umístění vašeho účtu Automation. 

3. Kliknutím na **Povolit** přidejte vybrané virtuální počítače do skupiny počítačů uloženého hledání této funkce. Další informace najdete v tématu [omezení rozsahu nasazení Change Tracking a inventáře](manage-scope-configurations.md).

      [![Povolení virtuálních počítačů Azure](./media/enable-from-automation-account/enable-azure-vms.png)](./media/enable-from-automation-account/enable-azure-vms-expanded.png#lightbox)

## <a name="enable-non-azure-vms"></a>Povolení virtuálních počítačů mimo Azure

Počítače, které nejsou v Azure, se musí přidat ručně. Doporučujeme nainstalovat agenta Log Analytics pro Windows nebo Linux, a to tak, že nejprve připojíte počítač k [serverům s podporou ARC Azure](../../azure-arc/servers/overview.md)a pak pomocí Azure Policy přiřadíte předdefinované zásady pro [nasazení agenta Log Analytics pro počítače se systémem *Linux* nebo *Windows* Azure](../../governance/policy/samples/built-in-policies.md#monitoring) . Pokud plánujete také monitorování počítačů pomocí Azure Monitor pro virtuální počítače, použijte raději iniciativu [Enable Azure monitor pro virtuální počítače](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

1. V účtu Automation vyberte v části **Správa konfigurace** **inventarizaci** nebo **sledování změn** .

2. Klikněte na **Přidat jiný počítač než Azure**. Tato akce otevře nové okno prohlížeče s [pokyny k instalaci a konfiguraci agenta Log Analytics pro systém Windows](../../azure-monitor/agents/log-analytics-agent.md) , aby počítač mohl začít vytvářet sestavy Change Tracking a operace inventáře. Pokud povolujete počítač, který je aktuálně spravován nástrojem Operations Manager, není vyžadován nový agent a informace o pracovním prostoru jsou zadány do stávajícího agenta.

## <a name="enable-machines-in-the-workspace"></a>Povolit počítače v pracovním prostoru

Ručně nainstalované počítače nebo počítače, které už hlásí do vašeho pracovního prostoru, se musí přidat do Azure Automation Change Tracking a inventáře, který se má povolit.

1. Ve svém účtu Automation v části **Správa konfigurace** vyberte **inventarizace** nebo **sledování změn** .

2. Vyberte **spravovat počítače**. Pokud jste předtím zvolili možnost **Povolit na všech dostupných a budoucích počítačích** , možnost **spravovat počítače** může být šedá.

    ![Uložená hledání](media/enable-from-automation-account/manage-machines.png)

3. Pokud chcete povolit Change Tracking a inventář pro všechny dostupné počítače, vyberte **Povolit na všech dostupných počítačích** na stránce **Správa počítačů** . Tato akce zakáže ovládacímu prvku přidat počítače jednotlivě a přidá všechny počítače, které nastavují sestavy do pracovního prostoru, do uloženého vyhledávacího dotazu skupiny počítačů. Když se tato akce vybere, zakáže se možnost **spravovat počítače** .

4. Pokud chcete funkci povolit pro všechny dostupné počítače a budoucí počítače, vyberte **Povolit na všech dostupných a budoucích počítačích**. Tato možnost odstraní uloženou konfiguraci hledání a rozsahu z pracovního prostoru a otevře ji pro všechny počítače Azure a mimo Azure, které vytvářejí sestavy do pracovního prostoru. Když se tato akce vybere, zakáže se možnost **spravovat počítače** trvale, protože není k dispozici žádná konfigurace oboru.

    > [!NOTE]
    > Vzhledem k tomu, že tato možnost odstraní uloženou konfiguraci hledání a rozsahu v rámci Log Analytics, je důležité před výběrem této možnosti odebrat všechna zámky pro odstranění v pracovním prostoru Log Analytics. Pokud to neuděláte, možnost odebrání konfigurací neproběhne a je nutné je odebrat ručně.

5. V případě potřeby můžete konfiguraci oboru přidat zpátky tak, že znovu přidáte počáteční uložené výsledky hledání. Další informace najdete v tématu [omezení rozsahu nasazení Change Tracking a inventáře](manage-scope-configurations.md).

6. Pokud chcete tuto funkci povolit pro jeden nebo víc počítačů, vyberte **Povolit na vybraných počítačích** a klikněte na **Přidat** vedle každého počítače, abyste funkci povolili. Tato úloha přidá vybrané názvy počítačů do uloženého vyhledávacího dotazu skupiny počítačů pro danou funkci.

## <a name="next-steps"></a>Další kroky

* Podrobnosti o práci s funkcí najdete v tématu [správa Change Tracking](manage-change-tracking.md) a [Správa inventáře](manage-inventory-vms.md).

* Řešení obecných problémů s funkcí najdete v tématu [řešení potíží s Change Tracking a inventářem](../troubleshoot/change-tracking.md).
