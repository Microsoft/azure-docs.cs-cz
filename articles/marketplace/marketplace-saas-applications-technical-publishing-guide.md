---
title: Průvodce technickým publikováním aplikací Azure Marketplace SaaS
description: Podrobný průvodce a publikování kontrolních seznamů pro publikování aplikací SaaS na Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
author: keithcharlie
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: kevidal
ms.openlocfilehash: 2c1cb755b62812336a306994f6820573130815e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288355"
---
# <a name="saas-applications-offer-publishing-guide"></a>Průvodce publikováním nabídek aplikací SaaS

Aplikace SaaS lze publikovat na trhu se třemi různými výzvami k akci: "Kontaktujte mě", "Zkuste to teď" a "Get it Now." Tato příručka vysvětluje tyto tři možnosti, včetně požadavků pro každou z nich. 

## <a name="offer-overview"></a>Přehled nabídky  

Aplikace SaaS jsou dostupné v obou azure storech Následující tabulka popisuje aktuální dostupné možnosti:

| Možnost výkladní skříně | Výpis | Zkušební verze/transakt |  
| --- | --- | --- |  
| AppSource | Ano (Kontaktujte mě) | Ano (PowerBI/Dynamics) |
| Azure marketplace | Ne | Ano (Aplikace SaaS) |   

**Seznam:**  Možnost výpisu publikování se skládá z typu nabídky Kontaktujte mě a používá se, když účast na zkušební verzi nebo na úrovni transakce není proveditelná. Výhodou tohoto přístupu je, že umožňuje vydavatelům s řešením na trhu okamžitě začít přijímat potenciální zákazníky, které lze přeměnit na nabídky pro zvýšení vašeho podnikání.  
**Zkušební verze/transakce:**  Zákazník má možnost přímo zakoupit nebo požádat o zkušební verzi vašeho řešení. Poskytování zkušební verze zvyšuje úroveň zapojení nabízených zákazníkům a umožňuje zákazníkům prozkoumat vaše řešení před nákupem. Díky zkušebnímu prostředí budete mít větší šanci na propagaci ve výkladních skříních a měli byste očekávat stále bohatší zájemce ze zapojení zákazníků. Zkoušky musí zahrnovat bezplatnou podporu alespoň po dobu zkušební doby.  

| Nabídka aplikací SaaS | Obchodní požadavky | Technické požadavky |  
| --- | --- | --- |  
| **Kontaktujte nás** | Ano | Ne |  
| **PowerBI / Dynamika** | Ano | Ano (integrace Azure AD) |  
| **Aplikace SaaS**| Ano | Ano (integrace Azure AD) |     

## <a name="saas-list"></a>Seznam SaaS

Výzva k akci pro výpis SaaS bez zkušební verze a bez funkce fakturace je "Kontaktujte mě". 

Není nutné konfigurovat službu Azure Active Directory tak, aby vypisovala aplikaci SaaS. 

|Požadavky  |Podrobnosti  |
|---------|---------|
|Vaše aplikace je nabídka SaaS  |   Vaše řešení je nabídka SaaS a vy nabízíte víceklientský produkt SaaS.      |


## <a name="saas-trial"></a>SaaS Trial

Řešení nebo aplikaci poskytujete pomocí bezplatné zkušební verze založené na softwaru jako službě (SaaS). Bezplatné zkušební nabídky mohou být prezentovány jako zkušební účet s omezeným použitím nebo s omezenou dobou trvání. 


|Požadavky  |Podrobnosti  |
|---------|---------|
|Vaše aplikace je nabídka SaaS  |   Vaše řešení je nabídka SaaS a vy nabízíte víceklientský produkt SaaS.      |
|Vaše aplikace je povolená pro aad     |   Zákazník bude přesměrován do vaší domény a vy budete jednat přímo se zákazníkem       |


## <a name="saas-trial-technical-requirements"></a>SaaS Trial Technické požadavky

Technické požadavky na aplikace SaaS jsou jednoduché. Vydavatelé se musí integrovat jenom s Azure Active Directory (Azure AD), které mají být publikovány. Integrace Azure AD s aplikacemi je dobře zdokumentována a Microsoft poskytuje více sad SDK a prostředků k dosažení tohoto cíle.  

Chcete-li začít, doporučujeme, abyste měli předplatné určené pro publikování na Azure Marketplace, což vám umožní izolovat práci od jiných iniciativ. Jakmile to uděláte, můžete začít nasazovat aplikaci SaaS v tomto předplatném a spustit vývojové práce.  

Nejlepší dokumentace, ukázky a pokyny služby Azure Active Directory jsou umístěny na následujících webech: 

