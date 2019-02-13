---
title: Virtuální počítač aktualizace a správa pomocí služby Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak používat řešení Update Management, Change Tracking a Inventory ve službě Azure Automation pro správu Windows a virtuální počítače s Linuxem, které jsou nasazené ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: rtiberiu
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 28f8300b83f55f4b083aa1e740dcbf1db0f1dc31
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168133"
---
# <a name="azure-stack-vm-update-and-management"></a>Azure Stack VM update a správu
Následující funkce řešení Azure Automation můžete použít ke správě Windows a virtuální počítače s Linuxem, které jsou nasazeny pomocí služby Azure Stack:

- **[Správa aktualizací](https://docs.microsoft.com/azure/automation/automation-update-management)**. Řešení Update Management můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro tyto virtuální počítače Linux a Windows.

- **[Sledování změn](https://docs.microsoft.com/azure/automation/automation-change-tracking)**. Změny nainstalovaného softwaru, služby Windows, Windows registru a souborů a procesy démon Linuxu na monitorovaných serverech odesílají do služby Log Analytics v cloudu pro zpracování. Logika platí pro přijatá data a cloudové službě zaznamenává data. Podle informací uvedených na řídicím panelu řešení Change Tracking, můžete snadno zobrazit změny, které byly provedeny v serverové infrastruktuře.

- **[Inventář](https://docs.microsoft.com/azure/automation/automation-vm-inventory)**. Sledování pro virtuální počítač Azure Stack inventáře poskytuje založené na prohlížeči uživatelské rozhraní pro nastavení a konfiguraci shromažďování dat pro inventarizaci. 

> [!IMPORTANT]
> Tato řešení jsou stejné jako ty, které slouží ke správě virtuálních počítačů Azure. Azure a virtuální počítače Azure Stack se spravují stejným způsobem, rozhraní, pomocí stejných nástrojů. Virtuální počítače Azure Stack také se počítají, stejně jako virtuální počítače Azure pomocí Update Management, Change Tracking a Inventory řešení pomocí služby Azure Stack.

## <a name="prerequisites"></a>Požadavky
Než začnete používat tyto funkce Aktualizovat a spravovat virtuální počítače Azure Stack musí splnit několik požadavků. Patří sem kroky, které se musí vzít v na webu Azure portal, jakož i na portálu pro správu služby Azure Stack.

### <a name="in-the-azure-portal"></a>Na webu Azure Portal
Pokud chcete použít inventář, Change Tracking a Update Management Azure automation funkcí pro virtuální počítače Azure Stack, je nejprve potřeba povolit tato řešení v Azure.

> [!TIP]
> Pokud už máte tyto funkce povolené pro virtuální počítače Azure, můžete použít už existující pracovní prostor LogAnalytics pověření. Pokud už máte LogAnalytics ID pracovního prostoru a primární klíč, který chcete použít, přeskočte k části [v další části](./vm-update-management.md#in-the-azure-stack-administration-portal). V opačném případě pokračujte v této části, abyste vytvořili nový účet LogAnalytics pracovní prostor a automatizace.

Prvním krokem při povolování těchto řešení je [vytvořit pracovní prostor LogAnalytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace) ve vašem předplatném Azure. Pracovní prostor Log Analytics je jedinečné prostředí Log Analytics s vlastním úložištěm dat, zdroje dat a řešení. Po vytvoření pracovního prostoru, poznamenejte si ID pracovního prostoru a klíč. Chcete-li zobrazit tyto informace, přejděte do okna pracovního prostoru, klikněte na **upřesňující nastavení**a projděte si **ID pracovního prostoru** a **primární klíč** hodnoty. 

V dalším kroku je nutné [vytvořit účet Automation](https://docs.microsoft.com/azure/automation/automation-create-standalone-account). Účet Automation je kontejnerem pro vaše prostředky Azure Automation. Poskytuje způsob, jak oddělení vašich prostředí nebo dalšího uspořádání pracovních postupů služby Automation a prostředky. Po vytvoření účtu automation, potřebujete povolení inventáře, sledování změn a aktualizace funkcí pro správu. K tomuto účelu těchto kroků k povolení jednotlivých funkcí:

1. Na webu Azure Portal přejděte do účtu Automation, který chcete použít.

2. Vyberte řešení, které chcete povolit (buď **inventáře**, **Change tracking**, nebo **Správa aktualizací**).

3. Použití **vyberte pracovní prostor...**  rozevírací seznam a vyberte pracovní prostor Log Analytics k použití.

4. Ověřte správnost všechny zbývající informace a potom klikněte na tlačítko **povolit** povolte řešení.

5. Opakujte kroky 2 až 4 povolit všechna tři řešení. 

   [![](media/vm-update-management/1-sm.PNG "Povolení funkcí, účet služby automation")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="in-the-azure-stack-administration-portal"></a>V portálu pro správu služby Azure Stack
Po povolení řešení Azure Automation na webu Azure Portal, dále musíte přihlásit na portál pro správu služby Azure Stack jako správce cloudu a stáhnout **Azure aktualizace a správa konfigurace** a  **Aktualizace a správa konfigurace pro Linux Azure** položky marketplace rozšíření Azure Stack. 

   ![Azure aktualizace a konfigurace správy rozšíření položky marketplace](media/vm-update-management/2.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>Povolení řešení Update Management pro virtuální počítače Azure Stack
Postupujte podle těchto kroků k povolení správy aktualizací pro virtuální počítače Azure Stack.

1. Přihlaste se na portálu user portal pro Azure Stack.

2. Na portálu Azure Stack uživatele –, přejděte do okna rozšíření virtuálních počítačů, pro které chcete povolit tato řešení, klikněte na tlačítko **+ přidat**, vyberte **Azure aktualizace a správa konfigurace** rozšíření a klikněte na tlačítko **vytvořit**:

   [![](media/vm-update-management/3-sm.PNG "Okno rozšíření virtuálního počítače")](media/vm-update-management/3-lg.PNG#lightbox)

3. Zadejte dříve vytvořeného ID pracovního prostoru a primární klíč k propojení agenta k pracovnímu prostoru LogAnalytics a klikněte na tlačítko **OK** k nasazení rozšíření.

   [![](media/vm-update-management/4-sm.PNG "Zadejte ID pracovního prostoru a klíč")](media/vm-update-management/4-lg.PNG#lightbox) 

4. Jak je popsáno v [dokumentace ke službě automation update management](https://docs.microsoft.com/azure/automation/automation-update-management), je potřeba povolit řešení Update Management pro každý virtuální počítač, který chcete spravovat. Pokud chcete řešení povolit u všech virtuálních počítačů odesílajících sestavy do pracovního prostoru, vyberte **Správa aktualizací**, klikněte na tlačítko **spravovat počítače**a pak vyberte **povolit na všech dostupných a budoucích počítačích** možnost.

   [![](media/vm-update-management/5-sm.PNG "Zadejte ID pracovního prostoru a klíč")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > Opakujte tento krok umožňuje každé řešení pro virtuální počítače Azure Stack této sestavy do pracovního prostoru. 
  
Po povolení rozšíření Azure aktualizace a správa konfigurace se kontrola provádí dvakrát za den pro každý spravovaný virtuální počítač. Dotaz na čas poslední aktualizace k určení, jestli se změnil stav každých 15 minut se volá rozhraní API. Pokud došlo ke změně stavu, zahájí se kontrola kompatibility.

Až se virtuální počítače jsou prohledávány, zobrazí se v účtu Azure Automation v řešení Update Management: 

   [![](media/vm-update-management/6-sm.PNG "Zadejte ID pracovního prostoru a klíč")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> Může trvat 30 minut až 6 hodin na řídicím panelu zobrazí aktualizovaná data ze spravovaných počítačů.

Virtuální počítače Azure Stack mohou být součástí nyní naplánovaná nasazení aktualizací společně s virtuálními počítači Azure.

## <a name="enable-update-management-using-a-resource-manager-template"></a>Povolení řešení Update Management pomocí šablony Resource Manageru
Pokud máte velký počet virtuálních počítačů Azure Stack, můžete použít [tuto šablonu Azure Resource Manageru](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) mnohem snazší nasadit řešení na virtuálních počítačích. Šablona nasadí rozšíření Microsoft Monitoring Agent do existujícího virtuálního počítače Azure Stack a přidá jej do existujícího pracovního prostoru Azure LogAnalytics.
 
## <a name="next-steps"></a>Další postup
[Optimalizace výkonu virtuálního počítače s SQL serverem](azure-stack-sql-server-vm-considerations.md)
