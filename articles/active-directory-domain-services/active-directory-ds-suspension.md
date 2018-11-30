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
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: ergreenl
ms.openlocfilehash: 31e7307e427b175eb7424ecaee380bd90ce8150e
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443399"
---
# <a name="suspended-domains"></a>Pozastavené domén
Když Azure Active Directory Domain Services (Azure AD DS) není schopen služby spravované domény pro dlouhou dobu, uloží je do pozastaveného stavu spravované domény. Tento článek vysvětluje, proč jsou pozastavené spravovaných domén a způsobu řešení pozastavené domény.


## <a name="states-your-managed-domain-can-be-in"></a>Stavy vaše spravovaná doména může být v

![Časová osa pozastavené domény](media\active-directory-domain-services-suspension\suspension-timeline.PNG)

Předchozí obrázek znázorňuje všechny možné stavy spravované domény služby Azure AD DS můžou být v.

### <a name="running-state"></a>Stavu "Spuštěno"
Spravované domény, který je správně nakonfigurované a provozní pravidelně se **systémem** stavu.

**Co můžete očekávat**
* Microsoft může pravidelně sledovat stav vaší spravované domény.
* Řadiče domény pro vaše spravovaná doména se opravují se a pravidelně aktualizuje.
* Změny z Azure Active Directory jsou pravidelně synchronizována do spravované domény.
* Pravidelné zálohy jsou prováděny vaší spravované domény.


### <a name="needs-attention-state"></a>Stav "Vyžaduje pozornost"
Spravovaná doména se v **potřebuje pozornost** stavu, pokud jeden nebo více problémů požadovat, aby správce akci. Stránka stavu vaší spravované domény obsahuje jedno nebo několik upozornění v tomto stavu.

Například pokud omezující skupiny zabezpečení sítě nakonfigurované pro vaši virtuální síť, Microsoft nemusí být možné aktualizovat a monitorování vaší spravované domény. Tato neplatná konfigurace aktivuje výstrahu, která se zařadí do stavu "Needs Attention" vaší spravované domény.

Každé upozornění obsahuje sadu kroků pro řešení. Některé výstrahy jsou přechodné a automaticky se vyřešila pomocí služby. Některé výstrahy můžete vyřešit podle pokynů v odpovídající postup řešení této výstrahy. Pro některé kritické výstrahy budete muset obrátit na podporu Microsoftu o rozlišení.

Další informace najdete v tématu [postupy řešení výstrah ve spravované doméně](active-directory-ds-troubleshoot-alerts.md).

**Co můžete očekávat**

V některých případech (například pokud máte konfigurace neplatný sítě) může nedostupný řadiče domény vaší spravované domény. Pokud vaše spravovaná doména je ve stavu "Potřebuje pozornost", společnost Microsoft nemůže zaručit, že bude monitorovat, opravit, aktualizovat nebo zálohovanou v pravidelných intervalech.

* Spravovaná doména je v pořádku a průběžný stav monitorování může přestat až do vyřešení výstrahy.
* Řadiče domény pro spravovanou doménu nelze opravit ani aktualizovat.
* Změny z Azure Active Directory nemusí synchronizovány do spravované domény.
* Může být uvedený zálohování vaší spravované domény, pokud je to možné.
* Pokud vyřešit výstrahy, které mají vliv na vaše spravovaná doména může být schopni obnovit do stavu "Spuštěno".
* Kritické výstrahy se spouštějí pro problémy s konfigurací, kde společnost Microsoft se nám kontaktovat řadiče domény. Pokud tyto výstrahy nejsou přeložit do 15 dnů, vaši spravovanou doménu přejde do stavu "Pozastaveno".


### <a name="the-suspended-state"></a>Stavu "Pozastaveno"
Spravovaná doména je umístěn **pozastaveno** stavu z následujících důvodů:

