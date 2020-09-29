---
title: Integrace mapy Azure Monitor pro virtuální počítače s Operations Manager | Microsoft Docs
description: Azure Monitor pro virtuální počítače automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikaci mezi službami. Tento článek popisuje použití funkce map k automatickému vytváření diagramů distribuovaných aplikací v Operations Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 489b4bfee1eaa2c381b16e88dbcc0bcb7fd8555f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91441508"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>Integrace System Center Operations Manager s funkcí map Azure Monitor pro virtuální počítače

V Azure Monitor pro virtuální počítače můžete zobrazit zjištěné součásti aplikace na virtuálních počítačích s Windows a Linux, které běží v Azure nebo ve vašem prostředí. Díky této integraci mezi funkcí map a System Center Operations Manager můžete automaticky vytvářet diagramy distribuovaných aplikací v Operations Manager, které jsou založené na mapch dynamických závislostí v Azure Monitor pro virtuální počítače. Tento článek popisuje, jak nakonfigurovat skupinu pro správu System Center Operations Manager pro podporu této funkce.

>[!NOTE]
>Pokud jste již nasadili Service Map, můžete zobrazit vaše mapy v Azure Monitor pro virtuální počítače, které obsahují další funkce pro monitorování stavu a výkonu virtuálních počítačů. Funkce map Azure Monitor pro virtuální počítače je určena k nahrazení samostatného řešení Service Map. Další informace najdete v tématu [přehled Azure monitor pro virtuální počítače](vminsights-overview.md).

## <a name="prerequisites"></a>Požadavky

