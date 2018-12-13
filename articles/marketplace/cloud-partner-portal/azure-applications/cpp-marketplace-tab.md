---
title: Aplikace Azure nabízejí kartu Marketplace | Dokumentace Microsoftu
description: Na kartě Marketplace k identifikaci marketingové prostředky pro nabídku aplikací Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 74b21a9dd8faa8eb35aa8a2ef738bd6303e4cdd7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196666"
---
# <a name="azure-application-marketplace-tab"></a>Karta aplikace Azure Marketplace

Na kartě Marketplace k popisu aplikace Azure a poskytnout marketingové prostředky. Tato karta obsahuje následujících forem: Přehled, artefakty Marketing, vedoucí správy a právní.

## <a name="overview-form"></a>Formulář přehledu

Přehled formulář má pole povinné a nepovinné znázorňuje následující snímek obrazovky. Povinná pole jsou uvést hvězdičkou (*).

![Formulář přehledu](./media/azureapp-marketplace-overview.png)

Následující tabulka popisuje nastavení se použije pro vytvoření výkladní skříň pro tuto nabídku.

|    **Pole**       |  **Popis**  |
|  ---------------   |  ---------------  |
|        Titul            |        Název nabídky. Se bude zobrazovat výrazném místě na webu Marketplace. Maximální délka je 50 znaků.           |
|         Souhrn           |        Krátký popis nabídky Maximální délka je 100 znaků.           |
|         Dlouhé shrnutí           |        Už souhrn nabídky (i když může být stejný jako souhrn). Maximální délka je 256 znaků.           |
|       Popis            |         Popis nabídky. Maximální délka je 3000 znaků. Jednoduché formátování HTML je povolený, včetně &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; hlavičky značky a žádné jiné.           |
|        Identifikátor marketing            |         Jedinečnou adresu URL pro přidružení k této nabídce obvykle zahrnuje vaší organizace a název řešení, maximální délka 50 znaků. Zvolte krátký, popisný marketingové identifikátor pro vaši službu. Ten se použije v marketplace adresy URL pro tuto nabídku. Například pokud je vaše ID vydavatele "contoso" a marketingové identifikátor "Ukázková aplikace", adresa URL pro nabídky na webu Azure Marketplace bude https://azuremarketplace.microsoft.com/en-us/marketplace/apps/contoso.sampleApp          |
|        ID předplatných ve verzi Preview            |         Přidejte jednu až 100 identifikátorů předplatného náhledy. Tato předplatná uvedená prázdné bude mít přístup k vaší nabídce po publikování dříve, než přejde za provozu je dostupná ve verzi preview.          |
|         Užitečné odkazy           |        Odkazy na různé zdroje můžete zadat pro uživatele vaší nabídky, jako je podpora, dokumentaci, fóra atd. Je nutné přidat alespoň jeden odkaz na dokumentaci.            |
|         Navrhované kategorie (max. 5)           |         Můžete si vyberte až o pěti kategorií. Vybrané kategorie se používají pro mapování vaší nabídky na kategorie produktů, k dispozici v Azure Marketplace a webu Azure Portal. Že se zobrazí na stránkách Procházet a na vaší stránce s podrobnostmi o produktu.          |

## <a name="marketing-artifacts"></a>Artefakty marketing

Marketingové artefakty formulář obsahuje povinné a nepovinné pole zobrazená v další snímek obrazovky. Povinná pole jsou uvést hvězdičkou (*).

![Marketing artefakty formuláře](./media/azureapp-marketplace-artifacts.png)

Následující tabulka popisuje marketingové artefakty.

|    **Pole**       |  **Popis**  |
|  ---------------   |  ---------------  |
|       Krátkodobé používání             |        40 x 40 pixelů ve formátu PNG           |
|       Střednědobé používání             |        90 x 90 pixelech ve formátu PNG           |
|       Dlouhodobé používání             |        115 x 115 pixelů ve formátu PNG         |
|       Široký             |        255 x 115 pixelů ve formátu PNG           |
|       Hero             |        815 x 290 pixelů ve formátu PNG. Volitelné. **Poznámka:** Ikona hero nelze odstranit po nahrání.           |
|       Snímky obrazovky (max. 5)             |        Snímky obrazovky se zobrazí na vaší stránce s podrobnostmi o produktu. Jsou to dobrý způsob, jak vizuálně komunikovat, co aplikace dělá a jak to funguje. Můžete například zobrazit diagramy architektury nebo použít případu obrázky. Snímky obrazovky jsou volitelné a omezeno na 5 za skladové položky. Chcete-li přidat snímek obrazovky:<ul><li>Vyberte **+ přidat snímek obrazovky** otevřete okno snímek obrazovky</li><li>**Název** – zadejte název nebo název (maximální délku 100 znaků.)</li><li>**Nahrát** – nahrání obrázku. Musí být ve formátu PNG a velikost je 533 x 324 pixelů.</li></ul>           |
|        Přidat video            |        Videa se zobrazí na vaší stránce s podrobnostmi o produktu. Jsou to dobrý způsob, jak vizuálně komunikovat, co aplikace dělá a jak to funguje. Chcete-li přidat video: <ul><li>Vyberte **+ přidat video** otevřete okno Video</li><li>**Název** – zadejte název nebo název (maximální délku 100 znaků.)</li><li>**Odkaz** – zadejte adresu URL pro web, který je hostitelem videa (YouTube nebo Vimeo)</li><li>**Miniatura** – nahrání miniatury. Musí být ve formátu PNG a velikost je 533 x 324 pixelů.</li></ul>          |

