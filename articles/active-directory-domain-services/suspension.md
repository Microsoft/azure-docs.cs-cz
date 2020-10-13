---
title: Pozastavené domény v Azure AD Domain Services | Microsoft Docs
description: Přečtěte si o různých stavech pro spravovanou doménu Azure služba AD DS a o tom, jak obnovit pozastavenou doménu.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: 9bcecdfaf3121ea024fee567dbe83544229373be
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967694"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Pochopení stavů a řešení pozastavených domén v Azure Active Directory Domain Services

Když Azure Active Directory Domain Services (Azure služba AD DS) nemůže po dlouhou dobu obsluhovat spravovanou doménu, převede spravovanou doménu do pozastaveného stavu. Pokud spravovaná doména zůstane v pozastaveném stavu, je automaticky odstraněna. Aby se vaše Azure služba AD DSa v dobrém stavu v pořádku a zabránila pozastavení, vyřešte všechny výstrahy tak rychle, jak to jde.

Tento článek vysvětluje, proč jsou spravované domény pozastavené a jak obnovit pozastavenou doménu.

## <a name="overview-of-managed-domain-states"></a>Přehled stavů spravovaných domén

V životním cyklu spravované domény existují různé stavy, které určují jeho stav. Pokud spravovaná doména nahlásí problém, můžete rychle vyřešit původní příčinu, než se stav zastaví, aby bylo možné pokračovat v degradování.