* [Průvodce vývojářem služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integrace s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Plán Azure – zabezpečení a identita](https://azure.microsoft.com/roadmap/?category=security-identity)

Výukové video lekce najděte v následujících postupech:

* [Ověřování azure active directory s Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Technická instruktáž identity služby Azure Active Directory – část 1 ze 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Technická instruktáž identity služby Azure Active Directory – část 2 ze 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Vytváření aplikací pomocí služby Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Videa Microsoft Azure zaměřená na službu Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Bezplatné školení služby Azure Active Directory je k dispozici na adrese  
* [Řada obsahu Microsoft Azure pro IT profesionály: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Služba Azure Active Directory navíc poskytuje web pro kontrolu aktualizací služby.   
* [Aktualizace služby Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Povolení zkušebních verzí pomocí služby Azure Active Directory  

Microsoft ověřuje všechny uživatele Marketplace pomocí Azure AD, takže když ověřený uživatel klikne na zkušební verzi na Marketplace a bude přesměrován do vašeho zkušebního prostředí, můžete uživatele zřídit přímo do zkušební verze, aniž byste museli další krok přihlášení. Token, který vaše aplikace obdrží z Azure AD během ověřování obsahuje cenné informace o uživateli, které můžete použít k vytvoření uživatelského účtu ve vaší aplikaci, což vám umožní automatizovat zřizování a zvýšit pravděpodobnost konverze. Další informace o tokenu naleznete v [tématu Ukázkové tokeny](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

Použití Azure AD k povolení ověřování jedním kliknutím do vaší aplikace nebo zkušební verze dělá následující akce:  
* Zjednodušuje zákaznickou zkušenost od Marketplace po zkušební verzi.  
* Udržuje pocit "prostředí v produktu", i když je uživatel přesměrován z Marketplace do vaší domény nebo zkušebního prostředí.  
* Snižuje pravděpodobnost opuštění při přesměrování, protože neexistuje další krok přihlášení.  
* Snižuje bariéry nasazení pro velké množství uživatelů Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certifikace integrace Azure AD pro Marketplace  

Certifikujte integraci Azure AD několika různými způsoby v závislosti na tom, jestli je vaše aplikace jednoklientská nebo víceklientská a jestli jste s Azure AD federovaným jednotném přihlašování (SSO) noví, nebo už ji podporujete.  

**Pro víceklientské aplikace:**  

Pokud už azure ad podporujete, postupujte takto:
1.  Registrace aplikace na webu Azure Portal
2.  Povolte funkci podpory více klienta ve službě Azure AD, abyste získali zkušební prostředí s jedním kliknutím. Konkrétnější informace naleznete [zde](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Pokud s azure ad federovaným přiřazovaným přihlašovaným zabezpečením tečune, postupujte takto: 
1.  Registrace aplikace na webu Azure Portal
2.  Vývoj přihlížení pomocí služby Azure AD pomocí [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) nebo [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Povolte funkci podpory více klientů v AAD, abyste získali zkušební prostředí "jedním kliknutím", více konkrétních informací naleznete [zde](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Pro aplikaci s jedním tenantem použijte některou z následujících možností:**  
* Přidání uživatelů do adresáře jako uživatelů typu Host pomocí [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Ruční zřizování zkoušek pro zákazníky pomocí "Kontaktujte mě"
* Vývoj "testovací jízdy" pro zákazníka
* Vytvoření ukázkové aplikace pro více klientů pomocí služby SSO

## <a name="saas-subscriptions"></a>Odběry SaaS

Použijte typ nabídky aplikace SaaS, který zákazníkovi umožní koupit si technické řešení založené na SaaS jako předplatné. Pro vaši aplikaci SaaS musí být splněny následující požadavky:
- Cena a faktura služby v bytě (měsíčně nebo ročně), nebo na cenu pro jednotlivé uživatele.
- Zadejte metodu upgradu nebo zrušení služby kdykoli.
Společnost Microsoft je hostitelem obchodní transakce. Společnost Microsoft účtuje zákazníkovi vaším jménem. Chcete-li nabídnout aplikaci SaaS jako předplatné, musíte integrovat s api plnění SaaS.  Vaše služba musí podporovat zřizování, upgrade a zrušení.

| Požadavek | Podrobnosti |  
|:--- |:--- |  
|Fakturace a měření | Vaše nabídka je oceněna na základě cenového modelu, který vyberete před publikováním (paušální sazba nebo uživatel).  Pokud používáte model paušální sazby, můžete volitelně zahrnout další dimenze používané k nabíjení zákazníků za použití, které není zahrnuto v paušální sazbě. |  
|Zrušení | Vaši nabídku zákazník kdykoli zruší. |  
|Vstupní stránka transakce | Hostujete vstupní stránku transakcí pod značkou Azure, kde mohou uživatelé vytvářet a spravovat svůj účet služby SaaS. |   
| Rozhraní API předplatného | Zpřístupníte službu, která může pracovat s předplatným SaaS, a vytvořit, aktualizovat a odstranit uživatelský účet a plán služeb. Kritické změny rozhraní API musí být podporovány do 24 hodin. Nekritické změny rozhraní API budou vydávány pravidelně. |  

>[!Note]
>Přihlášení partnerského kanálu zprostředkovatelů cloudových řešení (CSP) je nyní k dispozici.  Další informace o marketingu vaší nabídky prostřednictvím partnerských kanálů Microsoft CSP najdete v tématu [Poskytovatelé cloudových řešení.](./cloud-solution-providers.md)

## <a name="next-steps"></a>Další kroky
Pokud jste tak ještě neučinili,

- [Zaregistrujte se](https://azuremarketplace.microsoft.com/sell) na trhu.

Pokud jste zaregistrováni a vytváříte novou nabídku nebo pracujete na stávající nabídce,

- [Přihlaste se na portál cloudových partnerů](https://cloudpartner.azure.com) a vytvořte nebo dokončete svou nabídku.
- Další informace najdete v [nabídce aplikace Azure SaaS.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer)
