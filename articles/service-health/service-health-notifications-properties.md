---
title: Co jsou oznámení o službě Azure Service Health?
description: Oznámení o stavu služby umožňují zobrazit zprávy o stavu služby publikované pomocí Microsoft Azure.
ms.topic: conceptual
ms.date: 4/12/2018
ms.openlocfilehash: 4f2b4a0779d775ed5be5bfa28831cccc68c33caf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86529008"
---
# <a name="use-the-azure-portal-to-view-service-health-notifications"></a>Použití Azure Portal k zobrazení oznámení o stavu služby

Oznámení o stavu služby jsou publikována v Azure a obsahují informace o prostředcích v rámci vašeho předplatného. Tato oznámení jsou podtřídou událostí protokolu aktivit a lze je také najít v protokolu aktivit. Oznámení o stavu služby můžou být informativní nebo napadnutelná v závislosti na třídě.

Existují různé třídy oznámení o stavu služby:  

- **Požaduje se akce:** Azure si může všimnout, že váš účet má něco neobvyklého, a můžete s ním pracovat, abyste ho napravili. Azure vám pošle oznámení, a to buď s podrobnostmi o akcích, které potřebujete, nebo o tom, jak kontaktovat technickou podporu a technickou podporu Azure.  
- **Incident:** Událost, která ovlivňuje službu, v současné době ovlivňuje jeden nebo více prostředků v rámci vašeho předplatného.  
- **Údržba:** Plánovaná aktivita údržby, která může mít vliv na jeden nebo více prostředků v rámci vašeho předplatného.  
- **Informace:** Potenciální optimalizace, které mohou pomoci zlepšit využití prostředků. 
- **Zabezpečení:** Naléhavé informace související se zabezpečením týkající se řešení, která běží na Azure.

Každé oznámení o stavu služby zahrnuje podrobnosti o rozsahu a dopadu na vaše prostředky. Podrobnosti zahrnují:

Název vlastnosti | Description
-------- | -----------
barev | Jedna z následujících hodnot: **admin** nebo **Operation**.
correlationId | Obvykle identifikátor GUID ve formátu řetězce. Události, které patří do stejné akce, obvykle sdílejí stejné ID korelace.
eventDataId | Jedinečný identifikátor události
eventName | Název události
úroveň | Úroveň události
resourceProviderName | Název poskytovatele prostředků pro ovlivněný prostředek.
resourceType| Typ prostředku ovlivněného prostředku.
subStatus | Obvykle stavový kód HTTP odpovídajícího volání REST, ale může také obsahovat další řetězce popisující dílčí stav. Příklad: OK (kód stavu HTTP: 200), Vytvořeno (kód stavu HTTP: 201), přijato (kód stavu HTTP: 202), žádný obsah (kód stavu HTTP: 204), chybný požadavek (kód stavu HTTP: 400), Nenalezeno (kód stavu HTTP: 404), konflikt (kód stavu HTTP: 409), interní chyba serveru (kód stavu http: 500), nedostupná služba (Stavový kód HTTP: 503) a časový limit brány (kód stavu http: 504).
eventTimestamp | Časové razítko, kdy se událost vygenerovala službou Azure, zpracování žádosti odpovídající události
submissionTimestamp | Časové razítko, kdy se událost stala k dispozici pro dotazování
subscriptionId | Předplatné Azure, ve kterém se tato událost zaznamenala.
status | Řetězec popisující stav operace. Mezi běžné hodnoty patří: **spuštěno**, **probíhá**, **úspěšné**, **neúspěšné**, **aktivní**a **vyřešené**.
operationName | Název operace.
category | Tato vlastnost je vždy **ServiceHealth**.
resourceId | ID prostředku ovlivněného prostředku.
Properties. title | Lokalizovaný název této komunikace. Výchozím nastavením je angličtina.
Vlastnosti. komunikace | Lokalizované podrobnosti komunikace s označením HTML. Výchozím nastavením je angličtina.
Properties. incidentType | Jedna z následujících hodnot: **je vyžadována akce**, **informativní**, **incident**, **Údržba**nebo **zabezpečení**.
Properties. trackingId | Incident, ke kterému je přidružena tato událost Tento postup použijte ke korelaci událostí souvisejících s incidentem.
Properties. impactedServices | Řídicí objekt BLOB JSON, který popisuje služby a oblasti, na které se incidenty ovlivňují. Vlastnost obsahuje seznam služeb, z nichž každý má **ServiceName**, a seznam ovlivněných oblastí, z nichž každý má **RegionName**.
Properties. defaultLanguageTitle | Komunikace v angličtině.
Properties. defaultLanguageContent | Komunikace v angličtině buď jako kód HTML, nebo jako prostý text.
Vlastnosti. Stage | Možné hodnoty pro **incidenty**a **zabezpečení** jsou **aktivní,** **vyřešené** nebo **RCA**. Pro **je vyžadována akce** nebo **informativní** je jedinou hodnotou **aktivní.** V případě **údržby** : **aktivní**, **plánovaná**, **Nedokončená**, **zrušená**, **přeplánovaná**, **vyřešená**nebo **Dokončená**.
Properties. communicationId | Komunikace, ke které je tato událost přidružena.

### <a name="details-on-service-health-level-information"></a>Podrobnosti o úrovni stavu služby

**Požaduje se akce** (Properties. incidentType = = je vyžadována akce)
- Informační – akce správce je nutná k tomu, aby se zabránilo dopadu na stávající služby.
    
**Údržba** (vlastnosti. incidentType = = údržba)
- Upozornění – údržba v nouzi
- Informativní – standardní plánovaná údržba

**Informace** (vlastnosti. incidentType = = informace)
- Informační – správce může být nutný k tomu, aby se zabránilo dopadu na stávající služby.

**Zabezpečení** (vlastnosti. incidentType = = zabezpečení)
- Upozornění – informační zpravodaj zabezpečení, který ovlivňuje stávající služby a může vyžadovat akci správce.
- Informační zpravodaj zabezpečení, který má vliv na stávající služby.

**Problémy se službou** (Properties. incidentType = = incident)
- Chyba: rozšířené problémy s přístupem k více službám v různých oblastech mají vliv na širokou škálu zákazníků.
- Upozornění – problémy při přístupu ke konkrétním službám a/nebo konkrétním oblastem mají vliv na podmnožinu zákazníků.
- Informativní – problémy, které mají vliv na operace správy a/nebo latenci, nemají vliv na dostupnost služby.
