---
title: Integrace mapy Azure Monitor pro virtuální počítače s Operations Managerem | Dokumenty společnosti Microsoft
description: Azure Monitor pro virtuální počítače automaticky zjišťuje součásti aplikací v systémech Windows a Linux a mapuje komunikaci mezi službami. Tento článek popisuje použití funkce Map k automatickému vytváření distribuovaných aplikačních diagramů v nástroji Operations Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 112413720f969474369555a74bc89846666e2ef9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663449"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>Integrace správce operací system centra s funkcí Azure Monitor for VMs Map

V Azure Monitor pro virtuální počítače můžete zobrazit zjištěné součásti aplikací na virtuálních počítačích (VM) Windows a Linuxu, které běží v Azure nebo ve vašem prostředí. Díky této integraci mezi funkcí Map a správcem operací system center můžete automaticky vytvářet distribuované aplikační diagramy v nástroji Operations Manager, které jsou založené na mapách dynamických závislostí v Nástroji Azure pro virtuální počítače. Tento článek popisuje, jak nakonfigurovat skupinu pro správu nástroje System Center Operations Manager pro podporu této funkce.

>[!NOTE]
>Pokud jste už nasadili mapu služeb, můžete si zobrazit mapy v Azure Monitor u virtuálních počítačů, který obsahuje další funkce pro sledování stavu a výkonu virtuálních počítačových služeb. Funkce Map Azure Monitor pro virtuální počítače je určena k nahrazení řešení samostatné mapy služeb. Další informace najdete v tématu [Azure Monitor pro virtuální počítače přehled](vminsights-overview.md).

## <a name="prerequisites"></a>Požadavky

