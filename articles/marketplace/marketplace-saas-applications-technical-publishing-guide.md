---
title: Azure Marketplace SaaS aplikací technický průvodce publikováním
description: Podrobný průvodce a publikování kontrolní seznamy pro publikování aplikací SaaS na Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: keithcharlie
manager: nunoc
editor: keithcharlie
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: keithcharlie
ms.openlocfilehash: bd6c592f0d3d073ec2a05c8f4636086df01ddb06
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450741"
---
# <a name="saas-applications-offer-publishing-guide"></a>Aplikace SaaS nabízí průvodce publikováním

Aplikace SaaS je publikovat na webu Marketplace pomocí tří různých volání akce: "Kontaktujte mě," "Try it now" a "Získat." Tato příručka vysvětluje tyto tři možnosti, včetně požadavků pro každý. 

## <a name="offer-overview"></a>Přehled nabídky  

Aplikace SaaS jsou k dispozici v obou prodejních míst Azure následující tabulka popisuje aktuální dostupné možnosti:

| Prezentace možnost | Výpis | Příkaz Transact/zkušební verze |  
| --- | --- | --- |  
| AppSource | Ano (Kontaktujte mě) | Ano (Power BI/Dynamics) |
| Azure Marketplace | Ne | Ano (aplikace SaaS) |   

**Seznam:**  Možnost publikování výpisu se skládá z Kontaktujte mě typ nabídky a používá se při účasti úroveň zkušební verze nebo transakce není možné. Výhodou tohoto přístupu je, že umožňuje vydavatelům se řešení na trhu okamžitě začít přijímat potenciálních zákazníků, které je možné zapnout do obchodů pro zvýšení vaší firmy.  
**Zkušební verze nebo transakce:**  Zákazník má povolenou možnost přímo kupovat nebo požádejte o zkušební verzi pro vaše řešení. Zkušební prostředí. proto Zavádíme zvýší úroveň engagement nabízí zákazníkům a umožňuje zákazníkům prozkoumat své řešení před zakoupením. Zkušební prostředí s, budete mít lepší pravděpodobnost povýšení prodejní místa a měli byste očekávat více a více potenciálních zákazníků z zákazníky. Zkušební verze musí obsahovat bezplatná podpora k alespoň po dobu trvání zkušebního období.  

| Nabídky SaaS aplikace | Obchodní požadavky | Technické požadavky |  
| --- | --- | --- |  
| **Kontaktujte nás** | Ano | Ne |  
| **PowerBI / Dynamics** | Ano | Ano (integrace služby Azure AD) |  
| **Aplikace SaaS**| Ano | Ano (integrace služby Azure AD) |     

## <a name="saas-list"></a>Seznam SaaS

Výzvou k akci pro výpis SaaS s žádné testy a žádné funkce fakturace je "Kontakt Me." 

Nemusíte konfigurovat Azure Active Directory na seznam aplikací SaaS. 

|Požadavky  |Podrobnosti  |
|---------|---------|
|Je vaše aplikace SaaS, nabízí  |   Je vaše řešení SaaS nabízí a nabízejí víceklientské produkt SaaS.      |


## <a name="saas-trial"></a>Zkušební verze SaaS

Poskytnout řešení nebo aplikace pomocí bezplatné na try, software jako služba (SaaS) – na základě zkušební verze. Nabídky bezplatné zkušební verze může zobrazovat jako omezeného použití nebo omezené trvání zkušební účet. 


|Požadavky  |Podrobnosti  |
|---------|---------|
|Je vaše aplikace SaaS, nabízí  |   Je vaše řešení SaaS nabízí a nabízejí víceklientské produkt SaaS.      |
|Vaše aplikace je povolená AAD     |   Zákazník bude znovu orientovaného na vaši doménu a bude u zákazníka transact přímo       |


## <a name="saas-trial-technical-requirements"></a>SaaS zkušební verze technické požadavky

Technické požadavky pro aplikace SaaS jsou jednoduché. Vydavatel je vyžadováno pouze možné integrovat se službou Azure Active Directory (Azure AD) k publikování. Integrace Azure AD s aplikacemi je dobře zdokumentovaná a společnost Microsoft poskytuje několik sad SDK a prostředky jak toho dosáhnout.  

Pokud chcete začít, doporučujeme mít předplatné vyhrazenou pro vaše publikování webu Azure Marketplace díky tomu umožňuje izolaci práce z jiných iniciativ. Až to uděláte můžete začít nasazovat aplikace SaaS v tomto předplatném spustit vývojové práce.  

Nejlepší dokumentace ke službě Azure Active Directory, ukázky a doprovodné materiály jsou umístěny na následujících webech: 

