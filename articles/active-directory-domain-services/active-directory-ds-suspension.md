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
ms.date: 07/18/2018
ms.author: ergreenl
ms.openlocfilehash: 99896b0f618151cd368a21c4a212b7d8e28b7a6f
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126089"
---
# <a name="suspended-domains"></a>Pozastavené domén
Pokud Azure AD Domain Services nemůže pro služby spravované domény pro dlouhou dobu, přejde do pozastaveného stavu spravované domény. Tento článek vysvětluje, proč jsou pozastavené spravovaných domén a způsobu řešení pozastavené domény.


## <a name="states-your-managed-domain-can-be-in"></a>Stavy vaše spravovaná doména může být v

![Časová osa pozastavené domény](media\active-directory-domain-services-suspension\suspension-timeline.PNG)

Předchozí obrázek znázorňuje všechny možné stavy spravované domény služby Azure AD Domain Services může být v.

### <a name="running-state"></a>Spuštěném stavu.
Spravované domény, který je správně nakonfigurované a provozní pravidelně se **systémem** stavu.

**Co můžete očekávat:**
* Microsoft může pravidelně sledovat stav vaší spravované domény.
* Řadiče domény pro vaše spravovaná doména se opravují se a pravidelně aktualizuje.
* Změny z Azure Active Directory jsou pravidelně synchronizována do spravované domény.
* Pravidelné zálohy jsou prováděny vaší spravované domény.


### <a name="needs-attention-state"></a>Stav "vyžaduje pozornost.
Spravovaná doména se v **potřebuje pozornost** stavu, pokud jeden nebo více problémů vyžadují správce k akci. Na stránce stav vaší spravované domény, zobrazí se seznam jedno nebo několik upozornění v tomto stavu. Například pokud jste nakonfigurovali omezující skupiny zabezpečení sítě pro vaši virtuální síť, Microsoft možná nebudete moct aktualizace a sledování vaší spravované domény. Výsledkem této neplatná konfigurace výstrahy vytvořené a je ve stavu Needs Attention umístit vaší spravované domény.

Každé upozornění obsahuje sadu kroků pro řešení. Některé výstrahy jsou přechodné a bude automaticky vyřešila služba. Některé výstrahy můžete vyřešit podle pokynů v odpovídající postup řešení této výstrahy. K vyřešení některých kritické výstrahy, budete muset kontaktovat podporu Microsoftu.

Další informace najdete v tématu [postupy řešení výstrah ve spravované doméně](active-directory-ds-troubleshoot-alerts.md).

**Co můžete očekávat:**

V některých případech (například pokud máte konfigurace neplatný sítě) může nedostupný řadiče domény vaší spravované domény. Společnost Microsoft nemůže zaručit vaši spravovanou doménu je sledovat, opravit, aktualizovat nebo zálohovanou v pravidelných intervalech v tomto stavu.

* Spravovaná doména je v pořádku a průběžný stav monitorování může zastavit, dokud se výstraha vyřeší.
* Řadiče domény vaší spravované domény nemusí být opravit nebo aktualizovat.
* Změny z Azure Active Directory se proto nemusí synchronizovat do spravované domény.
* Může být přijata zálohování vaší spravované domény, pokud je to možné.
* Pokud vyřešit výstrahy vliv na vaši spravovanou doménu je možné obnovit do stavu "Spuštěno" vaší spravované domény.
* Kritické výstrahy se spouštějí pro problémy s konfigurací, kde společnost Microsoft se nám kontaktovat řadiče domény. Pokud nejsou tyto výstrahy do 15 dnů, zařadí se do stavu "Pozastaveno" vaší spravované domény.