* Skupina pro správu operations centra (2012 R2 nebo novější).
* Pracovní prostor Analýzy protokolů nakonfigurovaný pro podporu Azure Monitoru pro virtuální počítače.
* Jeden nebo více virtuálních počítačů se systémem Windows a Linux nebo fyzických počítačů, které jsou monitorovány službou Operations Manager a odesílají data do pracovního prostoru Log Analytics. Servery Linuxu, které se hlásí do skupiny pro správu Nástroje pro správu nástroje Operations Manager, musí být nakonfigurovány tak, aby se přímo připojovaly k Azure Monitoru. Další informace naleznete v přehledu [v části Shromáždit data protokolu s agentem Log Analytics](../platform/log-analytics-agent.md).
* Instancí s přístupem k předplatnému Azure, který je přidružený k pracovnímu prostoru Log Analytics. Další informace naleznete v najdete [v najdete v najetí do hlavního povinného vytváření služeb](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Instalace sady Management Pack pro mapu služeb

Integraci mezi programem Operations Manager a funkcí Map povolíte importem sady Microsoft.SystemCenter.ServiceMap management Pack (Microsoft.SystemCenter.ServiceMap.mpb). Sadu Management Pack si můžete stáhnout ze služby [Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=55763). Balíček obsahuje následující sady Management Pack:

* Zobrazení aplikací mapy služby Microsoft Service
* Interní mapa služby Microsoft System Center
* Přepsání mapy služby Microsoft System Center
* Mapa služeb systémového centra společnosti Microsoft

## <a name="configure-integration"></a>Konfigurace integrace

Po instalaci sady Management Pack pro mapu služeb se v části **Sada operations management suite** v podokně **Správa** operační hospůdky Operations Console zobrazí nový uzel Mapa **služeb**.

>[!NOTE]
>[Operations Management Suite byla kolekce služeb,](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) které zahrnovaly Log Analytics, je nyní součástí [Azure Monitor](../overview.md).

Pokud chcete nakonfigurovat integraci mapy Azure Monitor pro virtuální počítače, postupujte takto:

1. Chcete-li otevřít průvodce konfigurací, klepněte v podokně **Přehled mapy služeb** na tlačítko Přidat pracovní **prostor**.  

    ![Podokno Přehled mapy služeb](media/service-map-scom/scom-configuration.png)

2. V okně **Konfigurace připojení** zadejte název klienta nebo ID, ID aplikace (označované také jako uživatelské jméno nebo ID klienta) a heslo instančního objektu a klepněte na tlačítko **Další**. Další informace naleznete v najdete v na jet instanční objekt.

    ![Okno Konfigurace připojení](media/service-map-scom/scom-config-spn.png)

3. V okně **Výběr předplatného** vyberte předplatné Azure, skupinu prostředků Azure (ten, který obsahuje pracovní prostor Log Analytics) a pracovní prostor Log Analytics a klikněte na **Další**.

    ![Pracovní prostor konfigurace nástroje Operations Manager](media/service-map-scom/scom-config-workspace.png)

4. V okně **Výběr skupiny počítače** zvolíte, které skupiny servisních zařízení chcete synchronizovat s nástrojem Operations Manager. Klepněte na tlačítko **Přidat nebo odebrat skupiny strojů**, vyberte skupiny ze seznamu **dostupných skupin strojů**a klepněte na tlačítko **Přidat**.  Po dokončení výběru skupin dokončete klepnutí na tlačítko **Ok.**

    ![Skupiny konfiguračních počítačů nástroje Operations Manager](media/service-map-scom/scom-config-machine-groups.png)

5. V okně **Výběr serveru** nakonfigurujete skupinu mapových serverů služeb pomocí serverů, které chcete synchronizovat mezi nástrojem Operations Manager a funkcí Map. Klepněte na tlačítko **Přidat nebo odebrat servery**.

    Aby bylo možné vytvořit diagram distribuované aplikace pro server, musí být server:

   * Monitorováno operations managerem
   * Nakonfigurovaný pro hlášení do pracovního prostoru Analýzy protokolů nakonfigurovanépomocí Azure Monitor pro virtuální počítače
   * Uvedeny ve skupině Mapových serverů služeb

     ![Konfigurační skupina nástroje Operations Manager](media/service-map-scom/scom-config-group.png)

6. Volitelné: Vyberte fond prostředků všech serverů pro správu, chcete-li komunikovat s protokolovou analýzou, a klepněte na tlačítko **Přidat pracovní prostor**.

    ![Fond prostředků konfigurace nástroje Operations Manager](media/service-map-scom/scom-config-pool.png)

    Konfigurace a registrace pracovního prostoru Log Analytics může chvíli trvat. Po konfiguraci nástroj Operations Manager zahájí první synchronizaci mapy.

    ![Fond prostředků konfigurace nástroje Operations Manager](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Integrace monitorů

Po připojení pracovního prostoru Log Analytics se v podokně **Monitorování** operační konzole Operations Manager zobrazí nová složka Mapa služeb.

![Podokno Monitorování nástroje Operations Manager](media/service-map-scom/scom-monitoring.png)

Složka Mapa služeb má čtyři uzly:

* **Aktivní výstrahy**: Zobrazí seznam všech aktivních výstrah o komunikaci mezi Operations Managera a Azure Monitor.  

  >[!NOTE]
  >Tyto výstrahy nejsou výstrahy Analýzy protokolů synchronizované s Operations Manager, jsou generovány ve skupině pro správu na základě pracovních postupů definovaných v aktualizaci management Pack mapy služeb.

* **Servery**: Uvádí monitorované servery, které jsou nakonfigurované pro synchronizaci z funkce Azure Monitor pro virtuální počítače.

    ![Podokno Monitorovací servery nástroje Operations Manager](media/service-map-scom/scom-monitoring-servers.png)

* **Zobrazení závislostí skupiny strojů:** Zobrazí seznam všech skupin strojů, které jsou synchronizovány z funkce Mapa. Klepnutím na libovolnou skupinu zobrazíte diagram distribuované aplikace.

    ![Diagram distribuovaných aplikací nástroje Operations Manager](media/service-map-scom/scom-group-dad.png)

* **Zobrazení závislostí serveru**: Zobrazí seznam všech serverů, které jsou synchronizovány z funkce Mapa. Klepnutím na libovolný server zobrazíte jeho distribuovaný aplikační diagram.

    ![Diagram distribuovaných aplikací nástroje Operations Manager](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Úprava nebo odstranění pracovního prostoru

Nakonfigurovaný pracovní prostor můžete upravit nebo odstranit prostřednictvím podokna **Přehled mapy služeb** (podokno**správa** >**mapách služeb**Operations **Management Suite** > ).

>[!NOTE]
>[Operations Management Suite byla kolekce služeb,](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) které zahrnovaly Log Analytics, která je nyní součástí [Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

V této aktuální verzi můžete nakonfigurovat pouze jeden pracovní prostor Analýzy protokolů.

![Podokno Úpravy pracovního prostoru nástroje Operations Manager](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Konfigurace pravidel a přepsání

Pravidlo, *Microsoft.SystemCenter.ServiceMapImport.Rule*, pravidelně načítá informace z funkce Azure Monitor pro virtuální počítače Map. Chcete-li upravit interval synchronizace, můžete přepsat pravidlo a upravit hodnotu parametru **Interval Minuty**.

![Okno Vlastností přepsání nástroje Operations Manager](media/service-map-scom/scom-overrides.png)

* **Povoleno**: Povolení nebo zakázání automatických aktualizací.
* **IntervalMinutes**: Určuje čas mezi aktualizacemi. Výchozí interval je jedna hodina. Pokud chcete mapy synchronizovat častěji, můžete hodnotu změnit.
* **TimeoutSeconds**: Určuje dobu, po kterou časový limit požadavku zbývá.
* **TimeWindowMinutes**: Určuje časové okno pro dotazování dat. Výchozí hodnota je 60 minut, což je maximální povolený interval.

## <a name="known-issues-and-limitations"></a>Známé problémy a omezení

Aktuální návrh představuje následující problémy a omezení:

* Můžete se připojit pouze k jednomu pracovnímu prostoru Log Analytics.
* Přestože můžete servery přidat do skupiny mapových serverů služeb ručně prostřednictvím **podokna Vytváření,** mapy pro tyto servery nejsou synchronizovány okamžitě. Budou synchronizovány z funkce Azure Monitor pro mapy virtuálních počítačů během dalšího cyklu synchronizace.
* Pokud provedete nějaké změny distribuovaných aplikačních diagramů vytvořených sadou Management Pack, budou tyto změny pravděpodobně přepsány při další synchronizaci pomocí Azure Monitor pro virtuální počítače.

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Oficiální dokumentaci Azure o vytvoření instančního objektu najdete v tématu:

* [Vytvoření instančního objektu pomocí Prostředí PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Vytvoření instančního objektu pomocí příkazového příkazového příkazu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Vytvoření instančního objektu pomocí portálu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Váš názor
Máte pro nás nějakou zpětnou vazbu ohledně integrace s funkcí Azure Monitor for VMs Map nebo s touto dokumentací? Navštivte naši [stránku User Voice](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), kde můžete navrhnout funkce nebo hlasovat o stávajících návrzích.
