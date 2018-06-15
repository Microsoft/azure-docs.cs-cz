---
title: Zabezpečení na úrovni řádků s Power BI prostoru kolekce
description: Podrobnosti o zabezpečení na úrovni řádků s Power BI prostoru kolekce
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 7256e2f798fbc32c098f19f60b62e577300868c7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31414089"
---
# <a name="row-level-security-with-power-bi-workspace-collections"></a>Zabezpečení na úrovni řádků s Power BI prostoru kolekce

Zabezpečení na úrovni řádků (RLS) slouží k omezení přístupu uživatelů k konkrétní dat v rámci sestavu nebo datovou sadu, povolení pro několik různých uživatelům používat stejné sestavě při všech dat vidět jiné. Kolekce pracovních prostorů Power BI podporují datové sady, které jsou nakonfigurované s RLS.

![Tok zabezpečení na úrovni řádků v Power BI prostoru kolekce](media/row-level-security/flow-1.png)

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Aby bylo možné využít výhod RLS, je důležité, že rozumíte tři hlavní koncepty; Uživatelé, role a pravidla. Podívejme bližší pohled na každý:

**Uživatelé** – tyto jsou skutečné koncovým uživatelům prohlížení sestav. V Power BI prostoru kolekcí uživatelé se identifikují podle vlastnosti uživatelské jméno v tokenu aplikace.

**Role** – uživatelé patří do role. Role je kontejner pro pravidla a může mít název něco jako "Vedoucí prodeje" nebo "Obchodního zástupce". V Power BI prostoru kolekcí uživatelé se identifikují podle vlastnosti rolí v tokenu aplikace.

**Pravidla** – role mají pravidla a tato pravidla jsou skutečné filtry, které se chystáte použít k datům. To může být stejně jednoduché jako "země = USA" nebo něco víc dynamické.

### <a name="example"></a>Příklad:

Pro zbývající část tohoto článku poskytujeme příklad vytváření RLS a využívání, v rámci aplikace embedded. Naše Ukázka používá [prodejní analýzy ukázka](http://go.microsoft.com/fwlink/?LinkID=780547) soubor PBIX.

![Například prodejní sestavy](media/row-level-security/scenario-2.png)

Naše ukázka prodejní analýzy prodejů pro všechny úložiště v určité maloobchodní řetězec. Bez RLS, bez ohledu na to, které oblastní správce přihlásí a zobrazení sestavy, uvidí stejná data. Vedoucí pracovníci určil každý oblastní manager byste měli vidět jenom prodeje pro úložiště, které spravují a k tomu můžeme použít RLS.

RLS je vytvořené v Power BI Desktop. Po otevření datovou sadu a sestavu jsme můžete přepnout do zobrazení diagramu zobrazíte schéma:

![Diagram modelu v Power BI Desktop](media/row-level-security/diagram-view-3.png)

Zde je několik věcí Všimněte s toto schéma:

* Všechny míry, jako například **celkový prodej**, jsou uloženy v **prodej** tabulky faktů.
* Existují čtyři další dimenze související tabulky: **položky**, **čas**, **úložiště**, a **oblastní**.
* Šipky v řádcích vztah znamenat toho, jak filtry můžete toku z jedné tabulky do jiné. Například, pokud filtr je umístěn na **čas [datum]**, v aktuálním schématu bude pouze filtrovat dolů hodnoty **prodej** tabulky. Žádné jiné tabulky by ovlivnila tento filtr, protože všechny šipek na řádcích vztah bod registrace k tabulce prodeje a není rychle.
* **Oblastní** tabulka udává, který správce je pro každou oblast:
  
  ![Oblastní řádků tabulky](media/row-level-security/district-table-4.png)

Založené na tomto schématu v případě, že jsme použít filtr na **Manager oblastní** sloupce v tabulce oblastní, a pokud tento filtr odpovídá uživatele zobrazení sestavy, který filtrovat také filtry dolů **úložiště** a  **Prodej** tabulky jenom zobrazit data pro tento konkrétní oblastní správce.

Tady je jak:

1. Na kartě modelování klikněte na **spravovat role**.  
   ![Správa rolí tlačítka na modelování pásu karet](media/row-level-security/modeling-tab-5.png)
2. Vytvoření nové role s názvem **Manager**.  
   ![Vytvoření role v Power BI Desktop](media/row-level-security/manager-role-6.png)
3. V **oblastní** tabulky zadejte následující výraz DAX: **[oblastní Manager] = USERNAME()**  
   ![Výraz filtru jazyka DAX pro tabulku v roli](media/row-level-security/manager-role-7.png)
4. Abyste měli jistotu, pravidla pracují na **modelování** , klikněte na **zobrazení jako role**a potom zadejte následující:  
   ![Zobrazit jako role](media/row-level-security/view-as-roles-8.png)

   Sestavy se nyní zobrazí data jako v případě, že jste přihlášeni jako **Andrew Ma**.

Použití filtru způsob, jakým jsme se zde filtry dolů všechny záznamy v **oblastní**, **úložiště**, a **prodej** tabulky. Ale kvůli směr filtru na vztahy mezi **prodej** a **čas**, **prodej** a **položky**, a **položky** a **čas** tabulky se nebudou filtrovat dolů.

![Zobrazení diagramu se zvýrazněnou vztahy](media/row-level-security/diagram-view-9.png)

Které mohou být ok pro tento požadavek, ale pokud jsme nechcete, aby správce, kteří mají zobrazovat položky, pro které nemají žádné prodeje, jsme může zapněte obousměrné křížové filtrování pro relaci a toku zabezpečení filtr v obou směrech. To lze provést tak, že upravíte vztah mezi **prodej** a **položky**, podobné výjimky:

![Směr filtru pro vztah mezi](media/row-level-security/edit-relationship-10.png)

Teď, může také toku filtry z tabulky prodeje pro **položky** tabulky:

![Ikona směr filtru relace v zobrazení diagramu](media/row-level-security/diagram-view-11.png)

> [!NOTE]
> Pokud používáte režim DirectQuery pro data, budete muset povolit obousměrné křížové filtrování výběrem tyto dvě možnosti:

1. **Soubor** -> **možnosti a nastavení** -> **funkce verze Preview** -> **povolit křížové filtrování v obou směrech DirectQuery** .
2. **Soubor** -> **možnosti a nastavení** -> **DirectQuery** -> **Povolit neomezené míry v režimu DirectQuery**.

Další informace o obousměrné křížové filtrování, stáhněte si [obousměrné křížové filtrování v SQL Server Analysis Services 2016 a Power BI Desktop](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx) dokument White Paper.

Tím se zabalí do všech práci, kterou je třeba provést v Power BI Desktop, ale je, že jeden další část práce, která je potřeba zajistit, aby RLS pravidel, že jsme definovali pracovní v Power BI Embedded. Uživatelé se ověří a autorizuje vaše aplikace a tokeny aplikací slouží k udělení tohoto přístupu uživatelů k konkrétní sestavy Power BI Embedded. Power BI Embedded nemá žádné konkrétní informace, na který je vaše uživatele. Pro RLS pracovat je třeba předat některé další kontext jako součást tokenu vaší aplikace:

* **uživatelské jméno** (volitelné) – používá se s RLS Toto je řetězec, který slouží k identifikaci uživatele při použití pravidla RLS. V části používání řádek zabezpečení na úrovni s Power BI Embedded
* **role** – řetězec obsahující rolí vyberte při použití pravidla zabezpečení na úrovni řádků. Pokud předávání víc než jedné role, musí být předán jako pole řetězců.

Vytvoření tohoto tokenu pomocí [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#Microsoft_PowerBI_Security_PowerBIToken_CreateReportEmbedToken_System_String_System_String_System_String_System_DateTime_System_String_System_Collections_Generic_IEnumerable_System_String__) metoda. Pokud se vlastnost username nachází, je třeba alespoň jednu hodnotu předat také v rolích.

Například můžete změnit EmbedSample. Mohlo dojít k aktualizaci řádku DashboardController 55 z

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

na

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

Tokenu úplné aplikace vypadá přibližně takto:

![JSON web token příklad](media/row-level-security/app-token-string-12.png)

Nyní s všechny části společně, když se někdo přihlásí k naší aplikaci k zobrazení této sestavy, uvidí data, která mohou zobrazit, jak jsou definovány pomocí našich zabezpečení na úrovni řádků.

![Sestava zobrazí v aplikaci](media/row-level-security/dashboard-13.png)

## <a name="see-also"></a>Další informace najdete v tématech

[Zabezpečení na úrovni řádků (RLS) s výkonem](https://powerbi.microsoft.com/documentation/powerbi-admin-rls/)  
[Ověřování a autorizace v kolekcích Pracovních prostorů Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Vložená ukázka JavaScriptu](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Chcete se ještě na něco zeptat? [Vyzkoušejte komunitu Power BI](http://community.powerbi.com/)