### <a name="suspended-state"></a>"Pozastavena.
Spravovaná doména je umístěn **pozastaveno** stavu z následujících důvodů:
* Jeden nebo více kritické výstrahy nevyjasněné během 15 dnů. Kritické výstrahy může být způsobeno chybné konfiguraci, která zablokuje přístup k prostředkům vyžaduje Azure AD Domain Services.
    * Například, pokud spravovaná doména se výstraha [AADDS104: Chyba sítě](active-directory-ds-troubleshoot-nsg.md) nevyřešené víc než 15 dnů.
* Fakturační problému s vaším předplatným Azure, nebo vypršela platnost vašeho předplatného Azure.

Spravované domény jsou pozastavené, když Microsoft nemůže ke správě, monitorování, oprava nebo zálohování domény průběžně.

**Co můžete očekávat:**
* Řadiče domény vaší spravované domény jsou zrušení zřízení a nejsou dostupné v rámci virtuální sítě.
* Přístup ke spravované doméně přes internet (je-li povoleno) Secure LDAP přestane fungovat.
* Všimněte si chyby v ověřování do spravované domény, protokolování virtuální počítače připojené k doméně a propojení prostřednictvím protokolu LDAP nebo LDAPS.
* Zálohy pro vaše spravovaná doména se už přesunete.
* Synchronizace se službou Azure AD se zastaví.
* Vyřešte výstrahu způsobuje vaší spravované domény ve stavu "Pozastaveno" a potom kontaktovat podporu.
* Podpora vaší spravované domény, obnovit jenom v případě, že existuje zálohy, která je menší než 30 dní.

Spravovaná doména zůstane pouze v pozastaveném stavu po dobu 15 dnů. Pokud chcete obnovit spravované domény, Microsoft doporučuje že okamžitě vyřešit kritické výstrahy.


### <a name="deleted-state"></a>Stavu 'Odstraněno'
Spravovaná doména, který zůstane ve stavu "Pozastaveno" po dobu 15 dnů se **odstraněné**.

**Co můžete očekávat:**
* Odstraní se všechny prostředky a zálohy pro spravovanou doménu.
* Nelze obnovit spravované domény a musíte vytvořit nové spravované doméně používat službu Azure AD Domain Services.
* Po odstranění se vám nic neúčtuje pro spravovanou doménu.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Jak víte, pokud je pozastaveno vaše spravovaná doména?
Zobrazí [výstraha](active-directory-ds-troubleshoot-alerts.md) na stránce stavu Azure AD Domain Services na portálu Azure portal, který deklaruje domény pozastaveno. Stav domény také ukazuje "Pozastaveno".


## <a name="restore-a-suspended-domain"></a>Obnovení pozastavené domény
Chcete-li obnovit doménu ve stavu "Pozastaveno", proveďte následující kroky:

1. Přejděte [stránku Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) na portálu Azure portal
2. Klikněte na tlačítko ve spravované doméně.
3. Na levém navigačním panelu klikněte na tlačítko **stavu**.
4. Klikněte na výstrahu. ID výstrahy bude AADDS503 nebo AADDS504, v závislosti na příčině, ve kterém byly pozastaveny.
5. Klikněte na odkaz řešení k dispozici ve výstraze a postupujte podle kroků tuto výstrahu vyřešíte.

Vaše spravovaná doména lze obnovit pouze na datum poslední zálohy. Datum poslední zálohy je zobrazeny na stránce stav vaší spravované domény. Všechny změny, ke kterým došlo po poslední záloze nebude obnoven. Zálohy pro spravovanou doménu se uchovávají po dobu až 30 dnů. Zálohování, které jsou starší než 30 dnů se odstraní.


## <a name="next-steps"></a>Další postup
- [Vyřešit upozornění ve vaší spravované doméně](active-directory-ds-troubleshoot-alerts.md)
- [Přečtěte si víc o službě Azure AD Domain Services](active-directory-ds-overview.md)
- [Kontaktováním produktového týmu](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Kontaktujte nás
Obraťte se na Azure Active Directory Domain Services produktovému týmu a [podělit se o názory, nebo pro podporu](active-directory-ds-contact-us.md).
