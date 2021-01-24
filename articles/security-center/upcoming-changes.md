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
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 1b034c0f1c62eecf8139ed908a5a242060f3e886
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746556"
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
- [35 doporučení verze Preview pro zvýšení pokrytí srovnávacího testu zabezpečení Azure](#35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark)


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

Aktuální verze **citlivých dat v databázích SQL by měla být klasifikována** v části použití řízení zabezpečení **klasifikace dat** se bude zařazovat a nahrazena novou verzí, která je lépe zarovnaná s strategií klasifikace dat společnosti Microsoft. Výsledek:

- Doporučení už nebude mít vliv na vaše zabezpečené skóre.
- Řízení zabezpečení (použití klasifikace dat) už nebude mít vliv na vaše zabezpečené skóre.
- Změní se také ID doporučení (aktuálně se b0df6f56-862d-4730-8597-38c0fd4ebd59).


### <a name="35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark"></a>35 doporučených doporučení pro zvýšení pokrytí testu zabezpečení Azure

**Odhadované datum změny:** Leden 2021

Srovnávací test zabezpečení Azure je Microsoftem vytvořená sada pokynů pro zabezpečení a osvědčené postupy na základě běžných architektur dodržování předpisů, které jsou specifické pro Azure. [Další informace o srovnávacím testu zabezpečení Azure](../security/benchmarks/introduction.md)

Následující doporučení 35 verze Preview budou přidána do Security Center, aby se zvýšilo pokrytí tohoto srovnávacího testu.

Doporučení pro verzi Preview negenerují prostředek v pořádku a nejsou zahrnutá do výpočtů vašeho zabezpečeného skóre. Opravte je všude, kde je to možné, takže až do doby, kdy období Preview skončí, přispějete k vašemu skóre. Přečtěte si další informace o tom, jak na tato doporučení reagovat v tématu o [opravách doporučení v Azure Security Center](security-center-remediate-recommendations.md).

| Řízení zabezpečení                     | Nová doporučení                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Povolit šifrování v klidovém umístění            | -Azure Cosmos DB účty by měly používat klíče spravované zákazníkem k šifrování neaktivních dat<br>– Azure Machine Learning pracovní prostory by měly být zašifrované pomocí klíče spravovaného zákazníkem (CMK)<br>– Zajistěte, aby byla pro servery MySQL povolená ochrana dat klíčů.<br>– Zajistěte, aby byla pro servery PostgreSQL povolená vlastní ochrana dat klíčů.<br>-Cognitive Services účty by měly povolit šifrování dat pomocí klíče spravovaného zákazníkem (CMK).<br>-Registry kontejneru by měly být zašifrované pomocí klíče spravovaného zákazníkem (CMK)<br>– Spravované instance SQL by měly používat klíče spravované zákazníkem k šifrování neaktivních dat<br>– SQL servery by měly používat klíče spravované zákazníky k šifrování neaktivních dat.<br>– Účty úložiště by měly pro šifrování používat klíč spravovaný zákazníkem (CMK)                                                                                                                                                              |
| Implementace osvědčených postupů zabezpečení    | – Odběry by měly mít kontaktní e-mailovou adresu pro problémy se zabezpečením<br> – Automatické zřizování agenta Log Analytics by mělo být ve vašem předplatném povolené.<br> -E-mailové oznámení pro výstrahy s vysokou závažností by mělo být povolené.<br> -E-mailové oznámení vlastníkovi předplatného pro upozornění s vysokou závažností by mělo být povolené.<br> – Trezory klíčů by měly mít povolenou ochranu vyprázdnění<br> – Trezory klíčů by měly mít povolené obnovitelné odstranění. |
| Správa přístupu a oprávnění        | – Aplikace Function app by měly mít povolené klientské certifikáty (příchozí klientské certifikáty). |
| Ochrana aplikací před útoky DDoS | – Brána Firewall webových aplikací (WAF) by měla být povolená pro Application Gateway<br> – Služba brány firewall webových aplikací (WAF) by měla být povolená pro službu Azure front-dveří |
| Omezit neautorizovaný přístup k síti | – Brána firewall by měla být povolená na Key Vault<br> – Privátní koncový bod by měl být nakonfigurovaný pro Key Vault<br> -Konfigurace aplikace by měla používat privátní odkaz<br> – Mezipaměť Azure pro Redis by se měla nacházet v rámci virtuální sítě.<br> -Azure Event Grid domény by měly používat privátní odkaz<br> -Azure Event Grid témata by měla používat privátní odkaz<br> -Azure Machine Learning pracovní prostory by měly používat privátní odkaz<br> – Služba signalizace Azure by měla používat privátní odkaz<br> – Jarní cloud Azure by měl používat vkládání ze sítě<br> – Registry kontejneru by neměly umožňovat neomezený přístup k síti.<br> -Registry kontejneru by měly používat privátní odkaz<br> – Přístup k veřejné síti by měl být pro MariaDB servery zakázaný.<br> – Přístup k veřejné síti by měl být pro servery MySQL zakázaný.<br> – Přístup k veřejné síti by měl být pro PostgreSQL servery zakázaný.<br> – Účet úložiště by měl používat připojení privátního odkazu.<br> – Účty úložiště by měly omezovat přístup k síti pomocí pravidel virtuální sítě<br> – Šablony pro sestavovatele imagí virtuálních počítačů by měly používat privátní odkaz|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Související odkazy:

- [Další informace o testu zabezpečení Azure](../security/benchmarks/introduction.md)
- [Další informace o Azure Database for MariaDB](../mariadb/overview.md)
- [Další informace o Azure Database for MySQL](../mysql/overview.md)
- [Další informace o Azure Database for PostgreSQL](../postgresql/overview.md)





## <a name="next-steps"></a>Další kroky

Všechny nedávné změny v produktu najdete v tématu [co je nového v Azure Security Center?](release-notes.md).