---
title: Co jsou oznámení o stavu služby Azure?
description: Oznámení o stavu služby umožňují zobrazit zprávy o stavu služby, které zveřejnil Microsoft Azure.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2017
ms.author: dukek
ms.component: activitylog
ms.openlocfilehash: d6a87b17041c4ce6cf41da863354ef5a2a37141c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264438"
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
úroveň | Úroveň události
resourceProviderName | Název poskytovatele prostředků pro prostředek dopad.
Typ prostředku| Typ prostředku ovlivněné prostředku.
Podřízený stav | Stavový kód HTTP odpovídající REST obvykle volání, ale můžou taky patřit jiných řetězců popisující podřízeného stavu. Například: OK (stavový kód HTTP: 200), které byly vytvořeny (stavový kód HTTP: 201), platné (stavový kód HTTP: 202), ne obsahu (stavový kód HTTP: 204), chybný požadavek (stavový kód HTTP: 400), nebyl nalezen (stavový kód HTTP: 404), konflikt (stavový kód HTTP: 409), interní Server Chyba (kód stavu HTTP: 500), služba není k dispozici (kód stavu HTTP: 503) a vypršel časový limit brány (kód stavu HTTP: 504).
eventTimestamp | Časové razítko při zpracování požadavku odpovídající události služby Azure vygenerovalo událost.
submissionTimestamp | Časové razítko, když jsou události dostupné pro zadávání dotazů.
subscriptionId | Předplatné Azure, ve kterém se tato událost byla zaznamenána.
status | Řetězec popisující stav operace. Některé běžné hodnoty jsou: **Začínáme**, **probíhá**, **úspěšné**, **se nezdařilo**, **Active**, a **Přeložit**.
operationName | Název operace.
category | Tato vlastnost je vždy **ServiceHealth**.
resourceId | ID prostředku ovlivněných prostředků.
Properties.Title | Lokalizovaný název pro tuto komunikaci. Výchozí hodnota je angličtina.
Properties.Communication | Lokalizované podrobnosti o komunikaci se službou značka jazyka HTML. Výchozí hodnota je angličtina.
Properties.incidentType | Jeden z následujících hodnot: **ActionRequired**, **informace**, **Incident**, **údržby**, nebo **zabezpečení**.
Properties.trackingId | Incident, ke kterému je přiřazeno této události. Použijte ke korelaci událostí souvisejících s incidentem.
Properties.impactedServices | Uvozený blob JSON, popisující služby a oblastí, které jsou ovlivněné incidentu. Vlastnost obsahuje seznam služeb, z nichž každá má **ServiceName**a seznam ovlivněných oblastí, z nichž každá má **RegionName**.
Properties.defaultLanguageTitle | Komunikace v angličtině.
Properties.defaultLanguageContent | Komunikace v angličtině jako značka jazyka HTML nebo prostý text.
Properties.Stage | Možné hodnoty **Incident**, a **zabezpečení** jsou **aktivní,** **Vyřešeno** nebo **RCA**. Pro **ActionRequired** nebo **informace** je jediná hodnota **aktivní.** Pro **údržby** jsou: **Active**, **plánovaná**, **InProgress**, **zrušeno**, **Přeplánovat**, **přeložit**, nebo **dokončení**.
Properties.communicationId | Komunikace, ke kterému je přiřazeno této události.

### <a name="details-on-service-health-level-information"></a>Podrobnosti o informace o stavu úrovni služby
  <ul>
    <li><b>Je vyžadována akce</b> (properties.incidentType == ActionRequired) <dl>
            <dt>Informační</dt>
            <dd>Akce správce, které jsou nutné, aby vliv na stávající služby</dd>
        </dl>
    </li>
    <li><b>Údržby</b> (properties.incidentType == údržby) <dl>
            <dt>Upozornění</dt>
            <dd>Nouzový údržby<dd>
            <dt>Informační</dt>
            <dd>Standardní plánované údržby</dd>
        </dl>
    </li>
    <li><b>Informace o</b> (properties.incidentType == informace) <dl>
            <dt>Informační</dt>
            <dd>Správce může být nutné zabránit vliv na stávající služby</dd>
        </dl>
    </li>
    <li><b>Zabezpečení</b> (properties.incidentType == zabezpečení) <dl>
            <dt>Chyba</dt>
            <dd>Přístup k více službám nad několika oblastmi rozšířených problémů které mají vliv na širokou škálu zákazníků.</dd>
            <dt>Upozornění</dt>
            <dd>Přístup k určitým službám a/nebo určitých oblastí problémy které mají vliv na podmnožinu zákazníků.</dd>
            <dt>Informační</dt>
            <dd>Problémy s operace správy nebo latence, není vlivu na dostupnost služby, které mají vliv.</dd>
        </dl>
    </li>
    <li><b>Služba problémy</b> (properties.incidentType == Incident) <dl>
            <dt>Chyba</dt>
            <dd>Přístup k více službám nad několika oblastmi rozšířených problémů které mají vliv na širokou škálu zákazníků.</dd>
            <dt>Upozornění</dt>
            <dd>Přístup k určitým službám a/nebo určitých oblastí problémy které mají vliv na podmnožinu zákazníků.</dd>
            <dt>Informační</dt>
            <dd>Problémy s operace správy nebo latence, není vlivu na dostupnost služby, které mají vliv.</dd>
        </dl>
    </li>
  </ul>

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
