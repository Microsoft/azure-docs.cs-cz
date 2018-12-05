---
title: Požadavky na uvedením typu | Azure
description: Tento článek popisuje kritéria a požadavky partnerů snaží pochopit, jak publikovat aplikace na webu Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/05/2018
ms.author: ellacroi
ms.openlocfilehash: 1c850b16492f725114fb23a8503f6345e1509212
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868663"
---
# <a name="requirements-by-listing-type"></a>Požadavky na uvedením typu  
Obsahu požadavky na technickou a marketingovou se liší podle z prodejních míst, typ nabídky a výpis typu. Projděte si následující specifikace k ověření vaší dodržování předpisů.  
1. Prezentace požadavky:  
    *   [AppSource](#storefront-requirements-appSource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Typ výpisu a požadavky na typ nabídky:  
    *   Další informace o seznam typy a typy nabídek, navštivte určete typ výpis pro vaše řešení stránky umístěné na [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Prezentace požadavky: AppSource  
Následující tabulka popisuje požadavky pro publikování na AppSource.  

| Požadavek | Podrobnosti | Povinné nebo doporučené |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Aplikace musí povolit Azure Active Directory federované jednotné přihlašování (Azure AD federované jednotné přihlašování) s souhlasu povolena.<ul> <li>Pro další informace o povolení služby Azure AD federované jednotné přihlašování, přejděte Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii stránka aplikace se nachází v Azure Active Directory [docs.microsoft.com/azure/active-directory/ Active-directory--vlastní – aplikace saas](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Požaduje se |   
| ***Integrace se službami Microsoft Cloud*** | Vaše aplikace by měla integrace s jinými službami Microsoft Cloud stejně jako služby Microsoft Power BI, Cortana Intelligence nebo Microsoft Azure.<ul> <li>Příklad služby Microsoft Cloud je Internet of Things.</li> </ul> | Doporučené |  
| ***Cílová skupina*** | Vaše aplikace musí být pro řádek podnikoví uživatelé a vlastníci firem. | Požaduje se | 
| ***Software jako služba (SaaS) aplikací pro firmy*** | Aplikace musí splňovat následující požadavky.<ul> <li>Obchodní aplikaci SaaS</li> <li>Obchodní proces, zaměřuje</li> <li>Cílený na firemní zákazníky</li> <li>Umožníte uživatelům používat svoje pracovní přihlašovací údaje pro přihlášení jako je například uživatelské jméno a heslo</li> </ul> | Požaduje se |  
| ***Období bezplatné zkušební verze a zkušební verze*** | Aplikace musí obsahovat jednu následující možnosti v objednávky pro zákazníka aplikaci mohli používat zdarma po omezenou dobu.<ul> <li>Zadejte `try` metoda, takže zákazníci můžou začít se zkušební verzí vaší aplikace v AppSource</li> <li>Zadejte `request trial` možnost v AppSource, takže zákazníci můžou požádat o zkušební verzi vaší aplikace</li> </ul>Bezplatná zkušební verze, které poskytujete nabídnout zákazníkům předem určené časové období vyzkoušet si vaše aplikace bez dalších poplatků. | Požaduje se |  
| ***Dají se snadno konfigurovat a připravené k použití řešení*** | Snadné a rychlé ke konfiguraci a nastavení se žádné přizpůsobení nutné musí být vaše aplikace. | Požaduje se |  
| ***Průběžná správa*** | Je nutné povolit CRM tak, aby přijímal data zájemce předtím, než se zobrazí vede ze storu.<ul> <li>Certifikované příklady Marketo, Microsoft Dynamics nebo Salesforce</li> </ul> | Požaduje se |  
| ***Zásady ochrany osobních údajů a podmínky použití*** | Aplikaci musíte zadat odkaz na stránku zásady ochrany osobních údajů pomocí veřejné adresy URL. Vaše podmínky použití, musí být zadaný během publikování jako text. | Požaduje se |  
| ***Podpora*** | Aplikaci musíte zadat odkaz na stránku podpory zákazníků pomocí veřejné adresy URL. Pokud je vaše aplikace používat zkušební verzi, pak musíte podporovat bez dalších poplatků během zkušebního období. | Požaduje se |  

## <a name="storefront-requirements-azure-marketplace"></a>Prezentace požadavky: Azure Marketplace  
Tady jsou požadavky pro výpis typy na webu Azure Marketplace.  

| Požadavek | Podrobnosti | Typ seznamu |  
|:--- |:--- |:--- |  
| ***Zásady účasti na webu*** | Aplikace musí následovat zásadách pro účast na webu Azure Marketplace.<ul> <li>Další informace o zásadách pro účast, najdete na webu Azure Marketplace zásadách pro účast na stránce umístění [azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | list<br />Příkaz Transact<br />trial |  
| ***Integrace s Microsoftem*** | Nabídky použijte nebo rozšířit typů služeb Microsoft Azure, například compute, sítě nebo úložiště. Do existující kategorie Azure Marketplace, jako jsou databáze, zabezpečení nebo sítě by mělo odpovídat vaší nabídky.<ul> <li>Další informace o nabídky na webu Marketplace, navštivte aplikace Marketplace stránky umístěné na [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | list<br />Příkaz Transact<br />trial |  
| ***Cílová skupina*** | Vaše nabídka musí být pro IT profesionály, vývojářům pro cloud nebo jiné role technické zákazníka. | list<br />Příkaz Transact<br />trial |  
| ***Průběžná správa*** | Je nutné povolit CRM (Marketo, Microsoft Dynamics nebo Salesforce) tak, aby přijímal data zájemce předtím, než se zobrazí vede ze storu. | list<br />Příkaz Transact<br />trial |  
| ***Zásady ochrany osobních údajů a podmínky použití*** | Aplikaci musíte zadat odkaz na stránku zásady ochrany osobních údajů pomocí veřejné adresy URL. Vaše podmínky použití, musí být zadaný během publikování jako text. | list<br />Příkaz Transact<br />trial |  
| ***Podpora*** | Nabídky musíte zadat odkaz na stránku podpory zákazníků pomocí veřejné adresy URL. Pokud vaše nabídka zkušební verzi, pak musíte podporovat bez dalších poplatků během zkušebního období. | Příkaz Transact<br />trial |    

## <a name="non-transact-listings"></a>Transact výpisy  
Tato část popisuje všechny typy nabídek, které nepoužívají Transact typ seznamu. 

### <a name="list"></a>Seznam  
V seznamu typ seznamu zahrnuje následující typy nabídek na prodejní místa na webu Marketplace.  

| Typ nabídky | Prodejní místo | Podrobnosti |  
|:---        |:---        |:---     |  
| Konzultační služby | AppSource | [Požadavky: AppSource: seznam: konzultační služby](#requirements-appsource-list-consulting-services) |  
| Konzultační služby | Azure Marketplace | [Požadavky: Azure Marketplace: seznam: konzultační služby](#requirements-azure-marketplace-list-consulting-services) |  
| Kontaktujte mě | AppSource | [](#) |  
| Kontaktujte mě | Azure Marketplace | [Požadavky: AppSource: seznam: Kontaktujte mě](#requirements-azure-marketplace-list-contact-me) |  

#### <a name="requirements-appsource-list-consulting-service"></a>Požadavky: AppSource: seznam: konzultační služby  

| Požadavky | Podrobnosti |  
|:--- |:--- |  
| Vlastnosti nabídky služeb | Konzultační služby, musí splňovat následující kritéria.<ul> <li>Dodávejte engagement-oboru, dobou trvání, pevnou cenou (nebo zdarma).</li> <li>Orient především pro před prodejem.</li> <li>Omezení pro jediného zákazníka.</li> <li>Proveďte na webu.</li> </ul> |  
| Požadavky na partnera pro konzultační služby | Splňují kritéria v příslušné oblasti pro vaši službu.<table><tr><th>Oblast řešení</th><th>Kritéria</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Máte kompetenci stříbrné nebo zlaté řízení vztahů se cloudové zákazníky.</td></tr><tr><td>Dynamics 365 pro Finance and Operations, Enterprise edition</td><td>Máte kompetenci stříbrné nebo zlaté podnikového plánování zdrojů a výnosy z vašich cloudových operací v konci 12měsíčního období $ 25 000 nebo více.</td></tr><tr><td>Dynamics 365 pro Finance and Operations, verze Business edition</td><td>Slouží jako služby poskytovatele CSP (Cloud) nebo digitální DPOR Partner of Record () pro jeden nebo více zákazníků.</td></tr><tr><td>Power BI</td><td>Kritériím partnerských řešení.</td></tr><tr><td>PowerApps</td><td>Máte prezentaci partnerských řešení.</td></tr></table><ul> <li>Další informace o řízení vztahů se zákazníky, najdete v cloudu řízení vztahů se zákazníky stránky umístěného v [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Další informace o plánování prostředků najdete plánování podnikových zdrojů stránky umístění [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Další informace o CSP najdete poskytovatel cloudových služeb, stránka umístění [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Další informace o partnera DPOR, navštivte Digital Partner of Record a přidružení partnera stránky umístěného v [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Další informace o kritériích partnerských řešení, najdete přehled partnerských řešení a umístění dokumentu pobídky [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx? SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Další informace o prezentaci partnerů, navštivte prezentaci partnerů stránky umístěného v [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Požadavky: Azure Marketplace: seznam: konzultační služby  

| Požadavky | Podrobnosti |  
|:--- |:--- |  
| Vlastnosti nabídky služeb | Konzultační služby, musí splňovat následující kritéria.<ul> <li>Dodávejte engagement-oboru, dobou trvání, pevnou cenou (nebo zdarma).</li> <li>Orient především pro před prodejem.</li> <li>Omezení pro jediného zákazníka.</li> <li>Proveďte na webu.</li> </ul> |  
| Požadavky na partnera pro konzultační služby | Stříbrné nebo zlaté úrovně musí mít v jednom z následujících schopností v příslušné oblasti pro vaši službu. <table><tr><th>Oblast řešení</th><th>S kompetencí</th></tr><td>Cloudová platforma a infrastruktura</td><td>Cloudová platforma<br />Datové centrum</td><tr><td>Vývoj aplikací a nezávislé výrobce softwaru</td><td>Vývoj aplikací<br />Integrace aplikací<br />DevOps</td></tr><tr><td>Správa dat a analýz</td><td>Analýza dat<br />Datová platforma</td></tr></table><ul> <li>Další informace o možnosti, navštivte činnost prostřednictvím programu Microsoft Partner Network stránky umístěného v [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Další informace o seznam najdete Azure Marketplace Consulting Services stránky umístění [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---   

### <a name="trial"></a>Zkušební verze  

| Typ nabídky | Prodejní místo | Podrobnosti |  
|:---        |:---        |:---     |  
| Bezplatné a zkušební verze SaaS | AppSource | [Výpis požadavky na typ: zkušební verze](#listing-type-requirements-trial) |  
| Bezplatné a zkušební verze SaaS | Azure Marketplace | [Požadavky: Azure Marketplace: zkušební verze: bezplatná zkušební verze nebo zkušební verze SaaS](#requirements-azure-marketplace-trial-free-trial-/-saas-trial) |  
| Interaktivní ukázka | AppSource | [Výpis požadavky na typ: zkušební verze](#listing-type-requirements-trial) |  
| Interaktivní ukázka | Azure Marketplace | [Požadavky: Azure Marketplace: zkušební verze: interaktivní ukázka](#requirements-azure-marketplace-trial-interactive-demo) |  
| Testovací verze | AppSource | [Výpis požadavky na typ: zkušební verze](#listing-type-requirements-trial) |  
| Testovací verze | Azure Marketplace | [Požadavky: Azure Marketplace: zkušební verze: testovací verze](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Požadavky: Azure Marketplace: zkušební verze  

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| Období bezplatné zkušební verze a zkušební verze | Vaši zákazníci mohou využívat vaše aplikace zdarma po omezenou dobu.<br /><br />Váš zákazník není potřeba platit žádné poplatky za licence nebo předplatné pro vaši nabídku nebo aplikace. Váš zákazník není potřeba platit za základní produkt společnosti Microsoft první strany nebo služby. Všechny možnosti zkušebních verzí se nasadí do vašeho předplatného Azure. Máte jediný ovládací prvek zkušební verzi pro optimalizaci nákladů a řízení.<br /><br />Můžete zvolit bezplatnou zkušební verzi, interaktivní ukázka nebo testovací verze. Bez ohledu na to, které zvolíte musí si bezplatnou zkušební verzi nabídnout zákazníkům předem určené časové období pro vyzkoušení aplikace bez dalších poplatků.<ul> <li>Zahajte proces vytváření si testovací jízdu, odešlete e-mail na [ amp-testdrive@microsoft.com ](mailto:amp-testdrive@microsoft.com).</li> </ul>Poznámka: Zkušební prostředí Azure Marketplace SaaS musí umožnit zákazníkům využívat pracovní přihlašovací údaje pro přihlášení.<ul> <li>Další informace najdete na webu AppSource zkušební prostředí nacházející se na [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Dají se snadno konfigurovat a připravené k použití řešení | Snadné a rychlé ke konfiguraci a nastavení, musí být vaše aplikace. |  
| Dostupnost / doba provozu | Aplikace SaaS nebo platformy musí mít minimálně 99,9 % dostupnost. |  
| Azure Active Directory | Nabídky musíte povolit, že Azure Active Directory (Azure AD) federované jednotné přihlašování (SSO) (Azure AD federované jednotné přihlašování) pomocí souhlasu povolena. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Požadavky: Azure Marketplace: zkušební verze: bezplatná zkušební verze nebo zkušební verze SaaS  

| Výhoda | Požadavek |  
|:--- |:--- |  
| Umožňuje zákazníkům před nákup automatizované metodou pro převod na placené použití vyzkoušet produkt. Taky umožňuje testování konceptů, pro zákazníky a společné zapojení prodejního týmu Microsoftu. | Vaše řešení je virtuální počítač nebo šablonu řešení.<br /><br />Vaše řešení je SaaS, nabízí a nabízejí víceklientské produkt SaaS.<br /><br />Máte prvního spuštění zprovoznit zákazníka a rychle.<br /><br />Máte jednoho tenanta, ale zákazníci přidat jako uživatele typu Host. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Požadavky: Azure Marketplace: zkušební verze: interaktivní ukázka  

| Výhoda | Požadavek |  
|:--- |:--- |  
| Umožňuje zobrazit vaše řešení v akci, ale bez složité nastavení zákazníkům. | Vaše řešení nevyžaduje komplexní nastavení, které by bylo obtížné dosáhnout v rámci zkušebního období. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Požadavky: Azure Marketplace: zkušební verze: testovací verze  

| Výhoda | Požadavek |  
|:--- |:--- |  
| Zkuste svůj produkt před jejich zakoupení zákazníkům umožňuje.<br /><br />Poskytuje prostředí s asistencí vašeho řešení pomocí předem nakonfigurovaných nastavení.<br /><br />Tady jsou další výhody při použití si testovací jízdu.<ul> <li>uživatelé na pouze zobrazit nabídky s testovací verze jsou kontrast 27 % uživatelských hledání na webu marketplace.</li> <li>Nabídek s testovací verze generování prodejních příležitostí 38 % více než nabídky bez.</li> <li>36 % pořízení nového zákazníka na webu marketplace pocházet od zákazníků, u nichž trvalo si testovací jízdu.</li> <li>Testovací verze povolit Microsoftu k lepšímu pochopení vašeho produktu pro společný prodej úsilí.</li> </ul> | Vaše řešení je virtuální počítač, šablona řešení nebo SaaS aplikace s jedním tenantem nebo je složitý ke zřízení. <br /><br />Není nutné způsobů, jak převést vaše zkušební verze na placené předplatné. |  

---

## <a name="transact-specific-listings"></a>Příkaz Transact specifické pro výpisy

### <a name="transact"></a>Transakce  

| Typ nabídky | Prodejní místo | Podrobnosti |   
|:---        |:---        | :--- |  
| Aplikace Azure: spravované aplikace | Azure Marketplace | [Požadavky: Azure Marketplace: Transact: aplikace Azure: spravované aplikace](#requirements-azure-marketplace-transact-azure-apps-managed-app) |  
| Aplikace Azure: Šablona řešení | Azure Marketplace | [Požadavky: Azure Marketplace: Transact: aplikace Azure: Šablona řešení](#requirements-azure-marketplace-transact-azure-apps-solution-template) |  
| Containers | Azure Marketplace | [Požadavky: Azure Marketplace: Transact: kontejner](#requirements-azure-marketplace-transact-container) |  
| Aplikace SaaS  | Azure Marketplace | [Požadavky: Azure Marketplace: Transact: aplikace SaaS](#requirements-azure-marketplace-transact-saas-app) |  
| Virtuální počítač | Azure Marketplace | [Požadavky: Azure Marketplace: Transact: virtuální počítač](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Požadavky: Azure Marketplace: Transact: kontejner  

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| K fakturaci a měření | Podpora buď bezplatnou nebo fakturační model BYOL. |  
| Image dockeru | Svou image kontejneru musí být založené na formátu image Dockeru a musí vytáhnout z Azure Container Registry. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Požadavky: Azure Marketplace: Transact: aplikace SaaS  

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| K fakturaci a měření | Vaše nabídka se účtuje paušální sazbou měsíční. Na základě využití cen a založená na využití *true nahoru* možnosti nejsou v tuto chvíli nepodporuje. |  
| Zrušení | Kdykoli je zrušitelný zákazník vaši nabídku. |  
| Cílová stránka transakce | Hostování Azure značkou transakce cílovou stránkou. Cílová stránka umožňuje zákazníkům vytvářet a spravovat váš účet služby SaaS. |  
| Předplatné SaaS API | Zadejte služba, která komunikuje s předplatným služby SaaS vytvářet, aktualizovat a odstraňovat uživatelský účet a službu plán. Všechny důležité změny rozhraní API musí podporovat během 24 hodin. Méně důležité změny rozhraní API jsou pravidelně aktualizovány. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Požadavky: Azure Marketplace: Transact: virtuální počítač  

| Požadavek | Podrobnosti |  
|:--- |:--- | 
| K fakturaci a měření | Váš virtuální počítač musí podporovat BYOL či s průběžnými platbami měsíční fakturací. |  
| Kompatibilní s Azure virtuálního pevného disku (VHD) | Virtuální počítače musí být sestaveny jako ve Windows nebo Linuxu.<ul> <li>Další informace o vytváření linuxového virtuálního pevného disku najdete vytvořením kompatibilní s Azure virtuálního pevného disku (založené na Linuxu) části umístění [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-Based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Další informace o vytváření virtuálního pevného disku Windows najdete vytvořením kompatibilní s Azure virtuálního pevného disku (založený na Windows) části umístění [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-Based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul> |  

## <a name="next-steps"></a>Další postup
*   Přejděte [Azure Marketplace a AppSource příručce pro vydavatele](./marketplace-publishers-guide.md) stránky.  
 
---  
