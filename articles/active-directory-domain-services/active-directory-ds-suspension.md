---
title: 'Azure Active Directory Domain Services: Pozastaveno domény | Dokumentace Microsoftu'
description: Spravovaná doména pozastavení a zrušení
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2018
ms.author: ergreenl
ms.openlocfilehash: 5f350bacdc21ec4e5077c93ecc9d97f2fe6c39a5
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090905"
---
# <a name="suspended-domains"></a>Pozastavené domén
Pokud Azure AD Domain Services nemůže pro služby spravované domény pro dlouhou dobu, přejde do pozastaveného stavu spravované domény. Tento článek vysvětluje, proč jsou pozastavené spravovaných domén, délka pozastavení a řešení pozastavené domény.


## <a name="states-your-managed-domain-can-be-in"></a>Stavy vaše spravovaná doména může být v

![Časová osa pozastavené domény](media\active-directory-domain-services-suspension\suspension-timeline.PNG)

Na předchozím obrázku uvádí, jak je pozastavena doméně, jak dlouho se pozastaví a nakonec odstranění spravované domény. Následující části jsou podrobně popsané důvody, proč může pozastavit domény a jak zrušit pozastavení účtu uživatele spravované domény.

### <a name="running-state"></a>Spuštěném stavu.
Spravované domény, který je správně nakonfigurované a provozní pravidelně se **systémem** stavu.

**Co můžete očekávat:**
* Microsoft se pravidelně sledovat stav vaší spravované domény.
* Řadiče domény pro vaše spravovaná doména se opravují se a pravidelně aktualizuje.
* Změny z Azure Active Directory jsou pravidelně synchronizována do spravované domény.
* Pravidelné zálohy jsou prováděny vaší spravované domény.


### <a name="needs-attention-state"></a>Stav "vyžaduje pozornost.
Spravovaná doména se v **potřebuje pozornost** stavu, pokud jeden nebo více problémů vyžadují správce k akci. Na stránce stav vaší spravované domény, zobrazí se seznam jedno nebo několik upozornění v tomto stavu. Například pokud jste nakonfigurovali omezující skupiny zabezpečení sítě pro vaši virtuální síť, Microsoft možná nebudete moct aktualizace a sledování vaší spravované domény. Výsledkem této neplatná konfigurace výstrahy vytvořené a je ve stavu Needs Attention umístit vaší spravované domény.

Každé upozornění obsahuje sadu kroků pro řešení. Některé výstrahy jsou přechodné a bude automaticky vyřešila služba. Některé výstrahy můžete vyřešit podle pokynů v odpovídající postup řešení této výstrahy. K vyřešení některých kritické výstrahy, budete muset kontaktovat podporu Microsoftu.

Další informace najdete v tématu [postupy řešení výstrah ve spravované doméně](active-directory-ds-troubleshoot-alerts.md).

**Co můžete očekávat:**

V některých případech (například pokud máte konfigurace neplatný sítě) může nedostupný řadiče domény vaší spravované domény. Proto se společnost Microsoft nemůže zaručit vaši spravovanou doménu monitorovat, opravit, aktualizace nebo zálohovanou v pravidelných intervalech v tomto stavu.

* Spravovaná doména je v pořádku a průběžný stav může být ovlivněno monitorování, dokud se výstraha vyřeší.
* Řadiče domény vaší spravované domény nemusí být opravit nebo aktualizovat.
* Změny z Azure Active Directory se proto nemusí synchronizovat do spravované domény.
* Může být přijata zálohování vaší spravované domény, pokud je to možné.
* Pokud vyřešit výstrahy vliv na vaši spravovanou doménu je možné obnovit do stavu "Spuštěno" vaší spravované domény.
* Kritické výstrahy se spouštějí pro problémy s konfigurací, kde společnost Microsoft se nám kontaktovat řadiče domény. Pokud nejsou tyto výstrahy do 15 dnů, zařadí se do stavu "Pozastaveno" vaší spravované domény.


### <a name="suspended-state"></a>"Pozastavena.
Spravovaná doména je umístěn **pozastaveno** stavu z následujících důvodů:
* Jeden nebo více kritické výstrahy se neopravily během 15 dnů. To může způsobovat chybné konfiguraci, která zablokuje přístup k prostředkům vyžaduje Azure AD Domain Services.
    * Například, pokud spravovaná doména se výstraha [AADDS104: Chyba sítě](active-directory-ds-troubleshoot-nsg.md) nevyřešené víc než 15 dnů.
