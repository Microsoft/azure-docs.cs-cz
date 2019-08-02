---
title: 'Azure Active Directory Domain Services: Pozastavené domény | Microsoft Docs'
description: Pozastavení a odstranění spravované domény
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 781a81589032c290cef7342e7210ee36f388b22a
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233991"
---
# <a name="understand-the-suspended-states-for-an-azure-active-directory-domain-services-managed-domain"></a>Pochopení stavů pozastavených pro Azure Active Directory Domain Services spravovanou doménu

Když Azure Active Directory Domain Services (Azure služba AD DS) nemůže po dlouhou dobu obsluhovat spravovanou doménu, převede spravovanou doménu do pozastaveného stavu. Tento článek vysvětluje, proč jsou spravované domény pozastavené a jak napravit pozastavenou doménu.


## <a name="states-your-managed-domain-can-be-in"></a>Uvádí, že vaše spravovaná doména může být v

![Časová osa pozastavené domény](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Předchozí obrázek znázorňuje možné stavy, ve kterých může být spravovaná doména Azure služba AD DS.

### <a name="running-state"></a>Stav spuštění
Spravovaná doména, která je správně nakonfigurovaná a pravidelně funguje, je **** ve stavu spuštěno.

**Co očekávat**
* Společnost Microsoft může pravidelně monitorovat stav vaší spravované domény.
* Řadiče domény pro spravovanou doménu jsou opravené a pravidelně se aktualizují.
* Změny z Azure Active Directory jsou pravidelně synchronizovány do vaší spravované domény.
* Pro spravovanou doménu jsou pořízeny pravidelné zálohy.


### <a name="needs-attention-state"></a>Stav "vyžaduje pozornost"
Pokud jeden nebo více problémů vyžaduje, aby správce mohl provést akci, je spravovaná doména ve stavu **vyžaduje pozornost** . Na stránce stav ve vaší spravované doméně je uveden minimálně jeden výstraha v tomto stavu.

Pokud jste například nakonfigurovali omezující NSG pro virtuální síť, společnost Microsoft možná nebude moci aktualizovat a monitorovat vaši spravovanou doménu. Tato neplatná konfigurace aktivuje výstrahu, která vaše spravovaná doména vloží do stavu "potřebuje pozornost".

Každé upozornění má sadu kroků řešení. Některé výstrahy jsou přechodný a automaticky je vyřeší služba. Některé další výstrahy můžete vyřešit podle pokynů v odpovídajících krocích řešení pro tuto výstrahu. U některých kritických výstrah musíte kontaktovat podporu Microsoftu a získat tak řešení.

Další informace najdete v tématu [řešení potíží s výstrahami ve spravované doméně](troubleshoot-alerts.md).

**Co očekávat**

V některých případech (například pokud máte neplatnou konfiguraci sítě) můžou být řadiče domény pro spravovanou doménu nedosažitelné. Pokud je vaše spravovaná doména ve stavu "potřebuje pozornost", společnost Microsoft nemůže zaručit, že bude monitorována, opravena, aktualizována nebo zálohována pravidelně.

* Vaše spravovaná doména je ve stavu není v pořádku a průběžné monitorování stavu může skončit až do vyřešení výstrahy.
* Řadiče domény pro spravovanou doménu nejde opravit ani aktualizovat.
* Změny z Azure Active Directory pravděpodobně nebudou synchronizovány do vaší spravované domény.
* Pokud je to možné, mohou být provedeny zálohy spravované domény.
* Pokud vyřešíte výstrahy, které mají vliv na spravovanou doménu, je možné, že ji budete moci obnovit do stavu spuštěno.
* Pro problémy s konfigurací, kdy se Microsoft nemůže spojit s řadiči domény, se aktivují kritické výstrahy. Pokud se tyto výstrahy během 15 dnů nevyřeší, spravovaná doména se umístí do stavu "pozastaveno".


### <a name="the-suspended-state"></a>Stav pozastaveno
Spravovaná doména je v pozastaveném **** stavu z následujících důvodů:

* Jeden nebo více kritických výstrah nebylo v 15 dnech vyřešeno. Kritické výstrahy můžou být způsobené chybnou konfigurací, které blokují přístup k prostředkům, které vyžaduje Azure služba AD DS.
    * Například výstraha [AADDS104: Chyba](alert-nsg.md) sítě byla nevyřešena po dobu více než 15 dní ve spravované doméně.
* Došlo k potížím s fakturací v rámci vašeho předplatného Azure nebo vypršela platnost vašeho předplatného Azure.

Spravované domény jsou pozastavené, když společnost Microsoft nemůže pravidelně spravovat, monitorovat, opravovat nebo zálohovat doménu.

**Co očekávat**
* Řadiče domény pro spravovanou doménu jsou nezřízené a nejsou dostupné v rámci virtuální sítě.
* Protokol Secure LDAP přístup ke spravované doméně přes Internet (Pokud je povolený) přestane fungovat.
* Všimnete si selhání při ověřování ve spravované doméně, přihlašování k virtuálním počítačům připojeným k doméně nebo připojení přes LDAP/LDAP.
* Zálohy pro spravovanou doménu již nejsou přijímány.
* Synchronizace s Azure AD se zastaví.

Po vyřešení výstrahy přejde vaše spravovaná doména do stavu "pozastaveno". Pak musíte kontaktovat podporu.
Podpora může obnovit spravovanou doménu, ale jenom v případě, že existuje záloha, která je starší než 30 dní.

Spravovaná doména zůstane jenom v pozastaveném stavu po dobu 15 dnů. Aby bylo možné obnovit spravovanou doménu, společnost Microsoft doporučuje, abyste okamžitě vyřešili kritické výstrahy.


### <a name="deleted-state"></a>Stav odstraněno
Spravovaná doména, která zůstane ve stavu "pozastaveno" po dobu 15 dní, se **odstraní**.

**Co očekávat**
* Odstraní se všechny prostředky a zálohy pro spravovanou doménu.
* Nemůžete obnovit spravovanou doménu a potřebujete vytvořit novou spravovanou doménu pro použití Azure služba AD DS.
* Po odstranění se vám nebude účtovat spravovaná doména.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Jak poznáte, jestli je vaše spravovaná doména pozastavená?
Na stránce Stav [](troubleshoot-alerts.md) služby Azure služba AD DS v Azure Portal se zobrazí výstraha, která deklaruje, že je doména pozastavena. Stav domény také zobrazuje "pozastaveno".


## <a name="restore-a-suspended-domain"></a>Obnovení pozastavené domény
Chcete-li obnovit doménu, která je ve stavu "pozastaveno", proveďte následující kroky:

1. Přejít na [stránku Azure Active Directory Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) v Azure Portal.
2. Vyberte spravovanou doménu.
3. Na levém panelu vyberte **stav**.
4. Vyberte výstrahu. V závislosti na příčině pozastavení bude ID výstrahy buď AADDS503, nebo AADDS504.
5. Vyberte odkaz řešení, který je uvedený v upozornění. Pak postupujte podle kroků a vyřešte výstrahu.

Vaše spravovaná doména může být obnovena pouze do data poslední zálohy. Datum poslední zálohy se zobrazí na stránce stav vaší spravované domény. Jakékoli změny, ke kterým došlo po poslední záloze, se neobnoví. Zálohy spravované domény jsou uložené po dobu až 30 dnů. Zálohy, které jsou starší než 30 dní, se odstraní.


## <a name="next-steps"></a>Další postup
- [Řešení výstrah pro spravovanou doménu](troubleshoot-alerts.md)
- [Přečtěte si další informace o Azure Active Directory Domain Services](overview.md)
- [Kontaktovat produktový tým](contact-us.md)

## <a name="contact-us"></a>Kontaktujte nás
Kontaktujte tým Azure Active Directory Domain Services produktů, abyste mohli [sdílet zpětnou vazbu nebo podporu](contact-us.md).
