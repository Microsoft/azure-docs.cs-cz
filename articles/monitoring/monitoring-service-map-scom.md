---
title: Řešení Service Map integrace s nástrojem System Center Operations Manager | Dokumentace Microsoftu
description: Service Map je řešení v Azure, které automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikace mezi těmito službami. Tento článek popisuje použitím řešení Service Map automaticky vytvořit distribuovanou aplikaci diagramy v nástroji Operations Manager.
services: monitoring
documentationcenter: ''
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
ms.openlocfilehash: b9146bfc284a469f12eb626c2434f4afe52335dc
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50250432"
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>Řešení Service Map integrace s nástrojem System Center Operations Manager
  > [!NOTE]
  > Tato funkce je ve verzi public preview.
  > 
  
Service Map automaticky rozpozná komponenty aplikace v systémech Windows a Linux a mapuje komunikaci mezi službami. Řešení Service Map umožňuje zobrazit vaše servery tak, jak přirozeným způsobem, jako propojené systémy, které doručují důležité služby. Service Map ukazuje propojení mezi servery, procesy a porty v jakékoli architektuře propojené TCP, žádnou konfiguraci kromě instalace agenta vyžaduje. Další informace najdete v tématu [dokumentace ke službě Service Map]( monitoring-service-map.md).

Díky této integraci mezi službami Service Map a System Center Operations Manager můžete automaticky vytvořit diagramy distribuované aplikace v nástroji Operations Manager, které jsou založeny na mapách dynamické závislostí v Service Map.

## <a name="prerequisites"></a>Požadavky
* Skupinu pro správu nástroje Operations Manager (2012 R2 nebo novější), který spravuje sadu serverů.
* Pracovní prostor Log Analytics s povolené řešení Service Map.
* Sada servery (aspoň jeden), které jsou spravovány nástrojem Operations Manager a odesílání dat do Service Map. Servery se systémy Windows a Linux jsou podporovány.
* Objekt služby s přístupem k předplatnému Azure, který je přidružený k pracovnímu prostoru Log Analytics. Další informace najdete v části [vytvoření instančního objektu](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Nainstalujte sadu management pack Service Map
Povolení integrace mezi nástrojem Operations Manager a Service Map importováním Microsoft.SystemCenter.ServiceMap komplet sady management pack (Microsoft.SystemCenter.ServiceMap.mpb). Můžete si stáhnout sady management pack z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55763). Sada obsahuje následující sady management Pack:
* Zobrazení aplikací Microsoft Service Map
* Interní nástroje Microsoft System Center Service Map
* Přepsání mapy služby Microsoft System Center
* Microsoft System Center Service Map

## <a name="configure-the-service-map-integration"></a>Konfigurovat integraci řešení Service Map
Po instalaci sady management Service Map, nový uzel, **Service Map**, se zobrazí v části **Operations Management Suite** v **správu** podokně. 

