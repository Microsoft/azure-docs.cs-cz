---
title: Pozastavené domény ve službě Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Přečtěte si o různých stavech spravované domény Azure AD DS a o tom, jak obnovit pozastavenou doménu.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 42b26911c12b1e7c62444a6fb2ee68720b02a56b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654609"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Seznamte se se stavy stavu a vyřešte pozastavené domény ve službě Azure Active Directory Domain Services

Když služba Azure Active Directory Domain Services (Azure AD DS) nemůže dlouhou dobu obsluhovat spravovanou doménu, uvede spravovanou doménu do pozastaveného stavu. Pokud spravovaná doména zůstane v pozastaveném stavu, automaticky se odstraní. Chcete-li zachovat vaši spravovanou doménu Azure AD DS v pořádku a vyhnout se pozastavení, vyřešte všechna upozornění co nejrychleji.

Tento článek vysvětluje, proč jsou spravované domény pozastaveny a jak obnovit pozastavenou doménu.

## <a name="overview-of-managed-domain-states"></a>Přehled stavů spravované domény

Prostřednictvím životního cyklu spravované domény Azure AD DS existují různé stavy, které označují jeho stav. Pokud spravovaná doména hlásí problém, rychle vyřešte základní příčinu, která má zabránit tomu, aby se stav nadále zhoršoval.

