---
title: Požadavky podle typu výpisu | Azure
description: Tento článek popisuje kritéria způsobilosti a požadavky na publikování, které partneři pokoušejí pochopit, jak publikovat aplikace na Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 12/19/2018
ms.author: ellacroi
ms.openlocfilehash: b9a2e8b37f746eb86b2e0fe6a7304cd888a293e1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823032"
---
# <a name="requirements-by-listing-type"></a>Požadavky podle typu výpisu  
Požadavky na technický a marketingový obsah se liší podle prezentace, typu nabídky a typu výpisu. Zkontrolujte následující specifikace, abyste ověřili dodržování předpisů.  
1. Požadavky na prezentace:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Požadavky na výpis typu a typu nabídky:  
    *   Další informace o typech seznamů a typech nabídek najdete na stránce určení typu výpisu pro vaši stránku řešení na adrese [docs.Microsoft.com/Azure/Marketplace/Determine-Your-listing-Type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Požadavky na prezentace: AppSource  
V následující tabulce jsou popsány požadavky na předpoklady pro publikování v AppSource.  

| Požadavek | Podrobnosti | Povinné nebo doporučené |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Vaše aplikace musí umožňovat Azure Active Directory federované jednotné přihlašování (federované jednotné přihlašování Azure AD) se zapnutým souhlasem.<ul> <li>Další informace o povolení federovaného jednotného přihlašování služby Azure AD najdete v části Konfigurace jednotného přihlašování k aplikacím, které nejsou na stránce Azure Active Directory Galerie aplikací umístěné na adrese [docs.Microsoft.com/Azure/Active-Directory/Active-Directory-SaaS-Custom-Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Požaduje se |   
| ***Integrace s Microsoft Cloud službami*** | Vaše aplikace by se měla integrovat s jinými Microsoft Cloud službami, jako jsou Microsoft Power BI, Cortana Intelligence nebo služby Microsoft Azure.<ul> <li>Příkladem Microsoft Cloud služby je Internet věcí.</li> </ul> | Doporučené |  
| ***Osoby*** | Vaše aplikace musí být pro obchodní uživatele a vlastníky. | Požaduje se | 
| ***Aplikace SaaS (software jako služba) pro firmy*** | Vaše aplikace musí splňovat následující požadavky.<ul> <li>Obchodní aplikace SaaS</li> <li>Zaměření na obchodní procesy</li> <li>Cílené na obchodní zákazníky</li> <li>Povolit uživatelům používat své pracovní přihlašovací údaje pro přihlášení, jako je uživatelské jméno a heslo</li> </ul> | Požaduje se |  
| ***Bezplatné zkušební období a zkušební prostředí*** | Aby zákazník mohl používat vaši aplikaci zdarma po určitou dobu, musí obsahovat jednu z následujících možností.<ul> <li>Poskytněte `try` metodu, aby zákazníci mohli začít zkušební verzi vaší aplikace v rámci AppSource.</li> <li>Poskytněte možnost `request trial` v AppSource, aby si zákazníci mohli vyžádat zkušební verzi vaší aplikace.</li> </ul>Bezplatná zkušební verze, kterou poskytnete, musí nabídnout zákazníkovi předem nastavenou dobu, která si vyzkouší vaši aplikaci bez dalších poplatků. | Požaduje se |  
| ***Snadno konfigurovatelná řešení připravené k použití*** | Vaše aplikace musí být snadná a rychlá konfigurace a nastavení bez nutnosti přizpůsobení. | Požaduje se |  
| ***Správa potenciálních zákazníků*** | Umožněte, aby aplikace CRM přijímala data zájemců předtím, než dostanete zájemce z prezentace.<ul> <li>Příklady CRMs jsou Marketo, Microsoft Dynamics nebo Salesforce</li> </ul> | Požaduje se |  
| ***Zásady ochrany osobních údajů a podmínek použití*** | Vaše aplikace musí poskytnout odkaz na stránku zásad ochrany osobních údajů pomocí veřejné adresy URL. Vaše požadavky na použití je nutné zadat během publikování jako text. | Požaduje se |  
| ***Podpora*** | Vaše aplikace musí poskytnout odkaz na vaši stránku zákaznické podpory pomocí veřejné adresy URL. Pokud je vaše aplikace zkušební verze, musíte během zkušebního období zdarma podpořit žádné dodatečné náklady. | Požaduje se |  

## <a name="storefront-requirements-azure-marketplace"></a>Požadavky na prezentace: Azure Marketplace  
Níže jsou uvedeny požadavky na seznam typů v Azure Marketplace.  

| Požadavek | Podrobnosti | Typ výpisu |  
|:--- |:--- |:--- |  
| ***Zásady pro účast*** | Vaše aplikace musí splňovat Azure Marketplace zásady pro účast.<ul> <li>Další informace o zásadách účasti najdete na stránce věnované zásadám Azure Marketplace pro účast na adrese [Azure.Microsoft.com/support/Legal/Marketplace/Participation-Policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | list<br />provedena<br />trial |  
| ***Integrace s Microsoftem*** | Vaše nabídka by měla používat nebo rozšířila Microsoft Azure typy služeb, jako jsou výpočetní prostředky, sítě nebo úložiště. Vaše nabídka by se měla zarovnat do existující kategorie Azure Marketplace, jako jsou databáze, zabezpečení nebo sítě.<ul> <li>Další informace o nabídkách na webu Marketplace najdete na stránce aplikace na webu Marketplace, která se nachází na adrese [azuremarketplace.Microsoft.com/Marketplace/Apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | list<br />provedena<br />trial |  
| ***Osoby*** | Vaše nabídka musí být určená pro profesionály v oblasti IT, cloudové vývojáře nebo jiné technické role zákazníků. | list<br />provedena<br />trial |  
| ***Správa potenciálních zákazníků*** | Pokud chcete, aby se data o potenciálních zákaznících z prezentacea přijímala, Umožněte svým zákazníkům (Marketo, Microsoft Dynamics nebo Salesforce). | list<br />provedena<br />trial |  
| ***Zásady ochrany osobních údajů a podmínek použití*** | Vaše aplikace musí poskytnout odkaz na stránku zásad ochrany osobních údajů pomocí veřejné adresy URL. Vaše požadavky na použití je nutné zadat během publikování jako text. | list<br />provedena<br />trial |  
| ***Podpora*** | Vaše nabídka musí poskytnout odkaz na vaši stránku zákaznické podpory pomocí veřejné adresy URL. Pokud je vaše nabídka zkušební verze, musíte během zkušebního období zdarma podpořit žádné dodatečné náklady. | provedena<br />trial |    

## <a name="non-transact-listings"></a>Seznamy jiné než Transact  
V této části jsou popsány všechny typy nabídek, které nepoužívají typ výpisu transakcí. 

### <a name="list"></a>Seznam  
Typ výpisu seznamu obsahuje následující typy nabídek na prodejní místa na webu Marketplace.  

| Typ nabídky | Prodejní místo | Podrobnosti |  
|:---        |:---        |:---     |  
| Konzultační služby | AppSource | Požadavky: AppSource: seznam: konzultační služby |  
| Konzultační služby | Azure Marketplace | Požadavky: Azure Marketplace: seznam: konzultační služby |  
| Kontaktujte mě | AppSource | [](#) |  
| Kontaktujte mě | Azure Marketplace | Požadavky: AppSource: seznam: kontaktujte mě |  

#### <a name="requirements-appsource-list-consulting-service"></a>Požadavky: AppSource: seznam: konzultační služba  

| Požadavky | Podrobnosti |  
|:--- |:--- |  
| Vlastnosti nabídky služeb | Vaše konzultační služba musí splňovat následující kritéria.<ul> <li>Doručovat zapojení s pevným rozsahem, pevnou dobu trvání, pevnou cenu (nebo volnou).</li> <li>Orientovat primárně pro předem prodej.</li> <li>Omezení na jednoho zákazníka.</li> <li>Chování na webu.</li> </ul> |  
| Požadavky partnerů na konzultační služby | Splňujete kritéria v příslušné oblasti pro vaši službu.<table><tr><th>Oblast řešení</th><th>Kritéria</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Máte v cloudu pravomoc pro správu vztahů se zákazníky stříbrné nebo Gold.</td></tr><tr><td>Dynamics 365 pro finance a provoz, Enterprise Edition</td><td>Mějte na začátku 12 měsíců $25 000 nebo více a výnosy z vašich cloudových operací, které jsou náročné na práci s plánováním podnikových zdrojů nebo s nimi.</td></tr><tr><td>Dynamics 365 pro finance a provoz, Business Edition</td><td>Slouží jako zprostředkovatel CSP (Cloud Services Provider) nebo Digital partner záznamu (partnera DPOR) pro jednoho nebo více zákazníků.</td></tr><tr><td>Power BI</td><td>Splňovat kritéria pro partnery řešení.</td></tr><tr><td>PowerApps</td><td>Vyprezentujte řešení pro partnery.</td></tr></table><ul> <li>Další informace o správě vztahů se zákazníky najdete na stránce cloudu pro správu vztahů se zákazníky, která se nachází na adrese [partner.Microsoft.com/Membership/Cloud-Customer-Relationship-Management-Competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Další informace o plánování prostředků najdete na stránce plánování podnikových zdrojů, která se nachází na adrese [partner.Microsoft.com/Membership/Enterprise-Resource-Planning-Competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Další informace o zprostředkovateli CSP najdete na stránce poskytovatele Cloud Services, která se nachází na adrese [partner.Microsoft.com/Cloud-Solution-Provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Další informace o partnera DPOR najdete na stránce digitální partner pro záznam a přidružení partnera umístěný na adrese [partner.Microsoft.com/Membership/Digital-Partner-of-Record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Další informace o kritériích partnerů řešení najdete v článku Přehled partnerů řešení a motivace dokumentu na adrese [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.PDF](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Další informace o prezentujce partnerů najdete na stránce s informacemi o partnerovi na adrese [PowerApps.Microsoft.com/partner-Showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Požadavky: Azure Marketplace: seznam: konzultační služba  

| Požadavky | Podrobnosti |  
|:--- |:--- |  
| Vlastnosti nabídky služeb | Vaše konzultační služba musí splňovat následující kritéria.<ul> <li>Doručovat zapojení s pevným rozsahem, pevnou dobu trvání, pevnou cenu (nebo volnou).</li> <li>Orientovat primárně pro předem prodej.</li> <li>Omezení na jednoho zákazníka.</li> <li>Chování na webu.</li> </ul> |  
| Požadavky partnerů na konzultační služby | V příslušné oblasti pro vaši službu musíte mít stříbro nebo zlato v jedné z následujících kompetencí. <table><tr><th>Oblast řešení</th><th>Kompetencí</th></tr><td>Cloudová platforma a infrastruktura</td><td>Cloudová platforma<br />Datové centrum</td><tr><td>Vývoj aplikací a ISV</td><td>Vývoj aplikací<br />Integrace aplikací<br />DevOps</td></tr><tr><td>Správa dat a analýzy</td><td>Analýza dat<br />Datová platforma</td></tr></table><ul> <li>Další informace o kompetencích najdete v části kompetence prostřednictvím Microsoft Partner Network stránky umístěné na adrese [partner.Microsoft.com/Membership/Competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Další informace o výpisu najdete na stránce Azure Marketplace konzultační služby, která se nachází na adrese [docs.Microsoft.com/Azure/Marketplace/Consulting-Services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Zkušební verze  

| Typ nabídky | Prodejní místo | Podrobnosti |  
|:---        |:---        |:---     |  
| Bezplatné/SaaS zkušební verze | AppSource | Požadavky na typ výpisu: zkušební verze |  
| Bezplatné/SaaS zkušební verze | Azure Marketplace | Požadavky: Azure Marketplace: zkušební verze, zkušební verze bezplatné zkušební verze/SaaS. |  
| Interaktivní ukázka | AppSource | Požadavky na typ výpisu: zkušební verze |  
| Interaktivní ukázka | Azure Marketplace | [Požadavky: Azure Marketplace: zkušební verze: interaktivní ukázka](#requirements-azure-marketplace-trial-interactive-demo) |  
| Testovací verze | AppSource | Požadavky na typ výpisu: zkušební verze |  
| Testovací verze | Azure Marketplace | [Požadavky: Azure Marketplace: zkušební verze](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Požadavky: Azure Marketplace: zkušební verze  

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| Bezplatné zkušební období a zkušební prostředí | Zákazník může vaši aplikaci zdarma využívat po určitou dobu.<br /><br />Zákazníkovi není nutné platit žádné licence ani poplatky za předplatné vaší nabídky nebo aplikace. Zákazníkovi není nutné platit za základní produkt nebo službu od společnosti Microsoft. Všechny možnosti zkušební verze se nasadí do vašeho předplatného Azure. Máte jenom zkušební verzi pro kontrolu a optimalizaci nákladů.<br /><br />Můžete zvolit bezplatnou zkušební verzi, interaktivní ukázku nebo testovací jednotku. Bez ohledu na to, co zvolíte, musí vaše bezplatné zkušební verze nabídnout zákazníkovi předem nastavenou dobu, než se aplikace vyzkouší bez dalších nákladů.<ul> <li>Pokud chcete zahájit proces vytváření testovacích jednotek, pošlete e-mail na [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com).</li> </ul>Poznámka: Azure Marketplace zkušební verze SaaS musí zákazníkům dovolit, aby k přihlášení používali pracovní přihlašovací údaje.<ul> <li>Další informace najdete v části s informacemi o zkušebním prostředí AppSource, která najdete na adrese [docs.Microsoft.com/Azure/Active-Directory/Develop/Active-Directory-devhowto-appsource-Certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Snadno konfigurovatelná řešení připravené k použití | Vaše aplikace musí být snadná a rychlá konfigurace a nastavení. |  
| Dostupnost/doba provozu | Vaše aplikace nebo platforma SaaS musí mít dobu provozu alespoň 99,9%. |  
| Azure Active Directory | Vaše nabídka musí umožňovat federované jednotné přihlašování (SSO) Azure Active Directory (Azure AD) (SSO) (federované jednotné přihlašování Azure AD) se zapnutým souhlasem. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Požadavky: Azure Marketplace: zkušební verze, zkušební verze bezplatné zkušební verze/SaaS.  

| Výhoda | Požadavek |  
|:--- |:--- |  
| Umožňuje zákazníkovi vyzkoušet si produkt před nákupem pomocí automatizované metody pro převod na placené použití. Také umožňuje prokázání konceptu pro zákazníka a spolupráci s prodejními týmy společnosti Microsoft. | Vaše řešení je virtuální počítač nebo šablona řešení.<br /><br />Vaše řešení je nabídka SaaS a Vy nabízíte SaaS produkt s více klienty.<br /><br />Máte první spuštění, které vám umožní rychle začít pracovat se zákazníkem.<br /><br />Máte jednoho tenanta, ale přidáváte zákazníky jako uživatele typu Host. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Požadavky: Azure Marketplace: zkušební verze: interaktivní ukázka  

| Výhoda | Požadavek |  
|:--- |:--- |  
| Umožňuje zákazníkům vidět vaše řešení v praxi bez složitosti nastavení. | Vaše řešení vyžaduje komplexní nastavení, které by bylo obtížné dosáhnout v rámci zkušebního období. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Požadavky: Azure Marketplace: zkušební verze  

| Výhoda | Požadavek |  
|:--- |:--- |  
| Umožňuje zákazníkovi vyzkoušet si produkt před nákupem.<br /><br />Poskytuje zkušenosti s vaším řešením pomocí předem nakonfigurovaných nastavení.<br /><br />Následující jsou další výhody při použití testovací jednotky.<ul> <li>27% hledání uživatelů na webu Marketplace vylepší uživatelé, aby na nich viděli jenom nabídky s testovacími jednotkami.</li> <li>Nabídky s testovacími jednotkami vygenerují 38% větší zájem než nabídky bez.</li> <li>od zákazníků, kteří si pořídili zkušební verzi, 36% nových zákaznických nákupů na webu Marketplace.</li> <li>Testovací jednotky umožňují prodejcům v terénu Microsoftu lépe porozumět vašemu produktu v oblasti společného prodejního úsilí.</li> </ul> | Vaše řešení je virtuální počítač, šablona řešení nebo aplikace SaaS s jedním klientem nebo je složitá ke zřízení. <br /><br />K převedení zkušební verze na placenou nabídku nemáte metodu. |  

---

## <a name="transact-specific-listings"></a>Výpisy specifické pro jazyk Transact

### <a name="transact"></a>Transakce  

| Typ nabídky | Prodejní místo | Podrobnosti |   
|:---        |:---        | :--- |  
| Aplikace Azure: spravovaná aplikace | Azure Marketplace | Požadavky: Azure Marketplace: Transact: aplikace Azure: spravovaná aplikace |  
| Aplikace Azure: Šablona řešení | Azure Marketplace | Požadavky: Azure Marketplace: Transact: aplikace Azure: Šablona řešení |  
| Kontejnery | Azure Marketplace | [Požadavky: Azure Marketplace: Transact: Container](#requirements-azure-marketplace-transact-container) |  
| Aplikace SaaS  | Azure Marketplace | [Požadavky: Azure Marketplace: Transact: SaaS App](#requirements-azure-marketplace-transact-saas-app) |  
| Virtuální počítač | Azure Marketplace | [Požadavky: Azure Marketplace: Transact: virtuální počítač](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Požadavky: Azure Marketplace: Transact: Container  

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| Fakturace a měření | Podpora modelu fakturace Free nebo BYOL. |  
| Image založená na Docker | Vaše image kontejneru musí být založená na formátu Docker image a musí se načíst ze služby Azure Container Registry. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Požadavky: Azure Marketplace: Transact: SaaS App  

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| Fakturace a měření | Cena vaší nabídky se účtuje za měsíční paušální sazbu. Ceny založené na využití a možnosti pro *skutečné* použití založené na využití se v tuto chvíli nepodporují. |  
| Zrušení | Vaši nabídku nemůže zákazník kdykoli zrušit. |  
| Cílová stránka transakce | Hostování cílové stránky transakce v rámci značky Azure Vaše cílová stránka umožňuje zákazníkům vytvářet a spravovat účet služby SaaS. |  
| Rozhraní API pro předplatné SaaS | Poskytněte službu, která komunikuje s předplatným SaaS, aby vytvořila, aktualizovala a odstranila uživatelský účet a plán služby. Všechny kritické změny rozhraní API musí být podporované do 24 hodin. Všechny nekritické změny rozhraní API se pravidelně aktualizují. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Požadavky: Azure Marketplace: Transact: virtuální počítač  

| Požadavek | Podrobnosti |  
|:--- |:--- | 
| Fakturace a měření | Váš virtuální počítač musí podporovat BYOL nebo měsíční fakturaci s průběžnými platbami. |  
| Virtuální pevný disk kompatibilní s Azure (VHD) | Virtuální počítače musí být postavené na Windows nebo Linux.<ul> <li>Další informace o vytvoření virtuálního pevného disku se systémem Linux najdete v tématu [distribuce systému Linux schválené v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Další informace o vytvoření virtuálního pevného disku s Windows najdete v tématu [Vytvoření virtuálního pevného disku kompatibilního s Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

## <a name="next-steps"></a>Další kroky
*   Navštivte stránku [Příručka pro vydavatele Azure Marketplace a AppSource](./marketplace-publishers-guide.md) .  