* [Příručka pro vývojáře Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integrace s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Plány Azure do budoucna – zabezpečení a identita](https://azure.microsoft.com/roadmap/?category=security-identity)

Výuková videa zkontrolujte následující položky:

* [Ověřování pomocí Azure Active Directory s Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory Identity technické přehledové – část 1 / 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory Identity technické přehledové – část 2 ze 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Vytváření aplikací s Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Microsoft Azure videa, zaměřuje na Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Bezplatné školení Azure Active Directory je k dispozici na  
* [Microsoft Azure pro IT profesionály série: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Kromě toho Azure Active Directory poskytuje lokality aktualizace služby   
* [Aktualizace služby Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Pomocí Azure Active Directory umožníte zkušebních verzí  

Microsoft ověří všechny uživatele Marketplace pomocí Azure AD, proto když ověřený uživatel klikne na svůj výpis zkušební verze na webu Marketplace a přesměruje do vašeho zkušebního prostředí, bez nutnosti můžete zřídit uživatele přímo Další přihlašovacího kroku. Token, který vaše aplikace obdrží z Azure AD během ověřování zahrnuje uživatelské cenné informace, které můžete použít k vytvoření uživatelského účtu ve vaší aplikaci, která vám umožní automatizovat zřizování prostředí a zvýšit pravděpodobnost, že převod. Další informace o tokenu najdete v tématu [ukázka tokeny](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

Pokud chcete povolit ověřování 1 kliknutím do své aplikace nebo zkušební verze pomocí služby Azure AD provádí tyto kroky:  
* Zjednodušuje zkušenosti z webu Marketplace na zkušební verzi.  
* Udržuje chování 'prostředí v rámci produktu' i když se uživatel přesměruje z webu Marketplace k doméně nebo zkušebního prostředí.  
* Snižuje pravděpodobnost zřeknutí na přesměrování, protože není k dispozici další krok přihlásit.  
* Snižuje bariéry nasazení pro velký počet obyvatel uživatelů Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certifikace vašich integrace služby Azure AD pro Marketplace  

Můžete certifikovat několika různými způsoby v závislosti na tom, zda je vaše aplikace jedním tenantem nebo více tenanty, integraci služby Azure AD a, jestli jste ještě na Azure AD jednoduchou federované přihlašování (SSO), nebo již její podporu.  

**Pro víceklientské aplikace:**  

Pokud už podporuje Azure AD, postupujte takto:
1.  Registrace vaší aplikace na webu Azure Portal
2.  Povolte funkci podpory více tenantů v Azure AD a získat zkušební prostředí jedním kliknutím. Podrobnější informace můžete najít [tady](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Pokud jste ještě jednotného přihlašování k federovanému Azure AD, postupujte takto: 
1.  Registrace vaší aplikace na webu Azure Portal
2.  Vývoj jednotné přihlašování s Azure AD používat [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) nebo [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Povolit podporu více tenantů funkce v adresáři AAD. Chcete-li získat zkušební prostředí jedním kliknutím podrobnější informace můžete najít [tady](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Pro jednoho tenanta aplikaci použijte některou z následujících možností:**  
* Přidání uživatelů do vašeho adresáře jako uživatele typu Host pomocí [B2B ve službě Azure](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Ruční zřizování zkušební verze pro zákazníky s použitím "Kontaktujte Me"
* Vývoj na – Zákazník "testovací verze.
* Vytváření víceklientských ukázkovou aplikaci ukázku s jednotným Přihlašováním

## <a name="saas-subscriptions"></a>Předplatná SaaS

Typ nabídky SaaS aplikace použijte k povolení zákazník koupit řešení založené na SaaS, technické jako předplatné. Pro aplikaci SaaS, musí být splněny následující požadavky:
- Ceny a fakturu bez stromové struktury, jsou měsíční sazba za službu.
- Poskytuje metodu pro upgrade nebo službu kdykoli zrušit.
Microsoft je hostitelem transakce commerce. Microsoft účtuje zákazníkovi vaším jménem. Jako předplatné použít vyúčtování aplikaci SaaS, musíte povolit jste rozhraní API pro vlastní předplatné služby správy. Vaše předplatné služby API management musí komunikovat přímo s rozhraními API Azure Resource Manageru. Vaše předplatné služby API management musí podporovat službu zřizování, upgradování a rušení.

| Požadavek | Podrobnosti |  
|:--- |:--- |  
|K fakturaci a měření | Vaše nabídka se účtuje paušální sazbou měsíční. Ceny založené na využití a na základě využití možnosti "true nahoru" v tuto chvíli nepodporují. |  
|Zrušení | Kdykoli je zrušitelný zákazník vaši nabídku. |  
|Cílová stránka transakce | Hostujete Azure značkou transakce cílová stránka kde uživatelé můžou vytvářet a spravovat své účty služby SaaS. |   
| Předplatné rozhraní API | Můžete zpřístupnit služby, které mohou komunikovat s předplatným služby SaaS vytvářet, aktualizovat a odstraňovat uživatelský účet a službu plán. Důležité změny rozhraní API musí podporovat během 24 hodin. Pravidelně se vydá Nekritická změn rozhraní API. |  

## <a name="next-steps"></a>Další postup
Pokud jste tak již neučinili,

- [Zaregistrujte](https://azuremarketplace.microsoft.com/sell) na webu Marketplace.

Pokud budete zaregistrováni a vytvoření nové nabídky nebo pracující na existující,

- [Přihlaste se na portál Cloud Partner](https://cloudpartner.azure.com) k vytvoření nebo dokončení vaší nabídky.
- Zobrazit [nabídky aplikace Azure SaaS](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-offer) Další informace.
