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
ms.date: 12/14/2020
ms.author: memildin
ms.openlocfilehash: 63ae35da973525a8331eeeb463eb09e36e8b6e26
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97398928"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Důležité nadcházející změny Azure Security Center

> [!IMPORTANT]
> Informace na této stránce se vztahují k předběžným produktům nebo funkcím, které mohou být podstatně změněny předtím, než budou komerčně vydány. Společnost Microsoft neposkytuje žádné závazky ani záruky výslovně uvedené ani předpokládané, a to v souvislosti s informacemi, které jsou zde uvedeny.

Na této stránce se dozvíte o změnách, které jsou plánovány pro Security Center. Popisuje plánované změny produktu, které by mohly mít vliv na vaše zabezpečené skóre nebo pracovní postupy.

Pokud hledáte nejnovější poznámky k verzi, najdete je v [Azure Security Center novinky](release-notes.md).


## <a name="planned-changes"></a>Plánované změny

- ["Nepoužitelné" prostředky, které se mají ohlásit jako "vyhovující" v Azure Policy posouzení](#not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments)
- [35 doporučení verze Preview pro zvýšení pokrytí srovnávacího testu zabezpečení Azure](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)

### <a name="not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments"></a>"Nepoužitelné" prostředky, které se mají ohlásit jako "vyhovující" v Azure Policy posouzení

**Odhadované datum změny:** Leden 2021

V současné době se prostředky vyhodnocené pro doporučení a zjištěné jako nevyhovující **se zobrazí v** Azure Policy jako "nekompatibilní". Žádná akce uživatele nemůže změnit stav na "kompatibilní". Z této plánované změny budou pro lepší přehlednost nahlášeny jako "vyhovující".


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>35 doporučení verze Preview pro zvýšení pokrytí srovnávacího testu zabezpečení Azure

**Odhadované datum změny:** Prosince 2020

Srovnávací test zabezpečení Azure je Microsoftem vytvořená sada pokynů pro zabezpečení a osvědčené postupy na základě běžných architektur dodržování předpisů, které jsou specifické pro Azure. [Další informace o srovnávacím testu zabezpečení Azure](../security/benchmarks/introduction.md)

Následující doporučení 35 Preview byla přidána do Security Center pro zvýšení pokrytí tohoto srovnávacího testu.

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