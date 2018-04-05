---
title: Co jsou oznámení o stavu služby Azure? | Dokumenty Microsoft
description: Oznámení o stavu služby umožňují zobrazit zprávy o stavu služby, které zveřejnil Microsoft Azure.
author: anirudhcavale
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: 4a95e9882515e6a2861292829a44847e11f39063
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Zobrazit oznámení o stavu služby pomocí portálu Azure

Oznámení o stavu služby jsou publikovány nástrojem Azure a obsahují informace o prostředcích v rámci svého předplatného. Tato oznámení jsou podtřídou třídy aktivity protokolu události a možné také najít v protokolu aktivit. Oznámení o stavu služby může být informační nebo řešitelné, v závislosti na třídě.

Existují různé třídy oznámení o stavu služby:  

- **Je vyžadována akce:** Azure všimnout něco neobvyklého vyskytnout u vašeho účtu a spolupracovat s vámi, chcete-li to opravit. Azure odešle oznámení, buď s podrobnostmi o akce, které je nutné vzít nebo kontaktování Azure inženýrství nebo podporu.  
- **Asistované obnovení:** došlo k události a techniky potvrdili, že se stále setkáváte dopad. Azure inženýrství musí spolupracovat s vámi přímo k obnovení úplné stavu vašich služeb.  
- **Incident:** událost, která má dopad na služby aktuálně ovlivňuje jednu nebo více prostředků ve vašem předplatném.  
- **Údržba:** aktivitou plánované údržby, která může mít vliv na jeden nebo více prostředků v rámci svého předplatného.  
- **Informace o:** potenciální optimalizace, které vám může pomoci zvýšit vaši využití prostředků. 
- **Zabezpečení:** naléhavé informace týkající se zabezpečení týkající se řešení, které běží na Azure.

Každý oznámení o stavu služby obsahuje informace o oboru a vliv na vaše prostředky. Podrobnosti o patří:

Název vlastnosti | Popis
-------- | -----------
kanály | Jeden z následujících hodnot: **správce** nebo **operaci**.
correlationId | Obvykle GUID ve formátu řetězce. Události, které patří do stejné akce obvykle sdílet stejnou correlationId.
eventDataId | Jedinečný identifikátor události.
eventName | Název události.
úroveň | Úroveň události. Jeden z následujících hodnot: **kritický**, **chyba**, **upozornění** nebo **informační**.
resourceProviderName | Název poskytovatele prostředků pro prostředek dopad.
resourceType| Typ prostředku ovlivněné prostředku.
subStatus | Stavový kód HTTP odpovídající REST obvykle volání, ale můžou taky patřit jiných řetězců popisující podřízeného stavu. Například: OK (stavový kód HTTP: 200), které byly vytvořeny (stavový kód HTTP: 201), platné (stavový kód HTTP: 202), ne obsahu (stavový kód HTTP: 204), chybný požadavek (stavový kód HTTP: 400), nebyl nalezen (stavový kód HTTP: 404), konflikt (stavový kód HTTP: 409), interní Server Chyba (kód stavu HTTP: 500), služba není k dispozici (kód stavu HTTP: 503) a vypršel časový limit brány (kód stavu HTTP: 504).
eventTimestamp | Časové razítko při zpracování požadavku odpovídající události služby Azure vygenerovalo událost.
submissionTimestamp | Časové razítko, když jsou události dostupné pro zadávání dotazů.
subscriptionId | Předplatné Azure, ve kterém se tato událost byla zaznamenána.
status | Řetězec popisující stav operace. Některé běžné hodnoty jsou: **Začínáme**, **probíhá**, **úspěšné**, **se nezdařilo**, **Active**, a **Přeložit**.
operationName | Název operace.
category | Tato vlastnost je vždy **ServiceHealth**.
resourceId | ID prostředku ovlivněných prostředků.
Properties.title | Lokalizovaný název pro tuto komunikaci. Výchozí hodnota je angličtina.
Properties.communication | Lokalizované podrobnosti o komunikaci se službou značka jazyka HTML. Výchozí hodnota je angličtina.
Properties.incidentType | Jeden z následujících hodnot: **AssistedRecovery**, **ActionRequired**, **informace**, **Incident**,  **Údržby**, nebo **zabezpečení**.
Properties.trackingId | Incident, ke kterému je přiřazeno této události. Použijte ke korelaci událostí souvisejících s incidentem.
Properties.impactedServices | Uvozený blob JSON, popisující služby a oblastí, které jsou ovlivněné incidentu. Vlastnost obsahuje seznam služeb, z nichž každá má **ServiceName**a seznam ovlivněných oblastí, z nichž každá má **RegionName**.
Properties.defaultLanguageTitle | Komunikace v angličtině.
Properties.defaultLanguageContent | Komunikace v angličtině jako značka jazyka HTML nebo prostý text.
Properties.Stage | Možné hodnoty **AssistedRecovery**, **ActionRequired**, **informace**, **Incident**, a **zabezpečení**  jsou **Active** nebo **Vyřešeno**. Pro **údržby**, jsou: **Active**, **plánovaná**, **InProgress**, **zrušeno**, **Přeplánovat**, **přeložit**, nebo **dokončení**.
Properties.communicationId | Komunikace, ke kterému je přiřazeno této události.


## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Zobrazit oznámení o stavu služby v portálu Azure
1.  V [portál Azure](https://portal.azure.com), vyberte **monitorování**.

    ![Snímek obrazovky Azure portálu nabídce s monitorováním vybrané](./media/monitoring-service-notifications/home-monitor.png)

    Azure monitorování spojuje veškeré monitorování nastavení a data do jednoho konsolidované zobrazení. Nejprve se otevře část **Protokol aktivit**.

3.  Vyberte **výstrahy**.

    ![Snímek obrazovky monitorování aktivity protokolu s vybrané výstrahy](./media/monitoring-service-notifications/service-health-summary.png)
4. Vyberte **+ přidat aktivitu protokolu upozornění**a nastavit upozornění, aby upozornění pro budoucí služby oznámení. Další informace najdete v tématu [vytvářet aktivity protokolu upozornění na oznámení o službách](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="next-steps"></a>Další postup
Přijímat [výstrahy oznámení pokaždé, když oznámení o stavu služby](monitoring-activity-log-alerts-on-service-notifications.md) odeslání.  
Další informace o [aktivity protokolu výstrahy](monitoring-activity-log-alerts.md).
