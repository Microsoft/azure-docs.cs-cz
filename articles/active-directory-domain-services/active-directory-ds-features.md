---
title: 'Azure Active Directory Domain Services: Funkce | Dokumentace Microsoftu'
description: Funkce Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: maheshu
ms.openlocfilehash: a64401792fd034fde98fe1330340b1ffaa7dfcc1
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505408"
---
# <a name="azure-ad-domain-services"></a>Azure AD Domain Services
## <a name="features"></a>Funkce
Následující funkce jsou dostupné v Azure AD Domain Services spravované domény.

* **Jednoduché nasazení prostředí:** můžete povolit Azure AD Domain Services pro svůj adresář Azure AD pomocí několika kliknutí. Vaše spravovaná doména obsahuje uživatelské účty jenom cloudu a uživatelské účty synchronizované z místního adresáře.
* **Podpora pro připojení k doméně:** můžete snadno připojení k doméně počítače ve virtuální síti Azure, je k dispozici ve vaší spravované domény. Připojení k doméně dochází při používání klienta Windows a Server operační systémy funguje bez problémů s doménami, které jsou obsluhovány pomocí Azure AD Domain Services. Můžete také použít automatizované doméně nástrojů proti takové domény.
* **Instance pro jednu doménu každý adresář služby Azure AD:** můžete vytvořit jednu doménu služby Active Directory pro každý adresář služby Azure AD.
* **Vytvoření s vlastními názvy domén:** vytvoříte domén s vlastními názvy (například "contoso100.com") pomocí služby Azure AD Domain Services. Můžete použít buď názvy ověřené nebo neověřených domén. Volitelně můžete také vytvořit doménu s příponou domény integrované (tedy "*. onmicrosoft.com") nabízí adresáře služby Azure AD.
* **Integrovat se službou Azure AD:** není potřeba konfigurovat ani spravovat replikaci do Azure AD Domain Services. Uživatelské účty, členství ve skupinách a přihlašovací údaje uživatele (hesel) z adresáře služby Azure AD jsou automaticky dostupné ve službě Azure AD Domain Services. Noví uživatelé, skupiny nebo změny atributů z vašeho tenanta Azure AD nebo v místním adresáři jsou automaticky synchronizovat s Azure AD Domain Services.
* **Ověřování protokolů NTLM a Kerberos:** s podporou pro ověřování protokolů NTLM a Kerberos, můžete nasadit aplikace, které využívají Windows-Integrated ověřování.
* **Použít podnikové přihlašovací údaje a hesla:** hesla pro uživatele ve službě Azure AD tenant práce s Azure AD Domain Services. Uživatelům můžete pomocí svých podnikových přihlašovacích údajů k připojení k doméně počítače, přihlaste se interaktivně nebo přes vzdálenou plochu a ověřování na základě spravované domény.
* **Vázání protokolu LDAP & LDAP přečíst podpory:** může používat aplikace, které využívají vazby protokolu LDAP k ověřování uživatelů v doménách, které jsou obsluhovány pomocí Azure AD Domain Services. Kromě toho aplikace, které používají operace čtení protokolu LDAP k dotazu uživatele nebo počítač platí následující atributy z adresáře umí pracovat i ve službě Azure AD Domain Services.
* **Protokol Secure LDAP (LDAPS):** můžete povolit přístup k adresáři prostřednictvím zabezpečeného protokolu LDAP (LDAPS). Je k dispozici v rámci virtuální sítě ve výchozím nastavení protokolu Secure LDAP. Můžete však také v případě potřeby povolit protokolu Secure LDAP přes internet.
* **Zásady skupiny:** jednoho integrovaného objektu zásad skupiny každý můžete použít pro uživatele a počítače kontejnery pro vynucování dodržování předpisů s požadované zásady zabezpečení pro uživatelské účty a počítačů připojených k doméně. Můžete vytvořit vlastní vlastní objekty zásad skupiny a přiřadit vlastní organizační jednotky [Správa zásad skupiny](active-directory-ds-admin-guide-administer-group-policy.md).
* **Správa DNS:** členové skupiny "Správci AAD DC" můžete spravovat službu DNS pro spravovanou doménu pomocí známých nástrojů pro správu DNS jako je modul snap-in konzoly MMC Správa DNS.
* **Vytvořit vlastní organizační jednotky (OU):** členové skupiny 'Správci AAD DC' můžete vytvořit vlastní organizační jednotky ve spravované doméně. Tito uživatelé jsou udělena úplná oprávnění správce přes vlastní organizační jednotky, takže se můžete přidávat nebo odebírat účty služeb, počítačů, skupin atd. v rámci těchto vlastní organizační jednotky.
* **K dispozici v mnoha oblastech Azure globální:** najdete v článku [služby Azure podle oblasti](https://azure.microsoft.com/regions/#services/) stránky znát oblastí Azure, ve kterých je dostupná služba Azure AD Domain Services.
* **Vysoká dostupnost:** Azure AD Domain Services poskytuje vysokou dostupnost vaší domény. Tato funkce nabízí záruku vyšší dostupnost služby a odolnost proti selhání. Integrované monitorování nabídky stavu automatické nápravy v případě selhání zapínáním nových instancí nahradit neúspěšné instance a poskytovat další služby pro vaši doménu.
* **Funkce ochrany uzamčení účtu AD:** uživatelské účty jsou uzamčena na dobu 30 minut Pokud pět neplatných zadání hesla se používají během 2 minut. Účty se automaticky odemknout po 30 minutách.
* **Použijte nástroje pro správu známé:** můžete používat známé nástroje správy systému Windows Server Active Directory jako Centrum správy služby Active Directory nebo Active Directory PowerShell ke správě spravované domény.
