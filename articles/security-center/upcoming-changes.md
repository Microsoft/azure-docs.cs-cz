---
title: Důležité změny přicházející do Azure Security Center
description: Nadcházející změny Azure Security Center, na které může být nutné vědět a pro které může být nutné naplánovat
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: memildin
ms.openlocfilehash: 83c1d8ac316703d9d22d0716c86c5731c3db7133
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226467"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Důležité nadcházející změny Azure Security Center

> [!IMPORTANT]
> Informace na této stránce se vztahují k předběžným produktům nebo funkcím, které mohou být podstatně změněny předtím, než budou komerčně vydány. Společnost Microsoft neposkytuje žádné závazky ani záruky výslovně uvedené ani předpokládané, a to v souvislosti s informacemi, které jsou zde uvedeny.

Na této stránce se dozvíte o změnách, které jsou plánovány pro Security Center. Popisuje plánované změny produktu, které by mohly mít vliv na vaše zabezpečené skóre nebo pracovní postupy.

Pokud hledáte nejnovější poznámky k verzi, najdete je v [Azure Security Center novinky](release-notes.md).


## <a name="planned-changes"></a>Plánované změny

- [Doporučení pro ochranu úloh Kubernetes se brzy uvolní pro obecnou dostupnost (GA).](#kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga)
- [Nepoužívá se dvě doporučení pro řízení zabezpečení použít aktualizace systému.](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Vylepšení doporučení klasifikace dat SQL](#enhancements-to-sql-data-classification-recommendation)


### <a name="kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga"></a>Doporučení pro ochranu úloh Kubernetes se brzy uvolní pro obecnou dostupnost (GA).

**Odhadované datum změny:** Leden 2021

Doporučení pro ochranu úloh Kubernetes, která jsou popsaná v tématu [ochrana úloh Kubernetes](kubernetes-workload-protections.md) , jsou momentálně ve verzi Preview. I když je doporučení ve verzi Preview, nevystavuje prostředek není v pořádku a není zahrnutý do výpočtů vašeho zabezpečeného skóre.

Tato doporučení budou brzy vydána pro obecnou dostupnost (GA), a *proto budou* součástí výpočtu skóre. Pokud jste to ještě neudělali, může to vést ke mírnému dopadu na vaše zabezpečené skóre.

Opravte je všude, kde je to možné (Zjistěte, jak se v [Azure Security Centerují doporučení k nápravě](security-center-remediate-recommendations.md)).

Doporučení pro ochranu úloh Kubernetes jsou:

- V clusterech by měl být nainstalovaný a povolený Azure Policy doplněk pro Kubernetes.
- Měla by se vyhovět omezení procesoru a paměti kontejneru.
- Měly by se vyhnout privilegovanému kontejneru
- Neměnné (jen pro čtení) kořenový systém souborů by měl být vynutil pro kontejnery.
- Je třeba zabránit kontejneru s eskalací oprávnění.
- Spuštění kontejnerů jako kořenový uživatel by se mělo vyhnout.
- Kontejnery sdílející závislé obory názvů hostitele by se měly vyhnout
- Pro kontejnery by se měly vyhovět aspoň privilegované možnosti pro Linux.
- Použití ovládacího připojení svazku pod HostPath by mělo být omezené na známý seznam.
- Kontejnery by měly naslouchat jenom povoleným portům.
- Služby by měly naslouchat jenom povoleným portům.
- Používání hostitelských sítí a portů by se mělo omezit.
- Přepsání nebo zakázání profilu kontejnerů AppArmor by mělo být omezené.
- Image kontejneru by se měly nasadit jenom z důvěryhodných registrů.             

Přečtěte si další informace o těchto doporučeních v [ochraně vašich Kubernetes úloh](kubernetes-workload-protections.md).

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Nepoužívá se dvě doporučení pro řízení zabezpečení použít aktualizace systému. 

**Odhadované datum změny:** Únor 2021

Následující dvě doporučení se naplánují jako zastaralá v únoru 2021:

- Počítače **by měly být restartovány, aby bylo možné použít aktualizace systému**. To může vést ke mírnému dopadu na vaše zabezpečené skóre.
- **Agent monitorování by měl být nainstalovaný na vašich počítačích**. Toto doporučení se týká pouze místních počítačů a některé z jeho logiky se přenesou na jiné doporučení **Log Analytics by se měly vyřešit problémy se stavem agenta na vašich počítačích**. To může vést ke mírnému dopadu na vaše zabezpečené skóre.

Doporučujeme, abyste provedli kontrolu průběžného exportu a konfigurací automatizace pracovních postupů, abyste zjistili, jestli jsou tato doporučení zahrnutá v nich. Také by se měly aktualizovat všechny řídicí panely nebo jiné nástroje pro monitorování, které by je mohly používat.

Další informace o těchto doporučeních najdete na [referenční stránce doporučení zabezpečení](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Vylepšení doporučení klasifikace dat SQL

**Odhadované datum změny:** Q2 2021

Aktuální verze **citlivých dat v databázích SQL by měla být klasifikována** v části použít bezpečnostní ovládací prvek zabezpečení **klasifikace dat** bude nahrazena novou verzí, která je lépe zarovnána s strategií klasifikace dat společnosti Microsoft. Výsledek:

- Doporučení už nebude mít vliv na vaše zabezpečené skóre.
- Řízení zabezpečení (použití klasifikace dat) už nebude mít vliv na vaše zabezpečené skóre.
- Změní se také ID doporučení (aktuálně se b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Další kroky

Všechny nedávné změny v produktu najdete v tématu [co je nového v Azure Security Center?](release-notes.md).
