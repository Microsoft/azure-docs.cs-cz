---
title: Karta tržiště nabídky aplikací Azure
description: Kartu Marketplace použijte k identifikaci marketingových prostředků nabídky aplikace Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 967b66a67d51b3a79bcf930ce977af48acc3dd63
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827565"
---
# <a name="azure-application-marketplace-tab"></a>Karta Azure Application Marketplace

Kartu Marketplace použijte k popisu vaší aplikace Azure a poskytování marketingových prostředků. Tato karta obsahuje následující formuláře: Přehled, marketingové artefakty, řízení vedoucích a právní.

## <a name="overview-form"></a>Formulář s přehledem

Formulář s přehledem má požadovaná a volitelná pole zobrazená v následujícím snímku obrazovky. Požadovaná pole jsou indicted hvězdičkou (*).

![Formulář s přehledem](./media/azureapp-marketplace-overview.png)

Následující tabulka popisuje nastavení, která se použijí pro vytvoření prezentace pro nabídku.   Pole připojená pomocí hvězdičky jsou povinná.

|      Pole         |    Popis    |
|  ---------------   |  ---------------  |
| **\* názvu**        | Název nabídky Bude se zobrazovat na webu Marketplace na místě. Maximální délka je 50 znaků. |
| **Souhrn\***      | Krátké shrnutí nabídky Maximální délka je 100 znaků.           |
| **Dlouhý souhrn\*** | Delší souhrn nabídky (i když by to bylo stejné jako u souhrnu). Maximální délka je 256 znaků.           |
| **Popis\***  | Popis nabídky Maximální délka je 3000 znaků. Je povoleno jednoduché formátování HTML, včetně &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;&gt;, &lt;ol&gt; a značek hlaviček.  |
| **\* identifikátoru marketingu** | Jedinečná adresa URL, která se má přidružit k této nabídce, obvykle zahrnuje vaši organizaci a název řešení, maximální délka 50 znaků. Vyberte krátký a popisný identifikátor marketingu pro vaši službu. Tato nabídka bude použita v adresách URL webu Marketplace. Pokud je například vaším vydavatelem ID "contoso" a váš marketingový identifikátor je "dotazů", adresa URL vaší nabídky v Azure Marketplace bude https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp  
| **Náhled ID předplatných\*** | Přidat z jednoho až 100 identifikátorů předplatného pro náhledy. Tyto bílé odběry budou mít přístup k vaší nabídce, pokud jsou po publikování dostupné ve verzi Preview a teprve potom budou aktivní.          |
| **Užitečné odkazy**    | Volitelně můžete poskytnout odkazy na různé prostředky pro uživatele vaší nabídky, jako je podpora, dokumentace, fóra atd.  Doporučuje se přidat aspoň jeden odkaz na svou dokumentaci.            |
| **Navrhované kategorie (max. 5)\*** | Vyberte jednu až pět kategorií. Vybrané kategorie se používají k mapování vaší nabídky na kategorie produktů dostupné v Azure Marketplace a na webu Azure Portal. Budou zobrazeny na stránkách pro procházení a na stránce s informacemi o produktu. |
|  |  |


## <a name="marketing-artifacts"></a>Marketingové artefakty

Formulář artefaktů marketingu má požadovaná a volitelná pole zobrazená v následujícím snímku obrazovky. Požadovaná pole jsou indicted hvězdičkou (*).

![Formulář artefaktů marketingu](./media/azureapp-marketplace-artifacts.png)

V následující tabulce jsou popsány marketingové artefakty.

|      Pole         |    Popis    |
|  ---------------   |  ---------------  |
| **Malá\***        | Malé logo: 40x40 pixelů ve formátu PNG     |
| **Střední\***       | Střední logo: 90x90 pixelů ve formátu PNG    |
| **Velké\***        | Velké logo: 115x115 pixelů ve formátu PNG   |
| **Roztažitelné\***         | Logo roztažitelné: 255x115 pixelů ve formátu PNG    |
| **Hero**           | Volitelné logo Hero: 815x290 pixelů ve formátu PNG. **Poznámka:** Po nahrání nelze ikonu Hero odstranit. |
| **Snímky obrazovky (max. 5)** |        Snímky obrazovky se zobrazí na stránce s informacemi o produktu. Představují dobrý způsob, jak vizuálně sdělit, co vaše aplikace dělá a jak funguje. Můžete například zobrazit diagramy architektury nebo ilustrace případu použití. Snímky obrazovky jsou volitelné a omezené na 5 na SKU. Přidání snímku obrazovky:<ul><li>Výběr **+ Přidat snímek obrazovky** pro otevření okna snímku obrazovky</li><li>**Název** – zadejte název nebo název (maximální délka je 100 znaků).</li><li>**Nahrání** – nahrání obrázku Musí být ve formátu PNG a velikost je 533 × 324 pixelů.</li></ul>           |
| **Přidat video**      | Volitelná, videa se zobrazí na stránce s podrobnostmi o produktu. Představují dobrý způsob, jak vizuálně sdělit, co vaše aplikace dělá a jak funguje. Přidání videa: <ul><li>Vyberte **+ přidat video** a otevřete okno video.</li><li>**Název** – zadejte název nebo název (maximální délka je 100 znaků).</li><li>**Odkaz** – zadejte adresu URL pro web, který je hostitelem videa (YouTube nebo Vimeo).</li><li>**Miniatura** – nahrajte miniaturu. Musí být ve formátu PNG a velikost je 533 × 324 pixelů.</li></ul>          |
|  |  |