![Průběh stavů, které spravované domény Azure AD DS trvá k pozastavení](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Spravovaná doména Azure AD DS může být v jednom z následujících stavů:

* [Spuštěno](#running-state)
* [Potřebuje pozornost](#needs-attention-state)
* [Dočasně blokován.](#suspended-state)
* [Odstranění](#deleted-state)

## <a name="running-state"></a>Provozní stav

Spravovaná doména Azure AD DS, která je správně nakonfigurovaná a spuštěná bez problémů, je ve stavu *Spuštěno.* Toto je požadovaný stav pro spravovanou doménu.

### <a name="what-to-expect"></a>Co očekávat

* Platforma Azure může pravidelně sledovat stav spravované domény.
* Řadiče domény pro spravovanou doménu jsou pravidelně opravovány a aktualizovány.
* Změny ze služby Azure Active Directory se pravidelně synchronizují se spravovanou doménou.
* Pravidelné zálohy jsou převzaty pro spravovanou doménu.

## <a name="needs-attention-state"></a>Potřebuje stav pozornosti

Spravovaná doména Azure AD DS s jedním nebo více problémy, které je potřeba opravit, je ve stavu *Potřeby pozornosti.* Stránka stavu spravované domény uvádí výstrahy a označuje, kde došlo k potížím. Některé výstrahy jsou přechodné a jsou automaticky vyřešeny platformou Azure. U jiných výstrah můžete problém vyřešit podle uvedených kroků řešení. Je tu kritické výstrahy, [otevřete žádost o podporu Azure][azure-support] pro další pomoc při řešení potíží.

Jedním z příkladů výstrahy je, když existuje omezující skupina zabezpečení sítě. V této konfiguraci platformy Azure nemusí být možné aktualizovat a monitorovat spravovanou doménu. Je generována výstraha a stav se změní na *Vyžaduje pozornost*.

Další informace najdete v [tématu Jak řešit výstrahy pro spravovanou doménu Azure AD DS][resolve-alerts].

### <a name="what-to-expect"></a>Co očekávat

Když je spravovaná doména Azure AD DS ve stavu *Vyžaduje pozornost,* platforma Azure nemusí být schopná pravidelně monitorovat, opravovat, aktualizovat nebo zálohovat data. V některých případech, například při neplatné konfiguraci sítě, mohou být řadiče domény pro spravovanou doménu nedostupné.

* Spravovaná doména je v stavu Není v pořádku a průběžné monitorování stavu může zastavit, dokud výstraha je vyřešena.
* Řadiče domény pro spravovanou doménu nelze opravit ani aktualizovat.
* Změny ze služby Azure Active Directory nemusí být synchronizovány do spravované domény.
* Zálohy pro spravovanou doménu nemusí být přijata.
* Pokud vyřešíte nekritické výstrahy, které mají vliv na spravovanou doménu, stav by se měl vrátit do *stavu Spuštěno.*
* Kritické výstrahy se aktivují pro problémy s konfigurací, kde platforma Azure nemůže dosáhnout řadiče domény. Pokud tyto kritické výstrahy nejsou vyřešeny do 15 dnů, spravovaná doména vstoupí *do stavu Pozastaveno.*

## <a name="suspended-state"></a>Pozastavený stav

Spravovaná doména Azure AD DS přejde do **stavu Pozastaveno** z jednoho z následujících důvodů:

* Jeden nebo více důležitých výstrah nebylo vyřešeno za 15 dní.
    * Kritické výstrahy mohou být způsobeny chybnou konfigurací, která blokuje přístup k prostředkům, které jsou potřeba službou Azure AD DS. Například výstraha [AADDS104: Chyba sítě][alert-nsg] byla nevyřešena více než 15 dní ve spravované doméně.
* S předplatným Azure došlo k problému s fakturací nebo vypršela platnost předplatného Azure.

Spravované domény se pozastaví, když platforma Azure nemůže spravovat, monitorovat, opravovat nebo zálohovat doménu. Spravovaná doména zůstane v *pozastaveném* stavu po dobu 15 dnů. Chcete-li zachovat přístup ke spravované doméně, okamžitě vyřešte důležité výstrahy.

### <a name="what-to-expect"></a>Co očekávat

Následující chování dochází, když je spravovaná doména Azure AD DS ve stavu *Pozastaveno:*

* Řadiče domény pro spravovanou doménu jsou zrušené a nejsou dostupné v rámci virtuální sítě.
* Zabezpečený přístup LDAP ke spravované doméně přes internet, pokud je povolen, přestane fungovat.
* Existují chyby ověřování spravované domény, přihlášení k virtuálním počítačům připojení k doméně nebo připojení přes LDAP/LDAPS.
* Zálohy pro spravovanou doménu se již nepřijímají.
* Synchronizace se službou Azure AD se zastaví.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Jak poznáte, že je vaše spravovaná doména pozastavena?

Na webu Azure Portal se zobrazí [výstraha][resolve-alerts] s upozorněním, že se doména pozastavuje. Stav domény také zobrazuje *Pozastaveno*.

### <a name="restore-a-suspended-domain"></a>Obnovení pozastavené domény

Chcete-li obnovit stav spravované domény Azure AD DS, která je ve stavu *Pozastaveno,* proveďte následující kroky:

1. Na webu Azure Portal vyhledejte a vyberte **služby domény**.
1. V seznamu vyberte spravovanou doménu Azure AD DS, například *aaddscontoso.com*, a pak vyberte **Stav**.
1. Vyberte výstrahu, například *AADDS503* nebo *AADDS504*, v závislosti na příčině pozastavení.
1. Zvolte odkaz na řešení, který je k dispozici ve výstraze, a postupujte podle pokynů k jeho vyřešení.

Spravovanou doménu lze obnovit pouze k datu poslední zálohy. Datum poslední zálohy se zobrazí na stránce **Stav** spravované domény. Změny, ke kterým došlo po poslední záloze, nebudou obnoveny. Zálohy pro spravovanou doménu jsou uloženy po dobu až 30 dnů. Zálohy, které jsou starší než 30 dní, budou odstraněny.

Po vyřešení výstrahy, když je spravovaná doména ve stavu *Pozastaveno,* [otevřete žádost o podporu Azure][azure-support] pro návrat do stavu v pořádku. Pokud existuje záloha kratší než 30 dní, podpora Azure můžete obnovit spravovanou doménu.

## <a name="deleted-state"></a>Odstraněný stav

Pokud spravovaná doména Azure AD DS zůstane ve stavu *Pozastaveno* po dobu 15 dnů, je odstraněna. Tento proces je neopravitelný.

### <a name="what-to-expect"></a>Co očekávat

Když spravovaná doména Azure AD DS přejde do stavu *Odstraněno,* zobrazí se následující chování:

* Všechny prostředky a zálohy pro spravovanou doménu budou odstraněny.
* Spravované domény nelze obnovit a potřebujete vytvořit náhradní spravovanou doménu, abyste mohli znovu použít Azure AD DS.
* Po odstranění se vám neúčtují fakturované spravované domény.

## <a name="next-steps"></a>Další kroky

Chcete-li zachovat vaši spravovanou doménu Azure AD DS v pořádku a minimalizovat riziko, že se bude pozastavena, přečtěte si, jak [vyřešit výstrahy pro spravovanou doménu][resolve-alerts].

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
