---
title: Běžné výstrahy a řešení v Azure AD Domain Services | Microsoft Docs
description: Naučte se řešit běžné výstrahy vygenerované jako součást stavu pro Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 29bd7490ea9a03118f134ba11923ede2075e6fd5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88722581"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Známé problémy: běžné výstrahy a řešení v Azure Active Directory Domain Services

Jako centrální součást identity a ověřování pro aplikace Azure Active Directory Domain Services (Azure služba AD DS) někdy problémy. Pokud narazíte na problémy, budete mít k dispozici několik běžných výstrah a souvisejících kroků pro řešení potíží, které vám pomohou znovu spustit. Pro další pomoc s řešením potíží můžete také [otevřít žádost o podporu Azure][azure-support] .

Tento článek popisuje informace o řešení běžných výstrah v Azure služba AD DS.

## <a name="aadds100-missing-directory"></a>AADDS100: chybí adresář

### <a name="alert-message"></a>Zpráva výstrahy

*Je možné, že se odstranil adresář služby Azure AD přidružený ke spravované doméně. Spravovaná doména již není v podporované konfiguraci. Společnost Microsoft nemůže monitorovat, spravovat, opravovat a synchronizovat vaši spravovanou doménu.*

### <a name="resolution"></a>Řešení

K této chybě obvykle dochází, když se předplatné Azure přesune do nového adresáře služby Azure AD a starý adresář služby Azure AD, který je přidružený k Azure služba AD DS, se odstraní.

Tato chyba je neopravitelná. Pokud chcete tuto výstrahu vyřešit, [odstraňte existující spravovanou doménu](delete-aadds.md) a vytvořte ji znovu v novém adresáři. Pokud máte potíže s odstraněním spravované domény, [otevřete žádost o podporu pro Azure][azure-support] , kde najdete další pomoc při odstraňování potíží.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C je spuštěný v tomto adresáři.

### <a name="alert-message"></a>Zpráva výstrahy

*Azure AD Domain Services nelze povolit v Azure AD B2Cm adresáři.*

### <a name="resolution"></a>Řešení

Azure služba AD DS se automaticky synchronizuje s adresářem Azure AD. Pokud je pro B2C nakonfigurovaný adresář služby Azure AD, Azure služba AD DS nejde nasadit a synchronizovat.

Chcete-li použít Azure služba AD DS, je nutné znovu vytvořit spravovanou doménu v adresáři, který není Azure AD B2C, a to pomocí následujících kroků:

1. [Odstraňte spravovanou doménu](delete-aadds.md) ze stávajícího adresáře služby Azure AD.
1. Vytvořte nový adresář služby Azure AD, který není Azure AD B2C adresářem.
1. [Vytvořte náhradní spravovanou doménu](tutorial-create-instance.md).

Stav spravované domény se automaticky aktualizuje během dvou hodin a výstraha se odstraní.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: adresa je ve veřejném rozsahu IP adres.

### <a name="alert-message"></a>Zpráva výstrahy

*Rozsah IP adres pro virtuální síť, ve které jste povolili Azure AD Domain Services, je ve veřejném rozsahu IP adres. Ve virtuální síti s rozsahem privátních IP adres musí být povolená Azure AD Domain Services. Tato konfigurace má vliv na schopnost Microsoftu monitorovat, spravovat, opravovat a synchronizovat vaši spravovanou doménu.*

### <a name="resolution"></a>Řešení

