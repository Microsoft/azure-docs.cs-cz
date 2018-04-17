---
title: Plán, podle kterého Analytics Azure stavu
description: Pokyny k nasazení HIPAA nebo HITRUST stavu Analytics plán, podle kterého
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 26566e0a-0a54-49f4-a91d-48e20b7cef71
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2018
ms.author: simorjay
ms.openlocfilehash: 6bc2c63e75e9c750da852b77e4486666c3fc8561
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-security-and-compliance-blueprint---hipaahitrust-health-data-and-ai"></a>Zabezpečení Azure a dodržování předpisů plán, podle kterého - HIPAA nebo HITRUST Data o stavu a AI

## <a name="overview"></a>Přehled

**Zabezpečení Azure a dodržování předpisů plán, podle kterého - HIPAA nebo HITRUST Data o stavu a AI nabízí klíč nasazení řešení Azure PaaS do ukazují, jak bezpečně ingestování, ukládat, analyzovat a interagovat s daty stavu při schopnost dodržet platné předpisy odvětví požadavky. Plán, podle kterého pomůže urychlit přijetí cloudu a využití pro zákazníky s daty, která je upraveno.**

Zabezpečení Azure a dodržování předpisů plán, podle kterého - HIPAA nebo HITRUST Data o stavu a plán, podle kterého AI poskytuje nástroje a pokyny, které pomohou nasadit zabezpečený, zdravotním pojištění a odpovědnosti za Application Compatibility Toolkit (HIPAA) a stavu informace o vztahu důvěryhodnosti Alliance (HITRUST) připraveno Platforma jako služba (PaaS) prostředí pro příjem, ukládání, analýze a interakci s osobní a ne osobní lékařské záznamy v zabezpečené a vícevrstvé cloudové prostředí nasadit jako řešení začátku do konce. To umožňující prezentovat běžné referenční architektura a je navržená tak, aby se zjednodušila přijetí ve službě Microsoft Azure. Tato architektura zadaný znázorňuje řešení ke splnění potřeb organizace, které hledají cloudové přístup ke snížení zatížení a náklady na nasazení.

![](images/components.png)

Řešení je navrženo, aby využívat datové sady ukázka formátován pomocí funkce rychlého zdravotní péče interoperabilita prostředky (FHIR), po celém světě standard pro výměnu informací zdravotní péče elektronicky a uložte ho zabezpečeným způsobem. Zákazníci pak můžete použít Azure Machine Learning využívat výhod nástroje výkonné business intelligence a analýzy, chcete-li zkontrolovat předpovědí na ukázková data. Jako příklad druh experimentu, který může usnadnit Azure Machine Learning plán, podle kterého obsahuje ukázkovou datovou sadu, skripty a nástroje pro predikci délka pacientova zůstat v budovy měla nemocnice. 

Tento plán, podle kterého se má sloužit jako základ modulární pro zákazníky, chcete-li upravit na jejich specifické požadavky, vývoj nové Azure strojového učení experimentů k řešení obou scénářích případů použití klinické a funkční. Je navržen pro zabezpečené a kompatibilní, při nasazení; ale je zodpovědná za konfiguraci role správně a implementace veškeré úpravy zákazníků. Je třeba počítat s následujícím:

-   Tento plán, podle kterého poskytuje směrný plán pro pomoc zákazníkům používat Microsoft Azure v HITRUST a HIPAA prostředí.

-   I když plán, podle kterého byl navržený zarovnána s HIPAA a HITRUST (prostřednictvím společného rámce zabezpečení – CSF), nemělo by se používat vyhovující, dokud nebude ověřený auditor externí za HIPAA a HITRUST požadavky na certifikaci.

-   Je vhodné zabezpečení a dodržování předpisů recenze řešení vytvořená s využitím této základní architektury provádějící zákazníků.

## <a name="deploying-the-automation"></a>Nasazení automatizace

- K nasazení řešení, postupujte podle pokynů uvedených v nasazování aktualizací. 

