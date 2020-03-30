---
title: Co jsou oznámení o stavu služby Azure?
description: Oznámení o stavu služby umožňují zobrazit zprávy o stavu služby vydané Microsoft Azure.
ms.topic: conceptual
ms.date: 4/12/2018
ms.openlocfilehash: e40538ac98bbc7b79311d4fb0da7568d56a84e18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77653964"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Zobrazení oznámení o stavu služby s využitím webu Azure Portal

Oznámení o stavu služeb publikuje Azure a obsahují informace o prostředcích v rámci vašeho předplatného. Tato oznámení jsou podtřídou událostí protokolu aktivit a lze je také nalézt v protokolu aktivit. Oznámení o stavu služby může být informační nebo žalovatelné, v závislosti na třídě.

Existují různé třídy oznámení o stavu služby:  

- **Požadovaná akce:** Azure si může všimnout něčeho neobvyklého, co se stalo na vašem účtu, a spolupracovat s vámi na nápravě tohoto problému. Azure vám pošle oznámení s podrobnostmi o akcích, které je třeba provést, nebo s tím, jak kontaktovat inženýrské nebo podpůrné řešení Azure.  
- **Incident:** Událost, která má vliv na službu, momentálně ovlivňuje jeden nebo více prostředků ve vašem předplatném.  
- **Údržba:** Plánovaná aktivita údržby, která může mít vliv na jeden nebo více prostředků v rámci vašeho předplatného.  
- **Informace:** Potenciální optimalizace, které by mohly pomoci zlepšit využití prostředků. 
- **Zabezpečení:** Naléhavé informace týkající se zabezpečení týkající se vašich řešení, která běží v Azure.

Každé oznámení o stavu služby obsahuje podrobnosti o oboru a dopadu na vaše prostředky. Podrobnosti zahrnují:

Název vlastnosti | Popis
-------- | -----------
Kanály | Jedna z následujících hodnot: **Správce** nebo **Operace**.
correlationId | Obvykle identifikátor GUID ve formátu řetězce. Události, které patří do stejné akce obvykle sdílejí stejné correlationId.
eventDataId | Jedinečný identifikátor události.
Eventname | Název události.
level | Úroveň události
název_prostředku_prostředku_prostředku_prostředku | Název poskytovatele prostředků pro ovlivněný prostředek.
resourceType| Typ zdroje ovlivněného zdroje.
subStatus | Obvykle stavový kód HTTP odpovídající volání REST, ale může také obsahovat další řetězce popisující dílčí stav. Například: OK (stavový kód HTTP: 200), Vytvořeno (stavový kód HTTP: 201), Přijato (STAVOVÝ KÓD HTTP: 202), Žádný obsah (stavový kód HTTP: 204), Chybný požadavek (Stavový kód HTTP: 400), Nebyl nalezen (STAVOVÝ KÓD HTTP: 404), Konflikt (Stavový kód HTTP: 409), Interní server Chyba (stavový kód HTTP: 500), Služba není k dispozici (stavový kód HTTP: 503) a časový výtok brány (stavový kód HTTP: 504).
eventTimestamp | Časové razítko, kdy byla událost generována službou Azure, která zpracovává požadavek odpovídající události.
submissionTimestamp | Časové razítko, kdy událost byla k dispozici pro dotazování.
subscriptionId | Předplatné Azure, ve kterém byla zaznamenána tato událost.
status | Řetězec popisující stav operace. Některé běžné hodnoty jsou: **Spuštěno**, **Probíhá**, **Úspěšné**, **Neúspěšné**, **Aktivní**a **Vyřešeno**.
operationName | Název operace.
category | Tato vlastnost je vždy **ServiceHealth**.
resourceId | ID prostředku ovlivněného prostředku.
Vlastnosti.název | Lokalizovaný název pro toto sdělení. Angličtina je výchozí.
Vlastnosti.komunikace | Lokalizované podrobnosti o komunikaci se značkami HTML. Angličtina je výchozí.
Vlastnosti.type incidentu | Jedna z následujících hodnot: **ActionRequired**, **Informational**, **Incident**, **Maintenance**nebo **Security**.
Vlastnosti.trackingId | Incident, ke kterému je přidružena tato událost. Pomocí tohoto ke korelaci události související s incidentem.
Vlastnosti.impactedServices | Řídicí objekt blob JSON, který popisuje služby a oblasti ovlivněné incidentem. Vlastnost obsahuje seznam služeb, z nichž každá má **ServiceName**a seznam ovlivněných oblastí, z nichž každá má **Název_**
Vlastnosti.defaultNázev jazyka | Komunikace v angličtině.
Vlastnosti.defaultLanguageContent | Komunikace v angličtině jako značky HTML nebo prostý text.
Vlastnosti.fáze | Možné hodnoty pro **incident**a **zabezpečení** jsou **aktivní,** **vyřešené** nebo **RCA**. Pro **ActionRequired** nebo **Informational** je jedinou hodnotou **Aktivní.** Pro **údržbu** **jsou: Aktivní**, **Plánované**, **Probíhá**, **Zrušeno**, **Přeplánováno**, **Vyřešeno**nebo **Dokončeno**.
Vlastnosti.communicationId | Komunikace, ke které je tato událost přidružena.

### <a name="details-on-service-health-level-information"></a>Podrobnosti o informacích o úrovni zdravotního stavu služby

**Požadovaná akce** (properties.incidentType == ActionRequired)
- Informační – akce správce je nutná, aby se zabránilo dopadu na existující služby.
    
**Údržba** (vlastnosti.incidentTyp == Údržba)
- Varování - Nouzová údržba
- Informační - Standardní plánovaná údržba

**Informace** (properties.incidentType == Informace)
- Informační – správce může být vyžadován, aby zabránil dopadu na stávající služby.

**Zabezpečení** (vlastnosti.incidentType == Zabezpečení)
- Upozornění – informační zpravodaj zabezpečení, který ovlivňuje existující služby a může vyžadovat akci správce.
- Informační – informační zpravodaj, který ovlivňuje existující služby.

**Problémy se službou** (properties.incidentType == Incident)
- Chyba – rozšířené problémy s přístupem k více službám ve více oblastech mají vliv na širokou sadu zákazníků.
- Upozornění – problémy přistupující k určitým službám nebo konkrétním oblastem mají vliv na podmnožinu zákazníků.
- Informační – problémy, které mají vliv na operace správy nebo latenci, nemají vliv na dostupnost služby.
