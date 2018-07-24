---
title: Stav Azure Analytics podrobného plánu
description: Pokyny pro nasazení podrobný plán Analytics stavu HIPAA/HITRUST
services: security
documentationcenter: na
author: RajeevRangappa
ms.assetid: 26566e0a-0a54-49f4-a91d-48e20b7cef71
ms.service: security
ms.topic: article
ms.date: 07/23/2018
ms.author: rarangap
ms.openlocfilehash: b20da0f31f197ed23aa73b185d127a6d5f2dbd8a
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214937"
---
# <a name="azure-security-and-compliance-blueprint---hipaahitrust-health-data-and-ai"></a>Zabezpečení Azure a dodržování předpisů – Data o stavu HIPAA/HITRUST a AI

## <a name="overview"></a>Přehled

**Azure zabezpečení a dodržování předpisů – Data o stavu HIPAA/HITRUST a AI nabízí klíč nasazení Azure PaaS řešení, které ukazují, jak bezpečně ingestování, ukládání, analýzu a interakci s daty stavu při schopnost nesplní dodržování oborových předpisů požadavky. Podrobný plán pomáhá zrychlit přechod na cloud a využití pro zákazníky s daty, která je upravena.**

Azure zabezpečení a dodržování předpisů – Data o stavu HIPAA/HITRUST a AI podrobný plán poskytuje nástroje a pokyny k nasazení zabezpečeného, údajů o zdravotním pojištění a zdravotním pojištění (HIPAA) a stavu informace důvěřovat Alliance (HITRUST) připravené Platforma jako služba (PaaS) prostředí pro ingestování, ukládání, analýzu a interakci s osobní, tak i mimo osobní zdravotnickými záznamy v zabezpečené a vícevrstvé cloudovém prostředí, nasadit jako řešení začátku do konce. Představuje běžné referenční architekturu a je navržené pro zjednodušení přijetí Microsoft Azure. Tato architektura zadaná znázorňuje řešení, které vyhoví potřebám organizace, které hledají cloudový způsob ke snížení režie a náklady na nasazení.

![](images/components.png)

Řešení je navrženo využívat formátována pomocí rychlé Healthcare Interoperability prostředky (FHIR), po celém světě standard k výměně informací o zdravotní péče elektronicky, ukázkovou datovou sadu a uloží je bezpečným způsobem. Zákazníci mohou využívat Azure Machine Learning Studio pak umožní využívat nástroje výkonné business intelligence a analytické nástroje ke kontrole předpovědí na ukázková data. Jako příklad takového typu, experiment, který může usnadnit Azure Machine Learning Studio obsahuje podrobný plán ukázkovou datovou sadou, skripty a nástroje pro předpověď délky pobytu pacienta v nemocnici zařízení. 

Tento podrobný plán má sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům, vyvíjet nové Azure se strojovým učením k řeší oba scénáře použití klinické a provozní modulární. Slouží k zabezpečení a splňovat předpisy při nasazení; Zákazníci však zodpovídají za konfigurace rolí správně a implementaci změny. Je třeba počítat s následujícím:

-   Tento podrobný plán poskytuje základní pomáhá zákazníkům používat Microsoft Azure ve HITRUST a HIPAA prostředí.

-   I když chcete být v souladu s HIPAA a HITRUST (prostřednictvím společného rámce zabezpečení - CSF) je navržená podrobný plán, to by neměl být považované za vyhovující dokud certifikovaný externí auditor podle zákona HIPAA a HITRUST požadavky na certifikaci.

-   Zákazníci odpovídají za provedení příslušné kontroly zabezpečení a dodržování předpisů vytvořené pomocí této základní architektuře řešení.

## <a name="deploying-the-automation"></a>Automatizace nasazení

- K nasazení řešení, postupujte podle pokynů uvedených v nasazování aktualizací. 

