---
title: Virtual machine Marketplace záložce portál partnerů cloudu pro Azure | Dokumentace Microsoftu
description: Popisuje kartě Marketplace použité při vytváření virtuálního počítače Azure Marketplace nabídku.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: aa43a6f1f7c757366e321e7da0fb1e47d4928cee
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639621"
---
# <a name="virtual-machine-marketplace-tab"></a>Karta Tržiště virtuálních počítačů

**Marketplace** karty **nová nabídka** stránky umožňuje, aby pomáhala potenciálním zákazníkům poskytnout marketing, prodej a právní informace a smluv a správu generují z potenciálních zákazníků Marketplace. Tato dlouhý tvar je rozdělen do čtyř oddílů: **přehled**, **marketingové artefakty**, **vést správu**, a **právní**. 

## <a name="overview-section"></a>Přehled oddílu
V této části zadáte obecné informace o nabídky Azure Marketplace.  Připojený hvězdička (*) na název pole označuje, že je povinný.

![Přehled části Marketplace karty ve formuláři nová nabídka pro virtuální počítače](./media/publishvm_008.png)

Následující tabulka popisuje účel a obsah těchto polí.

|  **Pole**                |     **Popis**                                                          |
|  ---------                |     ---------------                                                          |
| **Název**                 | Název nabídky, často dlouhý, oficiální název. Tento název se zobrazí výrazném místě na webu Marketplace.  Maximální délka 50 znaků. |
| **Souhrn**               | Stručný účel nebo funkce řešení.  Delší než 100 znaků. |
| **Dlouhé shrnutí**          | Účel nebo funkce řešení.  Delší než 256 znaků. |
| **Popis**           | Popis řešení.  3000 znaků podporuje jednoduchý formátování HTML. |
| **Identifikátor marketing**  | Jedinečnou adresu URL pro přidružení k této nabídce obvykle zahrnuje vaší organizace a název řešení, maximální délka 50 znaků.  Příklad: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **ID předplatných ve verzi Preview** | Přidejte jeden až 100 identifikátorů předplatného náhledy. Tato předplatná uvedená prázdné budou mít přístup do nabídky publikovaný produkt, než přejde za provozu. |
| **Užitečné odkazy**          | Vícenásobného výběru obchodní a technické kategorií, které nabízejí lze nejlépe přidružit.  Maximální povolené pět.  |
|  |  |


## <a name="marketing-artifacts-section"></a>Část artefakty marketing

Tento druhý oddíl je rozdělen na tři pododdíly: **loga**, **snímek obrazovky**, a **videa**. Loga jsou pouze požadované marketingové artefakty, ale všechny důrazně doporučujeme pro nejlepší odvolání zákazníka.

![Marketingové artefakty části Marketplace karty ve formuláři nová nabídka pro virtuální počítače](./media/publishvm_009.png)

|  **Pole**                |     **Popis**                                                          |
|  ---------                |     ---------------                                                          |
| *Loga*  |  |
| **Malé**                 | rastrový obrázek 40 x 40 pixel .ico                                                      |
| **Střední**                | rastrový obrázek .ico 90 x 90 pixelů                                                      |
| **Velké**                 | rastrový obrázek .ico 115 x 115 pixelů                                                   |
| **Široký**                  | rastrový obrázek .ico 255 x 115 pixelů                                                    |
| **Hero**                  | 815 x 290 rastrový obrázek.  Volitelné, ale jednou nahrané ikonu hero nelze odstranit. |
| *Snímky obrazovky*  | Volitelné, ale maximálně pěti snímků obrazovky na SKU. |
| **Název**                  | Název nebo název <!-- TODO - max char length? none specified in UI -->                               |
| **Image**                 | Snímek obrazovky, 533 x 324 pixelů                                         |
| *Videa*  |  |
| **Název**                  | Název nebo název  <!-- TODO - max char length? -->                              |
| **Odkaz**                  | Adresa URL videa, hostované na YouTube nebo Vimeo                                        |
| **Miniatura**             | 533 x 324 rastrový obrázek                                                               |
|  |  |


### <a name="logo-guidelines"></a>Pokyny pro použití loga

