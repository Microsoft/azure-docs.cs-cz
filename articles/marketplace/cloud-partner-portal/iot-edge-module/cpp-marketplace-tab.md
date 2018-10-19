---
title: Popis webu Marketplace pro modul služby Azure IoT Edge | Dokumentace Microsoftu
description: Vytvořte popis marketplace pro modul IoT Edge.
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
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: c119869c47547fd5d91f6ee91fcd772ea5941f46
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430777"
---
# <a name="iot-edge-module-marketplace-tab"></a>Kartu Marketplace modul IoT Edge


**Marketplace** karty **nová nabídka** stránky umožňuje, aby pomáhala potenciálním zákazníkům poskytnout marketing, prodej a právní informace a smluv a správu generují z potenciálních zákazníků Marketplace. Tato dlouhý tvar je rozdělen do čtyř oddílů: **přehled**, **marketingové artefakty**, **vést správu**, a **právní**.

## <a name="overview"></a>Přehled

V této části zadáte obecné informace o nabídky Azure Marketplace.  Hvězdičku (*) připojeným k názvu pole označuje, že je to požadováno.

![Přehled část Marketplace kartě na formulář Nová nabídka pro moduly IoT Edge](./media/iot-edge-module-marketplace-tab-overview.png)

Následující tabulka popisuje účel a obsah těchto polí.

