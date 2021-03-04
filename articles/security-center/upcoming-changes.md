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
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 375e8a748e8833e9483d92353ed04add287e90fb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705088"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Důležité nadcházející změny Azure Security Center

> [!IMPORTANT]
> Informace na této stránce se vztahují k předběžným produktům nebo funkcím, které mohou být podstatně změněny předtím, než budou komerčně vydány. Společnost Microsoft neposkytuje žádné závazky ani záruky výslovně uvedené ani předpokládané, a to v souvislosti s informacemi, které jsou zde uvedeny.

Na této stránce se dozvíte o změnách, které jsou plánovány pro Security Center. Popisuje plánované změny produktu, které by mohly mít vliv na vaše zabezpečené skóre nebo pracovní postupy.

Pokud hledáte nejnovější poznámky k verzi, najdete je v [Azure Security Center novinky](release-notes.md).


## <a name="planned-changes"></a>Plánované změny

- [Dvě starší verze doporučení už nebudou zapisovat data přímo do protokolu aktivit Azure.](#two-legacy-recommendations-will-no-longer-write-data-directly-to-azure-activity-log)
- [Nepoužívá se dvě doporučení pro řízení zabezpečení použít aktualizace systému.](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Vylepšení doporučení klasifikace dat SQL](#enhancements-to-sql-data-classification-recommendation)
- [Vyřazení 11 výstrah v programu Azure Defender jako vyřazení](#deprecation-of-11-azure-defender-alerts)


### <a name="two-legacy-recommendations-will-no-longer-write-data-directly-to-azure-activity-log"></a>Dvě starší verze doporučení už nebudou zapisovat data přímo do protokolu aktivit Azure. 

**Odhadované datum změny:** Březen 2021

Security Center předá data pro téměř všechna doporučení zabezpečení, aby je Azure Advisor, která zase zapisuje do [protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md).

V případě dvou doporučení se data zapisují současně přímo do protokolu aktivit Azure. Tato změna Security Center zastaví zápis dat těchto starších doporučení zabezpečení přímo do protokolu aktivit. Místo toho vyexportujeme data, abychom Azure Advisori jako u všech dalších doporučení. 

Existují dvě starší verze doporučení:
- Na vašich počítačích by se měly vyřešit problémy stavu aplikace Endpoint Protection.
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno

Pokud jste přistupovali k informacím o těchto dvou doporučeních v kategorii "doporučení typu TaskDiscovery" protokolu aktivit, nebude už k dispozici.

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Nepoužívá se dvě doporučení pro řízení zabezpečení použít aktualizace systému. 

**Odhadované datum změny:** Únor 2021

Následující dvě doporučení se naplánují jako zastaralá v únoru 2021:

- Počítače **by měly být restartovány, aby bylo možné použít aktualizace systému**. To může vést ke mírnému dopadu na vaše zabezpečené skóre.
- **Agent monitorování by měl být nainstalovaný na vašich počítačích**. Toto doporučení se týká pouze místních počítačů a některé z jeho logiky se přenesou na jiné doporučení **Log Analytics by se měly vyřešit problémy se stavem agenta na vašich počítačích**. To může vést ke mírnému dopadu na vaše zabezpečené skóre.

Doporučujeme, abyste provedli kontrolu průběžného exportu a konfigurací automatizace pracovních postupů, abyste zjistili, jestli jsou tato doporučení zahrnutá v nich. Také by se měly aktualizovat všechny řídicí panely nebo jiné nástroje pro monitorování, které by je mohly používat.

Další informace o těchto doporučeních najdete na [referenční stránce doporučení zabezpečení](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Vylepšení doporučení klasifikace dat SQL

**Odhadované datum změny:** Q2 2021

Doporučení **citlivá data v databázích SQL by měla být klasifikována** v části použít bezpečnostní ovládací prvek zabezpečení **klasifikace dat** , bude nahrazena novou verzí, která je lépe zarovnána s strategií klasifikace dat společnosti Microsoft. V důsledku toho se změní i ID doporučení (aktuálně se b0df6f56-862d-4730-8597-38c0fd4ebd59).


### <a name="deprecation-of-11-azure-defender-alerts"></a>Vyřazení 11 výstrah v programu Azure Defender jako vyřazení

**Odhadované datum změny:** Březen 2021

Příští měsíc budou jedenácté výstrahy Azure Defenderu uvedené dál zastaralé.

- Nové výstrahy nahradí tyto dvě upozornění a budou poskytovat lepší pokrytí:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | PREVIEW – zjistilo se spuštění funkce pro mikroshluking Toolkit Get-AzureDomainInfo. |
    | ARM_MicroBurstRunbook    | PREVIEW – zjistilo se spuštění funkce pro mikroshluking Toolkit Get-AzurePasswords.  |
    |                          |                                                                          |

- Tyto devět výstrah se týkají konektoru Azure Active Directory Identity Protection, který již byl zastaralý:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Neznámé vlastnosti přihlášení |
    | AnonymousLogin      | Anonymní IP adresa          |
    | InfectedDeviceLogin | Propojená IP adresa pro malware     |
    | ImpossibleTravel    | Neobvyklá cesta               |
    | MaliciousIP         | Škodlivá IP adresa          |
    | LeakedCredentials   | Uniklé přihlašovací údaje            |
    | PasswordSpray       | Sprej hesla                |
    | LeakedCredentials   | Analýza hrozeb v Azure AD  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 



## <a name="next-steps"></a>Další kroky

Všechny nedávné změny v produktu najdete v tématu [co je nového v Azure Security Center?](release-notes.md).
