---
title: Přehled služby Azure Analysis Services | Dokumentace Microsoftu
description: Získejte celkový přehled o službě Analysis Services v Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: overview
ms.date: 05/16/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 27b394a6b2d2f85afeb809fcad3b88564f3054e7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="what-is-azure-analysis-services"></a>Co je služba Azure Analysis Services?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services poskytuje modelování dat na podnikové úrovni v cloudu. Je to plně spravovaná platforma jako služba (PaaS) integrovaná se službami datové platformy Azure. 

Se službou Analysis Services můžete provádět mashup a kombinování dat z více zdrojů, definovat metriky a zabezpečit svá data v jediném důvěryhodném sémantickém datovém modelu. Datový model poskytuje uživatelům nejsnadnější a nejrychlejší způsob procházení obrovských objemů dat pomocí klientských aplikací, jako jsou Power BI, Excel, Reporting Services, aplikace třetích stran a vlastní aplikace.

![Zdroje dat](./media/analysis-services-overview/aas-overview-data-sources.png)

Podívejte se na [toto video](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4) a zjistěte, jak služba Azure Analysis Services zapadá do celkových možností BI společnosti Microsoft a jaké výhody vám může přinést převedení datových modelů do cloudu.

## <a name="built-on-sql-server-analysis-services"></a>Vytvořeno na základě SQL Server Analysis Services
Služba Azure Analysis Services je kompatibilní s mnoha skvělými funkcemi, které už jsou ve službě SQL Server Analysis Services Enterprise Edition. Azure Analysis Services podporuje tabulkové modely na [úrovních kompatibility](analysis-services-compat-level.md) 1200 a 1400. Podporují se oddíly, zabezpečení na úrovni řádku, obousměrné relace i překlady. Režimy In-Memory a DirectQuery znamenají bleskově rychlé dotazy nad obrovskými a komplexními datovými sadami.

Tabulkové modely nabízejí rychlý vývoj a jsou vysoce přizpůsobitelné. Pro vývojáře tabulkové modely zahrnují tabulkový objektový model (TOM) pro popis objektů modelu. Tabulkový objektový model (TOM) je přístupný ve formátu JSON prostřednictvím [jazyka TMSL (Tabular Model Scripting Language)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) a jazyka DDL AMO v oboru názvů [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx).

## <a name="better-with-azure"></a>Lepší s Azure
Azure Analysis Services se integruje s mnoha službami Azure a umožňuje tak sestavovat sofistikovaná analytická řešení. Integrace s [Azure Active Directory](../active-directory/active-directory-whatis.md) poskytuje zabezpečený přístup na základě role k důležitým datům. Můžete provést integraci s kanály [Azure Data Factory](../data-factory/introduction.md) vložením aktivity, která načte data do modelu. Můžete použít služby [Azure Automation](../automation/automation-intro.md) a [Azure Functions](../azure-functions/functions-overview.md) k prosté orchestraci modelů pomocí vlastního kódu.

## <a name="get-up-and-running-quickly"></a>Rychlé zprovoznění
Na webu Azure Portal můžete [vytvořit server](analysis-services-create-server.md) během několika minut. A pomocí PowerShellu a [šablon](../azure-resource-manager/resource-manager-create-first-template.md) Azure Resource Manageru můžete zřizovat servery s využitím deklarativní šablony. S jedinou šablonou můžete nasadit několik služeb společně s dalšími komponentami Azure, jako jsou účty úložiště nebo služba Azure Functions. 

Jakmile máte vytvořený server, můžete vytvořit tabulkový model přímo na webu Azure Portal. Pomocí nové (ve verzi Preview) [funkce Webový návrhář](analysis-services-create-model-portal.md) se můžete připojit ke službě Azure SQL Database, zdroji dat Azure SQL Data Warehouse nebo importovat soubor .pbix aplikace Power BI Desktop. Relace mezi tabulkami se vytvoří automaticky a vy můžete vytvářet míry nebo upravovat soubor model.bim ve formátu JSON přímo z prohlížeče.

