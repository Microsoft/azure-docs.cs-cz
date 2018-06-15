---
title: Integrace mapy služeb s nástrojem System Center Operations Manager | Microsoft Docs
description: Service Map je řešení v Azure, které automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikace mezi těmito službami. Tento článek popisuje pomocí mapy služeb pro automatické vytvoření diagramy distribuované aplikace v nástroji Operations Manager.
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
ms.openlocfilehash: 6fbc49584b040f952fdff147207864d2d1f6377e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887871"
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>Integrace mapy služeb s nástrojem System Center Operations Manager
  > [!NOTE]
  > Tato funkce je ve verzi public preview.
  > 
  
Service Map automaticky rozpozná komponenty aplikace v systémech Windows a Linux a mapuje komunikaci mezi službami. Mapa služeb umožňuje zobrazit vaše servery způsob, jak si myslíte z nich, jako vzájemně propojena systémy, které doručují důležité služby. Mapy služeb zobrazí připojení mezi servery, procesy a porty mezi žádné připojení TCP architektura žádnou konfiguraci vyžaduje kromě instalaci agenta. Další informace najdete v tématu [mapy služeb dokumentaci]( monitoring-service-map.md).

Díky této integraci mezi mapy služeb a System Center Operations Manager můžete automaticky vytvořit diagramy distribuované aplikace v nástroji Operations Manager, které jsou založeny na map dynamické závislostí v mapy služeb.