* Fakturační problému s vaším předplatným Azure, nebo pokud vypršela platnost předplatného Azure.

Spravované domény jsou pozastavené, když Microsoft nemůže ke správě, monitorování, oprava nebo zálohování domény průběžně.

**Co můžete očekávat:**
* Řadiče domény vaší spravované domény jsou zrušení zřízení a nejsou dostupné v rámci virtuální sítě.
* Přístup ke spravované doméně přes internet (je-li povoleno) Secure LDAP přestane fungovat.
* Zobrazí se chyby při ověřování do spravované domény, přihlášení k doméně připojené virtuální počítače, připojení prostřednictvím protokolu LDAP/LDAPS atd.
* Zálohy pro vaše spravovaná doména se už přesunete.
* Je potřeba vyřešit výstrahu způsobuje vaší spravované domény ve stavu "Pozastaveno" a potom kontaktovat podporu.
* Podpora může mít možnost obnovit spravované domény, pouze v případě, že je existující zálohy, která je menší než 30 dní.


### <a name="deleted-state"></a>Stavu 'Odstraněno'
Spravovaná doména, který zůstane ve stavu "Pozastaveno" po dobu 15 dnů se **odstraněné**.

**Co můžete očekávat:**
* Odstraní se všechny prostředky a zálohy pro spravovanou doménu.
* Nelze obnovit spravované domény a bude muset vytvořit nové spravované doméně používat službu Azure AD Domain Services.
* Nebudou se vám účtovat spravované domény.


## <a name="what-happens-when-a-managed-domain-is-suspended"></a>Co se stane, když je pozastavený, spravované domény?
Při pozastavení domény Azure AD Domain Services zastaví a zruší zřizuje řadiče domény vaší spravované domény. V důsledku toho již zálohy jsou prováděny, nebudou moct uživatelé přihlásit se k vaší doméně a zastaví synchronizaci se službou Azure AD.

Spravovaná doména zůstane pouze v pozastaveném stavu maximálně 15 dnů. Aby bylo možné zajistit včasné obnovení, doporučujeme že nejdříve vyřešit pozastavení.


## <a name="how-do-i-know-if-my-managed-domain-is-suspended"></a>Jak poznám, že pokud je pozastavený, spravované domény?
Zobrazí se [výstraha](active-directory-ds-troubleshoot-alerts.md) na stránce stavu Azure AD Domain Services na portálu Azure portal, který deklaruje domény pozastaveno. Kromě toho stavu doméně ukazuje "Pozastaveno".


## <a name="how-do-i-restore-a-suspended-domain"></a>Jak obnovit pozastavenou domény?
Chcete-li obnovit doménu ve stavu "Pozastaveno", proveďte následující kroky:

1. Přejděte [stránku Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) na portálu Azure portal
2. Klikněte na doménu, kterou chcete zrušit pozastavení účtu uživatele
3. Na levém navigačním panelu klikněte na tlačítko **stavu**
4. Klikněte na výstrahu. ID výstrahy bude AADDS503 nebo AADDS504, v závislosti na příčině, ve kterém byly pozastaveny.
5. Klikněte na odkaz řešení k dispozici ve výstraze a postupujte podle kroků tuto výstrahu vyřešíte.

Vaše doména lze obnovit pouze na datum poslední zálohy. Datum poslední zálohy je zobrazeny na stránce stav vaší spravované domény. Všechny změny, ke kterým došlo po poslední záloze nebude obnoven. Zálohy pro spravovanou doménu se uchovávají po dobu až 30 dnů. Zálohování, které jsou starší než 30 dnů se odstraní.


## <a name="deleting-domains"></a>Odstranění domény
Pokud domény je pozastaven po dobu více než 15 dnů, Azure AD Domain Services odstraní spravovanou doménu z důvodu nečinnosti a neschopnost služby domény. Už nebudou účtovat služby Azure AD Domain Services. V tuto chvíli nelze obnovit vaše spravovaná doména a muset znovu vytvořit.


## <a name="next-steps"></a>Další postup
- [Vyřešit upozornění ve vaší spravované doméně](active-directory-ds-troubleshoot-alerts.md)
- [Přečtěte si víc o službě Azure AD Domain Services](active-directory-ds-overview.md)
- [Kontaktováním produktového týmu](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Kontaktujte nás
Obraťte se na Azure Active Directory Domain Services produktovému týmu a [podělit se o názory, nebo pro podporu](active-directory-ds-contact-us.md).