![Průběh stavů, které spravovaná doména potřebuje k pozastavení](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Spravovaná doména může být v jednom z následujících stavů:

* [Spuštěno](#running-state)
* [Vyžaduje pozornost](#needs-attention-state)
* [Dočasně blokován.](#suspended-state)
* [Odstraněné](#deleted-state)

## <a name="running-state"></a>Běžící stav

Spravovaná doména, která je nakonfigurovaná správně a bez problémů, je ve stavu *spuštěno* . Toto je požadovaný stav pro spravovanou doménu.

### <a name="what-to-expect"></a>Co očekávat

* Platforma Azure může pravidelně monitorovat stav spravované domény.
* Řadiče domény pro spravovanou doménu jsou opravené a pravidelně se aktualizují.
* Změny z Azure Active Directory jsou pravidelně synchronizovány do spravované domény.
* Pro spravovanou doménu jsou pořízeny pravidelné zálohy.

## <a name="needs-attention-state"></a>Vyžaduje stav pozornosti

Spravovaná doména s jedním nebo více problémy, které je potřeba opravit, je ve stavu *vyžaduje pozornost* . Stránka stav pro spravovanou doménu zobrazuje seznam výstrah a označuje, kde došlo k potížím.

Některé výstrahy jsou přechodné a automaticky se vyřeší platformou Azure. Pro jiné výstrahy můžete problém vyřešit pomocí uvedených kroků řešení. Je to kritická výstraha. pro další pomoc s řešením potíží [otevřete žádost o podporu Azure][azure-support] .

Jedním z příkladů výstrah je, že existuje omezující skupina zabezpečení sítě. V této konfiguraci nemusí být platforma Azure schopná aktualizovat a monitorovat spravovanou doménu. Vygeneruje se výstraha a stav se změní na *vyžaduje pozornost*.

Další informace najdete v tématu [řešení potíží s výstrahami pro spravovanou doménu][resolve-alerts].

### <a name="what-to-expect"></a>Co očekávat

Když je spravovaná doména ve stavu " *potřebuje pozornost* ", platforma Azure nemusí být schopná pravidelně monitorovat, opravovat, aktualizovat ani zálohovat data. V některých případech, třeba v případě neplatné konfigurace sítě, mohou být řadiče domény pro spravovanou doménu nedosažitelné.

* Spravovaná doména je ve stavu není v pořádku a průběžné monitorování stavu může skončit až do vyřešení výstrahy.
* Řadiče domény pro spravovanou doménu nejde opravit ani aktualizovat.
* Změny z Azure Active Directory pravděpodobně nebudou synchronizovány do spravované domény.
* Zálohy pro spravovanou doménu se nemůžou považovat.
* Pokud vyřešíte nekritické výstrahy, které mají vliv na spravovanou doménu, stav by se měl vrátit do stavu *spuštěno* .
* Kritické výstrahy se spouštějí při potížích s konfigurací, kdy se platforma Azure nemůže připojit k řadičům domény. Pokud se tyto kritické výstrahy během 15 dnů nevyřeší, bude spravovaná doména do stavu *pozastaveno* .

## <a name="suspended-state"></a>Pozastavený stav

Spravovaná doména vstoupí do stavu **pozastaveno** z jednoho z následujících důvodů:

* Jeden nebo více kritických výstrah nebylo v 15 dnech vyřešeno.
    * Kritické výstrahy můžou být způsobené chybnou konfigurací, které blokují přístup k prostředkům, které vyžaduje Azure služba AD DS. Například výstraha [AADDS104: Chyba sítě][alert-nsg] byla nevyřešena více než 15 dní ve spravované doméně.
* Došlo k potížím s fakturací v rámci předplatného Azure nebo vypršení platnosti předplatného Azure.

Spravované domény jsou pozastavené, když platforma Azure nemůže spravovat, monitorovat, opravovat nebo zálohovat doménu. Spravovaná doména zůstane v *pozastaveném* stavu po dobu 15 dnů. Chcete-li zachovat přístup ke spravované doméně, vyřešte kritické výstrahy okamžitě.

### <a name="what-to-expect"></a>Co očekávat

Pokud je spravovaná doména v *pozastaveném* stavu, dojde k následujícímu chování:

* Řadiče domény pro spravovanou doménu jsou nezřízené a nejsou dostupné v rámci virtuální sítě.
* Protokol Secure LDAP přístup ke spravované doméně přes Internet, pokud je tato možnost povolená, přestane fungovat.
* Při ověřování ve spravované doméně došlo k selhání, přihlašování k virtuálním počítačům připojeným k doméně nebo připojení přes LDAP/LDAP.
* Zálohy pro spravovanou doménu již nejsou přijímány.
* Synchronizace s Azure AD se zastaví.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Jak poznáte, jestli je vaše spravovaná doména pozastavená?

Na stránce Stav služby Azure služba AD DS v Azure Portal se zobrazí [Upozornění][resolve-alerts] , že je pozastavená doména. Stav domény také zobrazuje *pozastaveno*.

### <a name="restore-a-suspended-domain"></a>Obnovení pozastavené domény

Chcete-li obnovit stav spravované domény, která je v *pozastaveném* stavu, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **Domain Services**.
1. Ze seznamu vyberte spravovanou doménu, třeba *aaddscontoso.com*, a pak vyberte **stav**.
1. V závislosti na příčině pozastavení vyberte výstrahu, jako je například *AADDS503* nebo *AADDS504*.
1. Vyberte odkaz řešení, který je uveden v upozornění, a postupujte podle kroků k jeho vyřešení.

Spravovanou doménu lze obnovit pouze do data posledního zálohování. Datum poslední zálohy se zobrazí na stránce **stav** spravované domény. Jakékoli změny, ke kterým došlo po poslední záloze, se neobnoví. Zálohy spravované domény jsou uložené po dobu až 30 dnů. Zálohy, které jsou starší než 30 dní, se odstraní.

Po vyřešení upozornění, když je spravovaná doména v *pozastaveném* stavu, [otevřete žádost o podporu Azure][azure-support] a vraťte se do stavu v pořádku. Pokud je zálohování méně než 30 dní staré, podpora Azure může tuto spravovanou doménu obnovit.

## <a name="deleted-state"></a>Odstraněný stav

Pokud spravovaná doména zůstane v *pozastaveném* stavu po dobu 15 dnů, odstraní se. Tento proces nelze obnovit.

### <a name="what-to-expect"></a>Co očekávat

Když spravovaná doména vstoupí do *odstraněného* stavu, je vidět následující chování:

* Odstraní se všechny prostředky a zálohy pro spravovanou doménu.
* Nemůžete obnovit spravovanou doménu. K opakovanému použití Azure služba AD DS je nutné vytvořit náhradní spravovanou doménu.
* Po odstranění se vám nebude účtovat spravovaná doména.

## <a name="next-steps"></a>Další kroky

Aby se vaše spravovaná doména v dobrém stavu a minimalizovala rizika, přestala být pozastavena. Přečtěte si, jak [vyřešit výstrahy pro spravovanou doménu][resolve-alerts].

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
