---
title: Běžné výstrahy a řešení ve službě Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak vyřešit běžné výstrahy generované jako součást stavu služby Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: c83caf31e25ae2212ed120e77e017ac3849898e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612908"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Známé problémy: Běžné výstrahy a řešení ve službě Azure Active Directory Domain Services

Jako centrální součást identity a ověřování pro aplikace, Azure Active Directory Domain Services (Azure AD DS) má někdy problémy. Pokud narazíte na problémy, existují některé běžné výstrahy a související kroky řešení potíží, které vám pomohou znovu spustit věci. Kdykoli můžete také [otevřít žádost o podporu Azure][azure-support] pro další pomoc při řešení potíží.

Tento článek obsahuje informace o řešení potíží pro běžné výstrahy ve službě Azure AD DS.

## <a name="aadds100-missing-directory"></a>AADDS100: Chybějící adresář

### <a name="alert-message"></a>Výstražná zpráva

*Adresář Azure AD přidružený k vaší spravované doméně byl pravděpodobně odstraněn. Spravovaná doména již není v podporované konfiguraci. Společnost Microsoft nemůže sledovat, spravovat, opravovat a synchronizovat spravovanou doménu.*

### <a name="resolution"></a>Řešení

Tato chyba je obvykle způsobena, když se předplatné Azure přesune do nového adresáře Azure AD a odstraní se starý adresář Azure AD, který je přidružený k Azure AD DS.

Tato chyba je neopravitelná. Chcete-li výstrahu vyřešit, [odstraňte stávající spravovanou doménu Služby Azure AD DS](delete-aadds.md) a znovu ji vytvořte v novém adresáři. Pokud máte potíže s odstraněním spravované domény Azure AD DS, [otevřete žádost o podporu Azure][azure-support] pro další pomoc při řešení potíží.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: V tomto adresáři běží Azure AD B2C

### <a name="alert-message"></a>Výstražná zpráva

*Služby Azure AD Domain Services nelze povolit v adresáři Azure AD B2C.*

### <a name="resolution"></a>Řešení

Azure AD DS se automaticky synchronizuje s adresářem Azure AD. Pokud je adresář Azure AD nakonfigurovaný pro B2C, Azure AD DS nelze nasadit a synchronizovat.

Chcete-li používat Azure AD DS, musíte znovu vytvořit spravovanou doménu v adresáři než Azure AD B2C pomocí následujících kroků:

1. [Odstraňte spravovanou doménu Azure AD DS](delete-aadds.md) ze svého stávajícího adresáře Azure AD.
1. Vytvořte nový adresář Azure AD, který není adresář Azure AD B2C.
1. [Vytvořte náhradní spravovanou doménu Azure AD DS](tutorial-create-instance.md).

Stav spravované domény Azure AD DS se automaticky aktualizuje do dvou hodin a odebere výstrahu.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Adresa je ve veřejném rozsahu IP adres.

### <a name="alert-message"></a>Výstražná zpráva

*Rozsah IP adres pro virtuální síť, ve které jste povolili služby Azure AD Domain Services, je ve veřejném rozsahu IP adres. Služby Azure AD Domain Services musí být povolené ve virtuální síti s rozsahem privátních IP adres. Tato konfigurace má vliv na schopnost společnosti Microsoft monitorovat, spravovat, opravovat a synchronizovat spravovanou doménu.*

### <a name="resolution"></a>Řešení

