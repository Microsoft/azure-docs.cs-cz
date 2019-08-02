---
title: Technický průvodce publikováním aplikací Azure Marketplace SaaS
description: Podrobný průvodce a publikování kontrolních seznamů pro publikování SaaSch aplikací pro Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
author: keithcharlie
ms.service: marketplace
ms.topic: article
ms.date: 07/09/2018
ms.author: kevidal
ms.openlocfilehash: 92c3452e07dee126666e6ee1fe0c46b1f8f6dfa4
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876533"
---
# <a name="saas-applications-offer-publishing-guide"></a>Průvodce publikováním nabídky aplikací SaaS

Aplikace SaaS se dají publikovat na webu Marketplace se třemi různými voláními akce: Kontaktujte mě, vyzkoušet si ho hned a získat hned. Tato příručka vysvětluje tyto tři možnosti, včetně požadavků pro každý z nich. 

## <a name="offer-overview"></a>Přehled nabídky  

SaaS aplikace jsou k dispozici v Azure prodejní místa v následující tabulce jsou popsány aktuální dostupné možnosti:

| Prezentace – možnost | Fondů | Zkušební verze/transakce |  
| --- | --- | --- |  
| AppSource | Ano (kontaktujte mě) | Ano (PowerBI/Dynamics) |
| Azure Marketplace | Ne | Ano (aplikace SaaS) |   

**Seznamu**  Možnost publikování výpisu se skládá z nabídky typu kontakt mi a používá se v případě, že účast na úrovni zkušební verze nebo transakce není proveditelná. Výhodou tohoto přístupu je to, že vydavatelům v rámci trhu umožňuje okamžitě začít přijímat potenciální zákazníky, kteří se můžou rozrůstat, aby zvýšili vaši firmu.  
**Zkušební verze/transakce:**  Zákazník má možnost si přímo koupit nebo požádat o zkušební verzi vašeho řešení. Díky zkušebnímu prostředí se zvýší úroveň zapojení nabízená zákazníkům a umožní zákazníkům prozkoumat vaše řešení před nákupem. V rámci zkušebního prostředí budete mít lepší šanci na zvýšení úrovně v prodejní místa a měli byste očekávat více a mnohem víc a lepších příležitostí od zákaznických rezervací. Zkušební verze musí zahrnovat bezplatnou podporu aspoň po dobu trvání zkušebního období.  

| Nabídka aplikací pro SaaS | Obchodní požadavky | Technické požadavky |  
| --- | --- | --- |  
| **Kontaktujte nás** | Ano | Ne |  
| **PowerBI/Dynamics** | Ano | Ano (Integrace Azure AD) |  
| **Aplikace SaaS**| Ano | Ano (Integrace Azure AD) |     

## <a name="saas-list"></a>Seznam SaaS

Volání akce pro výpis SaaS bez zkušební verze a žádné funkce fakturace není "kontaktujte mě." 

Nemusíte konfigurovat Azure Active Directory k vypsání aplikace SaaS. 

|Požadavky  |Podrobnosti  |
|---------|---------|
|Vaše aplikace je nabídka SaaS  |   Vaše řešení je SaaS nabídka a nabízí SaaS produkt s více klienty.      |


## <a name="saas-trial"></a>Zkušební verze SaaS

Poskytnete řešení nebo aplikaci s využitím bezplatné zkušební verze SaaS (software jako služba). Nabídky bezplatné zkušební verze se můžou prezentovat jako zkušební účet omezeného použití nebo s omezeným trváním. 


|Požadavky  |Podrobnosti  |
|---------|---------|
|Vaše aplikace je nabídka SaaS  |   Vaše řešení je SaaS nabídka a nabízí SaaS produkt s více klienty.      |
|Vaše aplikace je povolená AAD.     |   Zákazník se znovu nasměruje do vaší domény a vy budete s tímto zákazníkem počítat přímo.       |


## <a name="saas-trial-technical-requirements"></a>Technické požadavky na zkušební verzi SaaS

Technické požadavky pro aplikace SaaS jsou jednoduché. Vydavatelé se musí integrovat jenom s Azure Active Directory (Azure AD), aby se publikovaly. Integrace Azure AD s aplikacemi je dobře dokumentováná a Microsoft poskytuje více sad SDK a prostředků k tomu.  

Pro začátek doporučujeme, abyste měli k dispozici předplatné vyhrazené pro publikování Azure Marketplace, což vám umožní izolovat práci z jiných iniciativ. Až to uděláte, můžete v tomto předplatném začít nasazovat aplikaci SaaS, aby se spustila vývojová práce.  

Nejlepší Azure Active Directory dokumentaci, ukázky a pokyny najdete v následujících lokalitách: 

