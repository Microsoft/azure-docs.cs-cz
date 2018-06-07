---
title: Technical aplikací SaaS Azure Marketplace publikování průvodce
description: Podrobný průvodce a publikování kontrolní seznamy pro publikování aplikací SaaS do Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ms.author: pabutler
ms.openlocfilehash: 2ac8119e36843e38e334fb5772ea4ade9962b4f9
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809467"
---
# <a name="saas-applications-technical-publishing-guide"></a>Aplikace SaaS, které se technické publikování průvodce

Vítá vás technická aplikací SaaS Azure Marketplace publikování průvodce. Tato příručka je určená ke candidate a existující vydavatelů seznam svých aplikací a služeb v Azure Marketplace pomocí aplikace SaaS nabídky. 

Lépe pochopit, jak publikovat na nabídku SaaS, je tato příručka rozdělené do následujících částí:
* Přehled nabídky
* Obchodní požadavky
* Technické požadavky
* Proces publikování
* Pomocí služby Azure Active Directory povolit zkušební verze
* Certifikuje svoji integraci služby Azure AD pro Marketplace.

## <a name="offer-overview"></a>Přehled nabídky  

Aplikace SaaS jsou k dispozici v obou obchodní poutače Azure následující tabulka popisuje aktuální dostupné možnosti:

| StoreFront možnost | Výpis | Příkaz Transact nebo zkušební verze |  
| --- | --- | --- |  
| AppSource | Ano (mě kontaktovat) | Ano (PowerBI nebo Dynamics) |
| Azure Marketplace | Ne | Ano (SaaS aplikace) |   

**Výpis:** možnost publikování seznamu se skládá z a obraťte se na mě typ nabídky a používá se při zapojení úrovni zkušební verze nebo transakce není možné. Výhodou tohoto přístupu je, že umožňuje vydavatelé s řešení na trhu pro okamžitě začne zájemce, které mohou být změněny na nabídky ke zvýšení vaší firmy.  
**Zkušební verze nebo Transact:** zákazník má možnost přímo koupit nebo žádostí o zkušební verze pro vaše řešení. Poskytuje prostředí zkušební verze zvyšuje úroveň engagement nabízí zákazníkům a umožňuje zákazníkům prozkoumat řešení než kdybyste kupovali. Zkušební verze prostředí budete mít lepší pravděpodobnost povýšení v obchodní poutače a byste měli očekávat další a bohatší zájemců od zákazníků oznámeních podporujících zapojení uživatelů. Zkušební verze musí zahrnovat bezplatnou podporou aspoň po dobu trvání zkušebního období.  

| Nabídka aplikace SaaS | Obchodní požadavky | Technické požadavky |  
| --- | --- | --- |  
| **Kontaktujte nás** | Ano | Ne |  
| **PowerBI nebo Dynamics** | Ano | Ano (integrace Azure AD) |  
| **Aplikace SaaS**| Ano | Ano (integrace Azure AD) |     

