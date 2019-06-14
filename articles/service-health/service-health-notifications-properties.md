---
title: Jaké jsou oznámení o stavu služby Azure?
description: Oznámení o stavu služby umožňují zobrazit zprávy služby service health publikování Microsoft Azure.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: 87efa7442b0c67f2ee5f83b6b3e8ac8530ce5285
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079545"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Zobrazení oznámení o stavu služby s využitím webu Azure Portal

Oznámení o stavu služby k publikování aplikací Azure a obsahují informace o prostředcích v rámci vašeho předplatného. Tato oznámení jsou dílčí třídy aktivity protokolu události a můžete také najít v protokolu aktivit. Oznámení o stavu služby může být informační nebo praktické, v závislosti na třídě.

Existují různé třídy oznámení o stavu služby:  

- **Požaduje se akce:** Azure si všimnout něco neobvyklého dojít u vašeho účtu a ve spolupráci s to napravit. Azure vám pošle oznámení, buď s podrobnostmi o akce, které je potřeba provést nebo kontaktování Azure engineering nebo podpory.  
- **S asistencí obnovení:** Došlo k události a technici ujistíte, že i nadále dochází k dopad. Azure engineering musí spolupracovat s vámi přímo k obnovení úplné stavu vašich služeb.  
- **Incident:** Událost, která ovlivňuje službu aktuálně ovlivňuje nejméně jeden z prostředků ve vašem předplatném.  
- **Údržba:** Aktivity plánované údržby, který může mít vliv na jeden nebo více prostředků v rámci vašeho předplatného.  
- **Informace:** Potenciální optimalizace, které může pomoct vylepšit váš prostředek použít. 
- **Zabezpečení:** Naléhavá související se zabezpečením informace týkající se řešení, které běží na Azure.

Každý oznámení o stavu služby obsahuje podrobnosti o rozsah a dopad na vaše prostředky. Podrobnosti zahrnují:

Název vlastnosti | Popis
-------- | -----------
kanály | Jeden z následujících hodnot: **Správce** nebo **operace**.
correlationId | Obvykle GUID ve formátu řetězce. Události, které obvykle patří do stejné akce sdílejí stejné ID korelace.
eventDataId | Jedinečný identifikátor události.
eventName | Název události.
level | Úroveň události
resourceProviderName | Název poskytovatele prostředků pro ovlivněných prostředků.
Typ prostředku| Typ prostředku ovlivněných prostředků.
Podřízený stav | Stavový kód HTTP odpovídající REST obvykle volat, ale může také obsahovat jiných řetězců s popisem substatus. Příklad: OK (kód stavu HTTP: 200), vytvořit (kód stavu HTTP: 201), přijato (kód stavu HTTP: 202), žádný obsah (kód stavu HTTP: 204), chybná žádost (kód stavu HTTP: 400), nebyl nalezen (kód stavu HTTP: 404), konflikt (kód stavu HTTP: 409), se interní chyba serveru (kód stavu HTTP: 500), služba není k dispozici (kód stavu HTTP: 503) a časový limit brány (kód stavu HTTP: 504).
eventTimestamp | Časové razítko události vygenerované službou Azure zpracování požadavku odpovídající události.
submissionTimestamp | Časové razítko, kdy je k dispozici pro dotazování na události.
subscriptionId | Předplatné Azure, ve kterém se tato událost byla zaznamenána.
status | Řetězec popisující stav operace. Některé běžné hodnoty jsou: **Spuštění**, **probíhá**, **úspěšné**, **se nezdařilo**, **Active**, a **přeložit**.
operationName | Název operace.
category | Tato vlastnost je vždy **ServiceHealth**.
resourceId | ID prostředku ovlivněných prostředků.
Properties.Title | Lokalizovaný název pro tuto komunikaci. Výchozí hodnota je angličtina.
Properties.communication | Lokalizované podrobnosti o komunikaci s značka jazyka HTML. Výchozí hodnota je angličtina.
Properties.incidentType | Jeden z následujících hodnot: **Je vyžadována akce**, **informační**, **Incident**, **údržby**, nebo **zabezpečení**.
Properties.trackingId | Incident, ke kterému tato událost je přidružena. Použijte ke korelaci událostí souvisejících s incidentem.
Properties.impactedServices | Uvozený uvozovacím znakem blob JSON, s popisem služby a oblasti vliv incident. Vlastnost obsahuje seznam služeb, z nichž každá má **ServiceName**a seznam ovlivněných oblastí, z nichž každá má **RegionName**.
Properties.defaultLanguageTitle | Komunikace v angličtině.
Properties.defaultLanguageContent | Komunikace v angličtině jako značka jazyka HTML nebo prostého textu.
Properties.Stage | Možné hodnoty parametru **Incident**, a **zabezpečení** jsou **aktivní,** **Vyřešeno** nebo **RCA**. Pro **je vyžadována akce** nebo **informativní** je jediná hodnota **aktivní.** Pro **údržby** jsou: **Aktivní**, **plánované**, **InProgress**, **zrušena**, **přeplánovat**, **přeložit**, nebo **kompletní**.
Properties.communicationId | Komunikace, ke kterému tato událost je přidružena.

### <a name="details-on-service-health-level-information"></a>Podrobné informace o stavu úrovni služby

**Požaduje se akce** (properties.incidentType == je vyžadována akce)
- Informativní – akce správce vyžaduje, aby se zabránilo dopadu na stávající služby
    
**Údržba** (properties.incidentType == udržování)
- Upozornění: Nouzový údržby
- Informační - standard plánovaná údržba

**Information** (properties.incidentType == Information)
- Informační – správce může být nutné zabránilo dopadu na stávající služby

**Security** (properties.incidentType == Security)
- Chyba – rozšířených problémy s přístupem k více služeb napříč několika oblastmi mají vliv na široké škále zákazníků.
- Upozornění: problémy s přístupem k určité služby a/nebo konkrétní oblasti mají vliv na podmnožinu zákazníků.
- Informativní – problémy vliv na operace správy nebo latence, bez dopadu na dostupnost služeb.

**Služba problémy** (properties.incidentType == Incident)
- Chyba – rozšířených problémy s přístupem k více služeb napříč několika oblastmi mají vliv na široké škále zákazníků.
- Upozornění: problémy s přístupem k určité služby a/nebo konkrétní oblasti mají vliv na podmnožinu zákazníků.
- Informativní – problémy vliv na operace správy nebo latence, bez dopadu na dostupnost služeb.