Než začnete, ujistěte se, že rozumíte [privátním adresním prostorům IP v4](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

Uvnitř virtuální sítě virtuální počítače můžete provádět požadavky na prostředky Azure ve stejném rozsahu IP adres, jak je nakonfigurované pro podsíť. Pokud nakonfigurujete rozsah veřejných IP adres pro podsíť, požadavky směrované v rámci virtuální sítě nemusí dosáhnout zamýšlených webových prostředků. Tato konfigurace může vést k nepředvídatelným chybám s Azure AD DS.

> [!NOTE]
> Pokud vlastníte rozsah IP adres v síti Internet, který je nakonfigurován ve vaší virtuální síti, tato výstraha může být ignorována. Služby Domény Azure AD však nelze potvrdit [do SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] s touto konfigurací, protože může vést k nepředvídatelným chybám.

Chcete-li tuto výstrahu vyřešit, odstraňte stávající spravovanou doménu Služby Azure AD DS a znovu ji vytvořte ve virtuální síti s rozsahem privátních IP adres. Tento proces je rušivý, protože spravovaná doména Azure AD DS není dostupná a všechny vlastní prostředky, které jste vytvořili, jako jsou hlavní sady nebo účty služeb, se ztratí.

1. [Odstraňte spravovanou doménu Azure AD DS](delete-aadds.md) z adresáře.
1. Pokud chcete aktualizovat rozsah IP adres virtuální sítě, vyhledejte a vyberte *Virtuální síť* na webu Azure Portal. Vyberte virtuální síť pro Azure AD DS, která nesprávně má nastaven rozsah veřejných IP adres.
1. V části **Nastavení**vyberte *Možnost Adresní prostor*.
1. Aktualizujte rozsah adres výběrem existujícího rozsahu adres a jeho úpravou nebo přidáním dalšího rozsahu adres. Ujistěte se, že nový rozsah IP adres je v soukromém rozsahu IP adres. Až budete připraveni, **uložte** změny.
1. V levém navigačním **panelu vyberte podsítě.**
1. Vyberte podsíť, kterou chcete upravit, nebo vytvořte další podsíť.
1. Aktualizujte nebo zadejte rozsah privátních IP adres **a uložte** změny.
1. [Vytvořte náhradní spravovanou doménu Azure AD DS](tutorial-create-instance.md). Ujistěte se, že jste vybrali aktualizovanou podsíť virtuální sítě s rozsahem privátních IP adres.

Stav spravované domény Azure AD DS se automaticky aktualizuje do dvou hodin a odebere výstrahu.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Vaše předplatné Azure se nenašlo

### <a name="alert-message"></a>Výstražná zpráva

*Vaše předplatné Azure přidružené k vaší spravované doméně bylo odstraněno.  Služby Azure AD Domain Services vyžaduje aktivní předplatné, aby mohly nadále správně fungovat.*

### <a name="resolution"></a>Řešení

Azure AD DS vyžaduje aktivní předplatné a nelze přesunout do jiného předplatného. Pokud se odstraní předplatné Azure AD DS spravované k asociované, musíte znovu vytvořit předplatné Azure a spravovanou doménu Azure AD DS.

1. [Vytvořte předplatné Azure](../cost-management-billing/manage/create-subscription.md).
1. [Odstraňte spravovanou doménu Azure AD DS](delete-aadds.md) ze svého stávajícího adresáře Azure AD.
1. [Vytvořte náhradní spravovanou doménu Azure AD DS](tutorial-create-instance.md).

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Vaše předplatné Azure je zakázané

### <a name="alert-message"></a>Výstražná zpráva

*Vaše předplatné Azure přidružené k vaší spravované doméně není aktivní.  Služby Azure AD Domain Services vyžaduje aktivní předplatné, aby mohly nadále správně fungovat.*

### <a name="resolution"></a>Řešení

Azure AD DS vyžaduje aktivní předplatné. Pokud předplatné Azure, ke kterému byla přidružena spravovaná doména Azure AD DS, není aktivní, musíte ho obnovit, abyste předplatné znovu aktivovali.

1. [Obnovte předplatné Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Po obnovení předplatného, oznámení Azure AD DS umožňuje znovu povolit spravované domény.

Když je spravovaná doména znovu povolena, stav spravované domény Azure AD DS se automaticky aktualizuje do dvou hodin a odebere výstrahu.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Předplatné přesunuté adresáře

### <a name="alert-message"></a>Výstražná zpráva

*Předplatné používané službou Azure AD Domain Services bylo přesunuto do jiného adresáře. Služba Azure AD Domain Services musí mít aktivní předplatné ve stejném adresáři, aby správně fungovala.*

### <a name="resolution"></a>Řešení

Azure AD DS vyžaduje aktivní předplatné a nelze přesunout do jiného předplatného. Pokud se přesune předplatné Azure AD DS spravované k přidružené, přesuňte předplatné zpět do předchozího adresáře nebo [odstraňte spravovanou doménu](delete-aadds.md) z existujícího adresáře a [vytvořte náhradní spravovanou doménu Azure AD DS ve zvoleném předplatném](tutorial-create-instance.md).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Prostředky pro spravovanou doménu nelze najít.

### <a name="alert-message"></a>Výstražná zpráva

*Prostředek, který se používá pro vaši spravovanou doménu, byl odstraněn. Tento prostředek je potřeba pro služby Azure AD Domain Services správně fungovat.*

### <a name="resolution"></a>Řešení

Azure AD DS vytváří další prostředky správně fungovat, jako jsou veřejné IP adresy, virtuální síťová rozhraní a nástroj pro vyrovnávání zatížení. Pokud jsou některé z těchto prostředků odstraněny, spravovaná doména je v nepodporovaném stavu a zabraňuje spravované doméně. Další informace o těchto prostředcích naleznete v [tématu síťové prostředky používané službou Azure AD DS](network-considerations.md#network-resources-used-by-azure-ad-ds).

Tato výstraha je generována při odstranění jednoho z těchto požadovaných prostředků. Pokud prostředek byl odstraněn před méně než 4 hodiny, je pravděpodobné, že platforma Azure můžete automaticky znovu odstraněný prostředek. Následující kroky popisují, jak zkontrolovat stav a časové razítko pro odstranění prostředků:

1. Na webu Azure Portal vyhledejte a vyberte **Domain Services**. Zvolte spravovanou doménu Azure AD DS, například *aaddscontoso.com*.
1. V levém navigačním panelu vyberte **možnost Zdraví**.
1. Na stránce stavu vyberte výstrahu s ID *AADDS109*.
1. Výstraha má časové razítko pro kdy byla poprvé nalezena. Pokud je časové razítko před méně než 4 hodinami, platforma Azure může být schopna automaticky znovu vytvořit prostředek a vyřešit výstrahu sama.

    Pokud je výstraha starší než 4 hodiny, je spravovaná doména Azure AD DS v neopravitelném stavu. [Odstraňte spravovanou doménu Azure AD DS](delete-aadds.md) a [vytvořte náhradní spravovanou doménu](tutorial-create-instance.md).

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: Podsíť přidružená ke spravované doméně je plná.

### <a name="alert-message"></a>Výstražná zpráva

*Podsíť vybraná pro nasazení služby Azure AD Domain Services je plná a nemá místo pro další řadič domény, který je potřeba vytvořit.*

### <a name="resolution"></a>Řešení

Podsíť virtuální sítě pro Azure AD DS potřebuje dostatek IP adres pro automaticky vytvořené prostředky. Tento adresní prostor IP zahrnuje potřebu vytvořit náhradní prostředky, pokud dojde k události údržby. Chcete-li minimalizovat riziko vyčerpání dostupných IP adres, nenasazujte další prostředky, jako jsou vlastní virtuální počítače, do stejné podsítě virtuální sítě jako Azure AD DS.

Tato chyba je neopravitelná. Chcete-li výstrahu vyřešit, [odstraňte stávající spravovanou doménu Azure AD DS](delete-aadds.md) a znovu ji vytvořte. Pokud máte potíže s odstraněním spravované domény Azure AD DS, [otevřete žádost o podporu Azure][azure-support] pro další pomoc při řešení potíží.

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Hlavní povinný služby neautorizovaný

### <a name="alert-message"></a>Výstražná zpráva

*Instanční objekt, který služba Azure AD Domain Services používá ke službě vaší domény, není oprávněna spravovat prostředky v rámci předplatného Azure. Instanční objekt musí získat oprávnění k poskytování služeb spravované doméně.*

### <a name="resolution"></a>Řešení

Některé automaticky generované instanční objekty se používají ke správě a vytváření prostředků pro spravovanou doménu Azure AD DS. Pokud se změní přístupová oprávnění pro jeden z těchto instančních objektů, doména není schopna správně spravovat prostředky. Následující kroky ukazují, jak porozumět a pak udělit přístupová oprávnění instančnímu objektu služby:

1. Přečtěte si o [řízení přístupu na základě rolí a jak udělit přístup k aplikacím na webu Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
2. Zkontrolujte přístup, který má instanční objekt s ID *abba844e-bc0e-44b0-947a-dc74e5d09022,* a udělte přístup, který byl odepřen k dřívějšímu datu.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Nedostatek IP adresy ve spravované doméně

### <a name="alert-message"></a>Výstražná zpráva

*Zjistili jsme, že podsíť virtuální sítě v této doméně nemusí mít dostatek ADRES IP. Služba Azure AD Domain Services potřebuje alespoň dvě dostupné IP adresy v rámci podsítě, ve které je povolená. Doporučujeme mít alespoň 3-5 náhradních IP adres v podsíti. K tomu může dojít, pokud jsou v podsíti nasazeny jiné virtuální počítače, což vyčerpává počet dostupných adres IP nebo pokud existuje omezení počtu dostupných adres IP v podsíti.*

### <a name="resolution"></a>Řešení

Podsíť virtuální sítě pro Azure AD DS potřebuje dostatek IP adres pro automaticky vytvořené prostředky. Tento adresní prostor IP zahrnuje potřebu vytvořit náhradní prostředky, pokud dojde k události údržby. Chcete-li minimalizovat riziko vyčerpání dostupných IP adres, nenasazujte další prostředky, jako jsou vlastní virtuální počítače, do stejné podsítě virtuální sítě jako Azure AD DS.

Chcete-li tuto výstrahu vyřešit, odstraňte stávající spravovanou doménu Služby Azure AD DS a znovu ji vytvořte ve virtuální síti s dostatečně velkým rozsahem IP adres. Tento proces je rušivý, protože spravovaná doména Azure AD DS není dostupná a všechny vlastní prostředky, které jste vytvořili, jako jsou hlavní sady nebo účty služeb, se ztratí.

1. [Odstraňte spravovanou doménu Azure AD DS](delete-aadds.md) z adresáře.
1. Pokud chcete aktualizovat rozsah IP adres virtuální sítě, vyhledejte a vyberte *Virtuální síť* na webu Azure Portal. Vyberte virtuální síť pro Azure AD DS, která má malý rozsah IP adres.
1. V části **Nastavení**vyberte *Možnost Adresní prostor*.
1. Aktualizujte rozsah adres výběrem existujícího rozsahu adres a jeho úpravou nebo přidáním dalšího rozsahu adres. Ujistěte se, že nový rozsah IP adres je dostatečně velký pro rozsah podsítě Azure AD DS. Až budete připraveni, **uložte** změny.
1. V levém navigačním **panelu vyberte podsítě.**
1. Vyberte podsíť, kterou chcete upravit, nebo vytvořte další podsíť.
1. Aktualizujte nebo zadejte dostatečně velký rozsah IP adres a **uložte** změny.
1. [Vytvořte náhradní spravovanou doménu Azure AD DS](tutorial-create-instance.md). Ujistěte se, že jste vybrali aktualizovanou podsíť virtuální sítě s dostatečně velkým rozsahem IP adres.

Stav spravované domény Azure AD DS se automaticky aktualizuje do dvou hodin a odebere výstrahu.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Zdroje jsou neopravitelné

### <a name="alert-message"></a>Výstražná zpráva

*Prostředky používané službou Azure AD Domain Services byly zjištěny v neočekávaném stavu a nelze je obnovit.*

### <a name="resolution"></a>Řešení

Tato chyba je neopravitelná. Chcete-li výstrahu vyřešit, [odstraňte stávající spravovanou doménu Azure AD DS](delete-aadds.md) a znovu ji vytvořte. Pokud máte potíže s odstraněním spravované domény Azure AD DS, [otevřete žádost o podporu Azure][azure-support] pro další pomoc při řešení potíží.

## <a name="aadds114-subnet-invalid"></a>AADDS114: Podsíť je neplatná.

### <a name="alert-message"></a>Výstražná zpráva

*Podsíť vybraná pro nasazení služby Azure AD Domain Services je neplatná a nelze ji použít.*

### <a name="resolution"></a>Řešení

Tato chyba je neopravitelná. Chcete-li výstrahu vyřešit, [odstraňte stávající spravovanou doménu Azure AD DS](delete-aadds.md) a znovu ji vytvořte. Pokud máte potíže s odstraněním spravované domény Azure AD DS, [otevřete žádost o podporu Azure][azure-support] pro další pomoc při řešení potíží.

## <a name="aadds115-resources-are-locked"></a>AADDS115: Prostředky jsou uzamčeny

### <a name="alert-message"></a>Výstražná zpráva

*Jeden nebo více síťových prostředků používaných spravovanou doménou nelze provozovat, protože cílový obor byl uzamčen.*

### <a name="resolution"></a>Řešení

Uzamčení prostředků lze použít na prostředky Azure zabránit změně nebo odstranění. Vzhledem k tomu, že Azure AD DS je spravovaná služba, platforma Azure potřebuje možnost provádět změny konfigurace. Pokud se zámek prostředků použije na některé součásti Azure AD DS, platforma Azure nemůže provádět své úlohy správy.

Chcete-li zkontrolovat uzamčení prostředků na součástech Azure AD DS a odebrat je, proveďte následující kroky:

1. Pro každou síťovou součást Azure AD DS ve vaší skupině prostředků, jako je virtuální síť, síťové rozhraní nebo veřejná IP adresa, zkontrolujte protokoly operací na webu Azure Portal. Tyto protokoly operací by měly označovat, proč operace selhává a kde je použit zámek prostředků.
1. Vyberte prostředek, kde je použit zámek, pak v části **Zámky**, vyberte a odeberte zámek (y).

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Zdroje jsou nepoužitelné

### <a name="alert-message"></a>Výstražná zpráva

*Jeden nebo více síťových prostředků používaných spravovanou doménou nelze provozovat z důvodu omezení zásad.*

### <a name="resolution"></a>Řešení

Zásady se používají pro prostředky Azure a skupiny prostředků, které řídí, jaké akce konfigurace jsou povoleny. Vzhledem k tomu, že Azure AD DS je spravovaná služba, platforma Azure potřebuje možnost provádět změny konfigurace. Pokud se zásada použije na některé součásti Azure AD DS, platforma Azure nemusí být schopna provádět své úlohy správy.

Chcete-li zkontrolovat použité zásady na součástech Azure AD DS a aktualizovat je, proveďte následující kroky:

1. Pro každou síťovou součást Azure AD DS ve vaší skupině prostředků, jako je virtuální síť, nic nebo veřejná IP adresa, zkontrolujte protokoly operací na webu Azure Portal. Tyto protokoly operací by měly označovat, proč operace selhává a kde je použita omezující zásada.
1. Vyberte zdroj, ve kterém je zásada použita, a v části **Zásady**vyberte a upravte zásadu tak, aby byla méně omezující.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Synchronizace nebyla dokončena za chvíli

### <a name="alert-message"></a>Výstražná zpráva

*Spravovaná doména byla naposledy synchronizována se službou Azure AD dne [date]. Uživatelé nemusí být schopni přihlásit se ke spravované doméně nebo členství ve skupinách nemusí být synchronizovány s Azure AD.*

### <a name="resolution"></a>Řešení

[Zkontrolujte stav Azure AD DS](check-health.md) pro všechny výstrahy, které označují problémy v konfiguraci spravované domény. Problémy s konfigurací sítě můžete blokovat synchronizaci z Azure AD. Pokud se vám podaří vyřešit výstrahy, které označují problém s konfigurací, počkejte dvě hodiny a zkontrolujte, zda byla synchronizace úspěšně dokončena.

Následující běžné důvody způsobit synchronizace zastavit ve spravovaných doménách Azure AD DS:

* Požadované připojení k síti je blokováno. Další informace o tom, jak zkontrolovat problémy s virtuální sítí Azure a co je potřeba, najdete [v tématu poradce při potížích se skupinami zabezpečení sítě](alert-nsg.md) a požadavky na síť pro [služby Azure AD Domain Services](network-considerations.md).
*  Synchronizace hesel nebyla nastavena nebo úspěšně dokončena při nasazení spravované domény Služby Azure AD DS. Synchronizaci hesel můžete nastavit pro [uživatele pouze v cloudu](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) nebo [hybridní uživatele z on-prem](tutorial-configure-password-hash-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Záloha nebyla přijata ve chvíli

### <a name="alert-message"></a>Výstražná zpráva

*Spravovaná doména byla naposledy zálohována k datu [date].*

### <a name="resolution"></a>Řešení

[Zkontrolujte stav Azure AD DS](check-health.md) pro výstrahy, které označují problémy v konfiguraci spravované domény. Problémy s konfigurací sítě můžete zablokovat platformě Azure úspěšně převzetí záloh. Pokud se vám podaří vyřešit výstrahy, které označují problém s konfigurací, počkejte dvě hodiny a zkontrolujte, zda byla synchronizace úspěšně dokončena.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Pozastavení z důvodu zakázaného předplatného

### <a name="alert-message"></a>Výstražná zpráva

*Spravovaná doména je pozastavena, protože předplatné Azure přidružené k doméně není aktivní.*

### <a name="resolution"></a>Řešení

> [!WARNING]
> Pokud je spravovaná doména Azure AD DS pozastavena na delší dobu, existuje nebezpečí, že bude odstraněna. Vyřešte důvod pozastavení co nejrychleji. Další informace naleznete [v tématu Understand suspendované stavy pro Azure AD DS](suspension.md).

Azure AD DS vyžaduje aktivní předplatné. Pokud předplatné Azure, ke kterému byla přidružena spravovaná doména Azure AD DS, není aktivní, musíte ho obnovit, abyste předplatné znovu aktivovali.

1. [Obnovte předplatné Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Po obnovení předplatného, oznámení Azure AD DS umožňuje znovu povolit spravované domény.

Když je spravovaná doména znovu povolena, stav spravované domény Azure AD DS se automaticky aktualizuje do dvou hodin a odebere výstrahu.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Pozastavení z důvodu neplatné konfigurace

### <a name="alert-message"></a>Výstražná zpráva

*Spravovaná doména je pozastavena z důvodu neplatné konfigurace. Služba již dlouho nemohla spravovat, opravovat nebo aktualizovat řadiče domény pro spravovanou doménu.*

### <a name="resolution"></a>Řešení

> [!WARNING]
> Pokud je spravovaná doména Azure AD DS pozastavena na delší dobu, existuje nebezpečí, že bude odstraněna. Vyřešte důvod pozastavení co nejrychleji. Další informace naleznete [v tématu Understand suspendované stavy pro Azure AD DS](suspension.md).

[Zkontrolujte stav Azure AD DS](check-health.md) pro výstrahy, které označují problémy v konfiguraci spravované domény. Pokud se vám bude dahovy, které označují problém s konfigurací, počkejte dvě hodiny a zkontrolujte, zda byla synchronizace dokončena. Až budete připraveni, [otevřete žádost o podporu Azure a][azure-support] znovu povolte spravovanou doménu Azure AD DS.

## <a name="next-steps"></a>Další kroky

Pokud máte stále problémy, [otevřete žádost o podporu Azure][azure-support] pro další pomoc při řešení potíží.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