[![](./images/deploy.png)](https://aka.ms/healthblueprintdeploy)

Získejte rychlý přehled toho, jak toto řešení funguje, podívejte se na to [videa](https://aka.ms/healthblueprintvideo) vysvětlí a předvede jeho nasazení.

- Nejčastější otázky najdete v [nejčastější dotazy k](https://aka.ms/healthblueprintfaq) pokyny.

-   **Diagram architektury.** Diagram znázorňuje referenční architektura používá pro podrobný plán a v příkladu scénáři použití.

-   **Nasazení šablony**. V tomto nasazení [šablon Azure Resource Manageru](/azure/azure-resource-manager/resource-group-overview#template-deployment) umožňují automaticky nasazovat součásti architektury do Microsoft Azure tak, že zadáte parametry konfigurace během instalace.

-   **[Automatizované skripty nasazení](https://aka.ms/healthblueprintdeploy)**. Tyto skripty pomáhající s nasazením řešení. Skripty obsahovat:


-   Instalace modulu a [globálního správce](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) instalační skript se používá k instalaci a ověřte, zda jsou správně nakonfigurovány požadované moduly Powershellu a role globálního správce. 
-   Instalace powershellu se používá k nasazení řešení, které jsou k dispozici prostřednictvím souboru .zip, který obsahuje předem připravené ukázky funkce.

## <a name="solution-components"></a>Součásti řešení


Základní architektura se skládá z následujících součástí:

-   **[Model hrozeb](https://aka.ms/healththreatmodel)**  komplexní rizik je součástí tm7 formátu pro použití se službou [nástroj pro modelování hrozeb Microsoftu](https://www.microsoft.com/en-us/download/details.aspx?id=49168), zobrazuje součástmi řešení, data proudí mezi a vztah důvěryhodnosti hranice. Model může pomoci zákazníkům pochopit body potenciální riziko v infrastruktuře systému při vývoji součástí Machine Learning Studio nebo jiné úpravy.

-   **[Zákaznická implementace matice](https://aka.ms/healthcrmblueprint)**  sešit aplikace Excel A Microsoft je uveden seznam požadavků příslušných HITRUST a vysvětluje, jak jsou zodpovědný za splnění každé z nich Microsoft a zákazníka.

-   **[Kontrola stavu.](https://aka.ms/healthreviewpaper)** Toto řešení byl recenzován uživatelem Coalfire systems, Inc. Stav dodržování předpisů (HIPAA a HITRUST) kontrolu a pokyny pro implementaci poskytuje auditor\'s kontrolu řešení a důležité informace pro transformaci podrobného plánu nasazení připravené pro produkční prostředí.

# <a name="architectural-diagram"></a>Diagram architektury


![](images/refarch.png)

## <a name="roles"></a>Role


Podrobný plán definuje dvě role uživatelů správce (operátoři) a tři role uživatelů v nemocnici správy a péče o pacienty. Šestý role je definována pro auditor k vyhodnocení dodržování zákonů HIPAA a nařízení. Azure na základě rolí řízení přístupu (RBAC) umožňuje přesně zaměřený správu přístupu pro každého uživatele řešení pomocí předdefinovaných a vlastních rolí. Zobrazit [Začínáme s řízením přístupu na základě rolí na portálu Azure portal](https://docs.microsoft.com/azure/role-based-access-control/overview) a [předdefinované role pro řízení přístupu na základě rolí Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) podrobné informace o RBAC, rolích a oprávněních.

### <a name="site-administrator"></a>Správce serveru


Správce serveru je zodpovědná za předplatné Azure zákazníka. Ovládací prvek celkové nasazení, ale nemají přístup k záznamy o pacientech.

-   Výchozí přiřazení rolí: [vlastníka](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)

-   Přiřazení vlastních rolí: není k dispozici

-   Obor: předplatné

### <a name="database-analyst"></a>Analytické databáze

Analytické databáze spravuje instanci systému SQL Server a databáze.
Nemají přístup k záznamy o pacientech.

-   Integrovaná přiřazení rolí: [Přispěvatel databází SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-db-contributor), [Přispěvatel SQL serveru](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-server-contributor)

-   Přiřazení vlastních rolí: není k dispozici

-   Obor: Skupina prostředků

 ### <a name="data-scientist"></a>Mezi odborníky přes data


Mezi odborníky přes data funguje nástroje Azure Machine Learning Studio. Můžete importovat, exportovat a spravovat data a spouštět sestavy. Má přístup k datům o pacientech mezi odborníky přes data, ale nemá oprávnění správce.

-   Integrovaná přiřazení rolí: [Přispěvatel účtů úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)

-   Přiřazení vlastních rolí: není k dispozici

-   Obor: Skupina prostředků

### <a name="chief-medical-information-officer-cmio"></a>Chief Medical Information Officer (CMIO)


CMIO přechází propast mezi informatikou/technologiemi a pracovníky v organizaci poskytující zdravotní péči. Jejich povinnosti obvykle patří využití analýz k určení, pokud se prostředky přidělují odpovídajícím způsobem v rámci organizace.

-   Integrovaná přiřazení rolí: žádné

### <a name="care-line-manager"></a>Ředitel úseku péče


Ředitel úseku péče se přímo zabývá péče o pacienty.
Jeho úkolem je monitorovat stav pacientů a také zajistit, aby byl k dispozici potřebný personál pro zajištění specifických požadavků péče o jednotlivé pacienty. Ředitel úseku péče zodpovídá za přidávání a aktualizaci záznamy o pacientech.

-   Integrovaná přiřazení rolí: žádné

-   Přiřazení vlastních rolí: má oprávnění ke spuštění HealthcareDemo.ps1 udělat i přijetí pacienta a plnit.

-   Obor: Skupina prostředků

### <a name="auditor"></a>Auditor


Auditor je vyhodnocen jako řešení pro dodržování předpisů. Jejich nemají žádný přímý přístup k síti.

-   Integrovaná přiřazení rolí: [čtečky](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)

-   Přiřazení vlastních rolí: není k dispozici

-   Obor: předplatné

## <a name="example-use-case"></a>Případy použití příklad


Tento plán je součástí případ použití příklad ukazuje, jak podrobný plán slouží k povolení strojového učení a analýz na data o stavu v cloudu. Contosoclinic je malý nemocnici nachází ve Spojených státech. Nemocniční síti chtějí správci používat Azure Machine Learning Studio lépe předvídat délku pobytu pacienta v době opravňujících, aby bylo možné zvýšit efektivitu provozu a zvýšit tak kvalitu péče, který může poskytnout.

### <a name="predicting-length-of-stay"></a>Předpověď délky pobytu


Příklad scénáře případu použití využívá Azure Machine Learning Studio předpovědět nově dovezené pacienta délky pobytu porovnáním lékařské podrobnosti provedené v pacientů příjmu agregované historických dat z předchozí pacientů.
Podrobný plán zahrnuje velké sady anonymizované zdravotnickými záznamy na trénovací a prediktivní předvedou možnosti dostupné v řešení. V produkčním nasazení by zákazníci používat jejich vlastní záznamy tak moct trénovat řešení pro přesnějších predikcí odráží jedinečné podrobnosti o jejich prostředí, zařízení a pacientů.

### <a name="users-and-roles"></a>Uživatelé a role


**Správce serveru – Alex**

*E-mailu: Alex\_SiteAdmin*

Alex úloha je vyhodnotit technologie, které může snížit režie správy v místní síti a snížit náklady na správu. Alex byla vyhodnocení Azure nějakou dobu, ale má za ke konfiguraci služby, které jsou potřebné pro splnění požadavků na dodržování předpisů HiTrust, ukládat Data o pacientech v cloudu. Alex vybrali Azure AI stavu nasazení připravené pro dodržování předpisů stav řešení, které se zákazníky a vyřešené požadavky, které splňují požadavky zákazníků HiTrust.

**Mezi odborníky přes data--Debra**

*E-mailu: Debra\_DataScientist*

Debra má na starosti používání a vytváření modelů, které analyzují zdravotnickými záznamy poskytnout přehled o péče o pacienty. Debra používá k vytvoření své modely SQL a statistický programovací jazyk R.

**Analytické databáze--Danny**

*E-mailu: Danny\_DBAnalyst*

Danny je hlavním kontaktem pro všechno, co týkající se Microsoft SQL Server, který ukládá všechna data o pacientech Contosoclinic. Danny je zkušeného správce systému SQL Server, který byl nedávno seznámit se s Azure SQL Database.

**Chief Medical Information Officer--Caroline**

Caroline ve spolupráci s Chris ředitel úseku péče a Debra mezi odborníky přes Data, chcete-li zjistit, jaké faktorů, které ovlivňují pacientů délky pobytu.
Caroline předpovědí z řešení délka pobytu (LOS) používá k určení, pokud se prostředky přidělují odpovídajícím způsobem v nemocniční síti. Například na řídicím panelu v tomto řešení.

**Care Úsekový manažer – Chris**

*E-mailu: Chris\_CareLineManager*

Jako jednotlivec přímo zodpovědného za správu přijetí pacienta a propouštění na Contosoclinic, Chris používá předpovědí vygenerovaných tímto řešením LOS ujistit, že jsou k dispozici péče o pacienty při pobytu v odpovídající zaměstnanci zařízení.

**Auditor – Han**

*E-mailu: Han\_Auditor*

Hanu je certifikované auditora, který má prostředí auditování pro ISO, SOC a HiTrust. Hanu byl přijat ke kontrole Contosoclinc vaší sítě. Hanu můžete zkontrolovat matice zákazníka odpovědnost řešení opatřeného zajistit, že podrobného plánu a LOS řešení můžete použít k ukládají, zpracovávají a zobrazení důvěrných osobních údajů.


# <a name="design-configuration"></a>Konfigurace návrhu


Tato část podrobně výchozí konfigurace a bezpečnostních opatření, které jsou součástí podrobného plánu uvedených na:

- **INGESTOVÁNÍ** nezpracovaná zdroje dat, včetně FHIR zdroj dat
- **ÚLOŽIŠTĚ** citlivých informací
- **ANALYZOVAT** a předvídat výstupy
- **INTERAKCE** s výsledky a předpovědi
- **Identita** správy řešení
- **ZABEZPEČENÍ** povolena funkce


## <a name="identity"></a>IDENTITA 

### <a name="azure-active-directory-and-role-based-access-control-rbac"></a>Azure Active Directory a řízení přístupu na základě role (RBAC)


**Ověřování:**

-   [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) je Microsoft\'s více tenanty založené na cloudu adresáři a identitami služby management. Všichni uživatelé pro řešení vytvořené v Azure Active Directory, včetně uživatele, kteří používají SQL Database.



-   Používání služby Azure AD se provádí ověřování do aplikace. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).

-   [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení dopadem na identity vaší organizace, nakonfiguruje automatické odpovědi na zjištěné podezřelé akce související s identity ve vaší organizaci, a prozkoumá podezřelé incidenty a provede příslušnou akci k jejich řešení.

-   [Azure na základě rolí řízení přístupu (RBAC)](/azure/role-based-access-control/role-assignments-portal) umožňuje přesněji zaměřené access management pro službu Azure. Přístup k předplatnému je omezená na správce předplatného a služby Azure Key Vault přístup je omezený na správce webu. Silná hesla (minimální s alespoň jedna velká a malá písmena, čísla a speciální znak 12 znaků) jsou povinné.

-   Ověřování službou Multi-Factor Authentication je podporováno, pokud přepínač - enableMFA je povoleno během nasazení.

-   Hesla vyprší po uplynutí 60 dnů, pokud je přepínač - enableADDomainPasswordPolicy povolená během nasazení.

**Role:**

-   Toto řešení využívá [předdefinované role](/azure/role-based-access-control/built-in-roles) ke správě přístupu k prostředkům.

-   Všichni uživatelé přiřazeni konkrétní předdefinované role ve výchozím nastavení.

### <a name="azure-key-vault"></a>Azure Key Vault

-   Obsahuje data uložená ve službě Key Vault:

    -   Klíč Application insight
    -   O pacientech klíč přístup k úložišti dat
    -   O pacientech připojovací řetězec
    -   Název tabulky dat o pacientech
    -   Koncový bod služby Azure ML Web Service
    -   Klíč rozhraní API služby Azure ML

-   Pokročilé zásady přístupu jsou nakonfigurované na základě potřeby
-   Zásady přístupu trezoru klíčů, které jsou definovány s minimální požadovaná oprávnění pro klíče a tajné kódy
-   Všechny klíče a tajné kódy ve službě Key Vault mít data vypršení platnosti
-   Všechny klíče ve službě Key Vault jsou chráněné pomocí HSM \[typ klíče HSM chráněné 2048bitový klíč RSA =\]
-   Všechny uživatele a identity jsou udělena minimální požadovaná oprávnění na základě řízení přístupu Role (RBAC) s použitím
-   Aplikace nesdílejí služby Key Vault, pokud navzájem důvěřují a které potřebují přístup ke stejným tajných kódů v době běhu
-   Jsou povolené diagnostické protokoly pro Key Vault s dobu uchování o délce alespoň 365 dnů.
-   Povolené kryptografických operací s klíči jsou omezeny na ty povinné

## <a name="ingest"></a>INGESTOVÁNÍ 

### <a name="azure-functions"></a>Azure Functions
Řešení byla navržena pro použití [Azure Functions](/azure/azure-functions/) zpracovat ukázka délku pobytu data používaná v ukázce analýzy. Byly vytvořeny tři možnosti ve funkcích.

**1. Hromadný import dat chráněných zdravotních informací data zákazníků**

Při použití ukázkový skript. . \\HealthcareDemo.ps1 s **BulkPatientAdmission** přepínat, jak je uvedeno v **nasazení a spuštění ukázky** provede následující kanálu zpracování:
1. **Azure Blob Storage** -ukázkový soubor CSV data o pacientech nahrané do úložiště
2. **Event Grid** -události publikuje data funkce Azure Functions (hromadný import - blob události)
3. **Funkce Azure Functions** – provádí zpracování a ukládá data do úložiště SQL pomocí zabezpečené funkce – událost (Typ; adresa url objektu blob)
4. **SQL DB** – databáze úložiště pro Data o pacientech s využitím značek pro klasifikaci a procesu ML je vydáno provedete výukového experimentu.

![](images/dataflow.png)

Kromě toho funkce azure Functions je navržená ke čtení a chránit určené citlivá data v datové sadě ukázka pomocí následující značky:
- dataProfile = > "ePHI"
- Vlastník = > \<UPN správce serveru\>
- prostředí = > "Pilotní nasazení"
- oddělení = > "Globální ekosystém" označení bylo použito na ukázkovou datovou sadu, ve kterém pacient "názvy" byla identifikována jako nešifrovaný text.

**2. Přijetí nové pacientů**

Při použití ukázkový skript. . \\HealthcareDemo.ps1 s **BulkPatientadmission** přepínat, jak je uvedeno v **nasazení a spuštění ukázky** provede následující kanálu zpracování: ![](images/securetransact.png) 
 **1. Funkce Azure Functions** aktivuje a funkci žádosti o [nosný token](/rest/api/) ze služby Azure Active directory.

**2. Key Vault** požadovaná pro tajný klíč, který je přidružen k požadovaný token.

**3. Role Azure ověření žádosti a autorizaci žádost o přístup ke službě Key Vault.

**4. Key Vault** vrací tajný klíč, v tomto případě SQL DB připojovací řetězec.

**5. Funkce Azure Functions** používá připojovací řetězec pro zabezpečené připojení k SQL Database a pokračuje k ukládání dat ePHI další zpracování.

K dosažení úložiště dat, společné schéma rozhraní API implementované po rychlé Healthcare prostředky Interoperability (FHIR vyslovováno fire). Funkce byla k dispozici následující prvky FHIR exchange:

-   [O pacientech schématu](https://www.hl7.org/fhir/patient.html) "," najdete informace o pacienta.

-   [Schéma sledování](https://www.hl7.org/fhir/observation.html) týká ústřední element ve zdravotnictví, používá pro podporu diagnostiky, sledovat průběh, určíte standardní hodnoty a vzorce a dokonce zaznamenat demografickým charakteristiky. 

-   [Dojde k schématu](https://www.hl7.org/fhir/encounter.html) zahrnuje typy narazí, jako je například ambulatory nouzový, home stavu, má za a virtuální narazí.

-   [Podmínka schématu](https://www.hl7.org/fhir/condition.html) najdete podrobné informace o podmínku, problém, diagnostiky, nebo další události, situace, problém nebo klinické koncept, který vzrostla na úroveň žádný problém.  



### <a name="event-grid"></a>Event Grid


Řešení podporuje Azure Event Grid, jediné služby pro správu směrování všech událostí z jakéhokoli zdroje do libovolného cíle poskytující:

-   [Zabezpečení a ověřování](/azure/event-grid/security-authentication)

-   [Řízení přístupu na základě rolí](/azure/event-grid/security-authentication#management-access-control) pro různé operace správy, jako je například seznam odběrů událostí, vytváření nových a generování klíčů

-   Auditování

## <a name="store"></a>ÚLOŽIŠTĚ 

### <a name="sql-database-and-server"></a>SQL Database a serveru 


-   [Transparentní šifrování dat (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) poskytuje v reálném čase šifrování a dešifrování dat uložených ve službě Azure SQL Database pomocí klíč uložený ve službě Azure Key Vault.

-   [Posouzení ohrožení zabezpečení SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) je snadno konfigurovatelné nástroj, který můžete zjistit, sledování a náprava potenciálních ohrožení zabezpečení databáze.

-   [Detekce hrozeb služby SQL Database](/azure/sql-database/sql-database-threat-detection) povolena.

-   [Auditování služby SQL Database](/azure/sql-database/sql-database-auditing) povolena.

-   [SQL Database metrik a protokolování diagnostiky](/azure/sql-database/sql-database-metrics-diag-logging) povolena.

-   [Pravidla brány firewall úrovni serveru a databáze](/azure/sql-database/sql-database-firewall-configure) mají byla zvýšit.

-   [Funkce Always Encrypted sloupce](/azure/sql-database/sql-database-always-encrypted-azure-key-vault) slouží k ochraně pacientů, střední, jména a příjmení.
    Kromě toho šifrování sloupců databáze také používá Azure Active Directory (AD) k ověření aplikace ke službě Azure SQL Database.

-   Přístup k SQL Database a SQL Server nakonfigurovaný podle principu nejnižší úrovně oprávnění.

-   Přístup přes bránu firewall služby SQL jsou povoleny pouze požadované IP adresy.

### <a name="storage-accounts"></a>Účty úložiště


-   [Data přenášená data se přenáší pomocí protokolu TLS/SSL pouze](/azure/storage/common/storage-require-secure-transfer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

-   Anonymní přístup není povolen pro kontejnery.

-   Pravidla upozornění jsou nakonfigurované pro sledování činnosti anonymous.

-   HTTPS je vyžadován pro přístup k prostředkům účtu úložiště.

-   Ověřovací data požadavku je zaznamenána a monitorovat.

-   Data v úložišti objektů Blob se šifrují při nečinnosti.

## <a name="analyze"></a>ANALÝZA

### <a name="machine-learning"></a>Machine Learning


-   [Je povoleno protokolování](/azure/machine-learning/studio/web-services-logging) pro Machine Learning Studio webové služby.
- pomocí [Machine Learning Studio](/azure/machine-learning/desktop-workbench/experimentation-service-configuration) workbench vyžaduje vývoj experimenty, poskytující možnost predikce délky sadu řešení. [Integrace aplikace workbench](/azure/machine-learning/desktop-workbench/using-git-ml-project) pomáhají zjednodušit správu experimentů.

## <a name="security"></a>ZABEZPEČENÍ

### <a name="azure-security-center"></a>Azure Security Center
- [Azure Security Center](https://azure.microsoft.com/services/security-center/) poskytuje centralizovaný pohled stavu zabezpečení všech vašich prostředků Azure. Na první pohled můžete ověřit, že kontrolních mechanismů zabezpečení jsou na místě a správně nakonfigurovaný, a můžete rychle identifikovat všechny prostředky, které vyžadují pozornost. 

- [Azure Advisor](/azure/advisor/advisor-overview) je individuální cloudový konzultant, který pomáhá dodržovat osvědčené postupy a optimalizovat nasazení Azure. Analyzuje konfiguraci vašich prostředků a telemetrii jejich využívání a následně doporučí řešení, která zlepší finanční úspornost, výkon, dostupnost a zabezpečení vašich prostředků Azure.

### <a name="application-insights"></a>Application Insights
- [Application Insights](/azure/application-insights/app-insights-overview) je rozšiřitelná služba správu výkonu aplikací (APM) pro webové vývojáře ve více platformách. Slouží k monitorování živé webové aplikace. Detekuje anomálie výkonu. Obsahuje výkonné analytické nástroje pro diagnostiku problémů a pomáhá porozumět, jak vlastně uživatelé vaši aplikaci používají. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití.

### <a name="azure-alerts"></a>Upozornění Azure
- [Výstrahy nabízejí způsob sledování služeb Azure a umožňují konfigurovat podmínky nad daty. Výstrahy také poskytují oznámení, když výstražný stav odpovídá data monitorování.

### <a name="operations-management-suite-oms"></a>Operations Management Suite (OMS)
[Operations Management Suite (OMS)](/azure/operations-management-suite/operations-management-suite-overview) je kolekce služeb pro správu.

-   Pracovní prostor povolený pro Security Center

-   Pracovní prostor povolený pro úlohy monitorování

-   Monitorování úloh je povolený pro:

    -   Identita a přístup

    -   Zabezpečení a audit

    -   Azure SQL DB Analytics

    -   [Azure WebApp Analytics](/azure/log-analytics/log-analytics-azure-web-apps-analytics) řešení

    -   Key Vault Analytics

    -   Sledování změn

-   [Application Insights Connector (Preview)](/azure/log-analytics/log-analytics-app-insights-connector) je povoleno

-   [Aktivita log analytics](/azure/log-analytics/log-analytics-activity) je povoleno