>[!NOTE]
>[Operations Management Suite se kolekce služeb](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) , které obsahovat Log Analytics, která je nyní součástí sady [Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

Pokud chcete nakonfigurovat integraci řešení Service Map, postupujte takto:

1. Chcete-li spustit Průvodce konfigurací v **Přehled služby mapy** podokně klikněte na tlačítko **přidat pracovní prostor**.  

    ![Podokno přehled mapy služeb](media/monitoring-service-map-scom/scom-configuration.png)

2. V **konfigurace připojení** okno, zadejte název klienta nebo ID, ID aplikace (označované také jako uživatelské jméno nebo ID klienta) a heslo instančního objektu a pak klikněte na tlačítko **Další**. Další informace najdete v části [vytvoření instančního objektu](#creating-a-service-principal).

    ![Okno Konfigurace připojení](media/monitoring-service-map-scom/scom-config-spn.png)

3. V **výběr předplatného** okna, vyberte předplatné Azure, skupinu prostředků Azure (ten, který obsahuje tento pracovní prostor Log Analytics) a pracovní prostor Log Analytics a pak klikněte na tlačítko **Další**.

    ![Pracovní prostor Operations Manageru konfigurace](media/monitoring-service-map-scom/scom-config-workspace.png)

4. V **výběr skupiny počítačů** okna, vyberte skupiny počítačů mapy které služby chcete synchronizovat do nástroje Operations Manager. Klikněte na tlačítko **přidat nebo odebrat skupiny počítačů**, vyberte ze seznamu skupin **dostupné skupiny počítačů**a klikněte na tlačítko **přidat**.  Když jste hotovi s výběrem skupin, klikněte na tlačítko **Ok** na dokončení.
    
    ![Skupiny počítačů Configuration Manager Operations](media/monitoring-service-map-scom/scom-config-machine-groups.png)
    
5. V **výběr serveru** okně Konfigurovat skupinu serverů Service Map se servery, které chcete synchronizovat mezi nástrojem Operations Manager a Service Map. Klikněte na tlačítko **přidat nebo odebrat servery**.   
    
    Pro integraci sestavení diagramu distribuované aplikace pro server musí být na serveru:

    * Spravovaných nástrojem Operations Manager
    * Spravuje řešení Service Map
    * Uveden ve skupině serverů Service Map

    ![Skupina konfigurace Operations Manageru](media/monitoring-service-map-scom/scom-config-group.png)

6. Volitelné: Vyberte fond zdrojů serveru pro správu komunikovat s Log Analytics a potom klikněte na tlačítko **přidat pracovní prostor**.

    ![Fond zdrojů Configuration Manager Operations](media/monitoring-service-map-scom/scom-config-pool.png)

    Může trvat minutu konfigurace a registrace v pracovním prostoru Log Analytics. Po dokončení konfigurace nástroje Operations Manager inicializuje první synchronizace Service Map.

    ![Fond zdrojů Configuration Manager Operations](media/monitoring-service-map-scom/scom-config-success.png)


## <a name="monitor-service-map"></a>Monitor Service Map
Po připojení pracovního prostoru Log Analytics novou složku Service Map se zobrazí v **monitorování** podokně konzole nástroje Operations Manager.

![V podokně monitorování nástroje Operations Manager](media/monitoring-service-map-scom/scom-monitoring.png)

Složka řešení Service Map má čtyři uzly:
* **Aktivní výstrahy**: Zobrazuje seznam všech aktivních výstrah o komunikaci mezi nástrojem Operations Manager a Service Map.  Všimněte si, že se tato upozornění Log Analytics výstrahy, synchronizuje do nástroje Operations Manager. 

* **Servery**: jsou monitorované servery, které jsou nakonfigurovány k synchronizaci ze služeb Service Map.

    ![V podokně monitorování servery nástroje Operations Manager](media/monitoring-service-map-scom/scom-monitoring-servers.png)

* **Zobrazení závislostí skupin počítačů**: jsou uvedeny všechny skupiny počítačů, které jsou synchronizované z řešení Service Map. Klikněte na možnost žádné skupiny k zobrazení jeho diagramu distribuované aplikace.

    ![Diagramu distribuované aplikace Operations Manageru](media/monitoring-service-map-scom/scom-group-dad.png)

* **Zobrazení závislostí serveru**: Vypíše všechny servery, které jsou synchronizované z řešení Service Map. Můžete kliknout na libovolný server zobrazíte jeho diagramu distribuované aplikace.

    ![Diagramu distribuované aplikace Operations Manageru](media/monitoring-service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Upravit nebo odstranit pracovní prostor
Můžete upravit nebo odstranit nakonfigurovaný pracovní prostor prostřednictvím **Přehled služby mapy** podokně (**správu** podokně > **Operations Management Suite**  >  **Service Map**).

>[!NOTE]
>[Operations Management Suite se kolekce služeb](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) , které obsahovat Log Analytics, která je nyní součástí sady [Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

Teď můžete nakonfigurovat pouze jeden pracovní prostor Log Analytics.

![V podokně pracovní prostor Operations Manageru upravit](media/monitoring-service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Konfigurace pravidla a přepsání
Pravidlo, _Microsoft.SystemCenter.ServiceMapImport.Rule_, vytvoření pravidelně načítat informace z řešení Service Map. Chcete-li změnit synchronizace časování, můžete nakonfigurovat přepsání pravidla (**Authoring** podokně > **pravidla** > **Microsoft.SystemCenter.ServiceMapImport.Rule**) .

![V okně Vlastnosti přepsání Operations Manageru](media/monitoring-service-map-scom/scom-overrides.png)

* **Povolené**: Povolí nebo zakáže automatické aktualizace. 
* **IntervalMinutes**: resetování času mezi aktualizacemi. Výchozí interval je jedna hodina. Pokud chcete synchronizovat server mapy častěji, můžete změnit hodnotu.
* **TimeoutSeconds**: resetování dobu, než vyprší časový limit žádosti. 
* **TimeWindowMinutes**: časový interval pro dotazování na data obnovit. Výchozí hodnota je 60 minut okna. Maximální hodnotu povolenou Service Map je 60 minut.

## <a name="known-issues-and-limitations"></a>Známé problémy a omezení

Současný návrh představuje následující problémy a omezení:
* Můžete připojit jenom k jedné pracovní prostor Log Analytics.
* I když přidáte servery do skupiny serverů mapy služby ručně až **Authoring** podokně mapování pro tyto servery nejsou synchronizované okamžitě.  Bude se synchronizovat z řešení Service Map při další cyklus synchronizace.
* Pokud provedete změny diagramy distribuované aplikace vytvořené pomocí sady management pack, tyto změny budou přepsány pravděpodobně při další synchronizaci s řešení Service Map.

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu
Oficiální dokumentace k Azure o vytvoření instančního objektu služby naleznete v tématu:
* [Vytvoření instančního objektu pomocí Powershellu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Vytvoření instančního objektu pomocí Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Vytvoření instančního objektu pomocí webu Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Váš názor
Máte jakoukoli zpětnou vazbu pro nás o mapy služeb nebo v této dokumentaci? Navštivte naše [User Voice stránky](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), kde můžete navrhnout funkce nebo hlasovat o doporučeních existující.
