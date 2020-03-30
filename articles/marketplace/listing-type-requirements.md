---
title: Požadavky podle typu výpisu | Azure
description: Tento článek popisuje kritéria způsobilosti a požadavky na publikování, kteří se snaží pochopit, jak publikovat aplikace na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: dsindona
ms.openlocfilehash: 1ff6e34936234d6636fabc6c5e17dd32416b4ac2
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387848"
---
# <a name="requirements-by-listing-type"></a>Požadavky podle typu výpisu  
Technické a marketingové požadavky na obsah se liší podle výkladní skříně, typu nabídky a typu nabídky. Zkontrolujte následující specifikace a ověřte dodržování předpisů.  
1. Požadavky na výkladní skříň:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Požadavky na typ nabídky a typ nabídky:  
    *   Další informace o typech výpisů a typech nabídek najdete na stránce Určení typu výpisu pro řešení, která se nachází na [adrese docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Požadavky na výkladní skříň: AppSource  
Následující tabulka popisuje požadavky na požadavky na požadavky pro publikování na AppSource.  

| Požadavek | Podrobnosti | Povinné nebo doporučené |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Vaše aplikace musí povolit Federované jednotné přihlašování služby Azure Active Directory (federované jednotné přihlašování Azure AD) s povoleným souhlasem.<ul> <li>Další informace o povolení federovaného jednotného přihlašování azure ad najdete na stránce Konfigurace jednotného přihlašování k aplikacím, které nejsou na stránce galerie aplikací Azure Active Directory umístěné na [docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Požaduje se |   
| ***Integrace se cloudovými službami Microsoftu*** | Vaše aplikace by se měla integrovat s dalšími cloudovými službami Microsoftu, jako jsou Microsoft Power BI, Cortana Intelligence nebo služby Microsoft Azure.<ul> <li>Příkladem služby Microsoft Cloud je Internet věcí.</li> </ul> | Doporučené |  
| ***Cílová skupina*** | Vaše aplikace musí být pro firemní uživatele a vlastníky firem. | Požaduje se | 
| ***Aplikace Software jako služba (SaaS) pro firmy*** | Vaše aplikace musí splňovat následující požadavky.<ul> <li>Obchodní aplikace SaaS</li> <li>Zaměřeno na obchodní procesy</li> <li>Cílené na firemní zákazníky</li> <li>Umožněte uživatelům používat jejich pracovní pověření k přihlášení, například uživatelské jméno a heslo.</li> </ul> | Požaduje se |  
| ***Bezplatná zkušební doba a zkušební verze*** | Aby mohl zákazník vaši aplikaci používat zdarma po omezenou dobu, musí aplikace obsahovat jednu z následujících možností.<ul> <li>Poskytněte metodu, `try` aby zákazníci mohli spustit zkušební verzi vaší aplikace v rámci AppSource</li> <li>Poskytněte `request trial` možnost v AppSource, aby zákazníci mohli požádat o zkušební verzi vaší aplikace</li> </ul>Bezplatná zkušební verze, kterou poskytnete, musí zákazníkovi nabídnout předem nastavenou dobu na vyzkoušení aplikace bez dalších nákladů. | Požaduje se |  
| ***Snadno konfigurovatelné řešení připravené k použití*** | Aplikace musí být snadná a rychlá konfigurace a nastavení bez nutnosti přizpůsobení. | Požaduje se |  
| ***Správa potenciálních zákazníků*** | Povolte aplikaci CRM přijímat data zájemců před přijetím zájemců z výkladní skříně.<ul> <li>Příklady CRM jsou Marketo, Microsoft Dynamics nebo Salesforce</li> </ul> | Požaduje se |  
| ***Zásady ochrany osobních údajů a podmínky použití*** | Vaše aplikace musí obsahovat odkaz na stránku zásad ochrany osobních údajů pomocí veřejné adresy URL. Vaše podmínky použití musí být poskytnuty během publikování jako text. | Požaduje se |  
| ***Podpora*** | Vaše aplikace musí poskytnout odkaz na stránku zákaznické podpory pomocí veřejné adresy URL. Pokud je vaše aplikace zkušební verze, musíte během zkušebního období bez dalších nákladů podporovat. | Požaduje se |  

## <a name="storefront-requirements-azure-marketplace"></a>Požadavky na výlohy: Azure Marketplace  
Níže jsou požadavky na předpoklady pro typy výpisů na Azure Marketplace.  

| Požadavek | Podrobnosti | Typ výpisu |  
|:--- |:--- |:--- |  
| ***Zásady účasti*** | Vaše aplikace musí dodržovat zásady účasti na Azure Marketplace.<ul> <li>Další informace o zásadách účasti najdete na stránce zásad účasti na Azure Marketplace, která se nachází na [adrese azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | list<br />Transact<br />trial |  
| ***Integrace se společností Microsoft*** | Vaše nabídka by měla používat nebo rozšiřovat typy služeb Microsoft Azure, jako jsou výpočetní prostředky, sítě nebo úložiště. Vaše nabídka by měla být zarovnána s existující kategorií Azure Marketplace, jako jsou databáze, zabezpečení nebo sítě.<ul> <li>Další informace o nabídkách Marketplace najdete na stránce Aplikace marketplace umístěné na [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | list<br />Transact<br />trial |  
| ***Cílová skupina*** | Vaše nabídka musí být pro IT profesionály, vývojáře cloudu nebo jiné technické role zákazníků. | list<br />Transact<br />trial |  
| ***Správa potenciálních zákazníků*** | Povolte aplikaci CRM (Marketo, Microsoft Dynamics nebo Salesforce) přijímat data zájemců před přijetím zájemců z obchodu. | list<br />Transact<br />trial |  
| ***Zásady ochrany osobních údajů a podmínky použití*** | Vaše aplikace musí obsahovat odkaz na stránku zásad ochrany osobních údajů pomocí veřejné adresy URL. Vaše podmínky použití musí být poskytnuty během publikování jako text. | list<br />Transact<br />trial |  
| ***Podpora*** | Vaše nabídka musí obsahovat odkaz na stránku zákaznické podpory pomocí veřejné adresy URL. Pokud je vaše nabídka zkušební verzí, musíte během zkušebního období bez dalších nákladů podporovat. | Transact<br />trial |    

## <a name="non-transact-listings"></a>Neobchodovatek Výpisy  
Tato část popisuje všechny typy nabídek, které nepoužívají typ výpisu Transact. 

### <a name="list"></a>Seznam  
Typ výpisu seznamu obsahuje následující typy nabídek na výlohách na trhu.  

| Typ nabídky | Průčelí | Podrobnosti |  
|:---        |:---        |:---     |  
| Konzultační služby | AppSource | Požadavky: AppSource: Seznam: Poradenské služby |  
| Konzultační služby | Azure Marketplace | Požadavky: Azure Marketplace: Seznam: Poradenské služby |  
| Kontaktujte mě | AppSource | [](#) |  
| Kontaktujte mě | Azure Marketplace | Požadavky: AppSource: Seznam: Kontaktujte mě |  

#### <a name="requirements-appsource-list-consulting-service"></a>Požadavky: AppSource: Seznam: Konzultační servis  

| Požadavky | Podrobnosti |  
|:--- |:--- |  
| Charakteristiky nabídky služeb | Vaše poradenská služba musí splňovat následující kritéria.<ul> <li>Poskytujte zapojení s pevným rozsahem, pevnou dobou trvání, pevnou cenou (nebo bezplatnou)</li> <li>Orient především pro předprodej.</li> <li>Omezit na jednoho zákazníka.</li> <li>Chování na místě.</li> </ul> |  
| Partnerské požadavky na poradenské služby | Splňujete kritéria v příslušné oblasti pro vaši službu.<table><tr><th>Oblast řešení</th><th>Kritéria</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Měj kompetenci silver nebo gold cloudové správy vztahů se zákazníky.</td></tr><tr><td>Dynamics 365 pro finance a provoz, edice Enterprise</td><td>Mějte kompetence silver nebo gold enterprise resource planning a výnosy z cloudových operací v období 12 měsíců od 25 000 USD nebo více.</td></tr><tr><td>Dynamics 365 pro finance a provoz, obchodní edice</td><td>Slouží jako poskytovatel cloudových služeb (CSP) nebo digitální partner záznamu (DPOR) pro jednoho nebo více zákazníků.</td></tr><tr><td>Power BI</td><td>Splňte kritéria partnera pro řešení.</td></tr><tr><td>PowerApps</td><td>Měj řešení Pro partnery.</td></tr></table><ul> <li>Další informace o řízení vztahů se zákazníky naleznete na stránce Cloud Customer Relationship Management umístěné na [adrese partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Další informace o plánování zdrojů naleznete na stránce Plánování zdrojů organizace na [adrese partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Další informace o zprostředkovateli ostřice naleznete na stránce Poskytovatele cloudových služeb na [adrese partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Další informace o dpor, navštivte digitální partner záznamu a partner sdružení stránky se nachází na [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Další informace o kritériích partnera řešení naleznete v dokumentu Přehled a pobídky partnera pro řešení, který se nachází na [adrese www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Další informace o prezentaci partnerů najdete na stránce Předvádění partnerů na [adrese powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Požadavky: Azure Marketplace: Seznam: Konzultační služba  

| Požadavky | Podrobnosti |  
|:--- |:--- |  
| Charakteristiky nabídky služeb | Vaše poradenská služba musí splňovat následující kritéria.<ul> <li>Poskytujte zapojení s pevným rozsahem, pevnou dobou trvání, pevnou cenou (nebo bezplatnou)</li> <li>Orient především pro předprodej.</li> <li>Omezit na jednoho zákazníka.</li> <li>Chování na místě.</li> </ul> |  
| Partnerské požadavky na poradenské služby | V jedné z následujících kompetencí v příslušné oblasti pro vaši službu musíte mít stříbro nebo zlato. <table><tr><th>Oblast řešení</th><th>Kompetence</th></tr><td>Cloudová platforma a infrastruktura</td><td>Cloudová platforma<br />Data Center</td><tr><td>Vývoj aplikací a ISV</td><td>Vývoj aplikací<br />Integrace aplikací<br />DevOps</td></tr><tr><td>Správa a analýza dat</td><td>Analýza dat<br />Datová platforma</td></tr></table><ul> <li>Další informace o kompetencích naleznete na stránce Kompetence prostřednictvím sítě partnerů společnosti Microsoft umístěné na [adrese partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Další informace o výpisu najdete na stránce služby Azure Marketplace Consulting Services, která se nachází na [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Zkušební verze  

| Typ nabídky | Průčelí | Podrobnosti |  
|:---        |:---        |:---     |  
| Zkušební verze Zdarma / SaaS | AppSource | Požadavky na typ výpisu: Zkušební verze |  
| Zkušební verze Zdarma / SaaS | Azure Marketplace | Požadavky: Azure Marketplace: Zkušební verze: Bezplatná zkušební verze / zkušební verze SaaS |  
| Interaktivní ukázka | AppSource | Požadavky na typ výpisu: Zkušební verze |  
| Interaktivní ukázka | Azure Marketplace | [Požadavky: Azure Marketplace: Zkušební verze: Interaktivní ukázka](#requirements-azure-marketplace-trial-interactive-demo) |  
| Zkušební jízda | AppSource | Požadavky na typ výpisu: Zkušební verze |  
| Zkušební jízda | Azure Marketplace | [Požadavky: Azure Marketplace: Zkušební verze: Testovací jízda](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Požadavky: Azure Marketplace: Zkušební verze  

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| Bezplatná zkušební doba a zkušební verze | Zákazník může vaši aplikaci používat zdarma po omezenou dobu.<br /><br />Zákazník není povinen platit za vaši nabídku nebo aplikaci žádné licenční poplatky nebo poplatky za předplatné. Zákazník nemusí platit za základní produkt nebo službu první strany společnosti Microsoft. Všechny možnosti zkušební verze se nasazují do vašeho předplatného Azure. Máte výhradní kontrolní zkušební verzi optimalizace a správy nákladů.<br /><br />Můžete si vybrat bezplatnou zkušební verzi, interaktivní ukázku nebo testovací jízdu. Bez ohledu na to, co si vyberete, vaše bezplatná zkušební verze musí zákazníkovi nabídnout předem nastavenou dobu na vyzkoušení aplikace bez dalších nákladů.<ul> <li>Chcete-li zahájit proces vytváření zkušební jednotky, odešlete e-mail na adresu [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com).</li> </ul>Poznámka: Zkušební prostředí Azure Marketplace SaaS musí zákazníkům umožňovat používat pracovní pověření k přihlášení.<ul> <li>Další informace naleznete v části Zkušební prostředí AppSource umístěné na [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Snadno konfigurovatelné řešení připravené k použití | Aplikace musí být snadná a rychlá konfigurace a nastavení. |  
| Dostupnost / doba dostupnosti | Vaše aplikace nebo platforma SaaS musí mít dostupnost alespoň 99,9 %. |  
| Azure Active Directory | Vaše nabídka musí povolit Azure Active Directory (Azure AD) federované jednotné přihlašování (SSO) (Azure AD federated SSO) s povoleným souhlasem. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Požadavky: Azure Marketplace: Zkušební verze: Bezplatná zkušební verze / zkušební verze SaaS  

| Výhoda | Požadavek |  
|:--- |:--- |  
| Umožňuje zákazníkovi vyzkoušet si váš produkt před nákupem pomocí automatizované metody převodu na placené použití. Umožňuje také doklady o koncepci pro zákazníka a společné spolupráci s prodejními týmy společnosti Microsoft. | Vaše řešení je virtuální počítač nebo šablona řešení.<br /><br />Vaše řešení je nabídka SaaS a vy nabízíte víceklientský produkt SaaS.<br /><br />Máte první spuštění zkušenosti, abyste rychle zprovoznění zákazníka.<br /><br />Máte jednoho klienta, ale přidáváte zákazníky jako uživatele typu Host. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Požadavky: Azure Marketplace: Zkušební verze: Interaktivní ukázka  

| Výhoda | Požadavek |  
|:--- |:--- |  
| Umožňuje zákazníkům vidět vaše řešení v akci bez složitosti nastavení. | Vaše řešení vyžaduje komplexní nastavení, které by bylo obtížné dosáhnout během zkušební doby. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Požadavky: Azure Marketplace: Zkušební verze: Testovací jízda  

| Výhoda | Požadavek |  
|:--- |:--- |  
| Umožňuje zákazníkovi vyzkoušet si produkt před nákupem.<br /><br />Poskytuje řízené prostředí vašeho řešení pomocí předem nakonfigurovaných nastavení.<br /><br />Následující jsou další výhody při použití zkušební jednotky.<ul> <li>27 % vyhledávání uživatelů na trhu uživatele vylepšuje tak, aby zobrazovalo nabídky pouze s testovacími jednotkami.</li> <li>Nabídky s testovacími pohony generují o 38 % více potenciálních zákazníků než nabídky bez.</li> <li>36 % nových akvizic zákazníků na trhu pochází od zákazníků, kteří se vydali na zkušební jízdu.</li> <li>Testovací jízdy umožňují prodejcům v terénu společnosti Microsoft lépe porozumět vašemu produktu pro spoluprodej.</li> </ul> | Vaše řešení je virtuální počítač, šablona řešení nebo aplikace SaaS s jedním tenantem nebo je složité zřídit. <br /><br />Nemáte způsob, jak převést zkušební verzi na placenou nabídku. |  

---

## <a name="transact-specific-listings"></a>Výpisy specifické pro transakt

### <a name="transact"></a>Transact  

| Typ nabídky | Průčelí | Podrobnosti |   
|:---        |:---        | :--- |  
| Aplikace Azure: Spravovaná aplikace | Azure Marketplace | Požadavky: Azure Marketplace: Transact: Aplikace Azure: Spravovaná aplikace |  
| Aplikace Azure: Šablona řešení | Azure Marketplace | Požadavky: Azure Marketplace: Transact: Aplikace Azure: Šablona řešení |  
| Kontejnery | Azure Marketplace | [Požadavky: Azure Marketplace: Transact: Kontejner](#requirements-azure-marketplace-transact-container) |  
| Aplikace SaaS  | Azure Marketplace | [Požadavky: Azure Marketplace: Transact: Aplikace SaaS](#requirements-azure-marketplace-transact-saas-app) |  
| Virtuální počítač | Azure Marketplace | [Požadavky: Azure Marketplace: Transact: Virtuální počítač](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Požadavky: Azure Marketplace: Transact: Kontejner  

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| Fakturace a měření | Podporujte bezplatný nebo BYOL fakturační model. |  
| Obrázek založený na Dockeru | Image kontejneru musí být založená na formátu image Dockeru a musí být vymáhána z registrů kontejnerů Azure. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Požadavky: Azure Marketplace: Transact: Aplikace SaaS  

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| Fakturace a měření | Vaše nabídka je oceněna měsíční maješkou. Ceny založené na využití a možnosti *true-up* založené na využití nejsou v současné době podporovány. |  
| Zrušení | Vaši nabídku zákazník kdykoli zruší. |  
| Vstupní stránka transakce | Hostuj vstupní stránku transakcí pod značkou Azure. Vstupní stránka umožňuje zákazníkům vytvářet a spravovat váš účet služby SaaS. |  
| Rozhraní API předplatného SaaS | Poskytněte službu, která spolupracuje s předplatným SaaS a vytváří, aktualizuje a odstraňuje uživatelský účet a plán služeb. Všechny důležité změny rozhraní API musí být podporovány do 24 hodin. Všechny nekritické změny rozhraní API jsou pravidelně aktualizovány. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Požadavky: Azure Marketplace: Transact: Virtuální počítač  

| Požadavek | Podrobnosti |  
|:--- |:--- | 
| Fakturace a měření | Váš virtuální počítač musí podporovat měsíční fakturaci BYOL nebo průběžných plateb. |  
| Virtuální pevný disk kompatibilní s Azure (VHD) | Virtuální počítače musí být postavené na Windows nebo Linuxu.<ul> <li>Další informace o vytváření virtuálního pevného disku Linuxu najdete v [tématu Linux distribuce schválila v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Další informace o vytvoření virtuálního pevného disku systému Windows naleznete [v tématu Vytvoření virtuálního pevného disku kompatibilního s Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

## <a name="next-steps"></a>Další kroky
*   Navštivte stránku [Azure Marketplace a Průvodce vydavatelem AppSource.](./marketplace-publishers-guide.md)  