## <a name="scale-to-your-needs"></a>Škálování podle vašich potřeb

### <a name="the-right-tier-when-you-need-it"></a>Správná úroveň kdykoli potřebujete

Služba Azure Analysis Services je dostupná v úrovních Developer, Basic a Standard. Ceny plánů na jednotlivých úrovních se liší podle výpočetního výkonu, jednotek QPU a velikosti paměti. Při vytváření serveru si vyberete plán na nějaké úrovni. Plány můžete měnit na vyšší nebo nižší v rámci stejné úrovně nebo upgradovat na vyšší úroveň, není však možné downgradovat z vyšší úrovně na nižší.

Zvětšete, zmenšete nebo pozastavte svůj server. Použijte web Azure Portal nebo mějte úplnou kontrolu za běhu pomocí PowerShellu. Platíte jenom za to, co používáte. Další informace o různých plánech a úrovních a cenovou kalkulačku, která vám pomůže s určením správného plánu, najdete v tématu [Ceny služby Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

### <a name="scale-out-resources-for-fast-query-responses"></a>Škálování prostředků na více instancí pro zajištění rychlých odpovědí na dotazy

Díky škálování služby Azure Analysis Services na více instancí se dotazy klientů distribuují napříč několika *replikami dotazů* ve fondu dotazů. Repliky dotazů obsahují synchronizované kopie vašich tabulkových modelů. Rozprostřením zátěže dotazů je možné snížit dobu odezvy u dotazů s vysokou zátěží. Operace zpracování modelů je možné oddělit od fondu dotazů a zajistit tím, aby neměly nepříznivý vliv na dotazy klientů. Můžete vytvořit fond dotazů s až sedmi dalšími replikami dotazů (celkově osmi, včetně vašeho serveru). 

Stejně jako u změny úrovně můžete repliky dotazů škálovat na více instancí podle vašich potřeb. Škálování na více instancí můžete nakonfigurovat na portálu nebo pomocí rozhraní REST API. Další informace najdete v tématu [Škálování služby Azure Analysis Services na více instancí](analysis-services-scale-out.md).

## <a name="keep-your-data-close"></a>Mějte data blízko sebe
Servery služby Azure Analysis Services je možné vytvořit v následujících [oblastech Azure](https://azure.microsoft.com/regions/):

| Amerika | Evropa | Asie a Tichomoří |
|----------|--------|--------------|
|  Brazílie – jih<br> Střední Kanada<br> Východ USA<br> Východní USA 2<br> Střed USA – sever<br> Střed USA<br> Střed USA – jih<br> Západní střed USA<br> Západní USA<br> Západní USA 2 | Severní Evropa<br> Spojené království – jih<br> Západní Evropa |   Austrálie – jihovýchod<br> Japonsko – východ<br> Jihovýchodní Asie<br> Indie – západ  |

Neustále se přidávají nové oblasti, takže tento seznam nemusí být kompletní. Umístění volíte při vytváření serveru na webu Azure Portal nebo pomocí šablon Azure Resource Manageru. Pokud chcete získat nejlepší výkon, zvolte umístění co nejblíže vaší největší uživatelské základně. Zajistěte [vysokou dostupnost](analysis-services-bcdr.md) nasazením modelů na redundantních serverech ve více oblastech.

## <a name="migrate-your-existing-tabular-models"></a>Migrace existujících tabulkových modelů
Pokud už máte existující řešení modelu SQL Server Analysis Services v místním prostředí, můžete je migrovat do služby Azure Analysis Services bez významných změn. Pokud chcete provést migraci, pomocí rozšíření SSDT můžete nasadit model na server. Nebo můžete v aplikaci SSMS použít zálohování a obnovení nebo jazyk TMSL.

Pokud máte místní zdroje dat, je potřeba nainstalovat a nakonfigurovat [místní bránu dat](analysis-services-gateway.md). Pokud už máte nakonfigurované role a členy rolí, vaše role se budou migrovat, ale členy rolí budete muset znovu přidat pomocí aplikace SSMS nebo PowerShellu.

## <a name="connect-to-popular-data-sources"></a>Připojení k oblíbeným zdrojům dat
Azure Analysis Services podporuje [připojení ke zdrojům dat](analysis-services-datasource.md) v místním prostředí vaší organizace i v cloudu. Kombinací dat z místních a cloudových zdrojů dat získáte hybridní řešení. 

Nové tabulkové modely 1400 využívají moderní funkci Získání dat v rozšíření SSDT založenou na dotazovacím jazyku se vzorci M. S funkcí Získání dat máte více funkcí pro transformaci a mashup dat a možnost vytvářet a upravovat vlastní pokročilé dotazy v jazyce vzorců M. Například u tabulkových modelů 1400 můžete modelovat na základě datových souborů ve službě Azure Blob Storage.

## <a name="use-the-tools-you-already-know"></a>Používejte známé nástroje.

![Vývojářské nástroje BI](./media/analysis-services-overview/aas-overview-dev-tools.png)

#### <a name="sql-server-data-tools-ssdt-for-visual-studio"></a>SQL Server Data Tools (SSDT) pro Visual Studio
Vyvíjejte a nasazujte modely pomocí bezplatného rozšíření [SQL Server Data Tools (SSDT) pro Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Rozšíření SSDT zahrnuje šablony projektů Analysis Services, které vám pomůžou s rychlým zprovozněním. Rozšíření SSDT teď pro tabulkové modely 1400 zahrnuje moderní funkci Získání dat pro dotazování a mashup zdrojů dat. Pokud už znáte funkci Získání dat v aplikacích Power BI Desktop a Excel 2016, tak už víte, jak snadné je vytvářet vysoce přizpůsobené dotazy na zdroj dat.

#### <a name="sql-server-management-studio"></a>SQL Server Management Studio
Spravujte servery a modelové databáze pomocí aplikace [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Připojte se k vašim serverům v cloudu. Spouštějte skripty jazyka TMSL přímo z okna dotazu XMLA a automatizujte pomocí nich úlohy. Nové funkce se přidávají rychle – aplikace SSMS se aktualizuje každý měsíc.

#### <a name="powershell"></a>PowerShell
Úlohy správy prostředků serveru, jako je například vytváření serverů, pozastavení nebo obnovení operací serveru nebo změna úrovně služby, používají rutiny Azure Resource Manageru (AzureRM). Ostatní úlohy správy databází, jako je například přidávání nebo odebírání členů rolí, zpracování nebo spouštění skriptů jazyka TMSL, používají rutiny v modulu SqlServer. Moduly AzureRM a SQLServer jsou k dispozici v [Galerii prostředí PowerShell](https://www.powershellgallery.com/).


## <a name="your-data-is-secure"></a>Vaše data jsou v bezpečí
![Vizualizace dat](./media/analysis-services-overview/aas-overview-secure.png)

#### <a name="authentication"></a>Authentication
Ověřování uživatelů pro Azure Analysis Services zařizuje služba [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md). Při pokusu o přihlášení k databázi Azure Analysis Services používají uživatelé identitu účtu organizace s přístupem k databázi, ke které se snaží získat přístup. Tyto identity uživatelů musí být členy výchozí služby Azure Active Directory pro předplatné, ve kterém se server služby Azure Analysis Services nachází. Další informace najdete v tématu [Ověřování a uživatelská oprávnění](analysis-services-manage-users.md).

#### <a name="data-security"></a>Zabezpečení dat
Služba Azure Analysis Services využívá úložiště Azure Blob Storage k zachování úložiště a metadat pro databáze služby Analysis Services. Datové soubory jsou v rámci objektu Blob šifrované pomocí šifrování na straně serveru Azure Blob. Při použití režimu přímých dotazů se ukládají jenom metada. Ke skutečným datům se přistupuje ze zdroje dat v době dotazu.

#### <a name="firewall"></a>Brána firewall

Brána firewall služby Azure Analysis Services blokuje všechna připojení klientů kromě těch zadaných v pravidlech. Pravidla můžete konfigurovat zadáním povolených IP adres jednotlivých klientů nebo jejich rozsahu. Povolit nebo blokovat je možné také připojení (služby) Power BI. 

#### <a name="on-premises-data-sources"></a>Místní zdroje dat
Zabezpečeného přístupu k datům nacházejícím se místně ve vaší organizaci se dosahuje instalací a konfigurací [místní brány dat](analysis-services-gateway.md). Brány poskytují přístup k datům pro režimy přímých dotazů a v paměti. Když se model služby Azure Analysis Services připojuje k místnímu zdroji dat, vytvoří se dotaz společně se zašifrovanými přihlašovacími údaji pro místní zdroj dat. Cloudová služba brány analyzuje dotaz a nabídne žádost sběrnici Azure Service Bus. Místní brána zadá dotazy sběrnici Azure Service Bus na čekající žádosti. Brána pak získá dotaz, dešifruje přihlašovací údaje a připojí se ke zdroji dat pro provedení. Výsledky se pak odešlou ze zdroje dat zpět do brány a potom dále do databáze Azure Analysis Services.

Služba Azure Analysis Services se řídí [podmínkami služeb Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) a [prohlášením o zásadách ochrany osobních údajů služeb Microsoft Online](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Pokud se o službě Azure Security chcete dozvědět víc, podívejte se na [Microsoft Trust Center](https://www.microsoft.com/trustcenter/Security/AzureSecurity).



## <a name="supports-the-latest-client-tools"></a>Podporuje nejnovější nástroje klienta
![Vizualizace dat](./media/analysis-services-overview/aas-overview-clients.png)

Podporují se všechny moderní nástroje pro zkoumání a vizualizaci dat, jako jsou Power BI, Excel, SQL Server 2017 Reporting Services a nástroje třetích stran, které poskytují uživatelům vysoce interaktivní a vizuálně bohaté přehledy dat modelu. 

Klienti pro připojení k serverům služby Analysis Services používají [klientské knihovny](analysis-services-data-providers.md) MSOLAP, AMO nebo ADOMD. Klientské aplikace Microsoftu jako Power BI Desktop a Excel instalují všechny tři klientské knihovny. Mějte však na paměti, že v závislosti na verzi nebo četnosti aktualizací nemusí klientské knihovny být nejnovější verze, kterou vyžaduje služba Azure Analysis Services. To samé platí pro vlastní aplikace a další rozhraní, jako jsou AsCmd, Tom nebo ADOMD.NET. Tyto aplikace obvykle vyžadují ruční instalaci těchto knihoven jako součást balíčku.


## <a name="get-help"></a>Podpora

#### <a name="documentation"></a>Dokumentace
Službu Azure Analysis Services je jednoduché nastavit a spravovat. Všechny potřebné informace týkající se vytvoření a správy služeb serveru najdete zde. Vytváření datového modelu pro nasazení na server je velmi podobné vytváření datového modelu nasazovaného na místní server. [Nápověda pro SQL Server Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services) obsahuje rozsáhlou knihovnu článků s koncepty, postupy, kurzy a odkazy.

#### <a name="videos"></a>Videa
Užitečná videa najdete v části [Azure Analysis Services na webu Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

#### <a name="blogs"></a>Blogy
Všechno se rychle mění. Nejnovější informace vždy najdete na [blogu týmu Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/) a [blogu o Azure](https://azure.microsoft.com/blog/).

#### <a name="community"></a>Komunita
Služba Analysis Services má velmi aktivní komunitu uživatelů. Připojte se ke konverzaci na [fóru Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Váš názor
Máte návrhy nebo požadavky na funkce? Své komentáře pište na web [Zpětná vazba ke službě Azure Analysis Services](https://aka.ms/azureanalysisservicesfeedback).

Máte návrhy týkající se dokumentace? Komentáře můžete přidávat pomocí Livefyre v dolní části každého článku.

## <a name="next-steps"></a>Další kroky
Zjistěte, jak [vytvořit server](analysis-services-create-server.md) v Azure.   
Jakmile budete mít vytvořený server, [přidejte ukázkový model](analysis-services-create-sample-model.md).   
