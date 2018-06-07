---
title: Požadavky tak, že uvedete typ | Azure
description: Tento článek popisuje podmínky kritéria a požadavky partnerů snaží pochopit, jak publikovat aplikace pro Azure Marketplace.
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
ms.openlocfilehash: dbb14854f00fb133c3604a1dd529d42120371fd2
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825094"
---
# <a name="requirements-by-listing-type"></a>Požadavky tak, že uvedete typu  
Obsahu požadavky na technické a marketing se liší podle výkladní skříň, typ nabídky a výpis typu. Zkontrolujte následující specifikace k ověření vaší shody.  
1. StoreFront požadavky:  
    *   [AppSource](#storefront-requirements:-appSource)  
    *   [Azure Marketplace](#storefront-requirements:-azure-marketplace)  
2. Výpis typu a požadavky na typ nabídky.  
    *   Další informace o seznamu typy a typy nabídek, navštivte určete typ výpis pro vaše řešení stránky umístěné v [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Požadavky na StoreFront: AppSource  
Následující tabulka popisuje požadavky na požadovaných součástí pro publikování na AppSource.  
| Požadavek | Podrobnosti | Povinné nebo doporučené |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Aplikace musí umožňovat Azure Active Directory federované jednotné přihlašování (Azure AD federované jednotné přihlašování) s souhlasu povolena.<ul> <li>Pro další informace o povolení služby Azure AD federovaného jednotného přihlašování, přejděte Konfigurace jednotného přihlašování k aplikacím, které nejsou ve službě Azure Active Directory stránka Galerie aplikace umístěné v [docs.microsoft.com/azure/active-directory/ Active-directory-saas vlastní apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Požaduje se |   
| ***Integrace s cloudové služby společnosti Microsoft*** | Aplikace by měla integraci s jinými službami Microsoft Cloud jako služeb Microsoft Power BI, Cortana Intelligence nebo Microsoft Azure.<ul> <li>Je například Microsoft cloudové služby Internet věcí.</li> </ul> | Doporučené |  
| ***Cílová skupina*** | Aplikace musí být-obchodní uživatelům a vlastníkům obchodních. | Požaduje se | 
| ***Software jako služba (SaaS) aplikace pro firmy*** | Aplikace musí splňovat následující požadavky.<ul> <li>-Obchodní aplikace SaaS</li> <li>Zaměřuje obchodní – proces</li> <li>Cílem obchodní zákazníkům</li> <li>Umožníte uživatelům používat jejich pracovní přihlašovací údaje pro přihlášení jako je například uživatelské jméno a heslo</li> </ul> | Požaduje se |  
| ***Bezplatné zkušební období a zkušební verze*** | Aplikace musí obsahovat jeden následující možnosti v pořadí zákazník používají vaši aplikaci zdarma po omezenou dobu.<ul> <li>Zadejte `try` proto zákazníky může spuštění zkušební verze aplikace v rámci AppSource</li> <li>Zadejte `request trial` možnost v AppSource, takže mohou požádat o zkušební verzi vaší aplikace</li> </ul>Bezplatná zkušební verze, které poskytujete nabídnout zákazníkům předem nastavené množství času můžete vyzkoušet na vaší aplikace pomocí bez dalších nákladů. | Požaduje se |  
| ***Řešení snadno konfigurovat, připravené k použití*** | Aplikace musí být snadno a rychle konfiguraci a nastavení s žádné přizpůsobení vyžaduje. | Požaduje se |  
| ***Vést správy*** | Je nutné povolit aplikaci CRM tak, aby přijímal realizace data předtím, než se zobrazí vede z výkladní skříň.<ul> <li>Příkladem certifikované jsou Marketo, Microsoft Dynamics nebo Salesforce</li> </ul> | Požaduje se |  
| ***Zásady ochrany osobních údajů a podmínky použití*** | Aplikace musí zadat odkaz na stránku zásady ochrany osobních údajů pomocí veřejnou adresu URL. Je třeba zadat vaše podmínky použití během publikování jako text. | Požaduje se |  
| ***Podpora*** | Aplikace musí zadat odkaz na stránku podpory zákazníků pomocí veřejnou adresu URL. Pokud je vaše aplikace k zkušební verzi, pak musíte podporovat bez dalších poplatků během zkušebního období. | Požaduje se |  

## <a name="storefront-requirements-azure-marketplace"></a>StoreFront požadavky: Azure Marketplace  
Tady jsou požadované požadavky pro výpis typy v Azure Marketplace.  
| Požadavek | Podrobnosti | Typ seznamu |  
|:--- |:--- |:--- |  
| ***Zásady zapojení*** | Aplikace musí splňovat zásady zapojení Azure Marketplace.<ul> <li>Další informace o zapojení zásady, navštivte stránku nacházející se v Azure Marketplace zapojení zásady [azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | Seznam<br />Příkaz Transact<br />Zkušební verze |  
| ***Integrace s Microsoft*** | Vaši nabídku použijte nebo rozšířit typů služeb Microsoft Azure, jako jsou výpočty, sítě nebo úložiště. Vaši nabídku, zarovnán do existující kategorie Azure Marketplace například databáze, zabezpečení nebo sítě.<ul> <li>Další informace o Marketplace nabízí na webu Marketplace aplikací stránky nacházející se v [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | Seznam<br />Příkaz Transact<br />Zkušební verze |  
| ***Cílová skupina*** | Vaši nabídku musí být pro profesionály v oblasti IT, vývojáři cloudu nebo jiné role technické zákazníka. | Seznam<br />Příkaz Transact<br />Zkušební verze |  
| ***Vést správy*** | Je nutné povolit CRM (Marketo, Microsoft Dynamics nebo Salesforce) tak, aby přijímal realizace data předtím, než se zobrazí vede z výkladní skříň. | Seznam<br />Příkaz Transact<br />Zkušební verze |  
| ***Zásady ochrany osobních údajů a podmínky použití*** | Aplikace musí zadat odkaz na stránku zásady ochrany osobních údajů pomocí veřejnou adresu URL. Je třeba zadat vaše podmínky použití během publikování jako text. | Seznam<br />Příkaz Transact<br />Zkušební verze |  
| ***Podpora*** | Vaši nabídku musíte zadat odkaz na stránku podpory zákazníků pomocí veřejnou adresu URL. Pokud vaši nabídku zkušební verzi, pak musíte podporovat bez dalších poplatků během zkušebního období. | Příkaz Transact<br />Zkušební verze |    

## <a name="non-transact-listings"></a>Transact výpisech  
Tato část popisuje všechny typy nabídky, které nepoužívají Transact typ seznamu. 

### <a name="list"></a>Seznam  
V seznamu typ seznamu zahrnuje následující typy nabídka na obchodní poutače v marketplace.  

| Typ nabídky | Prodejní místo | Podrobnosti |  
|:---        |:---        |:---     |  
| Konzultační služby | AppSource | [Požadavky na: AppSource: seznam: konzultační služby](#requirements:-appsource:-list:-consulting-services) |  
| Konzultační služby | Azure Marketplace | [Požadavky na: Azure Marketplace: seznam: konzultační služby](#requirements:-azure-marketplace:-list:-consulting-services) |  
| Kontaktujte mě | AppSource | [](#) |  
| Kontaktujte mě | Azure Marketplace | [Požadavky na: AppSource: seznam: mě](#requirements:-azure-marketplace:-list:-contact-me) |  

#### <a name="requirements-appsource-list-consulting-service"></a>Požadavky na: AppSource: seznam: konzultace ohledně služby  

| Požadavky | Podrobnosti |  
|:--- |:--- |  
| Vlastnosti nabídka služby | Konzultační služby musí splňovat následující kritéria.<ul> <li>Poskytování engagement-oboru, Pevná doba trvání, pevné ceny (nebo volné).</li> <li>Orient především pro předprodejní.</li> <li>Omezte na jednoho zákazníka.</li> <li>Proveďte v lokalitě.</li> </ul> |  
| Požadavky na partnera pro služby Consulting Services | Splňují kritéria v příslušné oblasti pro vaši službu.<table><tr><th>Řešení oblasti</th><th>Kritéria</th></tr><tr><td>Dynamics 365 pro zákaznické Engagement</td><td>Máte Gold cloudu zákazníka relace správy nebo Silver competency.</td></tr><tr><td>Dynamics 365 Finance a operace, Enterprise edition</td><td>Mějte koncové dobu 12 měsíců $ 25 000 nebo více competency Silver nebo plánování Gold podnikových prostředků a výnosy z vaší operace cloudu.</td></tr><tr><td>Dynamics 365 Finance a operace, Business edition</td><td>Sloužit jako poskytovatele cloudové služby (CSP) nebo digitální partnera z záznamu (DPOR) pro jeden nebo více zákazníků.</td></tr><tr><td>Power BI</td><td>Splňují kritéria partnerských řešení.</td></tr><tr><td>PowerApps</td><td>Máte představením partnerských řešení.</td></tr></table><ul> <li>Další informace o řízení vztahů se zákazníky, naleznete v cloudu řízení vztahů se zákazníky stránky nacházející se v [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Další informace o plánování prostředků, navštivte plánování prostředků Enterprise stránky nacházející se v [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Další informace o CSP, navštivte stránku nacházející se v poskytovatele cloudové služby [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Další informace o DPOR najdete v článku digitální preferovaného partnera a přidružení partnera stránky na [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Další informace o kritériích partnerských řešení, najdete v článku Přehled partnera řešení a motivace dokumentu na [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx? SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Další informace o představením partnera, navštivte představením partnera stránky nacházející se v [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Požadavky na: Azure Marketplace: seznam: konzultace ohledně služby  

| Požadavky | Podrobnosti |  
|:--- |:--- |  
| Vlastnosti nabídka služby | Konzultační služby musí splňovat následující kritéria.<ul> <li>Poskytování engagement-oboru, Pevná doba trvání, pevné ceny (nebo volné).</li> <li>Orient především pro předprodejní.</li> <li>Omezte na jednoho zákazníka.</li> <li>Proveďte v lokalitě.</li> </ul> |  
| Požadavky na partnera pro služby Consulting Services | Stříbrná nebo zlatý musí mít v jedné z následujících možnosti v příslušné oblasti pro vaši službu. <table><tr><th>Řešení oblasti</th><th>Competency</th></tr><td>Cloudová platforma a infrastruktury</td><td>Cloudová platforma<br />Datové centrum</td><tr><td>Vývoj aplikací a ISV</td><td>Vývoj aplikací<br />Integrace aplikací<br />DevOps</td></tr><tr><td>Správa dat a analýzy</td><td>Analýza dat<br />Datová platforma</td></tr></table><ul> <li>Další informace o možnosti naleznete možnosti prostřednictvím Microsoft Partner Network stránky nacházející se v [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Další informace o seznamu najdete na webu Azure Marketplace konzultace ohledně služby stránky nacházející se v [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---   

### <a name="trial"></a>Zkušební verze  

| Typ nabídky | Prodejní místo | Podrobnosti |  
|:---        |:---        |:---     |  
| Volné nebo zkušební verze SaaS | AppSource | [Výpis požadavky na typ: zkušební verze](#listing-type-requirements:-trial) |  
| Volné nebo zkušební verze SaaS | Azure Marketplace | [Požadavky na: Azure Marketplace: zkušební verze: bezplatnou zkušební verzi nebo zkušební verze SaaS](#requirements:-azure-marketplace:-trial:-free-trial-/-saas-trial) |  
| Interaktivní ukázka | AppSource | [Výpis požadavky na typ: zkušební verze](#listing-type-requirements:-trial) |  
| Interaktivní ukázka | Azure Marketplace | [Požadavky na: Azure Marketplace: zkušební verze: Interaktivní demo](#requirements:-azure-marketplace:-trial:-interactive-demo) |  
| Testovací verze | AppSource | [Výpis požadavky na typ: zkušební verze](#listing-type-requirements:-trial) |  
| Testovací verze | Azure Marketplace | [Požadavky na: Azure Marketplace: zkušební verze: Vyzkoušejte](#requirements:-azure-marketplace:-trial:-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Požadavky na: Azure Marketplace: zkušební verze  

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| Bezplatné zkušební období a zkušební verze | Zákazník může využít svoji aplikaci zdarma po omezenou dobu.<br /><br />Zákazník nemusí platit žádné poplatky licence nebo předplatné pro nabídky nebo aplikace. Zákazník není nutné věnovat pro základní Microsoft první strany produktu nebo služby. Všechny zkušební možnosti se nasadí do vašeho předplatného Azure. Máte zkušební verze jedinou řízení optimalizace nákladů a správu.<br /><br />Můžete vybrat bezplatnou zkušební verzi, interaktivní ukázku, nebo vyzkoušejte. Bez ohledu na to, co si zvolíte musí používat bezplatnou zkušební verzi nabídnout zákazníkům předem určené čas na vyzkoušení aplikace bez dalších poplatků.<ul> <li>Pokud chcete zahájit proces vytváření test jednotky, odešlete e-mail na [ amp-testdrive@microsoft.com ](mailto:amp-testdrive@microsoft.com).</li> </ul>Poznámka: Zkušební prostředí Azure Marketplace SaaS musí umožňovat zákazníkům používat pracovní přihlašovací údaje pro přihlášení.<ul> <li>Další informace najdete v článku AppSource zkušební prostředí nacházející se na [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Řešení snadno konfigurovat, připravené k použití | Aplikace musí být snadno a rychle konfiguraci a nastavení. |  
| Dostupnosti nebo provozu | Aplikace SaaS nebo platformy, musí mít nejméně 99,9 % dostupnost. |  
| Azure Active Directory | Vaši nabídku musíte povolit, že Azure Active Directory (Azure AD) federované jednotné přihlašování (SSO) (Azure AD federované jednotné přihlašování) s souhlasu povolena. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Požadavky na: Azure Marketplace: zkušební verze: bezplatnou zkušební verzi nebo zkušební verze SaaS  

| Výhoda | Požadavek |  
|:--- |:--- |  
| Umožňuje zákazník zkuste svůj produkt předtím, než kdybyste kupovali pomocí metody automatizované pro převod na placené použití. Taky umožňuje důkazy konceptu pro odběratele a společné zapojení týmy prodeje společnosti Microsoft. | Řešení je virtuální počítač nebo šablona řešení.<br /><br />Řešení je SaaS nabídky a nabízejí víceklientské SaaS produktu.<br /><br />Máte při prvním spuštění ke zprovoznění zákazníka a rychlý.<br /><br />Máte jednoho klienta, ale přidáváte zákazníkům jako uživatele typu Host. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Požadavky na: Azure Marketplace: zkušební verze: Interaktivní demo  

| Výhoda | Požadavek |  
|:--- |:--- |  
| Umožňuje zákazníkům najdete v části řešení v akci bez složitost nastavení. | Řešení vyžaduje komplexní nastavení, které by byly pevné dosažení během zkušebního období. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Požadavky na: Azure Marketplace: zkušební verze: Vyzkoušejte  

| Výhoda | Požadavek |  
|:--- |:--- |  
| Umožňuje zákazník zkuste svůj produkt před jejich zakoupení.<br /><br />Poskytuje návodné vašeho řešení pomocí předem nakonfigurovaných nastavení.<br /><br />Následující další výhody při používání se test jednotky.<ul> <li>uživatelé k jenom zobrazit nabídkám s test jednotky jsou vylepšení 27 % uživatele vyhledávání na webu marketplace.</li> <li>Nabízí se test jednotky generování zájemců 38 % více než nabízí bez.</li> <li>36 % nové akvizicích zákazníků na webu marketplace pocházet od zákazníků, kteří trvalo test jednotky.</li> <li>Test jednotky povolit Microsoft prodejci pole pro lepší pochopení vašeho produktu pro společné prodává úsilí.</li> </ul> | Řešení je virtuální počítač, šablona řešení nebo SaaS aplikace pomocí jednoho klienta, nebo je složitý zřídit. <br /><br />Nemáte metodu pro zkušební verzi převést na placené nabídku. |  

---

## <a name="transact-specific-listings"></a>Příkaz Transact konkrétní seznamy

### <a name="transact"></a>Transakce  

| Typ nabídky | Prodejní místo | Podrobnosti |   
|:---        |:---        | :--- |  
| Aplikace Azure: spravované aplikace | Azure Marketplace | [Požadavky na: Azure Marketplace: Transact: aplikace Azure: spravované aplikace](#requirements:-azure-marketplace:-transact:-azure-apps:-managed-app) |  
| Aplikace Azure: řešení šablony | Azure Marketplace | [Požadavky na: Azure Marketplace: Transact: aplikace Azure: řešení šablony](#requirements:-azure-marketplace:-transact:-azure-apps:-solution-template) |  
| Containers | Azure Marketplace | [Požadavky na: Azure Marketplace: Transact: kontejneru](#requirements:-azure-marketplace:-transact:-container) |  
| Aplikace SaaS  | Azure Marketplace | [Požadavky na: Azure Marketplace: Transact: SaaS aplikace](#requirements:-azure-marketplace:-transact:-saas-app) |  
| Virtuální počítač | Azure Marketplace | [Požadavky na: Azure Marketplace: Transact: virtuálního počítače](#requirements:-azure-marketplace:-transact:-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Požadavky na: Azure Marketplace: Transact: kontejneru  

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| Fakturace a měření | Podpora buď volné nebo fakturační model BYOL. |  
| Bitové kopie založené na docker | Bitové kopie kontejneru musí být založená na formát obrázku Docker a musí být vyžádány z Azure kontejneru registrech. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Požadavky na: Azure Marketplace: Transact: SaaS aplikace  

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| Fakturace a měření | Vaši nabídku je cenově paušálně měsíční. Na základě využití cenové a na základě využití *true-up* v tuto chvíli nepodporuje možnosti. |  
| Zrušení | Kdykoli je možné zrušit zákazník vaši nabídku. |  
| Cílová stránka transakce | Hostitel cílová stránka Azure související transakci. Cílová stránka umožňuje zákazníkům vytvářet a spravovat účet služby SaaS. |  
| Předplatné SaaS rozhraní API | Zadejte službu, která komunikuje s předplatným SaaS vytvářet, aktualizovat a odstraňovat plán uživatelského účtu a služby. Všechny kritické změny rozhraní API musí podporovat do 24 hodin. Méně závažné změny rozhraní API se pravidelně aktualizuje. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Požadavky na: Azure Marketplace: Transact: virtuálního počítače  

| Požadavek | Podrobnosti |  
|:--- |:--- | 
| Fakturace a měření | Virtuální počítač musí podporovat BYOL nebo průběžné platby měsíční fakturace. |  
| Azure kompatibilní virtuální pevný disk (VHD) | Virtuální počítače musí být založený na systému Windows nebo Linux.<ul> <li>Další informace o vytváření Linux VHD, najdete v článku vytvořit Azure kompatibilní virtuální pevný disk (systémem Linux) části nacházející se v [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-Based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Další informace o vytvoření virtuálního pevného disku Windows, najdete v článku vytvořit Azure kompatibilní virtuálního pevného disku (systému Windows) části nacházející se v [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-Based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul> |  

## <a name="next-steps"></a>Další postup
*   Přejděte [AppSource vydavatele průvodce a Azure Marketplace](./marketplace-publishers-guide.md) stránky.  
 
---  