* System Center Operations Manager skupina pro správu (2012 R2 nebo novější).
* Pracovní prostor Log Analytics nakonfigurovaný pro podporu Azure Monitor pro virtuální počítače.
* Jeden nebo více virtuálních počítačů se systémem Windows a Linux nebo fyzických počítačů, které jsou monitorovány Operations Manager a odesílají data do pracovního prostoru Log Analytics. Servery se systémem Linux, které vytvářejí Operations Manager skupině pro správu, musí být nakonfigurovány tak, aby se přímo připojovaly k Azure Monitor. Další informace najdete v přehledu [shromáždění dat protokolu pomocí agenta Log Analytics](../platform/log-analytics-agent.md).
* Instanční objekt s přístupem k předplatnému Azure, které je přidružené k pracovnímu prostoru Log Analytics. Další informace najdete v pro [Vytvoření instančního objektu](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Instalace Service Map Management Pack

Integraci mezi Operations Manager a funkcí map povolíte tak, že naimportujete sadu Microsoft.SystemCenter. ServiceMap Management Pack (Microsoft.SystemCenter. ServiceMap. mpb). Sadu Management Pack můžete stáhnout z webu [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=55763). Svazek obsahuje následující sady Management Pack:

* Zobrazení aplikací v Microsoft Service Map
* Microsoft System Center Service Map interní
* Přepsání aplikace Microsoft System Center Service Map
* Service Map softwaru Microsoft System Center

## <a name="configure-integration"></a>Konfigurace integrace

Po instalaci Service Map Management Pack se v rámci **Operations Management Suite** v Operations Manager podokně **Správa** konzoly Operations console zobrazí nový uzel **Service map**.

>[!NOTE]
>[Operations Management Suite byl kolekce služeb](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) , které Log Analytics, je teď součástí [Azure monitor](../overview.md).

Chcete-li nakonfigurovat integraci mapování Azure Monitor pro virtuální počítače, postupujte následovně:

1. Chcete-li otevřít Průvodce konfigurací, v podokně **přehled Service map** klikněte na možnost **Přidat pracovní prostor**.  

    ![Service Map – podokno přehledu](media/service-map-scom/scom-configuration.png)

2. V okně **Konfigurace připojení** zadejte název nebo ID TENANTA, ID aplikace (označované také jako uživatelské jméno nebo clientID) a heslo instančního objektu a potom klikněte na **Další**. Další informace najdete v pro vytvoření instančního objektu.

    ![Okno Konfigurace připojení](media/service-map-scom/scom-config-spn.png)

3. V okně **výběru předplatného** vyberte předplatné Azure, skupinu prostředků Azure (tu, která obsahuje Log Analytics pracovní prostor) a Log Analytics pracovní prostor a pak klikněte na **Další**.

    ![Pracovní prostor konfigurace Operations Manager](media/service-map-scom/scom-config-workspace.png)

4. V okně **Výběr skupiny počítačů** vyberte, které Service map skupiny počítačů chcete Operations Manager synchronizovat. Klikněte na **Přidat nebo odebrat skupiny počítačů**, v seznamu **dostupných skupin počítačů**vyberte skupiny a klikněte na **Přidat**.  Po dokončení výběru skupin klikněte na **OK** a dokončete.

    ![Skupiny počítačů konfigurace Operations Manager](media/service-map-scom/scom-config-machine-groups.png)

5. V okně **Výběr serveru** nakonfigurujte skupinu Service map servery se servery, které chcete synchronizovat mezi Operations Manager a funkcí map. Klikněte na **Přidat nebo odebrat servery**.

    Pro integraci k sestavení diagramu distribuované aplikace pro server musí být server:

   * Monitorováno pomocí Operations Manager
   * Nakonfigurováno pro sestavy do pracovního prostoru Log Analytics nakonfigurovaného pomocí Azure Monitor pro virtuální počítače
   * Uvedené ve skupině Service Map servery

     ![Skupina konfigurace Operations Manager](media/service-map-scom/scom-config-group.png)

6. Volitelné: Vyberte fond zdrojů všech serverů pro správu, který bude komunikovat s Log Analytics a pak klikněte na **Přidat pracovní prostor**.

    ![Snímek obrazovky fondu serverů v části přidat Microsoft Operations Management Suite pracovní prostor se zvoleným fondem zdrojů všech serverů pro správu.](media/service-map-scom/scom-config-pool.png)

    Konfigurace a registrace Log Analytics pracovního prostoru může trvat několik minut. Po nakonfigurování Operations Manager inicializuje první synchronizaci mapování.

    ![Snímek obrazovky s doplňováním v pracovním prostoru přidat Microsoft Operations Management Suite potvrzující, že byl pracovní prostor přidán.](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Monitorování integrace

Po připojení Log Analytics pracovního prostoru se v podokně **monitorování** konzoly Operations Manager Operations Console zobrazí nová složka Service map.

![Podokno monitorování Operations Manager](media/service-map-scom/scom-monitoring.png)

Složka Service Map má čtyři uzly:

* **Aktivní výstrahy**: vypíše všechny aktivní výstrahy týkající se komunikace mezi Operations Manager a Azure monitor.  

  >[!NOTE]
  >Tyto výstrahy nejsou Log Analytics výstrahy synchronizované s Operations Manager se generují ve skupině pro správu založené na pracovních postupech definovaných v Management Pack Service Map.

* **Servery**: vypíše monitorované servery, které jsou nakonfigurované pro synchronizaci z Azure monitor pro virtuální počítače funkce mapy.

    ![Podokno Operations Manager monitorovací servery](media/service-map-scom/scom-monitoring-servers.png)

* **Zobrazení závislostí skupiny počítačů**: vypíše všechny skupiny počítačů, které jsou synchronizované z funkce mapa. Kliknutím na libovolnou skupinu můžete zobrazit její diagram distribuované aplikace.

    ![Snímek obrazovky z Service Map zobrazující diagram s obrázky pro každou skupinu počítačů a řádky, které označují závislosti mezi nimi.](media/service-map-scom/scom-group-dad.png)

* **Zobrazení závislostí serveru**: vypíše všechny servery, které jsou synchronizované z funkce mapa. Pro zobrazení diagramu distribuované aplikace můžete kliknout na libovolný server.

    ![Snímek obrazovky z Service Map zobrazující diagram s obrázky pro každý server a řádky, které označují závislosti mezi nimi](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Upravit nebo odstranit pracovní prostor

Nakonfigurovaný pracovní prostor můžete upravit nebo odstranit pomocí podokna **přehledu Service map** (podokno**Správa** > **Operations Management Suite**  >  **Service map**).

> [!NOTE]
> [Operations Management Suite byla sada služeb](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) , které jsou součástí Log Analytics, což je teď součástí [Azure monitor](../overview.md).

V této aktuální verzi můžete nakonfigurovat pouze jeden pracovní prostor Log Analytics.

![Podokno upravit pracovní prostor Operations Manager](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Konfigurace pravidel a přepsání

Pravidlo *Microsoft.SystemCenter. ServiceMapImport. Rule*pravidelně načítá informace z funkce map Azure monitor pro virtuální počítače. Chcete-li upravit interval synchronizace, můžete pravidlo přepsat a změnit hodnotu parametru **IntervalMinutes**.

![Okno Vlastnosti Operations Manager Overrides](media/service-map-scom/scom-overrides.png)

* **Povoleno**: povolí nebo zakáže automatické aktualizace.
* **IntervalMinutes**: Určuje dobu mezi aktualizacemi. Výchozí interval je jedna hodina. Pokud chcete mapy synchronizovat častěji, můžete hodnotu změnit.
* **TimeoutSeconds**: Určuje dobu, po jejímž uplynutí vyprší časový limit požadavku.
* **TimeWindowMinutes**: Určuje časový interval pro dotazování na data. Výchozí hodnota je 60 minut, což je maximální povolený interval.

## <a name="known-issues-and-limitations"></a>Známé problémy a omezení

Aktuální návrh přináší následující problémy a omezení:

* Můžete se připojit pouze k jednomu pracovnímu prostoru Log Analytics.
* I když můžete přidat servery do skupiny serverů Service Map ručně prostřednictvím podokna **vytváření obsahu** , mapy pro tyto servery se nesynchronizují okamžitě. Budou synchronizovány z Azure Monitor pro virtuální počítače funkce mapa během dalšího cyklu synchronizace.
* Pokud provedete jakékoli změny v diagramech distribuovaných aplikací vytvořených Management Pack, budou tyto změny při příští synchronizaci s Azure Monitor pro virtuální počítače pravděpodobně přepsány.

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Oficiální dokumentaci k Azure o vytváření instančního objektu najdete v těchto tématech:

* [Vytvoření instančního objektu pomocí PowerShellu](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Vytvoření instančního objektu pomocí Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)
* [Vytvoření instančního objektu pomocí služby Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="suggestions"></a>Návrhy

Máte jakoukoli zpětnou vazbu pro nás o integraci s funkcí Azure Monitor pro virtuální počítače map nebo s touto dokumentací? Navštivte naši [hlasovou stránku uživatele](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), kde můžete navrhovat funkce nebo hlasovat o stávajících návrzích.