Další informace o obchodní poutače Marketplace a popis jednotlivých možností publikování najdete v tématu [Marketplace vydavatele průvodce](https://aka.ms/sellerguide) a [možnosti publikování](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#select-a-publishing-option).

## <a name="business-requirements"></a>Obchodní požadavky
SaaS nabízejí obchodní požadavky můžete dokončit paralelně s technickými požadavky. Většina obchodních požadavků a informace se shromažďují při vytváření této nabídky SaaS v cloudu portál pro partnery. Obchodní požadavky jsou následující: 
* Kterým Odsouhlasíte zásady zapojení
* Integrace s Microsoft 
* Určení cílové skupiny nabídka
* Definice a určení správy realizace, který se má použít
* Nastavení zásad ochrany osobních údajů a podmínky použití
* Definování kontakty podpory  

Další informace můžete najít v tématu [požadavky pro publikování na webu marketplace](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#prerequisites-for-marketplace-publishing)

## <a name="technical-requirements"></a>Technické požadavky

Technické požadavky pro SaaS aplikace jsou jednoduché. Vydavatelé jsou potřeba jenom ke integraci s Azure Active Directory (Azure AD) k publikování. Integrace služby Azure AD s aplikacemi dobře zdokumentovat a společnost Microsoft poskytuje několik sady SDK a prostředky toho chcete dosáhnout.  

Pokud chcete spustit, doporučujeme, abyste měli předplatné vyhrazené pro publikování vašeho webu Azure Marketplace umožňují izolovat práce z jiné iniciativy. Poté je můžete začít nasazovat aplikace SaaS v tomto předplatném zahájíte vývojové práci.  

Nejlepší dokumentaci služby Azure Active Directory, ukázky a pokyny jsou umístěny na následující servery: 

* [Příručka pro vývojáře Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integrace se službou Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Průvodce Azure – zabezpečení a identita](https://azure.microsoft.com/roadmap/?category=security-identity)

Výukové video zkontrolujte následující položky:

* [Ověřování služby Azure Active Directory s Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory identitu technické přehledové – část 1 / 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory identitu technické přehledové - část 2 / 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Vytváření aplikací s Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Microsoft Azure videa zaměřené na službě Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Volné školení Azure Active Directory je k dispozici na  
* [Microsoft Azure pro IT profesionály obsahu řady: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Kromě toho Azure Active Directory poskytuje lokalitou a kontrolovat aktualizace služby   
* [Aktualizace služby Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

Pro podporu můžete použít v následujících zdrojích informací:
* [Fóra na webu MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="publishing-process"></a>Proces publikování

SaaS procesu publikování má technická i obchodní kroky.  Většinu činností, které se provádí vývoj a integrace Azure Active Directory stačí paralelně s práce potřebné ke splnění podnikové požadavky nabídku. Hromadné jaké obchodní požadavky jsou součástí konfigurace nabídka aplikace SaaS portál pro partnery cloudu.  
Následující diagram znázorňuje, že hlavní publikování kroky pro zkušební verzi jazyka Transact nabídnout:  

![Postup publikování SaaS](./media/marketplace-saas-applications-technical-publishing-guide/saaspublishingsteps.png)  

Následující tabulka popisuje všechny publikování hlavní kroky:  

| Publikování krok | Popis |   
| --- | --- |  
| **Vytvoření aplikace SaaS** | Přihlaste se k portálu pro partnery cloudu, vyberte **nový**a pak vyberte **aplikace SaaS** nabízejí. |  
| **Vytvoření integraci s Azure AD** | Postupujte podle technické požadavky popsané v předchozí části integrovat vaši SaaS nabídky s Azure AD. |  
| **Nabídka nastavení**| Zadejte všechny SaaS nabídka počáteční informace. Nabízejí ID a název nabízejí chcete použít. |     
| **Technické informace o sady** | Zadejte technické informace o nabídku. Pro aplikace SaaS je vyžadován identifikátor URI a typ nabídky na pořízení tlačítka (Free, záznam nebo kontaktujte mi) na řešení. |  
| **Test Drive(Optional)** | Toto je volitelné druh zkušební verzi, hlavně pro jiné typy z Marketplace nabízí potřeba. Umožňuje vám získat zkušební verze nasazené v rámci předplatných vydavatele oproti koncového zákazníka. |  
| **Nastavit Storefront materiály nabídka**| V této části se vydavatele propojení a nahrát loga, marketingové materiály, právní dokumenty a konfigurace systému pro správu zájemce. |
| **Nastavit nabídka kontaktů** | Zadejte inženýrství kontakty i kontaktní informace podpory pro nabídku SaaS. |  
| **Ověření integrace SaaS aplikace Azure AD** | Před odesláním aplikace SaaS pro publikování, je nutné ověřit, že aplikace je integrované s Azure AD |  
| **Publikovat nabídku**| Po dokončení nabídku a technických prostředků, můžete odeslat nabídku. Tato akce spustí proces publikování, ve kterém šablona řešení je testována, ověřit, certifikaci a schválena k publikování. |

## <a name="using-azure-active-directory-to-enable-trials"></a>Pomocí služby Azure Active Directory povolit zkušební verze  

Microsoft ověří všichni uživatelé Marketplace s Azure AD, proto pokud ověřený uživatel klikne na tlačítko prostřednictvím vaše zkušební verze výpis v Marketplace. a je přesměrován na prostředí zkušební verzi, bez nutnosti můžete zřídit uživatele přímo do bezplatnou zkušební verzí Krok další přihlášení. Token, který vaše aplikace obdrží z Azure AD během ověřování zahrnuje cenné uživatelské informace, které můžete použít k vytvoření uživatelského účtu v aplikaci, umožňuje automatizovat zřizování prostředí a zvýšit pravděpodobnost převod. Další informace o tokenu najdete v tématu [ukázka tokeny](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

Pomocí služby Azure AD povolit 1 kliknutím ověřování do aplikace nebo zkušební verze provede následující akce:  
* Zjednodušuje zkušeností zákazníků z Marketplace na zkušební verzi.  
* Udržuje chování 'prostředí v produktu, i když se uživatel přesměruje, z webu Marketplace k doméně nebo zkušební verze prostředí.  
* Snižuje pravděpodobnost zřeknutí na přesměrování, protože není k dispozici další krok přihlášení.  
* Snižuje překážek nasazení pro velké naplnění uživatele Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certifikuje svoji integraci služby Azure AD pro Marketplace.  

Můžete certifikovat svoji integraci služby Azure AD několika různými způsoby v závislosti na tom, jestli je vaše aplikace klienta jeden nebo více klientů a zda jsou pro vás nové na Azure AD federované jedním přihlašování (SSO), nebo už ho podporují.  

**Pro více klientů aplikace:**  

Pokud jste již podporují Azure AD, postupujte takto:
1.  Registrace vaší aplikace na portálu Azure
2.  Povolte funkci podpory víceklientské ve službě Azure AD získat zkušební prostředí jedním kliknutím. Podrobnější informace můžete najít [zde](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Pokud jste ještě Federovanému služby Azure AD, postupujte takto: 
1.  Registrace vaší aplikace na portálu Azure
2.  Vývoj s Azure AD pomocí jednotného přihlašování k [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) nebo [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Povolit podporu víceklientské funkce v AAD získat zkušební prostředí jedním kliknutím podrobnější informace můžete najít [zde](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Pro jednoho klienta aplikace použijte některou z následujících možností:**  
* Přidání uživatelů do vašeho adresáře jako uživatele typu Host pomocí [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Ručně poskytnout zkušební verze pro zákazníky, obraťte se na mě.
* Vývoj na zákazníka, Test jednotky.
* Vytvoření ukázkové víceklientské ukázkovou aplikaci pomocí jednotného přihlašování

