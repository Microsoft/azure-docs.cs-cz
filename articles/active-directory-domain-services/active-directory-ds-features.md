---
title: 'Azure Active Directory Domain Services: Funkce | Microsoft Docs'
description: Funkce služby Azure Active Directory Domain Services
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
ms.topic: article
ms.date: 05/30/2018
ms.author: maheshu
ms.openlocfilehash: 5dd3cde69c6aa36c3d9cb3060dc6deb59ff74a5a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214963"
---
# <a name="azure-ad-domain-services"></a>Azure AD Domain Services
## <a name="features"></a>Funkce
Následující funkce jsou dostupné v Azure AD Domain Services spravované domény.

* **Jednoduché nasazení prostředí:** povolíte Azure AD Domain Services pro svůj adresář Azure AD pomocí několika kliknutí. Spravované domény obsahuje jenom pro cloud uživatelské účty a uživatelské účty synchronizované z místního adresáře.
* **Podpora pro připojení k doméně:** můžete snadno připojení k doméně počítače ve virtuální síti Azure, je k dispozici ve vaší spravované domény. Připojení k doméně prostředí na Windows klient a Server operační systémy funguje bezproblémově proti domén, které jsou obsluhovány pomocí Azure AD Domain Services. Můžete také použít automatizované domény spojení tooling proti takové domény.
* **Jednu doménu instance pro každý adresář Azure AD:** můžete vytvořit jednu doménu služby Active Directory pro každý adresář Azure AD.
* **Vytvoření domény s vlastními názvy:** můžete vytvořit domény s vlastními názvy (například "contoso100.com") pomocí služby Azure AD Domain Services. Můžete použít buď názvy ověřené nebo neověřených domén. Volitelně můžete také vytvořit doménu s příponou předdefinované domény (to znamená, ' *. onmicrosoft.com ") nabízené adresáře Azure AD.
* **Integrované s Azure AD:** není potřeba konfigurovat nebo spravovat replikaci do Azure AD Domain Services. Uživatelské účty, členství ve skupinách a přihlašovací údaje uživatele (hesla) z adresáře služby Azure AD jsou automaticky dostupné v Azure AD Domain Services. Noví uživatelé, skupiny nebo změny atributů z vašeho klienta Azure AD nebo místním adresářem se automaticky synchronizují do Azure AD Domain Services.
* **Ověřování NTLM a Kerberos:** s podporou pro ověřování NTLM a Kerberos, můžete nasazovat aplikace, které jsou závislé na Windows-Integrated ověřování.
* **Použít podnikové přihlašovací údaje a hesla:** hesla pro uživatele ve službě Azure AD klienta pracovat s Azure AD Domain Services. Uživatele můžete používat své podnikové přihlašovací údaje pro připojení k doméně počítače, přihlásit interaktivně nebo přes vzdálené plochy a ověřování na základě spravované domény.
* **Vazba LDAP & LDAP přečíst podpory:** používáte aplikace, které jsou závislé na vazby protokolu LDAP k ověřování uživatelů v doménách, které jsou obsluhovány pomocí Azure AD Domain Services. Kromě toho aplikace, které používají operace čtení protokolu LDAP k dotazu atributy uživatele nebo počítače z adresáře může spolupracovat taky s Azure AD Domain Services.
* **Zabezpečený LDAP (LDAPS):** můžete povolit přístup k adresáři přes zabezpečený LDAP (LDAPS). Zabezpečený LDAP přístup je k dispozici v rámci virtuální sítě ve výchozím nastavení. Však také volitelně můžete umožnit zabezpečený LDAP přístup přes internet.
* **Zásady skupiny:** pro uživatele a počítače můžete použít jeden předdefinovaný objektu zásad skupiny každý kontejnery na vynucování dodržování shody s požadované zásady zabezpečení pro účty uživatelů a počítačů připojených k doméně. Můžete také vytvořit vlastní vlastní objekty zásad skupiny a přiřadit vlastní organizační jednotky pro [správu zásad skupiny](active-directory-ds-admin-guide-administer-group-policy.md).
* **Umožňuje spravovat DNS:** členy skupiny "Správci AAD řadič domény, můžete spravovat DNS vaší spravované domény pomocí známých nástrojů pro správu DNS jako je například modul snap-in konzoly MMC Správa DNS.
* **Vytvořit vlastní organizačních jednotek (OU):** členy skupiny "Správci AAD řadič domény, můžete vytvořit vlastní organizačních jednotek ve spravované doméně. Tito uživatelé jsou udělena úplná oprávnění správce přes vlastní organizační jednotky, takže se můžete přidat nebo odebrat účty služeb, počítačů, skupin atd. v rámci těchto vlastních organizačních jednotek.
* **K dispozici v mnoha oblastech Azure globální:** najdete v článku [služby Azure podle oblasti](https://azure.microsoft.com/regions/#services/) stránky vědět oblastí Azure, ve kterých je dostupná služba Azure AD Domain Services.
* **Vysoká dostupnost:** Azure AD Domain Services nabízí vysokou dostupnost vaší domény. Tato funkce poskytuje záruku vyšší doba provozu služby a odolnost proti chybám. Sledování nabízí integrovanou stavu automatizovat nápravy selhání po roztočený do nové instance k nahrazení chybných instancí a zajistit nepřetržitou služby pro vaši doménu.
* **Ochrana uzamčení účtu AD:** účty uživatelů jsou uzamčena na dobu 30 minut, pokud pět neplatných zadání hesla se používají během 2 minut. Účty jsou automaticky odemknout po 30 minutách.
* **Pomocí nástroje pro správu známé:** známých nástrojů správy systému Windows Server Active Directory jako je například Centrum správy služby Active Directory nebo Active Directory PowerShell můžete použít ke správě spravované domény.