### <a name="artifact-examples-in-azure-marketplace"></a>Příklady artefaktů na webu Azure Marketplace

Následující snímek obrazovky ukazuje příklad výsledků na vyhledávacím webu Marketplace.

![Výsledek hledání nabídky Marketplace ](./media/azureapp-marketplace-example-browse.png)

Následující obrázek ukazuje, jak se nabídky zobrazí na webu Marketplace po zákazník klikne na dlaždici nabídky ve výsledcích hledání.

![Podrobnosti výsledku hledání nabídky Marketplace](./media/azureapp-marketplace-example-details.png)

### <a name="artifact-examples-in-azure-portal"></a>Příklady artefaktů na webu Azure Portal

Na následující obrazovce zaznamená zobrazit zobrazení nabídky na webu Azure Portal. Nabídky aplikace v tomto příkladu je najít procházením **Marketplace > vše > vývoj + testování > Jenkins**. Nabídka Jenkins zobrazuje logo, název a zobrazovaný název vydavatele.

![Procházet nabízí na webu Azure portal](./media/azureapp-portalbrowse-artifacts-jenkins.png)

Následující snímek obrazovky ukazuje podrobné informace o aplikaci, když uživatel vybere Jenkinse.

![Podrobnosti nabídky na webu Azure portal](./media/azureapp-portal-artifacts-jenkins-details.png)

### <a name="logo-guidelines"></a>Pokyny pro použití loga

Všechny loga nahráli na portál Cloud Partner postupujte podle pokynů:

- Design Azure má jednoduchou paletu barev. Zachovat počet primární a sekundární barvy na vaše logo nízké.
- Jsou bílé barvy motivu na webu Azure Portal a černé. Vyhněte se použití tyto barvy jako barva pozadí pro vašeho loga. Použijte barvu, která způsobí, že vaše loga viditelného na webu Azure Portal. Doporučujeme použít jednoduché primární barvy. Pokud používáte průhledným pozadím, ujistěte se, že nejsou loga/text bílé, černá a modrá.
- Nepoužívejte barevného přechodu pozadí na vaše logo.
- Předejde text, dokonce i vaše společnost nebo název značky, loga. Vzhled a chování vašeho loga musí být "ploché" a přechody se měli vyhnout.
- Logo není stretch.

#### <a name="hero-logo"></a>Hero logo

Hero logo je volitelné.

>[!IMPORTANT]
>Hero logo nelze odstranit po nahrání.

Pomocí následujících pokynů pro Hero logo:

- Nejsou povoleny černou, bílou a průhledné pozadí.
- Vyhněte se použití jakékoli světlé barevné jako pozadí loga. Vydavatel zobrazovaný název, plán nadpis a nabídky, dlouhé shrnutí se zobrazují Barva písma bílé a musí být pozadí.
- Vyhněte se použití Většina textu při navrhování loga. Název vydavatele, název plánu, nabídky, dlouhé shrnutí a tlačítko Vytvořit jsou vložené prostřednictvím kódu programu uvnitř logo při jejím uvedené.
- Patří nevyužité obdélníkové místo na pravé straně hero logo. Toto prázdné místo je 415 × 100 pixelů a posun od levé 370 pixelů.

## <a name="lead-management"></a>Průběžná správa

Formulář vést Management má volitelné pole můžete nakonfigurovat správu potenciálních zákazníků. Pokud chcete nakonfigurovat Správa potenciálních zákazníků, vyberte cíl potenciálních zákazníků z rozevíracího seznamu. Následující snímek obrazovky ukazuje dostupné cíle.

![Cíl správy vyberte zájemce](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Výběrem ikony informace zobrazí tato zpráva: "Vyberte systému, kam se budou ukládat vaše potenciálních zákazníků. Zjistěte, jak se připojit k systému CRM [tady](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) . "


Další informace najdete v tématu [konfigurace potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads).

## <a name="legal"></a>Právní informace

Právní formulář slouží k poskytování právní dokumentace, vyžaduje se pro každou nabídku.

Zadejte tyto informace:

- **Adresa URL zásad ochrany osobních údajů** – zadejte odkaz na zásady ochrany osobních údajů vaší aplikace.
- **Podmínky použití** – zadejte podmínky použití pro vaši aplikaci. Zákazníkům je potřeba přijmout tyto podmínky předtím, než se pokusí vaší aplikace.

![Platný tvar](./media/azureapp-marketplace-legal.png)

## <a name="next-steps"></a>Další postup

[Karta Podpora](./cpp-support-tab.md)