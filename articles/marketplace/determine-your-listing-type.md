---
title: Určit typ výpisu v Azure Marketplace | Azure
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
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 1c7fd3ac1a4cf62dd8f909acc26615ea5f96f32c
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309094"
---
# <a name="determine-the-listing-type-for-your-solution"></a>Určuje typ výpis pro vaše řešení  
Obchodní poutače podporují mnoho možností publikování: výpis typy a typy nabízet. Vyberte typ nabídky, který nejlépe představuje podrobností o řešení a služby. Všechny možnosti publikování poskytují že přístup k vést sdílení.   

| Prodejní místo | Typ seznamu | Typ aplikace | Technické pokyny |  
|:--- |:--- |:--- |:--- |  
| AppSource | [seznam](#list) | [Konzultační služby](#requirements-appSource-list-consulting-service) | [smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf](https://smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf) |  
| Azure Marketplace | [seznam](#list) | [Konzultační služby](#requirements-azure-marketplace-list-consulting-service) | [](#) |  
| Azure Marketplace | [seznam](#list) | [Obraťte se na mě](#requirements-azure-marketplace-list-contact-me) | [](#) |  
| Azure Marketplace | [Zkušební verze](#trial) | [Bezplatná zkušební verze nebo zkušební verze SaaS](#requirements-azure-marketplace-trial-free-trial-/-saas-trial) | [](#) |  
| AppSource |  | Cortana Intelligence | [docs.microsoft.com/Azure/Machine-Learning/Team-data-Science-Process/cortana-Intelligence-appsource-Publishing-Guide](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-intelligence-appsource-publishing-guide) |  
| AppSource |  |Dynamics 365 pro zákaznické Engagement | [docs.microsoft.com/dynamics365/Customer-engagement/Developer/Publish-App-appsource](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource) |  
| AppSource |  | Dynamics 365 Finance a operace | [docs.microsoft.com/dynamics365/Unified-Operations/dev-ITPro/LCS-Solutions/LCS-Solutions-App-Source](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/lcs-solutions/lcs-solutions-app-source) |  
| Azure Marketplace | [Zkušební verze](#trial) | [Interaktivní demo](#requirements-azure-marketplace-trial-interactive-demo) | [](#) |  
| Azure Marketplace | [Zkušební verze](#trial) | [Vyzkoušejte](#requirements-azure-marketplace-trial-test-drive) | [](#) |  
| AppSource |  | Office 365 | [docs.microsoft.com/Office/dev/Store/Submit-to-the-Office-Store](https://docs.microsoft.com/office/dev/store/submit-to-the-office-store) |  
| AppSource |  | Power BI | [docs.microsoft.com/Power-BI/Developer/Office-Store](https://docs.microsoft.com/power-bi/developer/office-store) | | Azure Marketplace | [Příkaz Transact](#transact) | [Aplikace Azure: spravované aplikace](#requirements-azure-marketplace-transact-azure-apps-managed-app) |  [docs.microsoft.com/Azure/Managed-Applications/Overview](https://docs.microsoft.com/azure/managed-applications/overview) |  
| Azure Marketplace | [Příkaz Transact](#transact) | [Aplikace Azure: řešení šablony](#requirements-azure-marketplace-transact-azure-apps-solution-template) |  [](#) |  
| Azure Marketplace | [Příkaz Transact](#transact) | [kontejner](#requirements-azure-marketplace-transact-container) |  [](#) |  
| Azure Marketplace | [Příkaz Transact](#transact) | [Aplikace SaaS](#requirements-azure-marketplace-transact-saas-app) |  [](#) |  
| Azure Marketplace | [Příkaz Transact](#transact) | [Virtuální počítač](#requirements-azure-marketplace-transact-virtual-machine) |  [](#) |   

## <a name="azure-certified-program"></a>Azure Certified programu  
Program Azure certifikované jsou testovány všechny virtuální počítače (VM) publikované v Azure Marketplace.  
*   Další informace najdete v článku Microsoft Azure certifikaci, stránka nacházející se v [azure.microsoft.com/marketplace/programs/certified](https://azure.microsoft.com/marketplace/programs/certified). 

Program certifikační obsahuje následující informace.  
*   Virtuální počítač je kompatibilní s platformou Azure a Marketplace prodávané modelu.  
*   Virtuální počítač je testován pro dodržování předpisů zabezpečení online bitové kopie, včetně virů a malwaru.  
*   Virtuální počítač obsahuje badging na úrovni nabídky. Badging jako ověřená řešení vylepšuje podporu pro Microsoft enterprise zákazníky.   

---   

## <a name="appsource-publishing-by-product"></a>AppSource publikování produktu  
Následující tabulka obsahuje další informace o požadavcích na konkrétní AppSource aplikace, které rozšiřují Office, Dynamics a Power BI.  

| Typ seznamu | Typ aplikace | Technické pokyny |  
|:--- |:--- |:--- |  
| [seznam](#list) | [Konzultační služby](#requirements-appSource-list-consulting-service) | [smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf](https://smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf) |  
|  | Cortana Intelligence | [docs.microsoft.com/Azure/Machine-Learning/Team-data-Science-Process/cortana-Intelligence-appsource-Publishing-Guide](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-intelligence-appsource-publishing-guide) |  
|  |Dynamics 365 pro zákaznické Engagement | [docs.microsoft.com/dynamics365/Customer-engagement/Developer/Publish-App-appsource](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource) |  
|  | Dynamics 365 Finance a operace | [docs.microsoft.com/dynamics365/Unified-Operations/dev-ITPro/LCS-Solutions/LCS-Solutions-App-Source](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/lcs-solutions/lcs-solutions-app-source) |  
|  | Office 365 | [docs.microsoft.com/Office/dev/Store/Submit-to-the-Office-Store](https://docs.microsoft.com/office/dev/store/submit-to-the-office-store) |  
|  | Power BI | [docs.microsoft.com/Power-BI/Developer/Office-Store](https://docs.microsoft.com/power-bi/developer/office-store) |  

---   

## <a name="azure-marketplace-publishing-by-product"></a>Azure Marketplace publikování produktu  

![](./media/marketplace-publishers-guide/workflow-azure-marketplace.png)  

Následující tabulka poskytuje že další informace o specifické požadavky pro Azure Marketplace nabízí.  

| Typ seznamu | Typ nabídky |  Technické pokyny |  
|:--- |:--- |:--- |  
| [seznam](#list) | [Konzultační služby](#requirements-azure-marketplace-list-consulting-service) | [](#) |  
| [seznam](#list) | [Obraťte se na mě](#requirements-azure-marketplace-list-contact-me) | [](#) |  
| [Zkušební verze](#trial) | [Bezplatná zkušební verze nebo zkušební verze SaaS](#requirements-azure-marketplace-trial-free-trial-/-saas-trial) | [](#) |  
| [Zkušební verze](#trial) | [Interaktivní demo](#requirements-azure-marketplace-trial-interactive-demo) | [](#) |  
| [Zkušební verze](#trial) | [Vyzkoušejte](#requirements-azure-marketplace-trial-test-drive) | [](#) |  
| [Příkaz Transact](#transact) | [Aplikace Azure: spravované aplikace](#requirements-azure-marketplace-transact-azure-apps-managed-app) |  [docs.microsoft.com/Azure/Managed-Applications/Overview](https://docs.microsoft.com/azure/managed-applications/overview) |  
| [Příkaz Transact](#transact) | [Aplikace Azure: řešení šablony](#requirements-azure-marketplace-transact-azure-apps-solution-template) |  [](#) |  
| [Příkaz Transact](#transact) | [kontejner](#requirements-azure-marketplace-transact-container) |  [](#) |  
| [Příkaz Transact](#transact) | [Aplikace SaaS](#requirements-azure-marketplace-transact-saas-app) |  [](#) |  
| [Příkaz Transact](#transact) | [Virtuální počítač](#requirements-azure-marketplace-transact-virtual-machine) |  [](#) |  

---   

## <a name="non-transact-listings"></a>Transact výpisech  
Tato část popisuje všechny nabídky, které nepoužívají Transact typ seznamu. 

### <a name="list"></a>Seznam  
V seznamu typ seznamu zahrnuje následující typy nabídka na obchodní poutače v marketplace.  

| Typ nabídky | Prodejní místo | Podrobnosti |  
|:---        |:---        |:---     |  
| Konzultační služby | AppSource | [Požadavky na: AppSource: seznam: konzultační služby](#requirements-appsource-list-consulting-service) |  
| Konzultační služby | Azure Marketplace | [Požadavky na: Azure Marketplace: seznam: konzultační služby](#requirements-azure-marketplace-list-consulting-service) |  
| Kontaktujte mě | AppSource | [](#) |  
| Kontaktujte mě | Azure Marketplace | [Požadavky na: AppSource: seznam: mě](#requirements-azure-marketplace-list-contact-me) |  

#### <a name="requirements-appsource-list-consulting-service"></a>Požadavky na: AppSource: seznam: konzultace ohledně služby  
Když nabídku se primárně skládá z profesionální služby, například hodnocení, implementace, pracovní konference, použijte typ nabídky konzultace ohledně služby.  

| Požadavky | Podrobnosti |  
|:--- |:--- |  
| Vlastnosti nabídka služby | Konzultační služby musí splňovat následující kritéria.<ul> <li>Poskytování engagement-oboru, Pevná doba trvání, pevné ceny (nebo volné).</li> <li>Orient především pro předprodejní.</li> <li>Omezte na jednoho zákazníka.</li> <li>Proveďte v lokalitě.</li> </ul> |  
| Požadavky na partnera pro služby Consulting Services | Splňují kritéria v příslušné oblasti pro vaši službu.<table><tr><th>Řešení oblasti</th><th>Kritéria</th></tr><tr><td>Dynamics 365 pro zákaznické Engagement</td><td>Máte Gold cloudu zákazníka relace správy nebo Silver competency.</td></tr><tr><td>Dynamics 365 Finance a operace, Enterprise edition</td><td>Mějte koncové dobu 12 měsíců $ 25 000 nebo více competency Silver nebo plánování Gold podnikových prostředků a výnosy z vaší operace cloudu.</td></tr><tr><td>Dynamics 365 Finance a operace, Business edition</td><td>Sloužit jako poskytovatele cloudové služby (CSP) nebo digitální partnera z záznamu (DPOR) pro jeden nebo více zákazníků.</td></tr><tr><td>Power BI</td><td>Splňují kritéria partnerských řešení.</td></tr><tr><td>PowerApps</td><td>Máte představením partnerských řešení.</td></tr></table><ul> <li>Další informace o řízení vztahů se zákazníky, naleznete v cloudu řízení vztahů se zákazníky stránky nacházející se v [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Další informace o plánování prostředků, navštivte plánování prostředků Enterprise stránky nacházející se v [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Další informace o CSP, navštivte stránku nacházející se v poskytovatele cloudové služby [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Další informace o DPOR najdete v článku digitální preferovaného partnera a přidružení partnera stránky na [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Další informace o kritériích partnerských řešení, najdete v článku Přehled partnera řešení a motivace dokumentu na [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx? SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Další informace o představením partnera, navštivte představením partnera stránky nacházející se v [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Požadavky na: Azure Marketplace: seznam: konzultace ohledně služby  

| Požadavky | Podrobnosti |  
|:--- |:--- |  
| Vlastnosti nabídka služby | Konzultační služby musí splňovat následující kritéria.<ul> <li>Poskytování engagement-oboru, Pevná doba trvání, pevné ceny (nebo volné).</li> <li>Orient především pro předprodejní.</li> <li>Omezte na jednoho zákazníka.</li> <li>Proveďte v lokalitě.</li> </ul> |  
| Požadavky na partnera pro služby Consulting Services | Stříbrná nebo zlatý musí mít v jedné z následujících možnosti v příslušné oblasti pro vaši službu. <table><tr><th>Řešení oblasti</th><th>Competency</th></tr><td>Cloudová platforma a infrastruktury</td><td>Cloudová platforma<br />Datové centrum</td><tr><td>Vývoj aplikací a ISV</td><td>Vývoj aplikací<br />Integrace aplikací<br />DevOps</td></tr><tr><td>Správa dat a analýzy</td><td>Analýza dat<br />Datová platforma</td></tr></table><ul> <li>Další informace o možnosti naleznete možnosti prostřednictvím Microsoft Partner Network stránky nacházející se v [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Další informace o seznamu najdete na webu Azure Marketplace konzultace ohledně služby stránky nacházející se v [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

#### <a name="requirements-azure-marketplace-list-contact-me"></a>Požadavky na: Azure Marketplace: seznam: mě  
Obraťte se na použití vám nabízejí typ při použití zkušební verze nebo transact výpis typu není vhodná. Výhodou obraťte se na mě nabízejí, že je schopný přijímat okamžitě vám vede řešení na trhu. Zájemců by měla vycházet do základní nabídky zahájíte setrvačníku vaší firmy. Nevýhodou je, že zákaznické engagement je omezená, v porovnání s jinými typy nabídky.  

>[!IMPORTANT]
>Zákaznické engagement je vhodné se zkušební verze a Transact typy nabídek. Hodnota mě kontaktovat typ seznamu je realizace, která se zobrazí. Pokud si zvolíte kontakt mi typ seznamu, pak je nutné ověřit, že je nakonfigurovaný cíl realizace a jste připraveni k maximalizaci vaší realizace.  

---   

### <a name="trial"></a>Zkušební verze  
Poskytnutí zkušební prostředí zvyšuje úroveň engagement poskytuje zákazníkům. Úroveň vyšší engagement způsobí, že bohatší vystavení řešení. Typ nabídka zkušební verze umožňuje zákazníkům prozkoumat řešení než kdybyste kupovali. Pomocí zkušební verze typ seznamu máte v obchodní poutače vyšší riziko povýšení. Také byste měli očekávat další a bohatší zájemců od zákazníků oznámeních podporujících zapojení uživatelů.  

Aplikace nebo nabídka pomocí zkušební verze typ seznamu je nasazený na zkušební prostředí a předplatné Azure. Jiné typy výpis se nasazuje prostředí nebo předplatné zákazníka. Aplikace nebo nabídka by měl být zákazníka vedla bez další požadavky na koupit. Aplikace nebo nabídka by měl být minimální, pokud existuje, na dokončení případ použití další konfigurace. Aplikace nebo nabídka musí zahrnovat bezplatnou podporou aspoň po dobu trvání zkušebního období. Vaši zákazníci by měla vycházet a monitorovat podél záměrné vyhodnocení cesty pro dosažení co nejlepších výsledků. Můžete se doporučuje zájemce zadaný na webu marketplace a z vlastní intelligence v aplikaci používat. Zájemců slouží ke sledování a správě vašich zákazníků.  

Zkušební verze typ seznamu zahrnuje následující typy nabídka na obchodní poutače v marketplace.  

| Typ nabídky | Prodejní místo | Podrobnosti |  
|:---        |:---        |:---     |  
| Volné nebo zkušební verze SaaS | AppSource | [Výpis požadavky na typ: zkušební verze](#listing-type-requirements-trial) |  
| Volné nebo zkušební verze SaaS | Azure Marketplace | [Požadavky na: Azure Marketplace: zkušební verze: bezplatnou zkušební verzi nebo zkušební verze SaaS](#requirements-azure-marketplace-trial-free-trial-/-saas-trial) |  
| Interaktivní ukázka | AppSource | [Výpis požadavky na typ: zkušební verze](#listing-type-requirements-trial) |  
| Interaktivní ukázka | Azure Marketplace | [Požadavky na: Azure Marketplace: zkušební verze: Interaktivní demo](#requirements-azure-marketplace-trial-interactive-demo) |  
| Testovací verze | AppSource | [Výpis požadavky na typ: zkušební verze](#listing-type-requirements-trial) |  
| Testovací verze | Azure Marketplace | [Požadavky na: Azure Marketplace: zkušební verze: Vyzkoušejte](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Požadavky na: Azure Marketplace: zkušební verze  

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| Bezplatné zkušební období a zkušební verze | Zákazník se může pokusit aplikaci zdarma po omezenou dobu.<br /><br />Zákazník nemusí platit žádné poplatky licence nebo předplatné pro vaši nabídku. Vaši zákazníci nejsou nutné k platbě za základní Microsoft první strany produktu nebo služby. Všechny zkušební možnosti se nasadí do vašeho předplatného Azure. Máte jedinou řízení optimalizace nákladů a správu.<br /><br />Můžete vybrat bezplatnou zkušební verzi, interaktivní ukázku, nebo vyzkoušejte. Bez ohledu na to, co si zvolíte musí vaše bezplatné zkušební období zadejte zákazník předem určené čas na vyzkoušení vaši nabídku bez dalších poplatků.<ul> <li>Pokud chcete zahájit proces vytváření test jednotky, odešlete e-mail na [ amp-testdrive@microsoft.com ](mailto:amp-testdrive@microsoft.com).</li> </ul>Poznámka: Všechny SaaS nabízí zkušební verze na webu Azure Marketplace musí umožňovat zákazníkovi použít pracovní přihlašovací údaje pro přihlášení.<ul> <li>Další informace najdete v článku AppSource zkušební prostředí nacházející se na [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Řešení snadno konfigurovat, připravené k použití | Aplikace musí být snadno a rychle konfiguraci a nastavení. |  
| Dostupnosti nebo provozu | Aplikace SaaS nebo platformy, musí mít nejméně 99,9 % dostupnost. |  
| Azure Active Directory | Vaši nabídku musíte povolit, že Azure Active Directory (Azure AD) federované jednotné přihlašování (SSO) (Azure AD federované jednotné přihlašování) s souhlasu povolena. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Požadavky na: Azure Marketplace: zkušební verze: bezplatnou zkušební verzi nebo zkušební verze SaaS  
Poskytuje řešení nebo aplikace pomocí uvolněte na zkuste, software jako služba (SaaS) – na základě zkušební verze. Zkušební SaaS nabízejí typ jednotky vysoce kvalitní zájemců od zúčastněných zákazníků, můžete spustit setrvačníku vaší firmy. Nabídka bezplatné zkušební verze typu může zobrazovat jako omezeného použití nebo omezené trvání zkušební účet. Bezplatná zkušební verze by měla obsahovat volání akce pro urychlení převod na placené použití vašeho řešení.  

| Výhoda | Požadavek |  
|:--- |:--- |  
| Umožňuje zákazníkovi pokusit svůj produkt předtím, než kdybyste kupovali pomocí metody automatizované pro převod na placené použití. Taky umožňuje důkazy konceptu pro odběratele a společné zapojení týmy prodeje společnosti Microsoft. | Řešení je virtuální počítač nebo Azure aplikací: typ nabídky šablonu řešení.<br /><br />Řešení je SaaS nabízejí typu a zadáte víceklientské SaaS produktu.<br /><br />Máte při prvním spuštění získat zákazníkovi rychle začít.<br /><br />Máte jednoho klienta, ale přidáváte zákazníkům jako hosty. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Požadavky na: Azure Marketplace: zkušební verze: Interaktivní demo  
S kompatibilitou řešení umožní vašim zákazníkům poskytovat pomocí interaktivní ukázka. Výhodou typ nabídky interaktivní ukázku je zadáte zkušební prostředí bez komplikované zřizování komplexní řešení. Typ nabídky interaktivní ukázka poskytuje zákazníkovi orientovat řešení. Typ nabídky interaktivní ukázka poskytuje vaší zájemce, které by měl bude možné rozvíjet základní nabídky zahájíte setrvačníku vaší firmy.  

| Výhoda | Požadavek |  
|:--- |:--- |  
| Umožňuje zákazníkům najdete v části řešení v akci bez složitost nastavení. | Řešení vyžaduje komplexní nastavení, které by byly pevné dosažení během zkušebního období. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Požadavky na: Azure Marketplace: zkušební verze: Vyzkoušejte  
Můžete nasadit jeden nebo více virtuálních počítačů prostřednictvím infrastructure-as-a-service(IaaS) nebo aplikací SaaS. Výhodou typ nabídky Test jednotky je automatického zřizování virtuálního počítače nebo celé řešení vedla podle hostovaného průvodce. Test jednotky poskytuje zákazníkovi vyhodnocení bez dalších poplatků. Zákazník nemusí být vede ke generování kvalitnější stávající zákazník Azure.  

| Výhoda | Požadavek |  
|:--- |:--- |  
| Umožňuje, aby zkuste svůj produkt než kdybyste kupovali zákazník.<br /><br />Poskytuje návodné vašeho řešení pomocí předem nakonfigurovaných nastavení.<br /><br />Následující další výhody při používání se test jednotky.<ul> <li>Zákazníci k jenom zobrazit nabídkám s test jednotky jsou vylepšení 27 % uživatele vyhledávání na webu marketplace.</li> <li>Nabízí se test jednotky generování zájemců 38 % více než nabízí bez.</li> <li>36 % nové akvizicích zákazníků na webu marketplace pocházet od zákazníků, kteří trvalo test jednotky.</li> <li>Test jednotky povolit Microsoft prodejci pole pro lepší pochopení vašeho produktu pro společné prodává úsilí.</li> </ul> | Řešení je virtuální počítač, aplikace Azure: řešení šablony, SaaS aplikace pomocí jednoho klienta, nebo je složitý zřídit. <br /><br />Nemáte metodu pro zkušební verzi převést na placené nabídku. |  

---

## <a name="transact-specific-listings"></a>Příkaz Transact konkrétní seznamy
Tato část popisuje všechny nabídky, které používají Transact typ seznamu.

### <a name="transact"></a>Transakce  
Typ seznamu Transact zahrnuje následující typy nabídka na obchodní poutače v marketplace.  

| Typ nabídky | Prodejní místo | Podrobnosti |   
|:---        |:---        | :--- |  
| Aplikace Azure: spravované aplikace | Azure Marketplace | [Požadavky na: Azure Marketplace: Transact: aplikace Azure: spravované aplikace](#requirements-azure-marketplace-transact-azure-apps-managed-app) |  
| Aplikace Azure: řešení šablony | Azure Marketplace | [Požadavky na: Azure Marketplace: Transact: aplikace Azure: řešení šablony](#requirements-azure-marketplace-transact-azure-apps-solution-template) |  
| Containers | Azure Marketplace | [Požadavky na: Azure Marketplace: Transact: kontejneru](#requirements-azure-marketplace-transact-container) |  
| Aplikace SaaS  | Azure Marketplace | [Požadavky na: Azure Marketplace: Transact: SaaS aplikace](#requirements-azure-marketplace-transact-saas-app) |  
| Virtuální počítač | Azure Marketplace | [Požadavky na: Azure Marketplace: Transact: virtuálního počítače](#requirements-azure-marketplace-transact-virtual-machine) |  

#### <a name="requirements-azure-marketplace-transact-azure-apps-managed-app"></a>Požadavky na: Azure Marketplace: Transact: aplikace Azure: spravované aplikace  
Používat aplikaci Azure: Správa typ nabídky aplikace, když tyto podmínky se vyžadují.  
*   Nasadit buď řešení na základě předplatného pro vaše zákazníky pomocí virtuálního počítače nebo celé řešení založené na IaaS.  
*   Můžete nebo zákazníkovi vyžadují řešení je spravuje partner.  

    >[!NOTE]
    >Partnera může být například na serveru nebo poskytovatel spravované služby (MSP).  
  
*   Seznam častých otázek, na webu Marketplace nejčastější dotazy k stránky nacházející se v [azure.microsoft.com/marketplace/faq](https://azure.microsoft.com/marketplace/faq).  

>[!NOTE]
> Spravované aplikace musí být nasadit přes Marketplace. Pokud zákazník komunikace obavu, potom by se oslovení chcete zákazníkům po povolení sdílení zájemce.  

#### <a name="requirements-azure-marketplace-transact-azure-apps-solution-template"></a>Požadavky na: Azure Marketplace: Transact: aplikace Azure: řešení šablony  
Používat aplikaci Azure: řešení šablony typ nabídky Pokud vaše řešení vyžaduje další automatizaci nasazení a konfigurace nad rámec jednoduchého virtuálního počítače. Může automatizovat zřizování jeden nebo více virtuálních počítačů pomocí aplikace Azure: řešení šablony. Mohou také vytvářet prostředky sítě a úložiště. Aplikace Azure: řešení šablony nabízejí typu poskytuje výhody automatizace pro jeden virtuální počítače a celé řešení založené na IaaS.  

#### <a name="requirements-azure-marketplace-transact-container"></a>Požadavky na: Azure Marketplace: Transact: kontejneru  
Typ nabídky kontejneru používejte při řešení je bitová kopie kontejner Docker zřízen jako na základě Kubernetes Azure container service.

>[!NOTE]
>Na základě Kubernetes Azure container service, například jako Kubernetes služby Azure nebo Azure kontejner instancí, výběr Azure zákazníci kontejneru na základě Kubernetes runtime.  

Společnost Microsoft podporuje aktuálně volné a přineste si vlastníte license licencování modely (BYOL). Další možnosti a obchodu Spojených států jsou naplánovány prostřednictvím následujících měsíců, včetně předplatného a na základě spotřeby fakturace možnosti.  

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| Fakturace a měření | Podpora buď volné nebo fakturační model BYOL. |  
| Bitové kopie z soubor Docker | Kontejner Image musí být založený na specifikaci Docker bitové kopie a z soubor Docker musí být sestaven.<ul> <li>Další informace o vytváření bitových kopií docker, naleznete v části využití nacházející se v [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hostování v ACR | Kontejner imagí musí být hostované v úložišti Azure kontejneru registru (ACR).<ul> <li>Další informace o práci s ACR najdete rychlý start: vytvoření kontejneru registru pomocí Azure stránky portálu nacházející se v [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Označování bitové kopie | Kontejner imagí musí obsahovat alespoň 1 značky (maximální značky: 16).<ul> <li>Další informace o označování bitovou kopii, najdete na stránce značky docker nacházející se v [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  


#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Požadavky na: Azure Marketplace: Transact: SaaS aplikace  
Typ nabídky SaaS aplikace použijte k povolení zákazníkovi Koupit řešení založené na SaaS, technické jako předplatné. Pro aplikace SaaS musí být splněny následující požadavky.  
*   Ceny a faktury službu ploché měsíční rychlostí.  
*   Zadejte metodu pro upgrade nebo kdykoli ukončit službu.  

Microsoft je hostitelem transakce commerce. Microsoft bills zákazníkovi vaším jménem. Pokud chcete použít jako předplatné faktury aplikace SaaS, je nutné povolit můžete vlastní předplatné služba správy rozhraní API. Rozhraní API pro vaše předplatné správu služby musí komunikovat přímo s rozhraními API sady Azure Resource Manager. Rozhraní API pro vaše předplatné správu služby musí podporovat zřizování služeb, upgrade a zrušení.  

| Požadavek | Podrobnosti |  
|:--- |:--- |  
|Fakturace a měření | Vaši nabídku je cenově paušálně měsíční. V tuto chvíli nejsou podporovány na základě využití ceny a na základě využití možnosti "true-up". |  
|Zrušení | Kdykoli je možné zrušit zákazník vaši nabídku. |  
|Cílová stránka transakce | Hostujete stránku cílová Azure související transakci, kde uživatelé mohou vytvářet a spravovat své účty služby SaaS. |   
| Předplatné rozhraní API | Vystavit služby, které mohou komunikovat s předplatným SaaS vytvářet, aktualizovat a odstraňovat plán uživatelského účtu a služby. Důležité změny rozhraní API musí podporovat do 24 hodin. Méně závažné změny rozhraní API, budou vydané pravidelně. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Požadavky na: Azure Marketplace: Transact: virtuálního počítače  
Typ nabídky virtuálního počítače můžete používejte při nasazování virtuálního zařízení k předplatnému přidružené vašich zákazníků. Virtuální počítače jsou plně obchodování povolit pomocí průběžné platby nebo Bring vaše vlastníte-licenci (BYOL) licencování modely. Společnost Microsoft je hostitelem transakce obchodování a bills zákazníkovi vaším jménem. Můžete využívat výhody používání upřednostňované platebních vztah mezi vašeho zákazníka a společnosti Microsoft, včetně veškeré smlouvy Enterprise.  

>[!NOTE]
>V tomto okamžiku jsou peněžních závazků přidružené smlouvu Enterprise Agreement nelze použít s Azure využití virtuálního počítače, ale není pro vaše poplatky pro licencování softwaru.  

>[!NOTE]
>Je možné omezit zjišťování a nasazení virtuálního počítače na konkrétní sadu odběratelů tak, že výpis bitovou kopii a ceny jako na privátní nabídku. Odemknout privátní nabízí možnost pro vytvoření exkluzivní nabídky pro nejbližší zákazníkům a nabízejí vlastní software a podmínky. Vlastní podmínky umožňují Zvýrazněte celou řadu scénářů, včetně vedla pole obchody se specializované ceny a podmínky stejně jako včasné přístup k softwaru omezené vydání. Privátní nabízí povolit můžete poskytnout konkrétní ceny nebo produktů pro omezenou sadu odběratelů tak, že vytvoříte novou SKU s těmito podrobnostmi.  
*   Další informace o privátní nabídky, navštivte privátní nabídky na stránku nacházející se v Azure Marketplace [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Požadavek | Podrobnosti |  
|:--- |:--- | 
| Fakturace a měření | Virtuální počítač musí podporovat BYOL nebo průběžné platby měsíční fakturace. |  
| Azure kompatibilní virtuální pevný disk (VHD) | Virtuální počítače musí být založený na systému Windows nebo Linux.<ul> <li>Další informace o vytváření Linux VHD, najdete v článku vytvořit Azure kompatibilní virtuální pevný disk (systémem Linux) části nacházející se v [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-Based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Další informace o vytvoření virtuálního pevného disku Windows, najdete v článku vytvořit Azure kompatibilní virtuálního pevného disku (systému Windows) části nacházející se v [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-Based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul> |  

## <a name="next-steps"></a>Další postup
*   Přejděte [AppSource vydavatele průvodce a Azure Marketplace](./marketplace-publishers-guide.md) stránky.  
 
---  
