---
title: "Technical aplikací SaaS Azure Marketplace publikování průvodce"
description: "Podrobný průvodce a publikování kontrolní seznamy pro publikování aplikací SaaS do Azure Marketplace"
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: 
author: BrentL-Collabera
manager: 
editor: BrentL-Collabera
ms.assetid: 
ms.service: marketplace
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 02/28/2018
ms.author: pabutler
ms.openlocfilehash: 64becc80192e69bd332d6657637c845acf93748b
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="saas-applications-technical-publishing-guide"></a>Aplikace SaaS, které se technické publikování průvodce

Vítá vás technická aplikací SaaS Azure Marketplace publikování průvodce. Tato příručka je určená ke candidate a existující vydavatelů seznam svých aplikací a služeb v Azure Marketplace pomocí aplikace SaaS nabídky.  
Chcete použít nabídka aplikace SaaS, když bude vaše řešení nasazeno v předplatného Azure a zákazníků se přihlaste se prostřednictvím rozhraní, které můžete navrhnout a spravovat aplikaci a otestovat. Dělá to pomocí [Azure Active Directory (Azure AD)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis) využít existující zkušební prostředí. Jinými slovy je bezplatnou zkušební verzi vedla zákazníka, hostovaného. Je důležité ke zveřejnění řešení způsobem, který dává příležitost prostředí řešení nezávisle na žádné zdarma nebo poplatek kupující cloudu a proto tento typ nabídky nabízí zkušební prostředí odpovídající jak zákazníci vyhledat cloudové řešení.  

Přehled všech dalších nabídek na Marketplace, naleznete [Marketplace vydavatele průvodce](https://aka.ms/sellerguide).

## <a name="saas-application-technical-guidance"></a>Technické pokyny SaaS aplikace
Technické požadavky pro SaaS aplikace jsou jednoduché. Vydavatelé pouze musí být integrované s Azure AD k publikování.  Integrace služby Azure AD s aplikacemi dobře zdokumentovat a společnost Microsoft poskytuje několik sady SDK a prostředky toho chcete dosáhnout.  

Pokud chcete spustit, doporučujeme, abyste měli předplatné vyhrazené pro publikování vašeho webu Azure Marketplace umožňují izolovat práce z jiné iniciativy. Kromě toho, pokud není již nainstalován, doporučujeme, abyste měli následující nástroje v rámci vývojového prostředí: 
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)  
- [Prostředí Azure powerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-5.0.0)  
- [Nástroje pro vývojáře Azure (přečtěte si, co je k dispozici)](https://azure.microsoft.com/tools/)  
- [Visual Studio Code](https://code.visualstudio.com/)  

### <a name="resources"></a>Zdroje a prostředky
Následující seznam uvádí odkazy na doporučené prostředků Azure AD, které vám pomůžou začít: 

**Dokumentace**

- [Příručka pro vývojáře Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide)

- [Integrace se službou Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-how-to-integrate)

- [Integrace aplikací s Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)

- [Průvodce Azure – zabezpečení a identita](https://azure.microsoft.com/roadmap/?category=security-identity)

**Videa**

- [Ověřování služby Azure Active Directory s Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

- [Azure Active Directory identitu technické přehledové – část 1 / 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

- [Azure Active Directory identitu technické přehledové - část 2 / 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

- [Vytváření aplikací s Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

- [Microsoft Azure videa zaměřené na službě Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

**Školení**  
- [Microsoft Azure pro IT profesionály obsahu řady: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

**Aktualizace služby Azure Active Directory**  
- [Aktualizace služby Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

Pro podporu můžete použít v následujících zdrojích informací:
- [Fóra na webu MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
- [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="the-azure-active-directory-gallery"></a>Galerie Azure Active Directory
Kromě nutnosti aplikace uvedené v Azure Marketplace/AppSource, může mít také aplikace uvedené v galerii aplikací Azure AD, která je součástí Azure Marketplace AppStore. Zákazníci, kteří používají Azure AD jako zprostředkovatele identity můžete najít jiné aplikace konektory SaaS Publikovat sem. Správci IT můžete přidat konektory z Galerie aplikace a potom nakonfigurovat a používat konektory pro jednotné přihlašování (SSO) a zřizování. Azure AD podporuje všechny hlavní federační protokoly pro jednotné přihlašování, včetně SAML 2.0, OpenID Connect, OAuth a WS-Fed.  

Po otestujete, že vaše integraci aplikací funguje s Azure AD, odešlete žádost pro přístup na náš portál síťové aplikace. Pokud máte účet Office 365, použijte k přihlášení na portál. Pokud nemáte účet Office 365, můžete k přihlášení účtu Microsoft (například aplikaci Outlook nebo Hotmail).

## <a name="program-benefits"></a>Výhody programu
- Poskytuje optimální jeden přihlašování průběh pro zákazníky
- Jednoduché a minimální konfigurace aplikace
- Zákazníci můžete vyhledat aplikace a najít v galerii
- Každý zákazník může použít tuto integraci bez ohledu na to, které Azure AD SKU používají (Free, Basic nebo Premium)
- Kurz krok za krokem konfigurace pro naše zákazníky vzájemné
- Umožňuje zřizování uživatelů pro stejnou aplikaci používáte SCIM

## <a name="prerequisites"></a>Požadavky
Pro zobrazení seznamu aplikace v galerii Azure AD, aplikace musí nejprve implementovat jednoho z protokolů federační podporovaný službou Azure AD. Přečtěte si podmínky a ujednání nacházející se v galerii aplikací Azure AD [Microsoft Azure právní informace](https://azure.microsoft.com/support/legal/).  

Následující text popisuje další požadované informace, v závislosti na protokol, který používáte:

**OpenID Connect** – vytvoření víceklientské aplikace ve službě Azure AD a implementaci framework souhlasu pro vaši aplikaci. Poslat žádost o přihlášení k běžné koncový bod, tak, aby každý zákazník může poskytnout souhlas k aplikaci. Můžete řídit přístup uživatelů zákazníka na základě ID klienta a uživatele (UPN) přijaté v tokenu.  
**SAML 2.0 nebo WS-Fed** -vaše aplikace by měly mít možnost udělat integrace SAML nebo WS-Fed jednotné přihlašování v režimu SP nebo deklarací identity.
