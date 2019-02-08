---
title: Zabezpečení na úrovni řádků s kolekcemi pracovních prostorů Power BI
description: Podrobnosti o zabezpečení na úrovní řádků s kolekcemi pracovních prostorů Power BI
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: f8a5c12bb57a8f59960320c6227174b240bcbc3d
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892231"
---
# <a name="row-level-security-with-power-bi-workspace-collections"></a>Zabezpečení na úrovni řádků s kolekcemi pracovních prostorů Power BI

Zabezpečení na úrovni řádků (RLS) slouží k omezení přístupu uživatelů na konkrétní data v rámci sestavy nebo datové sady a umožňuje různým uživatelům používat stejnou sestavu, přičemž všechny zajímalo jiná data. Kolekce pracovních prostorů Power BI podporují datové sady, které jsou nakonfigurované zabezpečení na úrovni řádků.

![Tok zabezpečení na úrovni řádků v kolekcích pracovních prostorů Power BI](media/row-level-security/flow-1.png)

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Pokud chcete využít výhod zabezpečení na úrovni řádků, je důležité pochopit tři hlavní pojmy Uživatelé, role a pravidla. Pojďme se na ně podívat podrobněji:

**Uživatelé** – jsou skutečné koncovým uživatelům prohlížení sestav. V kolekcích pracovních prostorů Power BI se uživatelé identifikují podle vlastnosti uživatelské jméno v tokenu pro aplikaci.

**Role** – uživatelé mají přiřazené role. Role je kontejnerem pro pravidla a může se nazývat například "Manažer prodeje" nebo "Obchodní zástupce". V kolekcích pracovních prostorů Power BI se uživatelé identifikují podle vlastnosti role v tokenu pro aplikaci.

**Pravidla** – role mají pravidla a tato pravidla jsou skutečnými filtry, které se chystáte použít pro data. To může být stejně snadné jako "země = Česká republika" nebo něco složitějšího.

### <a name="example"></a>Příklad:

