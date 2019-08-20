---
title: 'Azure Active Directory Domain Services: Řešení potíží s výstrahami | Microsoft Docs'
description: Řešení potíží s výstrahami pro Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 5a8f3401de0dc452193efbcf79aef87a19aed081
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617091"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services – řešení potíží s výstrahami
Tento článek popisuje Průvodce odstraňováním potíží pro všechny výstrahy, které se mohou vyskytnout ve spravované doméně.


Vyberte kroky pro řešení potíží, které odpovídají ID nebo zprávě v upozornění.

| **ID výstrahy** | **Zpráva výstrahy** | **Řešení** |
| --- | --- | :--- |
| AADDS001 | *Pro spravovanou doménu je povolená protokol Secure LDAP přes Internet. Přístup k portu 636 ale není uzamčený pomocí skupiny zabezpečení sítě. To může vystavit uživatelské účty ve spravované doméně pro zneužití hesla hrubou silou.* | [Nesprávná konfigurace zabezpečeného protokolu LDAP](alert-ldaps.md) |
| AADDS100 | *Je možné, že se odstranil adresář služby Azure AD přidružený ke spravované doméně. Spravovaná doména již není v podporované konfiguraci. Společnost Microsoft nemůže monitorovat, spravovat, opravovat a synchronizovat vaši spravovanou doménu.* | [Chybějící adresář](#aadds100-missing-directory) |
| AADDS101 | *Azure AD Domain Services nelze povolit v Azure AD B2Cm adresáři.* | [Azure AD B2C je spuštěný v tomto adresáři.](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Instanční objekt vyžadovaný pro správné fungování Azure AD Domain Services se odstranil z adresáře služby Azure AD. Tato konfigurace má vliv na schopnost Microsoftu monitorovat, spravovat, opravovat a synchronizovat vaši spravovanou doménu.* | [Chybějící objekt služby](alert-service-principal.md) |
| AADDS103 | *Rozsah IP adres pro virtuální síť, ve které jste povolili Azure AD Domain Services, je ve veřejném rozsahu IP adres. Ve virtuální síti s rozsahem privátních IP adres musí být povolená Azure AD Domain Services. Tato konfigurace má vliv na schopnost Microsoftu monitorovat, spravovat, opravovat a synchronizovat vaši spravovanou doménu.* | [Adresa je ve veřejném rozsahu IP adres.](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *Společnost Microsoft se nemůže spojit s řadiči domény pro tuto spravovanou doménu. K tomu může dojít v případě, že skupina zabezpečení sítě (NSG) nakonfigurovaná ve vaší virtuální síti blokuje přístup ke spravované doméně. Dalším možným důvodem je, že existuje trasa definovaná uživatelem, která blokuje příchozí provoz z Internetu.* | [Chyba sítě](alert-nsg.md) |
| AADDS105 | *Instanční objekt s ID aplikace "d87dcbc6-a371-462e-88e3-28ad15ec4e64" byl odstraněn a pak znovu vytvořen. Opětovné vytvoření opouští za nekonzistentní oprávnění na Azure AD Domain Services prostředky, které jsou potřeba pro obsluhu vaší spravované domény. Může to mít vliv na synchronizaci hesel ve spravované doméně.* | [Aplikace synchronizace hesel je zastaralá.](alert-service-principal.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS106 | *Vaše předplatné Azure přidružené k vaší spravované doméně se odstranilo.  Azure AD Domain Services vyžaduje, aby aktivní předplatné pokračovalo ve správném fungování.* | [Předplatné Azure se nenašlo.](#aadds106-your-azure-subscription-is-not-found) |
| AADDS107 | *Vaše předplatné Azure přidružené k vaší spravované doméně není aktivní.  Azure AD Domain Services vyžaduje, aby aktivní předplatné pokračovalo ve správném fungování.* | [Předplatné Azure je zakázané.](#aadds107-your-azure-subscription-is-disabled) |
| AADDS108 | *Předplatné používané službou Azure AD Domain Services bylo přesunuto do jiného adresáře. Azure AD Domain Services musí mít ve stejném adresáři aktivní předplatné, aby správně fungovalo.* | [Předplatné přesunuté adresáře](#aadds108-subscription-moved-directories) |
| AADDS109 | *Prostředek, který se používá pro spravovanou doménu, byl odstraněn. Tento prostředek je nutný, aby Azure AD Domain Services správně fungoval.* | [Prostředek se odstranil.](#aadds109-resources-for-your-managed-domain-cannot-be-found) |
| AADDS110 | *Podsíť vybraná pro nasazení Azure AD Domain Services je plná a nemá místo pro další řadič domény, který je potřeba vytvořit.* | [Podsíť je plná.](#aadds110-the-subnet-associated-with-your-managed-domain-is-full) |
| AADDS111 | *Instanční objekt, který Azure AD Domain Services používá ke službě vaší domény, nemá oprávnění ke správě prostředků v rámci předplatného Azure. Instanční objekt musí mít oprávnění k provozu vaší spravované domény.* | [Objekt služby je neautorizovaný.](#aadds111-service-principal-unauthorized) |
| AADDS112 | *Zjistili jsme, že podsíť virtuální sítě v této doméně nemusí mít dost IP adres. Azure AD Domain Services potřebuje aspoň dvě dostupné IP adresy v podsíti, ve které je povolený. V rámci podsítě doporučujeme mít minimálně 3-5 náhradních IP adres. K této chybě mohlo dojít, pokud jsou v rámci podsítě nasazeny další virtuální počítače, čímž dojde k vyčerpání počtu dostupných IP adres nebo omezení počtu dostupných IP adres v podsíti.* | [Nedostatečná IP adresa](#aadds112-not-enough-ip-address-in-the-managed-domain) |
| AADDS113 | *Prostředky používané Azure AD Domain Services byly zjištěny v neočekávaném stavu a nelze je obnovit.* | [Prostředky je možné obnovit.](#aadds113-resources-are-unrecoverable) |
| AADDS114 | *Podsíť vybraná pro nasazení Azure AD Domain Services není platná a nedá se použít.* | [Neplatná podsíť](#aadds114-subnet-invalid) |
| AADDS115 | *Jeden nebo více síťových prostředků používaných spravovanou doménou nelze provozovat, protože cílový obor byl uzamčen.* | [Prostředky jsou zamčené.](#aadds115-resources-are-locked) |
| AADDS116 | *Jeden nebo více síťových prostředků používaných spravovanou doménou nelze provozovat v důsledku omezení zásad.* | [Prostředky jsou nepoužitelné.](#aadds116-resources-are-unusable) |
| AADDS500 | *Tato spravovaná doména byla naposledy synchronizována se službou Azure AD dne [Date]. Uživatelé se nemusí přihlašovat ve spravované doméně nebo členství ve skupině se nemusí synchronizovat se službou Azure AD.* | [Synchronizace neproběhla v průběhu chvilky.](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *Tato spravovaná doména byla naposledy zálohována [Date].* | [Záloha se neuskutečnila během chvilky.](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *Platnost certifikátu zabezpečeného protokolu LDAP pro spravovanou doménu vyprší dne [Date].* | [Platnost certifikátu zabezpečeného protokolu LDAP vypršela](alert-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *Spravovaná doména je pozastavená, protože předplatné Azure přidružené k doméně není aktivní.* | [Pozastavení z důvodu zakázaného předplatného](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *Spravovaná doména je pozastavena z důvodu neplatné konfigurace. Služba nemohla po dlouhou dobu spravovat, opravovat ani aktualizovat řadiče domény pro spravovanou doménu.* | [Pozastavení z důvodu neplatné konfigurace](#aadds504-suspension-due-to-an-invalid-configuration) |



## <a name="aadds100-missing-directory"></a>AADDS100: Chybějící adresář
**Zpráva upozornění:**

*Je možné, že se odstranil adresář služby Azure AD přidružený ke spravované doméně. Spravovaná doména již není v podporované konfiguraci. Společnost Microsoft nemůže monitorovat, spravovat, opravovat a synchronizovat vaši spravovanou doménu.*

**Řešení:**

Tato chyba je obvykle způsobená nesprávně přesunutím předplatného Azure do nového adresáře služby Azure AD a odstraněním starého adresáře služby Azure AD, který je stále přidružený k Azure AD Domain Services.

Tato chyba je neopravitelná. Chcete-li tento problém vyřešit, je nutné [Odstranit existující spravovanou doménu](delete-aadds.md) a znovu ji vytvořit v novém adresáři. Pokud máte potíže s odstraněním, obraťte se na Azure Active Directory Domain Services produktového týmu [pro podporu](contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C je spuštěný v tomto adresáři.
**Zpráva upozornění:**

*Azure AD Domain Services nelze povolit v Azure AD B2Cm adresáři.*

**Řešení:**

>[!NOTE]
>Aby bylo možné pokračovat v používání Azure AD Domain Services, je nutné znovu vytvořit instanci Azure AD Domain Services v adresáři, který není Azure AD B2C.

K obnovení služby použijte následující postup:

1. [Odstraňte spravovanou doménu](delete-aadds.md) ze stávajícího adresáře služby Azure AD.
2. Vytvořte nový adresář, který není Azure AD B2C adresářem.
3. Pomocí průvodce [Začínáme](tutorial-create-instance.md) znovu vytvořte spravovanou doménu.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Adresa je ve veřejném rozsahu IP adres.

**Zpráva upozornění:**

*Rozsah IP adres pro virtuální síť, ve které jste povolili Azure AD Domain Services, je ve veřejném rozsahu IP adres. Ve virtuální síti s rozsahem privátních IP adres musí být povolená Azure AD Domain Services. Tato konfigurace má vliv na schopnost Microsoftu monitorovat, spravovat, opravovat a synchronizovat vaši spravovanou doménu.*

**Řešení:**

> [!NOTE]
> Chcete-li tento problém vyřešit, je nutné odstranit existující spravovanou doménu a znovu ji vytvořit ve virtuální síti s rozsahem privátních IP adres. Tento proces je rušivý.

Než začnete, přečtěte si část **privátní adresní prostor IP v4** v [tomto článku](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

V rámci virtuální sítě můžou počítače podávat požadavky na prostředky Azure, které jsou ve stejném rozsahu IP adres jako ty, které jsou pro tuto podsíť nakonfigurované. Vzhledem k tomu, že je pro tento rozsah nakonfigurovaná virtuální síť, budou tyto požadavky směrovány do virtuální sítě a nebudou se dostihnout k zamýšleným webovým prostředkům. Tato konfigurace může vést k nepředvídatelným chybám s Azure AD Domain Services.

**Pokud vlastníte rozsah IP adres v Internetu, který je nakonfigurovaný ve vaší virtuální síti, můžete tuto výstrahu ignorovat. Azure AD Domain Services ale s touto konfigurací nejde zapsat do [smlouvy SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)], protože to může vést k nepředvídatelným chybám.**


1. [Odstraňte spravovanou doménu](delete-aadds.md) z adresáře.
2. Opravit rozsah IP adres pro podsíť
   1. [Na Azure Portal přejděte na stránku virtuální sítě](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
   2. Vyberte virtuální síť, kterou plánujete použít pro Azure AD Domain Services.
   3. V části Nastavení klikněte na **adresní prostor** .
   4. Aktualizujte rozsah adres kliknutím na stávající rozsah adres a úpravou nebo přidáním dalšího rozsahu adres. Ujistěte se, že je nový rozsah adres v privátním rozsahu IP adres. Uložte provedené změny.
   5. V levém navigačním panelu klikněte na **podsítě** .
   6. Klikněte na podsíť, kterou chcete v tabulce upravit.
   7. Aktualizujte rozsah adres a uložte provedené změny.
3. Pokud chcete znovu vytvořit spravovanou doménu, postupujte podle [Začínáme s průvodcem Azure AD Domain Services](tutorial-create-instance.md) . Ujistěte se, že vyberete virtuální síť s rozsahem privátních IP adres.
4. Pokud chcete virtuální počítače připojit k doméně k nové doméně, postupujte podle pokynů v [tomto průvodci](join-windows-vm.md).
8. Chcete-li zkontrolovat, zda je výstraha vyřešena, zkontrolujte stav vaší domény ve dvou hodinách.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Vaše předplatné Azure se nenašlo.

**Zpráva upozornění:**

*Vaše předplatné Azure přidružené k vaší spravované doméně se odstranilo.  Azure AD Domain Services vyžaduje, aby aktivní předplatné pokračovalo ve správném fungování.*

**Řešení:**

Azure AD Domain Services vyžaduje, aby předplatné fungovalo a nebylo možné ho přesunout do jiného předplatného. Vzhledem k tomu, že předplatné Azure, se kterým byla vaše spravovaná doména přidružená, se odstranilo, budete muset znovu vytvořit předplatné Azure a Azure AD Domain Services.

1. Vytvořit předplatné Azure
2. [Odstraňte spravovanou doménu](delete-aadds.md) ze stávajícího adresáře služby Azure AD.
3. Pomocí průvodce [Začínáme](tutorial-create-instance.md) znovu vytvořte spravovanou doménu.

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Předplatné Azure se zakázalo

**Zpráva upozornění:**

*Vaše předplatné Azure přidružené k vaší spravované doméně není aktivní.  Azure AD Domain Services vyžaduje, aby aktivní předplatné pokračovalo ve správném fungování.*

**Řešení:**


1. [Prodlužte si platnost předplatného Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Po obnovení předplatného Azure AD Domain Services dostane oznámení od Azure, aby se vaše spravovaná doména znovu povolila.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Předplatné přesunuté adresáře

**Zpráva upozornění:**

*Předplatné používané službou Azure AD Domain Services bylo přesunuto do jiného adresáře. Azure AD Domain Services musí mít ve stejném adresáři aktivní předplatné, aby správně fungovalo.*

**Řešení:**

Předplatné spojené s Azure AD Domain Services můžete buď přesunout zpátky do předchozího adresáře, nebo potřebujete ze stávajícího adresáře [Odstranit spravovanou doménu](delete-aadds.md) a znovu ji vytvořit ve zvoleném adresáři (buď s novým předplatným, nebo Změňte adresář, ve kterém je instance Azure AD Domain Services.

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Nepovedlo se najít prostředky pro spravovanou doménu.

**Zpráva upozornění:**

*Prostředek, který se používá pro spravovanou doménu, byl odstraněn. Tento prostředek je nutný, aby Azure AD Domain Services správně fungoval.*

**Řešení:**

Azure AD Domain Services vytváří konkrétní prostředky při nasazení, aby fungovaly správně, včetně veřejných IP adres, síťových adaptérů a nástroje pro vyrovnávání zatížení. Pokud dojde k odstranění některého z těchto pojmenovaných, dojde k tomu, že vaše spravovaná doména bude v nepodporovaném stavu a znemožní správu vaší domény. Tato výstraha se najde, když někdo, kdo může upravit prostředky Azure AD Domain Services, odstraní potřebný prostředek. Následující kroky popisují, jak obnovit spravovanou doménu.

1. Přejít na stránku Azure AD Domain Services stav
   1.    Nacestování na [stránku Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) v Azure Portal.
   2.    V levém navigačním panelu klikněte na **stav** .
2. Zkontrolujte, jestli je výstraha starší než 4 hodiny.
   1.    Na stránce stav klikněte na výstrahu s ID **AADDS109** .
   2.    Výstraha bude mít časové razítko, které bylo poprvé nalezeno. Pokud je toto časové razítko před méně než 4 hodinami, existuje možnost, že Azure AD Domain Services může znovu vytvořit odstraněný prostředek.
3. Pokud je výstraha starší než 4 hodiny stará, je spravovaná doména v neobnovitelné stavu. Azure AD Domain Services je nutné odstranit a znovu vytvořit.


## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: Podsíť přidružená ke spravované doméně je plná.

**Zpráva upozornění:**

*Podsíť vybraná pro nasazení Azure AD Domain Services je plná a nemá místo pro další řadič domény, který je potřeba vytvořit.*

**Řešení:**

Tato chyba je neopravitelná. Chcete-li řešení vyřešit, je nutné [Odstranit existující spravovanou doménu](delete-aadds.md) a [znovu vytvořit spravovanou doménu](tutorial-create-instance.md) .

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Objekt služby je neautorizovaný.

**Zpráva upozornění:**

*Instanční objekt, který Azure AD Domain Services používá ke službě vaší domény, nemá oprávnění ke správě prostředků v rámci předplatného Azure. Instanční objekt musí mít oprávnění k provozu vaší spravované domény.*

**Řešení:**

Naše instanční objekty potřebují přístup, aby bylo možné spravovat a vytvářet prostředky ve spravované doméně. Někdo odepřel přístup k instančnímu objektu a teď nemůže spravovat prostředky. Postupujte podle kroků a udělte přístup k objektu služby.

1. Přečtěte si o [řízení RBAC a o tom, jak udělit přístup k aplikacím na Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
2. Zkontrolujte přístup, který instanční objekt s ID ```abba844e-bc0e-44b0-947a-dc74e5d09022``` , a udělte přístup, který byl zamítnut v dřívější datum.


## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Ve spravované doméně není dostatečná IP adresa.

**Zpráva upozornění:**

*Zjistili jsme, že podsíť virtuální sítě v této doméně nemusí mít dost IP adres. Azure AD Domain Services potřebuje aspoň dvě dostupné IP adresy v podsíti, ve které je povolený. V rámci podsítě doporučujeme mít minimálně 3-5 náhradních IP adres. K této chybě mohlo dojít, pokud jsou v rámci podsítě nasazeny další virtuální počítače, čímž dojde k vyčerpání počtu dostupných IP adres nebo omezení počtu dostupných IP adres v podsíti.*

**Řešení:**

1. Odstraňte ze svého tenanta spravovanou doménu.
2. Opravit rozsah IP adres pro podsíť
   1. [Na Azure Portal přejděte na stránku virtuální sítě](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
   2. Vyberte virtuální síť, kterou plánujete použít pro Azure AD Domain Services.
   3. V části Nastavení klikněte na **adresní prostor** .
   4. Aktualizujte rozsah adres kliknutím na stávající rozsah adres a úpravou nebo přidáním dalšího rozsahu adres. Uložte provedené změny.
   5. V levém navigačním panelu klikněte na **podsítě** .
   6. Klikněte na podsíť, kterou chcete v tabulce upravit.
   7. Aktualizujte rozsah adres a uložte provedené změny.
3. Pokud chcete znovu vytvořit spravovanou doménu, postupujte podle [Začínáme s průvodcem Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) . Ujistěte se, že vyberete virtuální síť s rozsahem privátních IP adres.
4. Pokud chcete virtuální počítače připojit k doméně k nové doméně, postupujte podle pokynů v [tomto průvodci](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).
5. Zkontrolujte stav vaší domény dvě hodiny, abyste měli jistotu, že jste tyto kroky dokončili správně.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Prostředky je možné obnovit.

**Zpráva upozornění:**

*Prostředky používané Azure AD Domain Services byly zjištěny v neočekávaném stavu a nelze je obnovit.*

**Řešení:**

Tato chyba je neopravitelná. Chcete-li tento problém vyřešit, je nutné [Odstranit existující spravovanou doménu](delete-aadds.md) a [znovu vytvořit spravovanou doménu](tutorial-create-instance.md).

## <a name="aadds114-subnet-invalid"></a>AADDS114: Neplatná podsíť

**Zpráva upozornění:**

*Podsíť vybraná pro nasazení Azure AD Domain Services není platná a nedá se použít.*

**Řešení:**

Tato chyba je neopravitelná. Chcete-li tento problém vyřešit, je nutné [Odstranit existující spravovanou doménu](delete-aadds.md) a [znovu vytvořit spravovanou doménu](tutorial-create-instance.md).

## <a name="aadds115-resources-are-locked"></a>AADDS115: Prostředky jsou zamčené.

**Zpráva upozornění:**

*Jeden nebo více síťových prostředků používaných spravovanou doménou nelze provozovat, protože cílový obor byl uzamčen.*

**Řešení:**

1.  Zkontrolujte protokoly Správce prostředků operací na síťových prostředcích (to by mělo poskytnout informace o tom, které uzamčení brání v úpravách).
2.  Odeberte zámky u prostředků, aby na nich Azure AD Domain Services instanční objekt fungovat.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Prostředky jsou nepoužitelné.

**Zpráva upozornění:**

*Jeden nebo více síťových prostředků používaných spravovanou doménou nelze provozovat v důsledku omezení zásad.*

**Řešení:**

1.  Zkontrolujte protokoly Správce prostředků operací na síťových prostředcích vaší spravované domény.
2.  Oslabení omezení zásad u prostředků tak, aby instanční objekt AAD-DS mohl na těchto prostředcích pracovat.



## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Synchronizace se ještě nedokončila.

**Zpráva upozornění:**

*Tato spravovaná doména byla naposledy synchronizována se službou Azure AD dne [Date]. Uživatelé se nemusí přihlašovat ve spravované doméně nebo členství ve skupině se nemusí synchronizovat se službou Azure AD.*

**Řešení:**

[Podívejte se na stav vaší domény](check-health.md) pro všechny výstrahy, které můžou naznačovat problémy v konfiguraci vaší spravované domény. Problémy s konfigurací můžou v některých případech zablokovat schopnost Microsoftu synchronizovat vaši spravovanou doménu. Pokud je možné vyhodnotit jakékoli výstrahy, počkejte dvě hodiny a vraťte se zpět, abyste viděli, jestli se synchronizace dokončila.

Tady jsou některé běžné důvody, proč se synchronizace zastaví ve spravovaných doménách:
- Síťové připojení je zablokované ve spravované doméně. Pokud chcete získat další informace o kontrole problémů v síti, přečtěte si téma [řešení potíží se skupinami zabezpečení sítě](alert-nsg.md) a [požadavky na síť pro Azure AD Domain Services](network-considerations.md).
-  Synchronizace hesel nebyla nikdy nastavena nebo dokončena. Chcete-li nastavit synchronizaci hesel, přečtěte si [Tento článek](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Zálohování se neuskutečnilo během chvilky.

**Zpráva upozornění:**

*Tato spravovaná doména byla naposledy zálohována [Date].*

**Řešení:**

[Podívejte se na stav vaší domény](check-health.md) pro všechny výstrahy, které můžou naznačovat problémy v konfiguraci vaší spravované domény. Problémy s konfigurací můžou v některých případech zablokovat možnost Microsoftu zálohovat spravovanou doménu. Pokud je možné vyhodnotit nějaké výstrahy, počkejte dvě hodiny a vraťte se zpátky, abyste viděli, jestli se zálohování dokončilo.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Pozastavení z důvodu zakázaného předplatného

**Zpráva upozornění:**

*Spravovaná doména je pozastavená, protože předplatné Azure přidružené k doméně není aktivní.*

**Řešení:**

> [!WARNING]
> Pokud je vaše spravovaná doména pozastavena po delší dobu, je nebezpečí odstranění. Je nejlepší řešit pozastavení co nejrychleji. Další informace najdete v [tomto článku](suspension.md).

Pokud chcete obnovit službu, [obnovte si předplatné Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable) přidružené k vaší spravované doméně.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Pozastavení z důvodu neplatné konfigurace

**Zpráva upozornění:**

*Spravovaná doména je pozastavena z důvodu neplatné konfigurace. Služba nemohla po dlouhou dobu spravovat, opravovat ani aktualizovat řadiče domény pro spravovanou doménu.*

**Řešení:**

> [!WARNING]
> Pokud je vaše spravovaná doména pozastavena po delší dobu, je nebezpečí odstranění. Je nejlepší řešit pozastavení co nejrychleji. Další informace najdete v [tomto článku](suspension.md).

[Podívejte se na stav vaší domény](check-health.md) pro všechny výstrahy, které můžou naznačovat problémy v konfiguraci vaší spravované domény. Pokud můžete některé z těchto výstrah vyřešit, udělejte to. Potom kontaktujte podporu a znovu povolte předplatné.


## <a name="contact-us"></a>Kontaktujte nás
Kontaktujte tým Azure Active Directory Domain Services produktů, abyste mohli [sdílet zpětnou vazbu nebo podporu](contact-us.md).