Než začnete, ujistěte se, že rozumíte [adresním prostorům privátního protokolu IP v4](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

Virtuální počítače ve virtuální síti můžou podávat požadavky na prostředky Azure ve stejném rozsahu IP adres, jaký je nakonfigurovaný pro podsíť. Pokud nakonfigurujete rozsah veřejných IP adres pro podsíť, požadavky směrované do virtuální sítě nemusí dosahovat zamýšlených webových prostředků. Tato konfigurace může vést k nepředvídatelným chybám s Azure služba AD DS.

> [!NOTE]
> Pokud vlastníte rozsah IP adres v Internetu, který je nakonfigurovaný ve vaší virtuální síti, můžete tuto výstrahu ignorovat. Azure AD Domain Services ale s touto konfigurací nejde zavázat do [smlouvy SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) , protože by mohlo vést k nepředvídatelným chybám.

Pokud chcete tuto výstrahu vyřešit, odstraňte existující spravovanou doménu a znovu ji vytvořte ve virtuální síti s rozsahem privátních IP adres. Tento proces je rušivý, protože spravovaná doména není k dispozici a všechny vlastní prostředky, které jste vytvořili jako OU nebo účty služeb, se ztratí.

1. [Odstraňte spravovanou doménu](delete-aadds.md) z adresáře.
1. Chcete-li aktualizovat rozsah IP adres virtuální sítě, vyhledejte a vyberte možnost *virtuální síť* v Azure Portal. Vyberte virtuální síť pro Azure služba AD DS, která má nesprávně nastavený rozsah veřejných IP adres.
1. V části **Nastavení**vyberte *adresní prostor*.
1. Aktualizujte rozsah adres tak, že vyberete stávající rozsah adres, upravíte ho nebo přidáte další rozsah adres. Ujistěte se, že je nový rozsah IP adres v rozsahu privátních IP adres. Až budete připraveni, změny **uložte** .
1. V levém navigačním panelu vyberte **podsítě** .
1. Vyberte podsíť, kterou chcete upravit, nebo vytvořte další podsíť.
1. Aktualizujte nebo zadejte rozsah privátních IP adres a potom změny **uložte** .
1. [Vytvořte náhradní spravovanou doménu](tutorial-create-instance.md). Ujistěte se, že jste si vybrali aktualizovanou podsíť virtuální sítě s rozsahem privátních IP adres.

Stav spravované domény se automaticky aktualizuje během dvou hodin a výstraha se odstraní.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: vaše předplatné Azure se nenašlo.

### <a name="alert-message"></a>Zpráva výstrahy

*Vaše předplatné Azure přidružené k vaší spravované doméně se odstranilo.  Azure AD Domain Services vyžaduje, aby aktivní předplatné pokračovalo ve správném fungování.*

### <a name="resolution"></a>Řešení

Azure služba AD DS vyžaduje aktivní předplatné a nedá se přesunout do jiného předplatného. Pokud se odstraní předplatné Azure, ke kterému byla spravovaná doména přidružená, musíte znovu vytvořit předplatné Azure a spravovanou doménu.

1. [Vytvořte si předplatné Azure](../cost-management-billing/manage/create-subscription.md).
1. [Odstraňte spravovanou doménu](delete-aadds.md) ze stávajícího adresáře služby Azure AD.
1. [Vytvořte náhradní spravovanou doménu](tutorial-create-instance.md).

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: vaše předplatné Azure je zakázané.

### <a name="alert-message"></a>Zpráva výstrahy

*Vaše předplatné Azure přidružené k vaší spravované doméně není aktivní.  Azure AD Domain Services vyžaduje, aby aktivní předplatné pokračovalo ve správném fungování.*

### <a name="resolution"></a>Řešení

Azure služba AD DS vyžaduje aktivní předplatné. Pokud předplatné Azure, ke kterému je spravovaná doména přidružená, není aktivní, musíte ho obnovit, aby se předplatné znovu aktivovalo.

1. [Prodlužte si platnost předplatného Azure](../cost-management-billing/manage/subscription-disabled.md).
2. Po obnovení odběru vám oznámení služby Azure služba AD DS umožní znovu povolit spravovanou doménu.

Pokud je spravovaná doména znovu povolena, stav spravované domény se automaticky aktualizuje během dvou hodin a výstraha se odstraní.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: odběr přesunutých adresářů

### <a name="alert-message"></a>Zpráva výstrahy

*Předplatné používané službou Azure AD Domain Services bylo přesunuto do jiného adresáře. Azure AD Domain Services musí mít ve stejném adresáři aktivní předplatné, aby správně fungovalo.*

### <a name="resolution"></a>Řešení

Azure služba AD DS vyžaduje aktivní předplatné a nedá se přesunout do jiného předplatného. Pokud je přesunuté předplatné Azure, ke kterému byla spravovaná doména přidružená, přesuňte předplatné zpátky do předchozího adresáře nebo z existujícího adresáře [odstraňte spravovanou doménu](delete-aadds.md) a [ve vybraném předplatném vytvořte náhradní spravovanou doménu](tutorial-create-instance.md).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: prostředky pro spravovanou doménu se nenašly.

### <a name="alert-message"></a>Zpráva výstrahy

*Prostředek, který se používá pro spravovanou doménu, byl odstraněn. Tento prostředek je nutný, aby Azure AD Domain Services správně fungoval.*

### <a name="resolution"></a>Řešení

Azure služba AD DS vytváří další prostředky pro správné fungování, jako jsou veřejné IP adresy, virtuální síťová rozhraní a nástroj pro vyrovnávání zatížení. Pokud se některý z těchto prostředků odstraní, spravovaná doména je v nepodporovaném stavu a zabraňuje správě domény. Další informace o těchto prostředcích najdete v tématu [síťové prostředky používané službou Azure služba AD DS](network-considerations.md#network-resources-used-by-azure-ad-ds).

Tato výstraha se vygeneruje, když se odstraní jeden z požadovaných prostředků. Pokud se prostředek odstranil před méně než 4 hodinami, existuje možnost, že platforma Azure může automaticky znovu vytvořit odstraněný prostředek. Následující kroky popisují, jak kontrolovat stav a časové razítko odstranění prostředku:

1. V Azure Portal vyhledejte a vyberte **Domain Services**. Vyberte spravovanou doménu, například *aaddscontoso.com*.
1. V levém navigačním panelu vyberte **stav**.
1. Na stránce stav vyberte výstrahu s ID *AADDS109*.
1. Výstraha má časové razítko, které bylo poprvé nalezeno. Pokud je toto časové razítko před méně než 4 hodinami, může být platforma Azure schopna automaticky znovu vytvořit prostředek a výstrahu vyřešit sám.

    Pokud je výstraha starší než 4 hodiny stará, je spravovaná doména v neobnovitelné stavu. [Odstraňte spravovanou doménu](delete-aadds.md) a pak [vytvořte náhradní spravovanou doménu](tutorial-create-instance.md).

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: podsíť přidružená ke spravované doméně je plná.

### <a name="alert-message"></a>Zpráva výstrahy

*Podsíť vybraná pro nasazení Azure AD Domain Services je plná a nemá místo pro další řadič domény, který je potřeba vytvořit.*

### <a name="resolution"></a>Řešení

Podsíť virtuální sítě pro Azure služba AD DS potřebuje dostatek IP adres pro automaticky vytvořené prostředky. Tento adresní prostor IP adres zahrnuje nutnost vytvořit náhradní prostředky, pokud dojde k události údržby. K minimalizaci rizika vycházejícího z dostupných IP adres nesaďte do stejné podsítě virtuální sítě, jako je spravovaná doména, další prostředky, například vaše vlastní virtuální počítače.

Tato chyba je neopravitelná. Pokud chcete tuto výstrahu vyřešit, [odstraňte existující spravovanou doménu](delete-aadds.md) a vytvořte ji znovu. Pokud máte potíže s odstraněním spravované domény, [otevřete žádost o podporu pro Azure][azure-support] , kde najdete další pomoc při odstraňování potíží.

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: objekt služby je neautorizovaný.

### <a name="alert-message"></a>Zpráva výstrahy

*Instanční objekt, který Azure AD Domain Services používá ke službě vaší domény, nemá oprávnění ke správě prostředků v rámci předplatného Azure. Instanční objekt musí mít oprávnění k provozu vaší spravované domény.*

### <a name="resolution"></a>Řešení

Některé automaticky generované objekty služby se používají ke správě a vytváření prostředků pro spravovanou doménu. Pokud dojde ke změně oprávnění pro přístup k některému z těchto instančních objektů, doména nemůže správně spravovat prostředky. Následující kroky ukazují, jak pochopit a pak udělit přístupová oprávnění k instančnímu objektu:

1. Přečtěte si o [řízení přístupu na základě role a o tom, jak udělit přístup k aplikacím v Azure Portal](../role-based-access-control/role-assignments-portal.md).
2. Zkontrolujte přístup, který instanční objekt s ID *abba844e-bc0e-44b0-947a-dc74e5d09022* má, a udělte přístup, který byl zamítnut v dřívější datum.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: ve spravované doméně není dostatečná IP adresa.

### <a name="alert-message"></a>Zpráva výstrahy

*Zjistili jsme, že podsíť virtuální sítě v této doméně nemusí mít dost IP adres. Azure AD Domain Services potřebuje aspoň dvě dostupné IP adresy v podsíti, ve které je povolený. V rámci podsítě doporučujeme mít minimálně 3-5 náhradních IP adres. K této chybě mohlo dojít, pokud jsou v rámci podsítě nasazeny další virtuální počítače, čímž dojde k vyčerpání počtu dostupných IP adres nebo omezení počtu dostupných IP adres v podsíti.*

### <a name="resolution"></a>Řešení

Podsíť virtuální sítě pro Azure služba AD DS potřebuje dostatek IP adres pro automaticky vytvořené prostředky. Tento adresní prostor IP adres zahrnuje nutnost vytvořit náhradní prostředky, pokud dojde k události údržby. K minimalizaci rizika vycházejícího z dostupných IP adres nesaďte do stejné podsítě virtuální sítě, jako je spravovaná doména, další prostředky, například vaše vlastní virtuální počítače.

Pokud chcete tuto výstrahu vyřešit, odstraňte existující spravovanou doménu a znovu ji vytvořte ve virtuální síti s velkým množstvím rozsahu IP adres. Tento proces je rušivý, protože spravovaná doména není k dispozici a všechny vlastní prostředky, které jste vytvořili jako OU nebo účty služeb, se ztratí.

1. [Odstraňte spravovanou doménu](delete-aadds.md) z adresáře.
1. Chcete-li aktualizovat rozsah IP adres virtuální sítě, vyhledejte a vyberte možnost *virtuální síť* v Azure Portal. Vyberte virtuální síť pro spravovanou doménu, která má malý rozsah IP adres.
1. V části **Nastavení**vyberte *adresní prostor*.
1. Aktualizujte rozsah adres tak, že vyberete stávající rozsah adres, upravíte ho nebo přidáte další rozsah adres. Zajistěte, aby byl nový rozsah IP adres dostatečně velký pro rozsah podsítí spravované domény. Až budete připraveni, změny **uložte** .
1. V levém navigačním panelu vyberte **podsítě** .
1. Vyberte podsíť, kterou chcete upravit, nebo vytvořte další podsíť.
1. Aktualizujte nebo zadejte velký rozsah IP adres a potom změny **uložte** .
1. [Vytvořte náhradní spravovanou doménu](tutorial-create-instance.md). Ujistěte se, že jste si vybrali aktualizovanou podsíť virtuální sítě s velkým množstvím rozsahu IP adres.

Stav spravované domény se automaticky aktualizuje během dvou hodin a výstraha se odstraní.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: prostředky jsou neobnovitelné.

### <a name="alert-message"></a>Zpráva výstrahy

*Prostředky používané Azure AD Domain Services byly zjištěny v neočekávaném stavu a nelze je obnovit.*

### <a name="resolution"></a>Řešení

Tato chyba je neopravitelná. Pokud chcete tuto výstrahu vyřešit, [odstraňte existující spravovanou doménu](delete-aadds.md) a vytvořte ji znovu. Pokud máte potíže s odstraněním spravované domény, [otevřete žádost o podporu pro Azure][azure-support] , kde najdete další pomoc při odstraňování potíží.

## <a name="aadds114-subnet-invalid"></a>AADDS114: Neplatná podsíť

### <a name="alert-message"></a>Zpráva výstrahy

*Podsíť vybraná pro nasazení Azure AD Domain Services není platná a nedá se použít.*

### <a name="resolution"></a>Řešení

Tato chyba je neopravitelná. Pokud chcete tuto výstrahu vyřešit, [odstraňte existující spravovanou doménu](delete-aadds.md) a vytvořte ji znovu. Pokud máte potíže s odstraněním spravované domény, [otevřete žádost o podporu pro Azure][azure-support] , kde najdete další pomoc při odstraňování potíží.

## <a name="aadds115-resources-are-locked"></a>AADDS115: prostředky jsou uzamčené.

### <a name="alert-message"></a>Zpráva výstrahy

*Jeden nebo více síťových prostředků používaných spravovanou doménou nelze provozovat, protože cílový obor byl uzamčen.*

### <a name="resolution"></a>Řešení

K tomu, aby se zabránilo změně nebo odstranění, se můžou použít zámky prostředků na prostředky Azure. Protože Azure služba AD DS je spravovaná služba, platforma Azure potřebuje možnost provádět změny konfigurace. Pokud se pro některé součásti Azure služba AD DS používá zámek prostředků, platforma Azure nemůže provádět úlohy správy.

Pokud chcete vyhledat zámky prostředků na součástech Azure služba AD DS a odebrat je, proveďte následující kroky:

1. U každé síťové součásti spravované domény, jako je třeba virtuální síť, síťové rozhraní nebo veřejná IP adresa, se podívejte na protokoly operací v Azure Portal. Tyto protokoly operací by měly ukazovat, proč selže operace a kde se používá zámek prostředku.
1. Vyberte prostředek, na kterém je zámek použit, a pak v části **zámky**vyberte a odeberte zámky.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: prostředky jsou nepoužitelné.

### <a name="alert-message"></a>Zpráva výstrahy

*Jeden nebo více síťových prostředků používaných spravovanou doménou nelze provozovat v důsledku omezení zásad.*

### <a name="resolution"></a>Řešení

Zásady se používají u prostředků Azure a skupin prostředků, které určují, jaké akce konfigurace jsou povolené. Protože Azure služba AD DS je spravovaná služba, platforma Azure potřebuje možnost provádět změny konfigurace. Pokud se na některých součástech Azure služba AD DS aplikuje zásada, platforma Azure nemusí být schopná provádět úlohy správy.

Pokud chcete vyhledat použité zásady v součástech Azure služba AD DS a aktualizovat je, proveďte následující kroky:

1. U každé síťové součásti spravované domény, jako je například virtuální síť, síťová karta nebo veřejná IP adresa, se podívejte na protokoly operací v Azure Portal. Tyto protokoly operací by měly ukazovat, proč selže operace a kde se uplatní omezující zásada.
1. Vyberte prostředek, u kterého se zásada používá, a pak v části **zásady**vyberte a upravte zásadu tak, aby byla méně omezující.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: synchronizace se ještě nedokončila.

### <a name="alert-message"></a>Zpráva výstrahy

*Tato spravovaná doména byla naposledy synchronizována se službou Azure AD dne [Date]. Uživatelé se nemusí přihlašovat ve spravované doméně nebo členství ve skupině se nemusí synchronizovat se službou Azure AD.*

### <a name="resolution"></a>Řešení

[Podívejte se na stav služby Azure služba AD DS](check-health.md) pro všechny výstrahy, které indikují problémy v konfiguraci spravované domény. Problémy se síťovou konfigurací můžou synchronizaci z Azure AD zablokovat. Pokud můžete vyřešit výstrahy indikující problém s konfigurací, počkejte dvě hodiny a vraťte se zpátky, abyste viděli, jestli se synchronizace úspěšně dokončila.

Následující běžné důvody způsobují zastavení synchronizace ve spravované doméně:

* Požadované síťové připojení je blokované. Další informace o tom, jak ve službě Azure Virtual Network vyhledat problémy a co je potřeba, najdete v tématu [řešení potíží se skupinami zabezpečení sítě](alert-nsg.md) a [požadavky na síť pro Azure služba AD DS](network-considerations.md).
*  Při nasazení spravované domény se synchronizace hesel nevytvořila nebo se úspěšně nedokončila. Synchronizaci hesel můžete nastavit [jenom pro cloudové uživatele](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) nebo [hybridní uživatele z Prem](tutorial-configure-password-hash-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: záloha se neuskutečnila během chvilky.

### <a name="alert-message"></a>Zpráva výstrahy

*Tato spravovaná doména byla naposledy zálohována [Date].*

### <a name="resolution"></a>Řešení

[Podívejte se na stav služby Azure služba AD DS](check-health.md) pro výstrahy, které indikují problémy v konfiguraci spravované domény. Problémy se síťovou konfigurací můžou na platformě Azure zablokovat úspěšné pořízení záloh. Pokud můžete vyřešit výstrahy indikující problém s konfigurací, počkejte dvě hodiny a vraťte se zpátky, abyste viděli, jestli se synchronizace úspěšně dokončila.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: pozastavení z důvodu zakázaného předplatného

### <a name="alert-message"></a>Zpráva výstrahy

*Spravovaná doména je pozastavená, protože předplatné Azure přidružené k doméně není aktivní.*

### <a name="resolution"></a>Řešení

> [!WARNING]
> Pokud je spravovaná doména pozastavena po delší dobu, dojde k jejímu nebezpečnému odstranění. Vyřešte důvod pro pozastavení co nejrychleji. Další informace najdete v tématu [pochopení stavů pozastavených pro Azure služba AD DS](suspension.md).

Azure služba AD DS vyžaduje aktivní předplatné. Pokud předplatné Azure, ke kterému je spravovaná doména přidružená, není aktivní, musíte ho obnovit, aby se předplatné znovu aktivovalo.

1. [Prodlužte si platnost předplatného Azure](../cost-management-billing/manage/subscription-disabled.md).
2. Po obnovení odběru vám oznámení služby Azure služba AD DS umožní znovu povolit spravovanou doménu.

Pokud je spravovaná doména znovu povolena, stav spravované domény se automaticky aktualizuje během dvou hodin a výstraha se odstraní.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: pozastavení z důvodu neplatné konfigurace

### <a name="alert-message"></a>Zpráva výstrahy

*Spravovaná doména je pozastavena z důvodu neplatné konfigurace. Služba nemohla po dlouhou dobu spravovat, opravovat ani aktualizovat řadiče domény pro spravovanou doménu.*

### <a name="resolution"></a>Řešení

> [!WARNING]
> Pokud je spravovaná doména pozastavena po delší dobu, dojde k jejímu nebezpečnému odstranění. Vyřešte důvod pro pozastavení co nejrychleji. Další informace najdete v tématu [pochopení stavů pozastavených pro Azure služba AD DS](suspension.md).

[Podívejte se na stav služby Azure služba AD DS](check-health.md) pro výstrahy, které indikují problémy v konfiguraci spravované domény. Pokud je možné vyřešit výstrahy indikující problém s konfigurací, počkejte dvě hodiny a vraťte se zpět, abyste viděli, jestli se synchronizace dokončila. Až budete připraveni, [otevřete žádost o podporu Azure][azure-support] pro opětovné povolení spravované domény.

## <a name="next-steps"></a>Další kroky

Pokud stále máte problémy, [otevřete žádost o podporu Azure][azure-support] , kde najdete další pomoc při řešení potíží.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md