Pro zbývající část tohoto článku poskytujeme příklad vytváření zabezpečení na úrovni řádků a následného využití uvnitř vložené aplikace. Naše Ukázka používá [ukázka analýzy maloobchodního prodeje](https://go.microsoft.com/fwlink/?LinkID=780547) souboru PBIX.

![Příklad prodejní sestavy](media/row-level-security/scenario-2.png)

Naše ukázka analýzy maloobchodního prodeje zobrazuje prodej všech obchodů konkrétní prodejního řetězce. Bez zabezpečení na úrovni řádků, bez ohledu na to, které district manager přihlásí a sestavu, uvidí stejná data. Vedoucí pracovníci určil každý oblastní manažer by měl vidět pouze prodej obchodů, které spravují a k tomu můžeme použít zabezpečení na úrovni řádků.

Zabezpečení na úrovni řádků se vytváří v Power BI Desktopu. Po otevření datové sady a sestavy se můžeme Přepnout na zobrazení diagramu, abychom viděli toto schéma:

![Diagram modelu v Power BI Desktopu](media/row-level-security/diagram-view-3.png)

Tady je několik věcí, Všimněte si, že se toto schéma:

* Všechny míry, například **celkový prodej**, jsou uloženy v **Sales** tabulka faktů.
* Existují čtyři další související tabulky dimenzí: **Položka**, **čas**, **Store**, a **District**.
* Šipky na vztahu čáry označují, kterým směrem filtry můžete tok z jedné tabulky do druhé. Například, pokud filtr je umístěn na **čas [Date]**, v aktuálním schématu bude pouze filtrovat seznam hodnot v **Sales** tabulky. Žádné další tabulky by tímto filtrem ovlivněné, protože všechny šipky na čárách vztah bod registrace k tabulce prodeje a neukazuje.
* **District** tabulka udává, kdo je manažerem každé oblasti:
  
  ![Řádky tabulky District](media/row-level-security/district-table-4.png)

Na základě tohoto schématu, pokud použijeme filtr na **District Manager** sloupec v tabulce District a pokud tento filtr odpovídá uživatel zobrazující sestavu, která vyfiltrovat také filtry **Store** a  **Prodej** tabulky pouze zobrazit data pro tento konkrétní district manager.

Uděláte to takto:

1. Na kartě modelování, klikněte na tlačítko **spravovat role**.  
   ![Správa rolí tlačítka pásu karet modelování](media/row-level-security/modeling-tab-5.png)
2. Vytvořit roli s názvem **správce**.  
   ![Vytvoření role v Power BI Desktopu](media/row-level-security/manager-role-6.png)
3. V **District** tabulky zadejte následující výraz jazyka DAX: **[District Manager] = USERNAME()**  
   ![Výraz DAX filtru pro tabulky v roli](media/row-level-security/manager-role-7.png)
4. Abyste měli jistotu, pravidla fungují, **modelování** klikněte na tlačítko **zobrazit jako role**a potom zadejte následující:  
   ![Zobrazit jako role](media/row-level-security/view-as-roles-8.png)

   Sestavy nyní zobrazí data, jako kdyby jste přihlášeni jako **Andrew Ma**.

Použití filtru tak, jak jsme tady, filtruje dolů všechny záznamy v **District**, **Store**, a **Sales** tabulky. Kvůli směru filtru u relací mezi **Sales** a **čas**, **Sales** a **položky**a **Položky** a **čas** tabulky filtrovat nebudou.

![Zobrazení diagramu se zvýrazněnou vztahy](media/row-level-security/diagram-view-9.png)

Které mohou být ok pro tento požadavek, ale pokud nechceme správcům zobrazit položky, pro které nemají žádné prodeje, jsme může zapněte obousměrné křížové filtrování pro relaci a tok filtr zabezpečení v obou směrech. To můžete udělat tak, že upravíte vztah mezi **Sales** a **položky**, tímto způsobem:

![Směr křížového filtru u relace](media/row-level-security/edit-relationship-10.png)

Teď můžete také tok filtry z tabulky Sales **položky** tabulky:

![Ikona směr filtru relace v zobrazení diagramu](media/row-level-security/diagram-view-11.png)

> [!NOTE]
> Pokud používáte režim DirectQuery pro vaše data, je potřeba povolit obousměrné křížové filtrování pomocí těchto dvou možností:

1. **Soubor** -> **možnosti a nastavení** -> **funkce ve verzi Preview** -> **povolit křížové filtrování v obou směrech pro DirectQuery** .
2. **Soubor** -> **možnosti a nastavení** -> **DirectQuery** -> **Povolit neomezené míry v režimu DirectQuery**.

Další informace o obousměrné křížové filtrování, stáhněte si [obousměrné křížové filtrování v SQL Server Analysis Services 2016 a Power BI Desktopu](https://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional%20cross-filtering%20in%20Analysis%20Services%202016%20and%20Power%20BI.docx) dokument White Paper.

Tím končí naše shrnutí všechnu práci, kterou je potřeba udělat v Power BI Desktopu, ale je, že jeden další část práce, kterou je potřeba udělat, aby bylo zabezpečení na úrovni řádků pravidel, že jsme definovali práce v Power BI Embedded. Uživatele se ověří a autorizuje vaší aplikace a tokenů aplikace se používají k udělení tohoto přístupu uživatelů do konkrétní sestavy Power BI Embedded. Power BI Embedded nemá žádné konkrétní informace o identitě uživatele je. Zabezpečení na úrovni řádků pro práci je potřeba předat širší kontext jako součást tokenu mechanismu vaší aplikace:

* **uživatelské jméno** (volitelné) – použít s zabezpečení na úrovni řádků, toto je řetězec, který slouží k identifikaci uživatele při použití pravidel zabezpečení na úrovni řádků. Podívejte se, využívá řádek zabezpečení na úrovni pomocí Power BI Embedded
* **role** – řetězec obsahující role, které chcete vybrat při použití pravidel zabezpečení na úrovni řádků. Pokud předáváte více než jednu roli, měli byste je předat jako pole řetězců.

Vytvořit token prostřednictvím [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) metody. Pokud vlastnost username má k dispozici, musíte předat také alespoň jednu hodnotu v rolích.

Například můžete změnit EmbedSample. DashboardController řádku 55 byste mohli aktualizovat z:

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

na

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

Token úplné aplikace vypadá přibližně takto:

![Příklad tokenu JSON web](media/row-level-security/app-token-string-12.png)

Nyní dali všechno dohromady, když uživatel přihlásí na naši aplikaci chcete zobrazit tuto sestavu zobrazí data, která se můžou zjistit, jak je definováno zabezpečení na úrovni řádků.

![Sestava zobrazí v aplikaci](media/row-level-security/dashboard-13.png)

## <a name="see-also"></a>Další informace najdete v tématech

[Zabezpečení na úrovní řádků (RLS) s výkonem](https://powerbi.microsoft.com/documentation/powerbi-admin-rls/)  
[Ověřování a autorizace v kolekcích Pracovních prostorů Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Vložená ukázka JavaScriptu](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Chcete se ještě na něco zeptat? [Vyzkoušejte komunitu Power BI](http://community.powerbi.com/)