## <a name="prerequisites"></a>Požadavky
* Skupinu pro správu nástroje Operations Manager (2012 R2 nebo novější), je Správa u sady serverů.
* Pracovní prostor analýzy protokolů s řešením mapy služby povolena.
* Sada serverů (alespoň jeden), které spravuje nástroj Operations Manager a odesílání dat do mapy služeb. Servery se systémy Windows a Linux jsou podporovány.
* Objekt služby s přístupem k předplatnému Azure, který je přidružen pracovní prostor analýzy protokolů. Další informace, přejděte na [vytvoření instančního objektu](#creating-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Nainstalujte sadu management pack mapy služeb
Povolit integraci mezi nástrojem Operations Manager a Service Map importováním Microsoft.SystemCenter.ServiceMap komplet sady management pack (Microsoft.SystemCenter.ServiceMap.mpb). Komplet sady management pack z si můžete stáhnout [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55763). Sada obsahuje následující sady management Pack:
* Zobrazení aplikací služby Microsoft mapy
* Mapa služeb Microsoft System Center interní
* Přepsání mapy služby Microsoft System Center
* Mapa služeb Microsoft System Center

## <a name="configure-the-service-map-integration"></a>Konfigurace integrace mapy služeb
Po instalaci mapy služeb sady pro správu, nový uzel, **mapy služeb**, se zobrazí v části **Operations Management Suite** v **správy** podokně. 

Při konfiguraci integrace mapy služeb, postupujte takto:

1. Chcete-li spustit Průvodce konfigurací v **přehled mapy služby** podokně klikněte na tlačítko **přidání prostoru**.  

    ![Přehled služby mapy podokno](media/monitoring-service-map/scom-configuration.png)

2. V **konfigurace připojení** okno, zadejte název klienta nebo ID, ID aplikace (také označované jako uživatelské jméno nebo clientID) a heslo instanční objekt a potom klikněte na **Další**. Další informace, přejděte na [vytvoření instančního objektu](#creating-a-service-principal).

    ![Okno Konfigurace připojení](media/monitoring-service-map/scom-config-spn.png)

3. V **výběr předplatného** oken, vyberte předplatné, skupinu prostředků Azure (ten, který obsahuje pracovní prostor analýzy protokolů) a pracovní prostor analýzy protokolů a pak klikněte na tlačítko **Další**.

    ![Pracovní prostor Operations Manager konfigurace](media/monitoring-service-map/scom-config-workspace.png)

4. V **výběr skupiny počítače** okně vyberte skupiny počítačů mapy které služby chcete synchronizovat do nástroje Operations Manager. Klikněte na tlačítko **skupiny počítačů přidat nebo odebrat**, vyberte skupiny ze seznamu **dostupných skupin počítačů**a klikněte na tlačítko **přidat**.  Po dokončení výběru skupiny klikněte na **Ok** ukončíte.
    
    ![Operace skupiny počítače Configuration Manager](media/monitoring-service-map/scom-config-machine-groups.png)
    
5. V **výběr serveru** okně konfiguraci skupiny serverů mapy služby se servery, které chcete synchronizovat mezi nástrojem Operations Manager a mapy služeb. Klikněte na tlačítko **přidat nebo odebrat servery**.   
    
    Pro integraci sestavení diagramu distribuované aplikace pro server musí být server:

    * Spravován nástrojem Operations Manager
    * Spravuje mapy služeb
    * Uvedené ve skupině služby mapy servery

    ![Skupinu konfigurace nástroje Operations Manager](media/monitoring-service-map/scom-config-group.png)

6. Volitelné: Vyberte fond zdrojů serveru pro správu ke komunikaci s analýzy protokolů a pak klikněte na tlačítko **přidání prostoru**.

    ![Operace fondu zdrojů Configuration Manager](media/monitoring-service-map/scom-config-pool.png)

    Může trvat několik minut, konfigurace a registrace pracovní prostor analýzy protokolů. Po dokončení své konfigurace, Operations Manager zahájí první synchronizace mapy služeb.

    ![Operace fondu zdrojů Configuration Manager](media/monitoring-service-map/scom-config-success.png)


## <a name="monitor-service-map"></a>Monitorování mapy služeb
Až se připojí pracovní prostor analýzy protokolů novou složku, mapy služeb, se zobrazí v **monitorování** podokně konzoly nástroje Operations Manager.

![V podokně monitorování nástroje Operations Manager](media/monitoring-service-map/scom-monitoring.png)

Mapa služeb složka obsahuje čtyři uzly:
* **Aktivní výstrahy**: uvádí všechny aktivní výstrahy o komunikaci mezi nástrojem Operations Manager a mapy služeb.  Všimněte si, že tyto výstrahy nejsou analýzy protokolů výstrahy se synchronizované do nástroje Operations Manager. 

* **Servery**: seznam monitorovaných serverů, které jsou nakonfigurovány pro synchronizaci z mapy služeb.

    ![V podokně monitorování servery nástroje Operations Manager](media/monitoring-service-map/scom-monitoring-servers.png)

* **Počítač zobrazení závislostí skupiny**: uvádí všechny skupiny počítačů, které jsou synchronizované z mapy služeb. Klikněte na možnost žádné skupiny k zobrazení jeho diagramu distribuované aplikace.

    ![Diagram distribuovaných aplikací nástroje Operations Manager](media/monitoring-service-map/scom-group-dad.png)

* **Zobrazení závislostí server**: uvádí všechny servery, které jsou synchronizované z mapy služeb. Můžete kliknout na jakýkoli server zobrazíte jeho diagramu distribuované aplikace.

    ![Diagram distribuovaných aplikací nástroje Operations Manager](media/monitoring-service-map/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Upravit nebo odstranit pracovní prostor
Můžete upravit nebo odstranit nakonfigurované prostoru prostřednictvím **přehled mapy služby** podokně (**správy** podokně > **Operations Management Suite**  >  **Služby mapy**). Teď můžete konfigurovat jenom jeden pracovní prostor analýzy protokolů.

![V podokně pracovní prostor upravit služby Operations Manager](media/monitoring-service-map/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Konfigurace pravidla a přepsání
Pravidlo, _Microsoft.SystemCenter.ServiceMapImport.Rule_, vytvoření pravidelně načíst informace z mapy služeb. Chcete-li změnit časování synchronizace, můžete nakonfigurovat přepsání pravidla (**vytváření** podokně > **pravidla** > **Microsoft.SystemCenter.ServiceMapImport.Rule**) .

![Operations Manager přepíše vlastnosti – okno](media/monitoring-service-map/scom-overrides.png)

* **Povolit**: Povolit nebo zakázat automatické aktualizace. 
* **IntervalMinutes**: resetování času mezi aktualizacemi. Výchozí interval je jedna hodina. Pokud chcete synchronizovat častěji mapy serveru, můžete změnit hodnotu.
* **TimeoutSeconds**: resetování dobu před vypršením časového limitu požadavku. 
* **TimeWindowMinutes**: resetování časový interval pro dotazování na data. Výchozí hodnota je 60 minut okno. Maximální povolená pomocí mapy služeb hodnota je 60 minut.

## <a name="known-issues-and-limitations"></a>Známé problémy a omezení

Současný návrh uvede následující problémy a omezení:
* Můžete připojit pouze k jedné pracovní prostor analýzy protokolů.
* I když přidáte servery do skupin serverů mapy služby ručně pomocí **vytváření** podokně mapy pro tyto servery není synchronizovaná okamžitě.  Bude se synchronizovat ze služby mapy při příštím synchronizačním cyklu.
* Pokud provedete změny diagramy distribuované aplikace vytvořené sady management pack, tyto změny budou přepsány pravděpodobně na příští synchronizaci s mapy služeb.

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu
Oficiální Azure dokumentaci o vytvoření objektu služby najdete v tématu:
* [Vytvoření objektu služby pomocí prostředí PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Vytvořit objekt služby pomocí rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Vytvořit objekt služby pomocí portálu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Váš názor
Máte k dispozici žádné zpětná vazba nám o mapy služeb nebo této dokumentace? Navštivte naše [User Voice stránky](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), kde můžete navrhnout funkce nebo hlasovat o existující návrhy.