### <a name="artifact-examples-in-azure-marketplace"></a>Příklady artefaktů v Azure Marketplace

Následující snímek obrazovky ukazuje příklad výsledku hledání na webu Marketplace.

![Výsledek hledání nabídky Marketplace](./media/azureapp-marketplace-example-browse.png)

Následující obrázek ukazuje, jak se nabídka zobrazuje na webu Marketplace poté, co zákazník klikne na dlaždici nabídky ve výsledku hledání.

![Podrobnosti o výsledku hledání nabídky Marketplace](./media/azureapp-marketplace-example-details.png)


### <a name="artifact-examples-in-azure-portal"></a>Příklady artefaktů na webu Azure Portal

Následující snímek obrazovky ukazuje, jak se nabídka zobrazuje na webu Azure Portal. Nabídka aplikace v tomto příkladu je k dispozici tak, že přejdete na **web Marketplace > vše > Dev + Test > Jenkinse**. V nabídce Jenkinse se zobrazuje logo, název a zobrazované jméno vydavatele.

![Procházet nabídky v Azure Portal](./media/azureapp-portalbrowse-artifacts-jenkins.png)

Další snímek obrazovky zobrazuje podrobné informace o aplikaci, když uživatel vybere Jenkinse.

![Podrobnosti o nabídce v Azure Portal](./media/azureapp-portal-artifacts-jenkins-details.png)


### <a name="logo-guidelines"></a>Pokyny pro použití loga

Všechna loga odeslaná na portál partnerů cloudu by měla postupovat podle pokynů:

- Design Azure má jednoduchou paletu barev. Ponechte počet primárních a sekundárních barev v logu nízký.
- Barvy motivu webu Azure Portal jsou bílé a černé. Vyhněte se použití těchto barev jako barvy pozadí pro vaše loga. Použijte barvu, která bude mít v Azure Portal výrazným logem. Doporučujeme použít jednoduché primární barvy. Pokud používáte průhledné pozadí, zajistěte, aby se loga/text nepoužívala bílá, černá nebo modrá.
- Nepoužívejte na svém logu pozadí přechodu.
- Vyhněte se umístění textu, dokonce i názvu společnosti nebo značky, na logo. Vzhled a chování loga by mělo být "ploché" a mělo by se vyhnout přechodu.
- Nepřizpůsobujte logo.


#### <a name="hero-logo"></a>Hero logo

Logo Hero je volitelné.

>[!IMPORTANT]
>Po nahrání se logo Hero nedá odstranit.

Pro logo Hero použijte následující pokyny:

- Černá, bílá a průhledná pozadí nejsou povoleny.
- Vyhněte se použití jakékoli světlé barvy jako pozadí loga. Zobrazované jméno vydavatele, název plánu a celková doba nabídky se zobrazují v barvě bílého písma a musí se vystupovat na pozadí.
- Nepoužívejte většinu textu při navrhování loga. V případě, že je nabídka uvedena, jsou do loga vložena pole název vydavatele, název plánu, dlouhý souhrn nabídky a tlačítko vytvořit.
- Zahrňte nepoužívané obdélníkové místo na pravé straně loga Hero. Toto prázdné místo je 415x100 pixelů a posun od levého po 370 pixelů.


## <a name="lead-management"></a>Správa zájemců

Formulář pro správu potenciálních zákazníků má volitelné pole pro konfiguraci správy zájemců. Chcete-li nakonfigurovat správu zájemců, vyberte cíl zájemce z rozevíracího seznamu. Následující snímek obrazovky zobrazuje dostupné cíle.

![Vybrat cíl správy potenciálních zákazníků](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Výběrem informační ikony zobrazíte tuto zprávu: "vyberte systém, ve kterém budou vaše zájemci uložená. [Tady](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) se dozvíte, jak se připojit k systému CRM. "

Další informace najdete v tématu [Konfigurace zákaznických zájemců](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads).


## <a name="legal"></a>Právní informace

K poskytnutí právní dokumentace vyžadované pro každou nabídku použijte právní formu.

Zadejte tyto informace:

- **Adresa URL zásad ochrany osobních údajů\*** – zadejte odkaz na zásady ochrany osobních údajů vaší aplikace.
- **Podmínky použití\*** – zadejte podmínek použití pro vaši aplikaci. Zákazníci musí tyto podmínky přijmout, aby mohli svoji aplikaci vyzkoušet.

![Právní forma](./media/azureapp-marketplace-legal.png)


## <a name="next-steps"></a>Další kroky

[Karta Podpora](./cpp-support-tab.md)
