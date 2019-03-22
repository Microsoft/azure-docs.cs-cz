---
title: Virtuální počítač aktualizace a správa pomocí služby Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak používat Azure Monitor pro virtuální počítače, správu aktualizací, řešení Change Tracking a Inventory řešení ve službě Azure Automation ke správě Windows a virtuální počítače s Linuxem, které jsou nasazené ve službě Azure Stack.
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
ms.date: 03/20/2019
ms.author: jeffgilb
ms.reviewer: rtiberiu
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: cb8258c0f837d0e70ba87a26246f055b0efe5c00
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316139"
---
# <a name="azure-stack-vm-update-and-management"></a>Azure Stack VM update a správu
Následující funkce řešení Azure Automation můžete použít ke správě Windows a virtuální počítače s Linuxem, které jsou nasazeny pomocí služby Azure Stack:

- **[Správa aktualizací](https://docs.microsoft.com/azure/automation/automation-update-management)**. Řešení Update Management můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro tyto virtuální počítače Linux a Windows.

- **[Sledování změn](https://docs.microsoft.com/azure/automation/automation-change-tracking)**. Změny nainstalovaného softwaru, služby Windows, Windows registru a souborů a procesy démon Linuxu na monitorovaných serverech se odesílají do služby Azure Monitor v cloudu pro zpracování. Logika platí pro přijatá data a cloudové službě zaznamenává data. Podle informací uvedených na řídicím panelu řešení Change Tracking, můžete snadno zobrazit změny, které byly provedeny v serverové infrastruktuře.

- **[Inventář](https://docs.microsoft.com/azure/automation/automation-vm-inventory)**. Sledování pro virtuální počítač Azure Stack inventáře poskytuje založené na prohlížeči uživatelské rozhraní pro nastavení a konfiguraci shromažďování dat pro inventarizaci.

- **[Azure Monitor pro virtuální počítače](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)**. Azure Monitor pro virtuální počítače monitoruje virtuálních počítačů Azure a Azure Stack (VM) a škálovacích sad virtuálních počítačů ve velkém měřítku. Analyzuje výkonu a stavu systému Windows a virtuální počítače s Linuxem a monitoruje jejich procesy a závislosti na jiných zdrojů a externí procesů. 

> [!IMPORTANT]
> Tato řešení jsou stejné jako ty, které slouží ke správě virtuálních počítačů Azure. Azure a virtuální počítače Azure Stack se spravují stejným způsobem, rozhraní, pomocí stejných nástrojů. Virtuální počítače Azure Stack také se počítají, stejně jako virtuální počítače Azure pomocí řešení Update Management, Change Tracking, inventáře a monitorování virtuálních počítačů Azure pomocí služby Azure Stack.

## <a name="prerequisites"></a>Požadavky
Než začnete používat tyto funkce Aktualizovat a spravovat virtuální počítače Azure Stack musí splnit několik požadavků. Patří sem kroky, které se musí vzít v na webu Azure portal, jakož i na portálu pro správu služby Azure Stack.

### <a name="in-the-azure-portal"></a>Na webu Azure Portal
Použití Azure monitoru pro virtuální počítače, inventáře, Change Tracking a Update Management Azure automation funkcí pro virtuální počítače Azure Stack, je nejprve potřeba povolit tato řešení v Azure.

> [!TIP]
> Pokud už máte tyto funkce povolené pro virtuální počítače Azure, můžete použít už existující pracovní prostor LogAnalytics pověření. Pokud už máte LogAnalytics ID pracovního prostoru a primární klíč, který chcete použít, přeskočte k části [v další části](./vm-update-management.md#in-the-azure-stack-administration-portal). V opačném případě pokračujte v této části, abyste vytvořili nový účet LogAnalytics pracovní prostor a automatizace.

Prvním krokem při povolování těchto řešení je [vytvořit pracovní prostor LogAnalytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace) ve vašem předplatném Azure. Pracovní prostor Log Analytics je jedinečné prostředí protokoly Azure monitoru s vlastním úložištěm dat, zdroje dat a řešení. Po vytvoření pracovního prostoru, poznamenejte si ID pracovního prostoru a klíč. Chcete-li zobrazit tyto informace, přejděte do okna pracovního prostoru, klikněte na **upřesňující nastavení**a projděte si **ID pracovního prostoru** a **primární klíč** hodnoty. 

V dalším kroku je nutné [vytvořit účet Automation](https://docs.microsoft.com/azure/automation/automation-create-standalone-account). Účet Automation je kontejnerem pro vaše prostředky Azure Automation. Poskytuje způsob, jak oddělení vašich prostředí nebo dalšího uspořádání pracovních postupů služby Automation a prostředky. Po vytvoření účtu automation, potřebujete povolení inventáře, sledování změn a aktualizace funkcí pro správu. K tomuto účelu těchto kroků k povolení jednotlivých funkcí:

1. Na webu Azure Portal přejděte do účtu Automation, který chcete použít.

2. Vyberte řešení, které chcete povolit (buď **inventáře**, **Change tracking**, nebo **Správa aktualizací**).

3. Použití **vyberte pracovní prostor...**  rozevírací seznam a vyberte pracovní prostor Log Analytics k použití.

4. Ověřte správnost všechny zbývající informace a potom klikněte na tlačítko **povolit** povolte řešení.

5. Opakujte kroky 2 až 4 povolit všechna tři řešení. 

   [![](media/vm-update-management/1-sm.PNG "Povolení funkcí, účet služby automation")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="enable-azure-monitor-for-vms"></a>Povolit monitorování Azure pro virtuální počítače

Azure Monitor pro virtuální počítače monitoruje virtuální počítače Azure (VM) a škálovacích sad virtuálních počítačů ve velkém měřítku. Analyzuje výkonu a stavu systému Windows a virtuální počítače s Linuxem a monitoruje jejich procesy a závislosti na jiných zdrojů a externí procesů.

Azure Monitor pro virtuální počítače jako řešení, zahrnuje podporu pro monitorování výkonu a používání závislosti virtuálních počítačů, které jsou hostované v místním prostředí nebo jiného zprostředkovatele cloudu. Tři klíčové funkce poskytovat podrobnější informace:

1. Logické součásti virtuálních počítačů Azure, který spustí Windows a Linux: Se měří podle kritérií stavu předem nakonfigurované a že vás upozornit, když je splněna podmínka Vyhodnocená. 

2. Předdefinované populárních grafy výkonu: Zobrazit základní metriky výkonu z hostovaného operačního systému virtuálního počítače.

3. Mapa závislostí: Zobrazí vzájemně propojených součástí virtuálního počítače z různých skupin prostředků a předplatných.

Po vytvoření pracovního prostoru Log Analytics, je potřeba povolit čítače výkonu v pracovním prostoru pro kolekci v Linuxu a virtuálních počítačů s Windows a nainstalovat a povolit ServiceMap a InfrastructureInsights řešení ve vašem pracovním prostoru. Proces je popsán v [nasazení Azure Monitor pro virtuální počítače](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-onboard#deploy-azure-monitor-for-vms) průvodce.

### <a name="in-the-azure-stack-administration-portal"></a>V portálu pro správu služby Azure Stack
Po povolení řešení Azure Automation na webu Azure Portal, dále musíte přihlásit na portál pro správu služby Azure Stack jako správce cloudu a stáhnout **Azure Monitor, aktualizace a správa konfigurace** a **Azure Monitor, aktualizace a správa konfigurace pro Linux** položky marketplace rozšíření Azure Stack. 

   ![Azure Monitor, aktualizace a konfigurace správy rozšíření položky marketplace](media/vm-update-management/2.PNG) 

Pokud chcete povolit monitorování Azure pro virtuální počítače mapování řešení a získání přehledů o síti závislosti, musíte si také stáhnout **Azure Monitor závislosti Agent**:

   ![Azure Monitor agenta závislostí](media/vm-update-management/2-dependency.PNG) 

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

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack"></a>Povolit monitorování Azure pro virtuální počítače spuštěné ve službě Azure Stack
Když má virtuální počítač **Azure Monitor, aktualizace a správa konfigurace** a **Azure Monitor závislosti Agent** nainstalovaná rozšíření, spustí vykazujících data za [Azure Monitor pro virtuální počítače](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) řešení. 

> [!TIP]
> **Azure Monitor závislosti Agent** rozšíření nevyžaduje žádné parametry. Azure Monitor pro agenta závislostí mapování virtuálních počítačů nebude přenášet vlastní data a nevyžaduje žádné změny brány firewall nebo porty. Mapy dat je vždy přenášených v rámci agenta Log Analytics ve službě Azure Monitor, buď přímo nebo prostřednictvím [bránu OMS](https://docs.microsoft.com/azure/azure-monitor/platform/gateway) Pokud zásady zabezpečení IT neumožňují počítače v síti pro připojení k Internetu.

Azure Monitor pro virtuální počítače obsahuje sadu grafy výkonu, které se zaměřují provádění několika klíčových ukazatelů výkonu (KPI), které vám pomohou určit, jak dobře virtuálního počítače. Grafy zobrazit využití prostředků za časové období, abyste mohli identifikovat kritické body, anomálie, nebo přepněte do pohledu výpis každý počítač, chcete-li zobrazit využití prostředků na základě metriky vybrané. I když existují mnoho prvků vzít v úvahu při práci s výkonem, související s Azure Monitor pro monitorování virtuálních počítačů, klíče operačního systému ukazatele výkonu procesoru, paměti, síťového adaptéru a využití disku. Výkon doplňuje funkci monitorování stavu a pomáhá vystavit problémy, které označují selhání součásti systému je to možné, podpora ladění a optimalizace pro dosažení efektivity nebo podporují plánování kapacity.

   ![Azure kartu monitorování výkonu](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

Zobrazení komponenty zjištěných aplikací na virtuální počítače Windows a Linuxem běžících v Azure stacku můžete pozorovat dvě možnosti, jak pomocí Azure monitoru pro virtuální počítače z virtuálního počítače přímo nebo přes skupiny virtuálních počítačů ze služby Azure Monitor.
[Pomocí Azure monitoru pro virtuální počítače (preview) mapovat na seznámení s komponentami aplikace](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-maps) článek vám pomůže pochopit prostředí mezi dvěma perspektivy a jak používat funkci Mapa.

   ![Azure kartu monitorování výkonu](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)


## <a name="enable-update-management-using-a-resource-manager-template"></a>Povolení řešení Update Management pomocí šablony Resource Manageru
Pokud máte velký počet virtuálních počítačů Azure Stack, můžete použít [tuto šablonu Azure Resource Manageru](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) mnohem snazší nasadit řešení na virtuálních počítačích. Šablona nasadí rozšíření Microsoft Monitoring Agent do existujícího virtuálního počítače Azure Stack a přidá jej do existujícího pracovního prostoru Azure LogAnalytics.
 
## <a name="next-steps"></a>Další postup
[Optimalizace výkonu virtuálního počítače s SQL serverem](azure-stack-sql-server-vm-considerations.md)