<!-- TD: It seems like this section could be better located in some common area, maybe a AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Všechny loga nahráli na portál Cloud Partner postupujte podle pokynů:

*  Design Azure má jednoduchou paletu barev. Zachovat počet primární a sekundární barvy na vaše logo nízké.
*  Jsou bílé barvy motivu na webu Azure portal a černé. Vyhněte se proto používání tyto barvy jako barva pozadí vašeho loga. Použijte některé barvu, která by provedla vašeho loga viditelného na webu Azure Portal. Doporučujeme použít jednoduché primární barvy. Pokud používáte průhledným pozadím, ujistěte se, že nejsou loga/text bílé nebo černé nebo modrou.
*  Nepoužívejte logo barevného přechodu pozadí.
*  Předejde text – dokonce i vaše společnost nebo název značky – na loga. Vzhled a chování vašeho loga musí být "ploché" a přechody se měli vyhnout.
*  Není roztáhnout loga.

#### <a name="hero-logo"></a>Hero logo

Hero logo je volitelná. ale po nahrání ikony hero nelze odstranit.  Ikona loga Hero postupujte podle pokynů:

*  Černou, bílou a průhledné pozadí nejsou povoleny pro hero ikony.
*  Vyhněte se použití jakékoli světlé barva pozadí ikony hero.  Zobrazovaný název vydavatele, název plánu a nabídky, dlouhé shrnutí zobrazené v Barva písma bílé a musí být pozadí.
*  Vyhněte se použití Většina textu při návrhu hero logo.  Název vydavatele, název plánu, nabídky, dlouhé shrnutí a tlačítko Vytvořit jsou vložené prostřednictvím kódu programu uvnitř ikonu hero při seznamy nabídky. 
* Zahrnout nevyužité obdélník na pravé straně vaše hero ikonu velikost 415 × 100 pixelů a posun 370 pixelů od levého okraje.  

Například následující ikona hero je pro službu Azure Container Service.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Příklad hero ikonu pro službu Azure Container Service](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Příklad informace Marketing 

Následující obrázek znázorňuje, jak marketingové informace se zobrazí na stránce hlavní produktová Microsoft Windows Server.

![Příklad stránky produktu pro systém Microsoft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Vedoucí bodu správy
<!-- this all should be referenced in a common location for lead management, not in this file. nothing unique for a vm specifically. -->

Třetí část umožňuje zákazníkům shromažďovat zájemce získané z nabídky na webu Azure Marketplace. Nabízí následující možnosti ukládání těchto informací potenciálních zákazníků (z rozevíracího seznamu).

* **Žádný** – výchozí informace o zájemcích nejsou shromažďována.
* Azure Table - zapisovat do tabulky Azure, který je zadán pomocí připojovacího řetězce.
* Dynamics CRM Online – zapsána do [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) instance určené parametrem adresu URL a ověřování pověření.
* Koncový bod HTTPS - zapsána do zadaného koncového bodu HTTPS jako datovou část JSON.
* Marketo - zapisovat na daný [Marketo](https://www.marketo.com/) instance určené ID serveru, munchkin ID a ID formuláře.
* Salesforce – zapsána do [Salesforce](https://www.salesforce.com/) databáze určené parametrem identifikátor objektu.

Po úspěšném publikování vaší nabídky, realizace připojení ověříte a zájemce test je automaticky odeslán do nakonfigurované cílové. Vedoucí informace by měly být spravovány průběžně a tato nastavení by být okamžitě aktualizovat pokaždé, když dojde ke změně vaší architektury správy zákazníka.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Právní oddílu

Tento poslední část umožňuje zadat dva právní dokumenty, vyžaduje se pro každou nabídku: zásady ochrany osobních údajů a podmínky použití.

|  **Pole**                |     **Popis**                                                          |
|  ---------                |     ---------------                                                          |
| **Adresa URL zásad ochrany osobních údajů**    | Adresa URL zásad odeslaných ochrany osobních údajů                                            |
| **Podmínky použití**          | zásady jako prostý text nebo jednoduchého kódu HTML.  <!-- TODO - max char length? -->       |
|  |  |

<br/>

V dalším [podporují](./cpp-support-tab.md) kartu, bude poskytovat uživatele a technické podpory prostředků pro vaši nabídku.

