---
title: 'Azure Active Directory Domain Services: Funkce | Microsoft Docs'
description: Funkce Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: e0f38ed8367f076c9f9c4da77440bb3e9603d514
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234212"
---
# <a name="features-provided-by-azure-active-directory-domain-services"></a>Funkce poskytované nástrojem Azure Active Directory Domain Services

V Azure AD Domain Services spravovaných doménách jsou k dispozici následující funkce.

* **Možnosti jednoduchého nasazení:** Azure AD Domain Services pro adresář služby Azure AD můžete povolit pomocí několika kliknutí. Vaše spravovaná doména zahrnuje jenom cloudové uživatelské účty a uživatelské účty synchronizované z místního adresáře.
* **Podpora připojení k doméně:** Počítače ve virtuální síti Azure, ke kterým je vaše spravovaná doména dostupná, můžete snadno snadno připojit. Prostředí pro připojení k doméně v klientských a serverových operačních systémech Windows funguje bez problémů s doménami, které jsou obsluhované pomocí Azure AD Domain Services. K takovým doménám můžete použít taky automatizované nástroje pro připojení k doméně.
* **Jedna instance domény na adresář služby Azure AD:** Pro každý adresář služby Azure AD můžete vytvořit jednu doménu služby Active Directory.
* **Vytváření domén s vlastními názvy:** Pomocí Azure AD Domain Services můžete vytvořit domény s vlastními názvy (například ' contoso100.com '). Můžete použít buď ověřené, nebo neověřené názvy domén. Volitelně můžete také vytvořit doménu s integrovanou příponou domény (tj. "*. onmicrosoft.com"), kterou nabízí váš adresář služby Azure AD.
* **Integrace se službou Azure AD:** Nemusíte konfigurovat ani spravovat replikaci na Azure AD Domain Services. Uživatelské účty, členství ve skupinách a přihlašovací údaje uživatele (hesla) z adresáře Azure AD jsou automaticky dostupné v Azure AD Domain Services. Noví uživatelé, skupiny nebo změny atributů z vašeho tenanta Azure AD nebo místního adresáře se automaticky synchronizují s Azure AD Domain Services.
* **Ověřování protokolem NTLM a Kerberos:** Díky podpoře ověřování protokolem NTLM a protokolu Kerberos můžete nasazovat aplikace, které spoléhají na ověřování integrované v systému Windows.
* **Použijte své podnikové přihlašovací údaje nebo hesla:** Hesla pro uživatele v tenantovi Azure AD fungují s Azure AD Domain Services. Uživatelé můžou používat své podnikové přihlašovací údaje k počítačům připojeným k doméně, přihlašovat se interaktivně nebo přes vzdálenou plochu a ověřovat vůči spravované doméně.
* **Vazba LDAP & podpora čtení LDAP:** Pomocí aplikací, které využívají vazby LDAP, můžete ověřovat uživatele v doménách, které se obsluhují Azure AD Domain Services. Kromě toho můžou aplikace, které používají operace čtení protokolu LDAP k dotazování atributů uživatele nebo počítače z adresáře, fungovat i na Azure AD Domain Services.
* **Protokol Secure LDAP (LDAPs):** Můžete povolit přístup k adresáři přes zabezpečený protokol LDAP (LDAPs). Ve výchozím nastavení je ve virtuální síti k dispozici přístup protokol Secure LDAP. Volitelně můžete také povolit zabezpečený přístup pomocí protokolu LDAP přes Internet.
* **Zásady skupiny:** Pro zajištění dodržování předpisů pomocí požadovaných zásad zabezpečení u uživatelských účtů a počítačů připojených k doméně můžete použít jeden vestavěný objekt zásad skupiny pro všechny kontejnery uživatelů a počítačů. Můžete také vytvořit vlastní objekty zásad skupiny a přiřadit jim vlastní organizační jednotky pro [správu zásad skupiny](manage-group-policy.md).
* **Správa DNS:** Členové skupiny Správci AAD DC můžou spravovat DNS pro spravovanou doménu pomocí známých nástrojů pro správu DNS, jako je modul snap-in konzoly MMC Správa DNS.
* **Vytvořit vlastní organizační jednotky (OU):** Členové skupiny Správci AAD DC můžou vytvářet vlastní organizační jednotky ve spravované doméně. Těmto uživatelům jsou udělena úplná oprávnění správce pro vlastní organizační jednotky, aby mohli přidat nebo odebrat účty služeb, počítače, skupiny atd. v těchto vlastních organizačních jednotkách.
* **K dispozici v mnoha globálních oblastech Azure:** Na stránce [Služby Azure podle oblasti](https://azure.microsoft.com/regions/#services/) se dozvíte, pro jaké oblasti je dostupná služba Azure AD Domain Services.
* **Vysoká dostupnost:** Azure AD Domain Services nabízí vysokou dostupnost vaší domény. Tato funkce nabízí záruku větší provozní doby provozu a odolnosti proti chybám. Integrované sledování stavu nabízí automatizovanou nápravu před selháním tím, že zachází nové instance, aby nahradily neúspěšné instance a poskytovaly nepřetržitou službu pro vaši doménu.
* **Ochrana při uzamčení účtu AD:** Pokud se během 2 minut použije pět neplatných hesel, účty uživatelů se zablokují na 30 minut. Účty se po 30 minutách automaticky odemkní.
* **Používejte známé nástroje pro správu:** Ke správě spravovaných domén můžete použít známé nástroje pro správu služby Active Directory systému Windows Server, jako je Centrum správy služby Active Directory nebo prostředí PowerShell služby Active Directory.