* [Azure Active Directory příručka pro vývojáře](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integrace s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Plán Azure – zabezpečení a identita](https://azure.microsoft.com/roadmap/?category=security-identity)

Výuková videa najdete v následujících příkladech:

* [Ověřování Azure Active Directory pomocí Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory technické přehledy identity – část 1 ze 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory technické přehledy identity – část 2 ze 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Sestavování aplikací pomocí Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Microsoft Azure videa zaměřená na službu Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Bezplatné školení Azure Active Directory k dispozici na adrese  
* [Microsoft Azure pro řadu obsahu pro IT profesionály: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Kromě toho Azure Active Directory poskytuje lokalitu pro kontrolu aktualizací služby   
* [Aktualizace služby Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Povolení zkušebních verzí pomocí Azure Active Directory  

Microsoft ověřuje všechny uživatele na webu Marketplace pomocí Azure AD, takže když ověřený uživatel klikne na webu Marketplace na základě zkušebního výpisu a přesměruje se na vaše zkušební prostředí, můžete uživatele zřídit přímo do zkušební verze bez nutnosti Další krok přihlašování Token, který vaše aplikace obdrží z Azure AD během ověřování, zahrnuje cenné uživatelské informace, pomocí kterých můžete v aplikaci vytvořit uživatelský účet, který vám umožní automatizovat prostředí pro zřizování a zvýšit pravděpodobnost převodu. Další informace o tokenu najdete v tématu [ukázkové tokeny](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

Pomocí Azure AD můžete pro aplikaci nebo zkušební verzi povolit ověřování 1 kliknutím na toto:  
* Zjednodušuje uživatelské prostředí z webu Marketplace až po zkušební období.  
* Bez ohledu na to, jak se uživatel přesměruje z webu Marketplace do vaší domény nebo zkušebního prostředí, udržuje dojem "prostředí" v rámci produktu.  
* Snižuje pravděpodobnost zrušení přesměrování, protože není k dispozici další krok přihlášení.  
* Omezuje překážky nasazení pro velké populace uživatelů Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certifikace integrace služby Azure AD pro Marketplace  

V závislosti na tom, jestli je vaše aplikace založená na jednom tenantovi nebo víceklientské úrovni a jestli už ji nepodporujete, můžete svou integraci Azure AD certifikovat několika různými způsoby.  

**Pro aplikace s více klienty:**  

Pokud už Azure AD podporujete, udělejte toto:
1.  Registrace aplikace v Azure Portal
2.  Ve službě Azure AD povolte funkci podpory více tenantů, abyste získali zkušební prostředí jedním kliknutím. Konkrétnější informace najdete [tady](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Pokud pro federované jednotné přihlašování Azure AD začínáte, postupujte takto: 
1.  Registrace aplikace v Azure Portal
2.  K vývoji jednotného přihlašování použijte Azure AD pomocí [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) nebo [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Povolení podpory více tenantů v AAD Chcete-li získat další informace o zkušebním prostředí jedním kliknutím, najdete je [tady](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**V případě aplikace pro jednoho tenanta použijte některou z následujících možností:**  
* Přidání uživatelů do adresáře jako uživatelů typu Host pomocí [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Ruční zřízení zkušebních verzí pro zákazníky pomocí ' kontaktujte mě '
* Vývoj testovacích jednotek pro jednotlivé zákazníky
* Sestavte ukázkovou ukázkovou aplikaci pro více tenantů pomocí jednotného přihlašování.

## <a name="saas-subscriptions"></a>SaaS předplatná

Pomocí typu nabídky aplikace SaaS můžete zákazníkům umožnit, aby si koupili své technické řešení založené na SaaS jako předplatné. Pro vaši aplikaci SaaS musí být splněné následující požadavky:
- Price a účtuje službu paušální měsíční sazbou.
- Poskytněte způsob, jak službu kdykoli upgradovat nebo zrušit.
Společnost Microsoft hostuje obchodní transakci. Společnost Microsoft účtuje vaše zákazníky vaším jménem. Pokud chcete jako předplatné použít aplikaci SaaS, musíte si povolit vlastní rozhraní API služby pro správu předplatného. Rozhraní API služby pro správu předplatného musí komunikovat přímo s rozhraními API Azure Resource Manager. Vaše rozhraní API služby pro správu předplatného musí podporovat zřizování služeb, upgrade a zrušení.

| Požadavek | Podrobnosti |  
|:--- |:--- |  
|Fakturace a měření | Cena vaší nabídky se účtuje za měsíční paušální sazbu. V tuto chvíli se nepodporují funkce pro ocenění na základě využití a možnosti "true-up" založené na využití. |  
|Výmaz | Vaši nabídku nemůže zákazník kdykoli zrušit. |  
|Cílová stránka transakce | Můžete hostovat cílovou stránku transakce se značkou Azure, kde můžou uživatelé vytvářet a spravovat jejich účet služby SaaS. |   
| Rozhraní API pro předplatné | Vystavíte službu, která může komunikovat s předplatným SaaS k vytváření, aktualizaci a odstraňování uživatelských účtů a plánu služeb. Kritické změny rozhraní API musí být podporované do 24 hodin. Pravidelně se vydávají nekritické změny rozhraní API. |  

>[!Note]
>Výslovný souhlas kanálu pro partnery Cloud Solution Providers (CSP) je teď k dispozici.  Další informace o marketingu vaší nabídky prostřednictvím kanálů partnerů Microsoft CSP najdete v tématu [Poskytovatelé cloudových řešení](./cloud-solution-providers.md) .

## <a name="next-steps"></a>Další postup
Pokud jste to ještě neudělali,

- [](https://azuremarketplace.microsoft.com/sell) Zaregistrujte se na webu Marketplace.

Pokud jste zaregistrováni a vytváříte novou nabídku nebo pracujete na stávajícím typu,

- [Přihlaste se k portál partnerů cloudu](https://cloudpartner.azure.com) a vytvořte nebo dokončete vaši nabídku.
- Další informace najdete v [nabídce aplikace Azure SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer) .
