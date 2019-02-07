---
title: 'Azure Active Directory Domain Services: Řešení výstrah | Dokumentace Microsoftu'
description: Řešení výstrah pro službu Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2018
ms.author: ergreenl
ms.openlocfilehash: 492b15bddad598d65c15c48f04d3148c41cd3c7e
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55817525"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services – řešení výstrah
Tento článek obsahuje Průvodce řešením potíží pro všechny výstrahy, které mohou nastat ve vaší spravované doméně.


Vyberte kroků pro řešení problémů, které odpovídají ID nebo zprávy ve výstraze.

| **ID upozornění** | **Zpráva s výstrahou** | **Řešení** |
| --- | --- | :--- |
| AADDS001 | *Pro spravovanou doménu je povolený protokol Secure LDAP přes internet. Přístup k portu 636 ale není uzamčen pomocí skupiny zabezpečení sítě. To může vystavit uživatelské účty ve spravované doméně vůči útokům hrubou silou heslo.* | [Nesprávná konfigurace zabezpečeného protokolu LDAP](active-directory-ds-troubleshoot-ldaps.md) |
| AADDS100 | *Adresář Azure AD přidružené k vaší spravované domény může být odstraněna. Spravovaná doména už není podporovaná konfigurace. Microsoft nelze monitorovat, spravovat, opravovat a synchronizovat vaši spravovanou doménu.* | [Chybí adresář](#aadds100-missing-directory) |
| AADDS101 | *Azure AD Domain Services nelze povolit v adresáři Azure AD B2C.* | [Azure AD B2C je spuštěna v tomto adresáři](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Vyžaduje se pro Azure AD Domain Services, aby správně fungoval hlavního názvu služby se odstranil z adresáře služby Azure AD. Tato konfigurace má dopad na schopnost Microsoftu monitorovat, spravovat, opravovat a synchronizovat vaši spravovanou doménu.* | [Chybí instanční objekt služby](active-directory-ds-troubleshoot-service-principals.md) |
| AADDS103 | *Rozsah IP adres pro virtuální síť, ve kterém jste povolili službu Azure AD Domain Services je v rozsahu veřejných IP. Azure AD Domain Services musí být povolena ve virtuální síti s rozsah privátních IP adres. Tato konfigurace má dopad na schopnost Microsoftu monitorovat, spravovat opravy a synchronizovat vaši spravovanou doménu.* | [Adresa je v rozsahu veřejných IP](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *Společnost Microsoft se nám kontaktovat řadiče této spravované doméně. To může stát v případě skupinu zabezpečení sítě (NSG) nakonfigurovaná ve vaší virtuální síti blokuje přístup ke spravované doméně. Dalším důvodem je, uživatelem definovaná trasa této blokuje příchozí provoz z Internetu.* | [Chyba sítě](active-directory-ds-troubleshoot-nsg.md) |
| AADDS105 | *Instanční objekt s ID aplikace "d87dcbc6-a371-462e-88e3-28ad15ec4e64" byl odstraněn a potom znovu vytvořit. Opětovnému vytvoření zůstanou za nekonzistentní oprávnění v Azure AD Domain Services prostředky potřebné pro služby spravované domény. Může mít vliv na synchronizaci hesel ve vaší spravované doméně.* | [Synchronizace hesla aplikace je zastaralá](active-directory-ds-troubleshoot-service-principals.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS106 | *Vaše předplatné Azure spojené s vaší spravované domény se odstranil.  Azure AD Domain Services vyžaduje aktivní předplatné nadále fungovat správně.* | [Předplatné Azure se nenašel.](#aadds106-your-azure-subscription-is-not-found) |
| AADDS107 | *Vaše předplatné Azure spojené s vaší spravované doméně není aktivní.  Azure AD Domain Services vyžaduje aktivní předplatné nadále fungovat správně.* | [Předplatné Azure je deaktivované.](#aadds107-your-azure-subscription-is-disabled) |
| AADDS108 | *Předplatné, které používá služba Azure AD Domain Services se přesunul do jiného adresáře. Azure AD Domain Services musí mít aktivní předplatné ve stejném adresáři, aby správně fungoval.* | [Přesunout předplatné adresáře](#aadds108-subscription-moved-directories) |
| AADDS109 | *Prostředek, který se používá pro vaše spravovaná doména se odstranil. Tento prostředek je potřeba pro Azure AD Domain Services, aby správně fungoval.* | [Prostředek se odstranil.](#aadds109-resources-for-your-managed-domain-cannot-be-found) |
| AADDS110 | *Podsíť vybraná pro nasazení služby Azure AD Domain Services je plný a nemá žádné místo pro další řadič domény, který je potřeba vytvořit.* | [Podsíť je plná](#aadds110-the-subnet-associated-with-your-managed-domain-is-full) |
| AADDS111 | * A instanční objekt, který používá Azure AD Domain Services pro vaši doménu služby nemá oprávnění ke správě prostředků předplatného Azure. Instanční objekt je potřeba získat oprávnění pro služby spravované domény. * | Instanční objekt není autorizovaný. |
| AADDS112 | *Zjistili jsme, že podsíť virtuální sítě v této doméně nemá dostatek IP adres. Azure AD Domain Services potřebuje alespoň dvě dostupné IP adresy v podsíti, ve které je povolený v. Doporučujeme mít alespoň 3 až 5 náhradních adres IP v rámci podsítě. To mohly nastat, pokud jsou ostatní virtuální počítače nasazené v rámci podsítě, tedy vyčerpáním počet dostupných IP adres nebo pokud je omezení počtu dostupných IP adres v podsíti.* | [Není k dispozici dostatek IP adres](#aadds112-not-enough-ip-address-in-the-managed-domain) |
| AADDS113 | *Prostředky používané službou Azure AD Domain Services byly zjištěny v neočekávaném stavu a nelze jej obnovit.* | [Prostředky neopravitelné](#aadds113-resources-are-unrecoverable) |
| AADDS114 | * Podsíť vybraná pro nasazení služby Azure AD Domain Services je neplatný a nelze použít. * | [Neplatná podsíť](#aadds114-subnet-invalid) |
| AADDS115 | *Jeden nebo více síťových prostředků používat spravovanou doménu nelze ho zpracovat jako cílový obor byl uzamčen.* | [Prostředky jsou zamknuté.](#aadds115-resources-are-locked) |
| AADDS116 | *Jeden nebo více síťových prostředků používat spravovanou doménu nelze ho zpracovat z důvodu omezení zásad.* | [Prostředky nepoužitelné](#aadds116-resources-are-unusable) |
| AADDS500 | *Spravovaná doména poslední synchronizace s Azure AD na [date]. Uživatelé možná nebudete moct přihlásit ve spravované doméně nebo členství ve skupinách možná není synchronizována s Azure AD.* | [K synchronizaci nedošlo nějakou dobu](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *Spravovaná doména posledního zálohování [Date].* | [Záloha ve chvíli zálohování](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *Certifikát protokolu secure LDAP pro spravovanou doménu vyprší [date].* | [Certifikát protokolu secure LDAP vyprší](active-directory-ds-troubleshoot-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *Spravovaná doména je pozastavená, protože není aktivní předplatné Azure spojené s doménou.* | [Pozastavení z důvodu zakázaného předplatného](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *Spravovaná doména se pozastavuje z důvodu neplatné konfigurace. Služba bylo možné spravovat, opravovat ani aktualizovat řadiče domény vaší spravované domény po dlouhou dobu.* | [Pozastavení z důvodu neplatné konfigurace](#aadds504-suspension-due-to-an-invalid-configuration) |



## <a name="aadds100-missing-directory"></a>AADDS100: Chybí adresář
**Zpráva s výstrahou:**

*Adresář Azure AD přidružené k vaší spravované domény může být odstraněna. Spravovaná doména už není podporovaná konfigurace. Microsoft nelze monitorovat, spravovat, opravovat a synchronizovat vaši spravovanou doménu.*

**Řešení:**

Tato chyba je obvykle způsobeno nesprávně přesun předplatného Azure na nový Azure AD directory a odstranění starého adresář Azure AD, který je stále spojena s Azure AD Domain Services.

Tato chyba Neopravitelná. Pokud chcete vyřešit, je nutné [odstranit svoji existující spravovanou doménu](active-directory-ds-disable-aadds.md) a znovu ho vytvořte v novém adresáři. Pokud máte potíže při odstraňování, kontaktujte produktový tým na Azure Active Directory Domain Services [pro podporu](active-directory-ds-contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C je spuštěna v tomto adresáři
**Zpráva s výstrahou:**

*Azure AD Domain Services nelze povolit v adresáři Azure AD B2C.*

**Řešení:**

>[!NOTE]
>Pokud chcete dál používat Azure AD Domain Services, musíte znovu vytvořit instanci aplikace Azure AD Domain Services v adresáři Azure AD B2C.

Chcete-li obnovit vaše služby, postupujte takto:

1. [Odstranit vaši spravovanou doménu](active-directory-ds-disable-aadds.md) z existujícího adresáře Azure AD.
2. Vytvořte nový adresář, který není adresář Azure AD B2C.
3. Postupujte podle [Začínáme](active-directory-ds-getting-started.md) průvodce znovu vytvořit spravovanou doménu.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Adresa je v rozsahu veřejných IP

**Zpráva s výstrahou:**

*Rozsah IP adres pro virtuální síť, ve kterém jste povolili službu Azure AD Domain Services je v rozsahu veřejných IP. Azure AD Domain Services musí být povolena ve virtuální síti s rozsah privátních IP adres. Tato konfigurace má dopad na schopnost Microsoftu monitorovat, spravovat opravy a synchronizovat vaši spravovanou doménu.*

**Řešení:**

> [!NOTE]
> A tento problém vyřešit, musíte odstranit svoji existující spravovanou doménu a znovu ho vytvořte ve virtuální síti s rozsah privátních IP adres. Tento proces je rušivé.

Než začnete, přečtěte si **privátního adresního prostoru IP v4** tématu [v tomto článku](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

Ve virtuální síti může být počítače požadavky na prostředky Azure, které jsou ve stejném rozsahu IP adres jako porty nakonfigurované pro podsíť. Ale vzhledem k tomu, že virtuální síť je nakonfigurovaný pro tento rozsah, tyto žádosti v rámci virtuální sítě se budou směrovat a nebude mít přístup k prostředkům určené webové. Tato konfigurace může vést k nepředvídatelným chyby s Azure AD Domain Services.

**Pokud vlastníte rozsah IP adres v síti internet, který je nakonfigurovaný ve vaší virtuální síti, můžete toto upozornění ignorovat. Však nelze potvrdit Azure AD Domain Services [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] s touto konfigurací, protože to může vést k neočekávaným chybám.**


1. [Odstranit vaši spravovanou doménu](active-directory-ds-disable-aadds.md) z adresáře.
2. Odstranit rozsah IP adres podsítě
  1. Přejděte [stránce virtuální sítě na portálu Azure portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
  2. Vyberte virtuální síť, kterou chcete použít pro službu Azure AD Domain Services.
  3. Klikněte na **adresní prostor** v části Nastavení
  4. Aktualizujte rozsah adres kliknutím na stávající rozsah adres a úpravy nebo přidání další rozsah adres. Ujistěte se, že se nový rozsah adres rozsah privátních IP. Uložte provedené změny.
  5. Klikněte na **podsítě** v levém navigačním panelu.
  6. Klikněte na podsíť, kterou chcete upravit v tabulce.
  7. Aktualizovat rozsah adres a uložte provedené změny.
3. Postupujte podle [Průvodce Začínáme spuštění pomocí Azure AD Domain Services](active-directory-ds-getting-started.md) znovu vytvořit vaši spravovanou doménu. Ujistěte se, že vyberete virtuální síť s rozsah privátních IP adres.
4. Připojení k doméně virtuálních počítačů, aby vaše nová doména, postupujte podle [Tato příručka](active-directory-ds-admin-guide-join-windows-vm-portal.md).
8. K zajištění, že výstraha vyřeší, zkontrolujte stav vaší domény do dvou hodin.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Vaše předplatné Azure se nenašel.

**Zpráva s výstrahou:**

*Vaše předplatné Azure spojené s vaší spravované domény se odstranil.  Azure AD Domain Services vyžaduje aktivní předplatné nadále fungovat správně.*

**Řešení:**

Azure AD Domain Services vyžaduje předplatné funkce a nelze jej přesunout do jiného předplatného. Protože byl odstraněn předplatné Azure, který je přidružený k vaší spravované domény, musíte znovu vytvořit předplatné Azure a Azure AD Domain Services.

1. Vytvořit předplatné Azure
2. [Odstranit vaši spravovanou doménu](active-directory-ds-disable-aadds.md) z existujícího adresáře Azure AD.
3. Postupujte podle [Začínáme](active-directory-ds-getting-started.md) průvodce znovu vytvořit spravovanou doménu.

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Vaše předplatné Azure je deaktivované

**Zpráva s výstrahou:**

*Vaše předplatné Azure spojené s vaší spravované doméně není aktivní.  Azure AD Domain Services vyžaduje aktivní předplatné nadále fungovat správně.*

**Řešení:**


1. [Obnovení vašeho předplatného Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Po prodloužení předplatného služby Azure AD Domain Services přijde oznámení z Azure na opětovné povolení vaší spravované domény.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Přesunout předplatné adresáře

**Zpráva s výstrahou:**

*Předplatné, které používá služba Azure AD Domain Services se přesunul do jiného adresáře. Azure AD Domain Services musí mít aktivní předplatné ve stejném adresáři, aby správně fungoval.*

**Řešení:**

Přesuňte předplatné spojené s zpět na předchozí adresář Azure AD Domain Services, nebo je potřeba [odstranit vaši spravovanou doménu](active-directory-ds-disable-aadds.md) z existujícího adresáře a znovu ho vytvořte v adresáři zvolené (buď nové předplatné nebo změnit adresář Azure AD Domain Services je vaše instance ve).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Prostředky pro vaše spravovaná doména se nenašel.

**Zpráva s výstrahou:**

*Prostředek, který se používá pro vaše spravovaná doména se odstranil. Tento prostředek je potřeba pro Azure AD Domain Services, aby správně fungoval.*

**Řešení:**

Azure AD Domain Services vytvoří konkrétní prostředky při nasazování mohl fungovat správně, včetně veřejných IP adres, síťových adaptérů a nástroj pro vyrovnávání zatížení. Pokud se odstraní všechny pojmenované, to způsobí vaší spravované domény v nepodporovaném stavu a zabrání spravované domény. Tato výstraha se nachází, když někdo, kdo může upravovat prostředky služby Azure AD Domain Services odstraní potřebných prostředků. Následující kroky popisují postup při obnovení vaší spravované domény.

1.  Přejděte na stránku stavu Azure AD Domain Services
  1.    Dopravíte do [stránku Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) na webu Azure Portal.
  2.    V levém navigačním panelu klikněte na tlačítko **stavu**
2.  Zaškrtněte, pokud chcete zjistit, jestli je výstraha méně než 4 hodiny
  1.    Na stránce stavu, klikněte na upozornění s ID **AADDS109**
  2.    Výstraha bude mít časové razítko pro kdy byl nalezen první. Pokud tento časové razítko je menší než 4 hodinami, může se stát, že Azure AD Domain Services, můžete znovu vytvořit odstraněný prostředek.
3.  Pokud je výstraha více než 4 hodiny, spravovanou doménu je ve stavu neobnovitelná. Je potřeba odstranit a znovu vytvořit Azure AD Domain Services.


## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: Podsíť přidružené k vaší spravované domény je plná

**Zpráva s výstrahou:**

*Podsíť vybraná pro nasazení služby Azure AD Domain Services je plný a nemá žádné místo pro další řadič domény, který je potřeba vytvořit.*

**Řešení:**

Tato chyba Neopravitelná. Pokud chcete vyřešit, je nutné [odstranit svoji existující spravovanou doménu](active-directory-ds-disable-aadds.md) a [znovu vytvořit vaši spravovanou doménu](active-directory-ds-getting-started.md)

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Instanční objekt není autorizovaný.

**Zpráva s výstrahou:**

*Objekt služby, která používá Azure AD Domain Services pro vaši doménu služby nemá oprávnění ke správě prostředků předplatného Azure. Instanční objekt je potřeba získat oprávnění pro služby spravované domény.*

**Řešení:**

Naše instanční objekty potřebují přístup k mohli spravovat a vytvářet prostředky v rámci vaší spravované domény. Uživatel byl odepřen přístup k hlavní službě a nyní je možné spravovat prostředky. Postupujte podle kroků pro udělení přístupu ke službě instančního objektu.

1. Přečtěte si informace o [RBAC ovládací prvek a jak udělit přístup k aplikacím na portálu Azure portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
2. Zkontrolovat přístup, který instanční objekt s ID ```abba844e-bc0e-44b0-947a-dc74e5d09022``` a udělit přístup, který byl odepřen v dřívějšímu datu.


## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Není k dispozici dostatek IP adres ve spravované doméně

**Zpráva s výstrahou:**

*Zjistili jsme, že podsíť virtuální sítě v této doméně nemá dostatek IP adres. Azure AD Domain Services potřebuje alespoň dvě dostupné IP adresy v podsíti, ve které je povolený v. Doporučujeme mít alespoň 3 až 5 náhradních adres IP v rámci podsítě. To mohly nastat, pokud jsou ostatní virtuální počítače nasazené v rámci podsítě, tedy vyčerpáním počet dostupných IP adres nebo pokud je omezení počtu dostupných IP adres v podsíti.*

**Řešení:**

1. Odstraňte vaši spravovanou doménu z vašeho tenanta.
2. Odstranit rozsah IP adres podsítě
  1. Přejděte [stránce virtuální sítě na portálu Azure portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
  2. Vyberte virtuální síť, kterou chcete použít pro službu Azure AD Domain Services.
  3. Klikněte na **adresní prostor** v části Nastavení
  4. Aktualizujte rozsah adres kliknutím na stávající rozsah adres a úpravy nebo přidání další rozsah adres. Uložte provedené změny.
  5. Klikněte na **podsítě** v levém navigačním panelu.
  6. Klikněte na podsíť, kterou chcete upravit v tabulce.
  7. Aktualizovat rozsah adres a uložte provedené změny.
3. Postupujte podle [Průvodce Začínáme spuštění pomocí Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) znovu vytvořit vaši spravovanou doménu. Ujistěte se, že vyberete virtuální síť s rozsah privátních IP adres.
4. Připojení k doméně virtuálních počítačů, aby vaše nová doména, postupujte podle [Tato příručka](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).
5. Kontrola stavu domény do dvou hodin k zajištění, že jste dokončili postup správně.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Prostředky neopravitelné

**Zpráva s výstrahou:**

*Prostředky používané službou Azure AD Domain Services byly zjištěny v neočekávaném stavu a nelze jej obnovit.*

**Řešení:**

Tato chyba Neopravitelná. Pokud chcete vyřešit, je nutné [odstranit svoji existující spravovanou doménu](active-directory-ds-disable-aadds.md) a [znovu vytvořit vaši spravovanou doménu](active-directory-ds-getting-started.md).

## <a name="aadds114-subnet-invalid"></a>AADDS114: Neplatná podsíť

**Zpráva s výstrahou:**

*Podsíť vybraná pro nasazení služby Azure AD Domain Services je neplatný a nelze použít.*

**Řešení:**

Tato chyba Neopravitelná. Pokud chcete vyřešit, je nutné [odstranit svoji existující spravovanou doménu](active-directory-ds-disable-aadds.md) a [znovu vytvořit vaši spravovanou doménu](active-directory-ds-getting-started.md).

## <a name="aadds115-resources-are-locked"></a>AADDS115: Prostředky jsou zamknuté.

**Zpráva s výstrahou:**

*Jeden nebo více síťových prostředků používat spravovanou doménu nelze ho zpracovat jako cílový obor byl uzamčen.*

**Řešení:**

1.  Protokoly operací kontroly Resource Manageru na síťové prostředky (to měl dát informace na uzamčení, které brání změny).
2.  Odeberte zámky u prostředků, tak, aby instanční objekt služby Azure AD Domain Services můžete pracovat s nimi.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Prostředky nepoužitelné

**Zpráva s výstrahou:**

*Jeden nebo více síťových prostředků používat spravovanou doménu nelze ho zpracovat z důvodu omezení zásad.*

**Řešení:**

1.  Operace správce prostředků Zkontrolujte protokoly na síťových prostředků vaší spravované domény.
2.  Oslabení omezení zásad na prostředky tak, aby instanční objekt služby AAD DS může pracovat s nimi.



## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Synchronizace nebyla dokončena v while

**Zpráva s výstrahou:**

*Spravovaná doména poslední synchronizace s Azure AD na [date]. Uživatelé možná nebudete moct přihlásit ve spravované doméně nebo členství ve skupinách možná není synchronizována s Azure AD.*

**Řešení:**

[Kontrola stavu domény](active-directory-ds-check-health.md) pro všechny výstrahy, které mohou indikovat potíže v konfiguraci vaší spravované domény. Problémy s konfigurací v některých případech může blokovat Microsoftu neblokujete možnost Synchronizovat vaši spravovanou doménu. Pokud je to možné vyřešit všechny výstrahy, počkejte dvou hodin a vrácení zpět zobrazíte, pokud byla dokončena synchronizace.

Tady je několik běžných příčin, proč se synchronizace zastaví ve spravovaných domén:
- Připojení k síti je blokován ve spravované doméně. Další informace o kontrole vaší síti pro problémy, přečtěte si, jak k [řešení potíží se skupinami zabezpečení sítě](active-directory-ds-troubleshoot-nsg.md) a [síťové požadavky pro Azure AD Domain Services](active-directory-ds-networking.md).
-  Synchronizace hesel se nikdy nastavení nebo dokončení. Nastavení synchronizace hesel, přečtěte si téma [v tomto článku](active-directory-ds-getting-started-password-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Záloha ve chvíli zálohování

**Zpráva s výstrahou:**

*Spravovaná doména posledního zálohování [Date].*

**Řešení:**

[Kontrola stavu domény](active-directory-ds-check-health.md) pro všechny výstrahy, které mohou indikovat potíže v konfiguraci vaší spravované domény. Problémy s konfigurací v některých případech můžete zablokovat možnost společnosti Microsoft k zálohování vaší spravované domény. Pokud je to možné vyřešit všechny výstrahy, počkejte dvou hodin a vrácení zpět zobrazíte, pokud se po dokončení zálohování.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Pozastavení z důvodu zakázaného předplatného

**Zpráva s výstrahou:**

*Spravovaná doména je pozastavená, protože není aktivní předplatné Azure spojené s doménou.*

**Řešení:**

> [!WARNING]
> Pokud vaše spravovaná doména se pozastavuje delší dobu, je nebezpečí odstraňuje. Doporučujeme co nejdříve vyřešit pozastavení. Další informace najdete v tématu [v tomto článku](active-directory-ds-suspension.md).

Chcete-li obnovit vaše služba [obnovení vašeho předplatného Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable) přidružené k vaší spravované domény.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Pozastavení z důvodu neplatné konfigurace

**Zpráva s výstrahou:**

*Spravovaná doména se pozastavuje z důvodu neplatné konfigurace. Služba bylo možné spravovat, opravovat ani aktualizovat řadiče domény vaší spravované domény po dlouhou dobu.*

**Řešení:**

> [!WARNING]
> Pokud vaše spravovaná doména se pozastavuje delší dobu, je nebezpečí odstraňuje. Doporučujeme co nejdříve vyřešit pozastavení. Další informace najdete v tématu [v tomto článku](active-directory-ds-suspension.md).

[Kontrola stavu domény](active-directory-ds-check-health.md) pro všechny výstrahy, které mohou indikovat potíže v konfiguraci vaší spravované domény. Pokud některý z těchto výstrah vyřešíte to provést. Po obraťte se na podporu, aby znovu aktivovali odběr.


## <a name="contact-us"></a>Kontaktujte nás
Obraťte se na Azure Active Directory Domain Services produktovému týmu a [podělit se o názory, nebo pro podporu](active-directory-ds-contact-us.md).
