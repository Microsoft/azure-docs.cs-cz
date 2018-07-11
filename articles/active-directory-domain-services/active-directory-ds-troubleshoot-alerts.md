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
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: ergreenl
ms.openlocfilehash: 360c6c98227e52f0540b00ef136888d3d143b9fb
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951070"
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
| AADDS500 | *Spravovaná doména poslední synchronizace s Azure AD na [date]. Uživatelé možná nebudete moct přihlásit ve spravované doméně nebo členství ve skupinách možná není synchronizována s Azure AD.* | [K synchronizaci nedošlo nějakou dobu](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *Spravovaná doména posledního zálohování [Date].* | [Záloha ve chvíli zálohování](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *Certifikát protokolu secure LDAP pro spravovanou doménu vyprší [date]].* | [Certifikát protokolu secure LDAP vyprší](active-directory-ds-troubleshoot-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
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

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Synchronizace nebyla dokončena v while

**Zpráva s výstrahou:**

*Spravovaná doména poslední synchronizace s Azure AD na [date]. Uživatelé možná nebudete moct přihlásit ve spravované doméně nebo členství ve skupinách možná není synchronizována s Azure AD.*

**Řešení:**

[Kontrola stavu domény](active-directory-ds-check-health.md) pro všechny výstrahy, které mohou indikovat potíže v konfiguraci vaší spravované domény. Problémy s konfigurací v některých případech může blokovat Microsoftu neblokujete možnost Synchronizovat vaši spravovanou doménu. Pokud je to možné vyřešit všechny výstrahy, počkejte dvou hodin a vrácení zpět zobrazíte, pokud byla dokončena synchronizace.

Tady je několik běžných příčin, proč se synchronizace zastaví ve spravovaných domén:
- Připojení k síti je blokován ve spravované doméně. Další informace o kontrole vaší síti pro problémy, přečtěte si, jak k [řešení potíží se skupinami zabezpečení sítě](active-directory-ds-troubleshoot-nsg.md) a [síťové požadavky pro Azure AD Domain Services](active-directory-ds-networking.md).
-  Synchronizace hesel se nikdy nastavení nebo dokončení. Nastavení synchronizace hesel, přečtěte si téma [v tomto článku](active-directory-ds-getting-started-password-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Zálohu záloha nějakou dobu

**Zpráva s výstrahou:**

*Spravovaná doména posledního zálohování [Date].*

**Řešení:**

[Kontrola stavu domény](active-directory-ds-check-health.md) pro všechny výstrahy, které mohou indikovat potíže v konfiguraci vaší spravované domény. Problémy s konfigurací v některých případech může blokovat Microsoftu neblokujete možnost Synchronizovat vaši spravovanou doménu. Pokud je to možné vyřešit všechny výstrahy, počkejte dvou hodin a vrácení zpět zobrazíte, pokud byla dokončena synchronizace.


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