[![](./images/deploy.png)](https://aka.ms/healthblueprintdeploy)

Pro rychlý přehled o tom, jak toto řešení funguje, si pusťte toto [video](https://aka.ms/healthblueprintvideo) vysvětlením a předvedení jeho nasazení.

- Odpovědi na dotaz lze nalézt v [– nejčastější dotazy](https://aka.ms/healthblueprintfaq) pokyny.

-   **Diagram architektury.** Diagram znázorňuje referenční architektura používá pro plán, podle kterého a používat v příkladu scénář případu.

-   **Nasazení šablony**. V tomto nasazení [šablon Azure Resource Manageru](/azure/azure-resource-manager/resource-group-overview#template-deployment) se používají k automaticky nasazení komponent architektury do Microsoft Azure tak, že zadáte parametry konfigurace během instalace.

-   **[Automatizované skripty nasazení](https://aka.ms/healthblueprintdeploy)**. Tyto skripty pomoct nasadit řešení. Skripty se zabývají:


-   Instalace modulu a [globálního správce](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) instalační skript se používá k instalaci a ověřte, zda jsou správně nakonfigurovány požadované moduly prostředí PowerShell a rolí globálního správce. 
-   Instalace skriptu prostředí PowerShell slouží k nasazení řešení, poskytnuto prostřednictvím soubor .zip, který obsahuje předem připravené ukázkové funkce.

## <a name="solution-components"></a>Součásti řešení


Základní architektura se skládá z následujících součástí:

-   **[Model hrozeb](https://aka.ms/healththreatmodel)**  model komplexní hrozeb je zadané ve formátu tm7 pro použití s [Microsoft Threat modelování nástroj](https://www.microsoft.com/en-us/download/details.aspx?id=49168), zobrazující komponenty řešení, tok dat mezi nimi a vztah důvěryhodnosti hranice. Model může pomoci zákazníky, pochopit body potenciální riziko v infrastruktuře systému při vývoji machine learning součásti nebo jiných úprav.

-   **[Matice implementace zákazníka](https://aka.ms/healthcrmblueprint)**  sešitu aplikace Microsoft Excel A uvádí relevantní HITRUST požadavky a vysvětluje, jak jsou zodpovědní za splnění každé z nich společnosti Microsoft a Zákazník.

-   **[Kontrola stavu.](https://aka.ms/healthreviewpaper)** Řešení bylo zkontrolovány uživatelem Coalfire systems, Inc. Stav dodržování předpisů (HIPAA a HITRUST) zkontrolujte a pokyny pro implementaci poskytuje auditor\'s kontrolu na řešení a důležité informace pro transformaci plán, podle kterého nasazení produkční prostředí.

# <a name="architectural-diagram"></a>Diagram architektury


![](images/refarch.png)

## <a name="roles"></a>Role


Plán, podle kterého definuje dvě role pro správce (operátory) a tři role pro uživatele v správu měla nemocnice a pacienta pozor. Šesté role je definována pro auditor k vyhodnocení dodržování předpisů HIPAA a jiné předpisy. Azure na základě rolí řízení přístupu (RBAC) umožňuje přesněji cílených správu přístupu pro každého uživatele řešení pomocí předdefinované a vlastní rolí. V tématu [Začínáme s řízením přístupu na základě rolí na portálu Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) a [předdefinované role pro řízení přístupu Azure na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) podrobné informace o RBAC, rolí a oprávnění.

### <a name="site-administrator"></a>Správce webu


Správce webu je zodpovědná za předplatné zákazníka. Řízení celkové nasazení, ale nemají přístup k pacienta záznamy.

-   Výchozí přiřazení role: [vlastníka](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)

-   Přiřazení vlastních rolí: není k dispozici

-   Obor: předplatného

### <a name="database-analyst"></a>Analytik databáze

Analytik databáze spravuje instance systému SQL Server a databáze.
Žádný přístup k pacienta záznamy mají.

-   Předdefinovaná role přiřazení: [Přispěvatel databází SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-db-contributor), [Přispěvatel serveru SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-server-contributor)

-   Přiřazení vlastních rolí: není k dispozici

-   Obor: ResourceGroup

 ### <a name="data-scientist"></a>Vědecký pracovník dat


Vědecký pracovník dat funguje službu Azure Machine Learning. Se můžou importovat, exportovat a spravovat data a spouštět sestavy. Data vědecký pracovník má přístup k datům pacienta, ale nemá oprávnění správce.

-   Předdefinovaná role přiřazení: [Přispěvatel účtu úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)

-   Přiřazení vlastních rolí: není k dispozici

-   Obor: ResourceGroup

### <a name="chief-medical-information-officer-cmio"></a>Technický ředitel lékařské (CMIO)


CMIO překrývá hranici mezi výpočetní techniky či technologie a poskytovatelem zdravotní Odborníci v oblasti zdravotní péče organizace. Jejich úkolů obvykle zahrnují pomocí analytics k určení, pokud v organizaci se správně přidělování zdrojů.

-   Předdefinovaná role přiřazení: žádné

### <a name="care-line-manager"></a>Pozor Úsekový manažer


Úsekový manažer pozor je přímo zahrnut dát pozor pacientů.
Jeho úkolem je monitorovat stav pacientů a také zajistit, aby byl k dispozici potřebný personál pro zajištění specifických požadavků péče o jednotlivé pacienty. Úsekový manažer pozor zodpovídá za přidávání a aktualizace pacienta záznamů.

-   Předdefinovaná role přiřazení: žádné

-   Přiřazení vlastních rolí: oprávnění ke spuštění HealthcareDemo.ps1 udělat i jejich příchodu pacienta a plnit.

-   Obor: ResourceGroup

### <a name="auditor"></a>Auditor


Auditor vyhodnotí řešení pro dodržování předpisů. Mají žádný přímý přístup k síti.

-   Předdefinovaná role přiřazení: [čtečky](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)

-   Přiřazení vlastních rolí: není k dispozici

-   Obor: předplatného

## <a name="example-use-case"></a>Příklad případ použití


Případ použití příklad součástí tento plán, podle kterého ukazuje, jak plán, podle kterého lze povolit machine learningu a analýzy dat stavu v cloudu. Contosoclinic je malý nemocnice umístěné ve Spojených státech amerických. Správci sítě měla nemocnice chcete lépe předpovědi délka pacientova pobytu v době opravňujících, chcete-li zvýšit efektivitu provozní úlohy a vylepšení kvality pozor, můžete získat pomocí Azure Machine Learning.

### <a name="predicting-length-of-stay"></a>Predikci doba trvání


Ukázkový scénář případu použití používá Azure Machine Learning k předvídání nově dovezené pacientova doba trvání tak, že porovnáte lékařské podrobnosti provedených na pacienta příjmu agregované historická data od předchozí pacientů.
Plán, podle kterého obsahuje velké sady anonymizovaná lékařské záznamy k předvedení funkcí školení a prediktivní řešení. V produkčním nasazení by zákazníkům používat jejich vlastní záznamy ke cvičení řešení pro přesnější předpovědi, které odráží jedinečné podrobnosti jejich prostředí, zařízení a pacientů.

### <a name="users-and-roles"></a>Uživatelé a role


**Správce webu – Alex**

*E-mailu: Alex\_SiteAdmin*

Úloha je Alex je vyhodnotit technologie, které může snížit režie správy k místní síti a snížit náklady na správu. Alex byla vyhodnocování Azure nějakou dobu, ale má měli problémy, ke konfiguraci služby, které mu je potřeba splnit požadavky na dodržování HiTrust k ukládání dat pacienta v cloudu. Alex vybral AI stavu Azure chcete nasadit řešení stavu dodržování předpisů připravené, který má řešit požadavky pro požadavkům zákazníka HiTrust.

**Dat – vědecký pracovník Debra**

*E-mailu: Debra\_DataScientist*

Debra má na starosti pomocí a vytváření modelů, které analyzovat lékařské záznamy zajistit přehled o pacientech pozor. Debra používá SQL a statistické programovací jazyk R vytváření jeho modelů.

**Analytik databáze – Danny**

*E-mailu: Danny\_DBAnalyst*

Danny je hlavním kontaktem pro všechno, co týkající se Microsoft SQL Server, která ukládá všechny pacienta data pro Contosoclinic. Danny se na zkušeného správce systému SQL Server, který má nedávno seznámit se s Azure SQL Database.

**Technický lékařské ředitel – Caroline**

Caroline ve spolupráci s Jan úsekového manažera pro vás a Debra vědecký pracovník dat k určení, jaké faktory vliv pacienta doba trvání.
Caroline predikcím ze délka trvání řešení (LOS) používá k určení, pokud v síti měla nemocnice se správně přidělování zdrojů. Například pomocí řídicího panelu, zadaný v tomto řešení.

**Jan pro vás úsekového manažera--**

*E-mailu: Jan\_CareLineManager*

Jako jednotlivých přímo zodpovědní za správu pacienta jejich příchodu a vypouštění v Contosoclinic, Jan používá předpovědi generuje řešení LOS zajistit, že odpovídající zaměstnanci jsou k dispozici zajistit pozor pacientům, když jsou zůstávají ve zařízení.

**Auditor – Hanu**

*E-mailu: Hanu\_Auditor*

Hanu je certifikovaných auditora, který má prostředí auditování pro ISO, SOC a HiTrust. Hanu byl přijat ke kontrole Contosoclinc na síti. Hanu můžete zkontrolovat matice zákazníka odpovědnost součástí řešení zajistit, že plán, podle kterého a LOS řešení lze použít k uložení, proces a zobrazení důvěrných osobních údajů.


# <a name="design-configuration"></a>Konfigurace návrhu


Tato část popisuje, o výchozí konfigurace a bezpečnostní opatření, které jsou součástí plán, podle kterého uvedených na:

- **INGESTOVÁNÍ** nezpracovaná zdrojů dat včetně FHIR zdroje dat
- **ÚLOŽIŠTĚ** citlivé informace
- **Analýza** a předvídání výstupy
- **INTERAKCÍ** s výsledky a předpovědi
- **IDENTITY** správu řešení
- **ZABEZPEČENÍ** povolena funkce


## <a name="identity"></a>IDENTITY 

### <a name="azure-active-directory-and-role-based-access-control-rbac"></a>Azure Active Directory a řízení přístupu na základě role (RBAC)


**Ověřování:**

-   [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) je Microsoft\'s víceklientské cloudové adresáře a identity management service. Všechny uživatele pro řešení byly vytvořeny v Azure Active Directory, včetně uživatelů přístup k databázi SQL.



-   Ověřování do aplikace se provádí pomocí služby Azure AD. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).

-   [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení, které ovlivňují identity ve vaší organizaci, nakonfiguruje automatické odpovědi na zjištěné podezřelé akcí souvisejících s identity ve vaší organizaci, a prozkoumá podezřelé incidenty a provede příslušnou akci jejich řešení.

-   [Azure na základě rolí řízení přístupu (RBAC)](/azure/role-based-access-control/role-assignments-portal) umožňuje přesněji správu cílených přístupu k Azure. Předplatné přístup je omezen na správce předplatného a Azure Key Vault přístup je omezen na správce webu. Silná hesla (minimální s aspoň jedno velké nebo malé písmeno, číslo a zvláštní znak 12 znaků) se vyžadují.

-   Služby Multi-Factor authentication je podporována, pokud je povoleno přepínačem - enableMFA během nasazení.

-   Hesla vyprší po 60 dnech, pokud je povolená přepínačem - enableADDomainPasswordPolicy během nasazení.

**Role:**

-   Řešení využívá [předdefinované role](/azure/role-based-access-control/built-in-roles) ke správě přístupu k prostředkům.

-   Všichni uživatelé jsou přiřazeny určité předdefinované role ve výchozím nastavení.

### <a name="azure-key-vault"></a>Azure Key Vault

-   Data uložená v Key Vault, zahrnují:

    -   Klíč přehled aplikace
    -   Klíč pacienta přístup k úložišti dat
    -   Pacienta připojovací řetězec
    -   Název tabulky pacienta dat
    -   Koncový bod Azure ML webové služby
    -   Klíč rozhraní API služby Azure ML

-   Konfigurace zásad pokročilé přístupu na základě potřeba
-   Zásady přístupu Key Vault jsou definovány s minimální požadovaná oprávnění k klíče a tajné klíče
-   Všechny klíče a tajné klíče v Key Vault mají datum vypršení platnosti
-   Všechny klíče v trezoru klíče jsou chráněny HSM \[typu klíč = klíče RSA HSM chráněné 2048 bitů\]
-   Všichni uživatelé nebo identity jsou udělena minimální požadovaná oprávnění pomocí Role na základě řízení přístupu (RBAC)
-   Aplikace nesdílejí Key Vault, pokud si navzájem důvěřují a potřebují přístup ke stejným tajných klíčů za běhu
-   Diagnostické protokoly Key Vault je povolená s dobu uchování o délce alespoň 365 dnů.
-   Povolené kryptografických operací pro klíče jsou omezeny na ty, které vyžaduje

## <a name="ingest"></a>INGESTOVÁNÍ 

### <a name="azure-functions"></a>Azure Functions
Řešení je navrženo, aby použít [Azure Functions](/azure/azure-functions/) zpracovat délka ukázka zůstat dat používaných v ukázkové analýzy. Tři možnosti funkce byly vytvořeny.

**1. Hromadný import dat Phi – dat zákazníka**

Při použití ukázkový skript. . \\HealthcareDemo.ps1 s **BulkPatientAdmission** přepínat, jak je uvedeno v **nasazení a spuštění ukázku** se provede následující kanálu zpracování:
1. **Azure Blob Storage** -ukázkový soubor .csv data pacienta nahrané do úložiště
2. **Událost mřížky** -událostí publikuje data funkce Azure (hromadným importem – objekt blob události)
3. **Funkce Azure** - provede zpracování a ukládá data do úložiště SQL pomocí funkce zabezpečení – událost (Typ; adresa url objektu blob)
4. **Databáze SQL** – úložiště databáze pro pacienta dat pomocí značky pro klasifikaci a proces ML je spuštěna Uděláte to výukový experiment.

![](images/dataflow.png)

Kromě toho azure funkce byl navržen ke čtení a chránit určené citlivá data v sadě dat ukázka pomocí těchto značek:
- dataProfile = > "ePHI"
- Vlastník = > \<UPN správce lokality\>
- prostředí = > "Pilotního projektu"
- oddělení = > "Globální ekosystém" označení bylo použito pro sadu ukázkových dat, kde pacienta 'názvy' byla identifikována jako nešifrovaný text.

**2. Jejich příchodu nových pacientů**

Při použití ukázkový skript. . \\HealthcareDemo.ps1 s **BulkPatientadmission** přepínat, jak je uvedeno v **nasazení a spuštění ukázku** se provede následující kanálu zpracování: ![](images/securetransact.png) 
 **1. Funkce Azure** spustit a funkce požadavky [tokenu nosiče](/rest/api/) ze služby Azure Active directory.

**2. Key Vault** požadovaný pro tajný klíč, který je přidružen k požadovaný token.

**3. Azure role ověření žádosti a autorizaci žádost o přístup do služby Key Vault.

**4. Key Vault** vrací tajný klíč, v tomto případě SQL DB připojovací řetězec.

**5. Funkce Azure** používá připojovací řetězec k bezpečnému připojování k databázi SQL a pokračuje k ukládání dat ePHI další zpracování.

K dosažení úložiště dat, společné schéma rozhraní API implementovat následující rychlé zdravotní péče prostředky Interoperability (FHIR vyslovováno ještě efektivněji). Funkce zadaná následující prvky FHIR exchange:

-   [Pacienta schématu](https://www.hl7.org/fhir/patient.html) "kdo" obsahuje informace o pacienta.

-   [Schéma pozorování](https://www.hl7.org/fhir/observation.html) obsahuje centrální element v zdravotní péče, lze podporovat diagnostiku, sledovat průběh, určit směrné plány a vzory a i zaznamenat demografické charakteristiky. 

-   [Dojde k schématu](https://www.hl7.org/fhir/encounter.html) obsahuje typy komunikaci například ambulatory nouzový, domácí stav, má za a virtuální komunikaci.

-   [Podmínka schématu](https://www.hl7.org/fhir/condition.html) obsahuje podrobné informace o podmínku, problém, diagnostiku, nebo další události, situaci, problém nebo klinické konceptu, které vzrostla úroveň se jednat o problém.  



### <a name="event-grid"></a>Event Grid


Podporuje řešení Azure událostí mřížky, jedinou službu pro správu směrování pro všechny události z jakéhokoli zdroje do žádné cíle, pokud:

-   [Zabezpečení a ověřování](/azure/event-grid/security-authentication)

-   [Řízení přístupu na základě role](/azure/event-grid/security-authentication#management-access-control) pro různé operace správy, například výpis událostí odběrů, vytváření nové a generování klíčů

-   Auditování

## <a name="store"></a>ÚLOŽIŠTĚ 

### <a name="sql-database-and-server"></a>SQL Database a serveru 


-   [Transparentní šifrování šifrování dat (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) poskytuje v reálném čase šifrování a dešifrování dat uložených v databázi SQL Azure, pomocí klíče uloženého v Azure Key Vault.

-   [Vyhodnocení ohrožení zabezpečení SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) je snadno konfigurovat nástroj, který lze zjišťovat, sledovat a napravovat potenciální ohrožení zabezpečení databáze.

-   [Detekce hrozeb databáze SQL](/azure/sql-database/sql-database-threat-detection) povolena.

-   [Auditování databáze SQL](/azure/sql-database/sql-database-auditing) povolena.

-   [Databáze SQL metrik a protokolování diagnostiky](/azure/sql-database/sql-database-metrics-diag-logging) povolena.

-   [Úroveň serveru a databáze brány firewall pravidla](/azure/sql-database/sql-database-firewall-configure) mít byla zvýšit.

-   [Vždy šifrované sloupce](/azure/sql-database/sql-database-always-encrypted-azure-key-vault) slouží k ochraně pacienta první a poslední názvy.
    Kromě toho sloupce šifrování databáze také používá Azure Active Directory (AD) k ověřování aplikace do Azure SQL Database.

-   Přístup k SQL Database a SQL Server nakonfigurovaný podle Princip nejnižších nutných oprávnění.

-   Přístup přes bránu firewall SQL jsou povoleny pouze požadované IP adresy.

### <a name="storage-accounts"></a>Účty úložiště


-   [Data v provozu se přenáší pomocí protokolu TLS/SSL pouze](/azure/storage/common/storage-require-secure-transfer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

-   Anonymní přístup není povolen pro kontejnery.

-   Pravidla výstrah jsou nakonfigurované pro sledování anonymní aktivity.

-   Je požadován pro přístup k prostředkům účtu úložiště protokol HTTPS.

-   Ověřování dat požadavku je zaznamenána a sledovat.

-   Data v úložišti objektů Blob se šifrují v klidovém stavu.

## <a name="analyze"></a>ANALÝZA

### <a name="machine-learning"></a>Machine Learning


-   [Je povoleno protokolování](/azure/machine-learning/studio/web-services-logging) pro Machine Learning webové služby.
- pomocí [Machine Learning](/azure/machine-learning/preview/experimentation-service-configuration) workbench vyžaduje vývoj experimenty, která poskytuje možnost k předvídání do sady řešení. [Integrace nástroje workbench](/azure/machine-learning/preview/using-git-ml-project) můžete zjednodušit správu experimenty.

## <a name="security"></a>ZABEZPEČENÍ

### <a name="azure-security-center"></a>Azure Security Center
- [Azure Security Center](https://azure.microsoft.com/services/security-center/) poskytuje centralizovanou zobrazení stavu zabezpečení všech vašich prostředků Azure. Na první pohled můžete ověřit příslušná bezpečnostní prvky jsou na místě a správně nakonfigurovaný, a můžete rychle zjistit všechny prostředky, které vyžadují pozornost. 

- [Azure Advisor](/azure/advisor/advisor-overview) je konzultantem přizpůsobené cloudu, který vám pomůže dodržujte doporučené postupy, chcete-li optimalizovat nasazení Azure. Analyzuje konfiguraci vašich prostředků a telemetrii jejich využívání a následně doporučí řešení, která zlepší finanční úspornost, výkon, dostupnost a zabezpečení vašich prostředků Azure.

### <a name="application-insights"></a>Application Insights
- [Application Insights](/azure/application-insights/app-insights-overview) je rozšiřitelný služba Správa výkonu aplikace (APM) pro vývojáře, kteří ve více platformách. Slouží k monitorování živé webové aplikace. Zjistí anomálie výkonu. Obsahuje výkonné analytické nástroje pro diagnostiku problémů a pomáhá porozumět, jak vlastně uživatelé vaši aplikaci používají. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití.

### <a name="azure-alerts"></a>Azure výstrahy
- [Výstrahy nabízejí metoda monitorování služby Azure a umožňují konfigurovat podmínky nad daty. Výstrahy obsahují i oznámení, když výstražný stav odpovídá data monitorování.

### <a name="operations-management-suite-oms"></a>Služby Operations Management Suite (OMS)
[Služby Operations Management Suite (OMS)](/azure/operations-management-suite/operations-management-suite-overview) je kolekce služeb pro správu.

-   Pracovní prostor je povolený pro Security Center

-   Pracovní prostor je povolený pro úlohy monitorování

-   Monitorování úloh je povoleno pro:

    -   Identita a přístup

    -   Zabezpečení a Audit

    -   Analýza Azure SQL DB

    -   [Azure WebApp Analytics](/azure/log-analytics/log-analytics-azure-web-apps-analytics) řešení

    -   Key Vault Analytics

    -   Sledování změn

-   [Konektor služby Statistika aplikace (Preview)](/azure/log-analytics/log-analytics-app-insights-connector) je povoleno

-   [Analýzy protokolů aktivity](/azure/log-analytics/log-analytics-activity) je povoleno