* Jeden nebo více kritické výstrahy nevyjasněné během 15 dnů. Kritické výstrahy může být způsobeno chybné konfiguraci, která zablokuje přístup k prostředkům, které jsou potřeba pomocí služby Azure AD DS.
    * Například výstraha [AADDS104: Chyba sítě](active-directory-ds-troubleshoot-nsg.md) obsahuje nevyřešený po dobu více než 15 dnů ve spravované doméně.
* Fakturační problému s vaším předplatným Azure, nebo vypršela platnost vašeho předplatného Azure.

Spravované domény jsou pozastavené, když Microsoft nemůže ke správě, monitorování, oprava nebo zálohovat i doménu průběžně.

**Co můžete očekávat**
* Řadiče domény vaší spravované domény jsou zrušení zřízení a nejsou dostupné v rámci virtuální sítě.
* Přístup ke spravované doméně přes internet (Pokud je povolená) Secure LDAP přestane fungovat.
* Všimněte si selhání v ověřování do spravované domény, protokolování virtuální počítače připojené k doméně nebo připojení prostřednictvím protokolu LDAP nebo LDAPS.
* Zálohy pro vaše spravovaná doména se už přesunete.
* Synchronizace se službou Azure AD se zastaví.

Po vyřešení výstrahy vaší spravované domény přejde do stavu "Pozastaveno". Musíte se obrátit na podporu.
Podpora může obnovit vaší spravované domény, ale existuje pouze v případě zálohy, která je menší než 30 dní.

Spravovaná doména zůstane pouze v pozastaveném stavu po dobu 15 dnů. Pokud chcete obnovit spravované domény, společnost Microsoft doporučuje okamžitě vyřešit kritické výstrahy.


### <a name="deleted-state"></a>Stavu "Odstraněno"
Spravovaná doména, který zůstane ve stavu "Pozastaveno" po dobu 15 dnů se **odstraněné**.

**Co můžete očekávat**
* Odstraní se všechny prostředky a zálohy pro spravovanou doménu.
* Nelze obnovit spravované domény a muset vytvořit nové spravované domény pro použití služby Azure AD DS.
* Po odstranění, se vám nic neúčtuje pro spravovanou doménu.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Jak víte, pokud je pozastaveno vaše spravovaná doména?
Zobrazí [výstraha](active-directory-ds-troubleshoot-alerts.md) na stránce služby Azure AD DS Health na portálu Azure portal, který deklaruje, že doménu je pozastaveno. Stav domény také ukazuje "Pozastaveno".


## <a name="restore-a-suspended-domain"></a>Obnovení pozastavené domény
Chcete-li obnovit doméně, která je ve stavu "Pozastaveno", proveďte následující kroky:

1. Přejděte [stránky Azure Active Directory Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) na webu Azure Portal.
2. Vyberte spravovanou doménu.
3. Na levém panelu, vyberte **stavu**.
4. Vyberte příslušnou výstrahu. ID výstrahy bude AADDS503 nebo AADDS504, v závislosti na příčině, ve kterém byly pozastaveny.
5. Vyberte řešení odkaz, který je k dispozici ve výstraze. Postupujte podle pokynů tuto výstrahu vyřešíte.

Vaše spravovaná doména lze obnovit pouze na datum poslední zálohy. Datum poslední zálohy je zobrazeny na stránce stav vaší spravované domény. Všechny změny, ke kterým došlo po poslední záloze nebude obnoven. Zálohy pro spravovanou doménu se uchovávají po dobu až 30 dnů. Zálohování, které jsou starší než 30 dnů se odstraní.


## <a name="next-steps"></a>Další postup
- [Vyřešte výstrahy pro spravované domény](active-directory-ds-troubleshoot-alerts.md)
- [Další informace o Azure Active Directory Domain Services](active-directory-ds-overview.md)
- [Kontaktováním produktového týmu](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Kontaktujte nás
Obraťte se na Azure Active Directory Domain Services produktovému týmu a [podělit se o názory, nebo pro podporu](active-directory-ds-contact-us.md).