|  **Pole**                |     **Popis**                                                          |
|  ---------                |     ---------------                                                          |
| **Název**                 | Název nabídky. Se bude zobrazovat výrazném místě na webu Marketplace.  Maximální délka 50 znaků. <!--ADD PICTURE IN ACTION-->|
| **Souhrn**               | Krátký popis nabídky Delší než 100 znaků. <!--ADD PICTURE IN ACTION-->|
| **Dlouhé shrnutí**          | Už souhrn nabídky (i když může být stejná jako **souhrnu**).  Delší než 256 znaků. <!--ADD PICTURE IN ACTION-->|
| **Popis**           | Popis nabídky.  3000 znaků podporuje jednoduchý formátování HTML.<br/> Musí obsahovat *minimální požadavky na hardware* odstavce v dolní části. Příklad: <br/> <p><u>Minimální požadavky na hardware:</u> Linux x64 a arm32 operační systém, 1 GB paměti RAM, 500 Mb úložiště</p>
| **Identifikátor marketing**  | Jedinečnou adresu URL pro přidružení k této nabídce obvykle zahrnuje vaší organizace a název řešení, maximální délka 50 znaků.  Příklad: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **ID předplatných ve verzi Preview** | Přidejte jeden až 100 identifikátorů předplatného náhledy. Tato předplatná uvedená prázdné budou mít přístup do nabídky publikovaný produkt, než přejde za provozu. |
| **Užitečné odkazy**          | Vícenásobného výběru obchodní a technické kategorií, které nabízejí lze nejlépe přidružit.  Maximálně 10 povolené. Ujistěte se, že chcete přidat alespoň jeden odkaz na dokumentaci a jedno propojení kompatibilních zařízení IoT Edge z [katalog zařízení Azure IoT](https://catalog.azureiotsolutions.com/). |
| **Navrhované kategorií** | Můžete si vyberte až o pěti kategorií. Že se zobrazí na vaší stránce s podrobnostmi o produktu. Na stránkách procházet všechny moduly IoT Edge se zobrazí pod *Internet of Things \> modul IoT Edge* kategorie.|

### <a name="offer-example"></a>Příklad nabídky

 Následující příklady ukazují jak nabídky **Title**, **Souhrn**, **popis**, **loga**, a **snímky obrazovky** pole se zobrazí v zobrazení.
 
#### <a name="on-the-azure-marketplace-website"></a>Na webu Azure Marketplace:

- Při procházení nabídky:

    ![Jak se zobrazují nabídky na webu marketplace na webu Azure Marketplace – procházení](./media/iot-edge-module-ampdotcom-card.png)

- Při prohlížení podrobnosti nabídky:

    ![Jak modul IoT Edge se zobrazí při hledání podrobnosti o produktu na webu](./media/iot-edge-module-ampdotcom-pdp.png)

#### <a name="on-the-azure-portal-website"></a>Na webu Azure portal:

- Při procházení nabídky:

    ![Jak modul IoT Edge se zobrazí při procházení webu Azure portal #1](./media/iot-edge-module-portal-browse.png)

    ![Jak modul IoT Edge se zobrazí při procházení webu Azure portal #2](./media/iot-edge-module-portal-product-picker.png)

- Při hledání v rámci nabídky:

    ![Jak modul IoT Edge se zobrazí při vyhledávání na webu Azure portal](./media/iot-edge-module-portal-search.png)

- Při prohlížení podrobnosti nabídky:

    ![Jak modul IoT Edge se zobrazí při hledání podrobnosti o produktu na portálu](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>Artefakty marketing

Tato část obsahuje následující pododdíly: **loga**, **snímek obrazovky**, a **videa**. 

>[!Note]
>Loga jsou pouze požadované marketingové artefakty, ale všechny důrazně doporučujeme pro nejlepší odvolání zákazníka.

![Marketingové artefakty části Marketplace karty ve formuláři nová nabídka pro virtuální počítače](./media/publishvm_009.png)

|  **Pole**                |     **Popis**                                                          |
|  ---------                |     ---------------                                                          |
| *Loga*  | Podívejte se, že pokud chcete zobrazit, jak a kde se použije vašeho loga zachycuje na předchozí obrazovku.  |
| **Malé**                 | 40 x 40 Pixelový formát PNG                                                     |
| **Střední**                | Formát PNG 90 x 90 pixelů                                                     |
| **Velké**                 | Formát PNG 115 x 115 pixelů                                                  |
| **Široký**                  | Formát PNG 255 x 115 pixelů                                                   |
| **Hero**                  | Formát PNG 815 x 290 pixelů.  Volitelné, ale jednou nahrané ikonu hero nelze odstranit. |
| *Snímky obrazovky*  | Snímky obrazovky se zobrazí na vaší stránce s podrobnostmi o produktu. Jsou vhodné k vizuálnímu zobrazení činnosti modulu IoT Edge a jak to funguje. Můžete zobrazit diagramy architektury nebo použít případu obrázky pro instanci. Volitelné, ale maximálně pěti snímků obrazovky na SKU. |
| **Název**                  | Název nebo název. Delší než 100 znaků.                             |
| **Image**                 | Snímek obrazovky, 533 x 324 Pixelový formát PNG                                         |
| *Videa*  | Videa se zobrazí na vaší stránce s podrobnostmi o produktu. Jsou vhodné k vizuálnímu zobrazení činnosti modulu IoT Edge a jak to funguje. |
| **Název**                  | Název nebo název. Delší než 100 znaků.                              |
| **Odkaz**                  | Adresa URL videa, hostované na YouTube nebo Vimeo                                        |
| **Miniatura**             | Formát PNG 533 x 324 pixelů                                                           |


### <a name="logo-guidelines"></a>Pokyny pro použití loga

<!-- It seems like this section could be better located in some common area, maybe a AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Všechny loga nahráli na portál Cloud Partner postupujte podle pokynů:

*  Design Azure má jednoduchou paletu barev. Zachovat počet primární a sekundární barvy na vaše logo nízké.
*  Jsou bílé barvy motivu na webu Azure Portal a černé. Vyhněte se použití tyto barvy jako barva pozadí pro vašeho loga. Použijte barvu, která způsobí, že vaše loga viditelného na webu Azure Portal. Doporučujeme použít jednoduché primární barvy. Pokud používáte průhledným pozadím, ujistěte se, že nejsou loga/text bílé, černá a modrá.
*  Nepoužívejte barevného přechodu pozadí na vaše logo.
*  Předejde text – dokonce i vaše společnost nebo název značky – na loga. Vzhled a chování vašeho loga musí být "ploché" a přechody se měli vyhnout.
*  Logo není stretch.

#### <a name="hero-logo"></a>Hero logo

Hero logo je volitelné.

>[!Important]
>Po nahrání loga Hero nelze odstranit.

Pomocí následujících pokynů pro Hero logo: 

*  Nejsou povoleny černou, bílou a průhledné pozadí.
*  Vyhněte se použití jakékoli světlé barevné jako pozadí loga.  Vydavatel zobrazovaný název, plán nadpis a nabídky, dlouhé shrnutí se zobrazují Barva písma bílé a musí být pozadí.
*  Vyhněte se použití Většina textu při navrhování loga. Název vydavatele, název plánu, nabídky, dlouhé shrnutí a tlačítko Vytvořit jsou vložené prostřednictvím kódu programu uvnitř logo při jejím uvedené. 
* Patří nevyužité obdélníkové místo na pravé straně hero logo. Toto prázdné místo je 415 × 100 pixelů a posun od levé 370 pixelů.  

<!-- P2: woudl be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>Průběžná správa

Tato část umožňuje nastavení možnosti k shromažďování údajů o zákazníkovi vede generované z nabídky na webu Azure Marketplace. Z rozevíracího seznamu můžete vybrat následující možnosti úložiště.

* **Žádný** – výchozí informace o zájemcích nejsou shromažďována.
* Azure Table - zapisovat do tabulky Azure, který je zadán pomocí připojovacího řetězce.
* Dynamics CRM Online – zapsána do [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) instance určené parametrem adresu URL a ověřování pověření.
* Koncový bod HTTPS - zapsána do zadaného koncového bodu HTTPS jako datovou část JSON.
* Marketo - zapisovat na daný [Marketo](https://www.marketo.com/) instance určené ID serveru, munchkin ID a ID formuláře.
* Salesforce – zapsána do [Salesforce](https://www.salesforce.com/) databáze určené parametrem identifikátor objektu.

Po úspěšném publikování vaší nabídky, ověření připojení potenciálních zákazníků a vedoucí test je automaticky odeslán do cíle, které jste nakonfigurovali. 

>[!Note]
>Vedoucí informace by měly být spravovány průběžně a tato nastavení by být okamžitě aktualizovat pokaždé, když dojde ke změně vaší architektury správy zákazníka.

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->

## <a name="legal"></a>Právní informace

Tato část umožňuje poskytnout dvě právní dokumenty, které jsou požadovány pro každou nabídku: zásady ochrany osobních údajů a podmínky použití.

|  **Pole**                |     **Popis**                                                          |
|  ---------                |     ---------------                                                          |
| **Adresa URL zásad ochrany osobních údajů**    | Adresa URL zásad odeslaných ochrany osobních údajů                                            |
| **Podmínky použití**          | Podmínky použití jako vložený jednoduchý HTML nebo odkaz na svoje odeslaných podmínky použití       |

<br/>

## <a name="next-steps"></a>Další postup

Použití [podporují](./cpp-support-tab.md) kartu a zadat uživatele a technické podpory prostředků pro vaši nabídku.
