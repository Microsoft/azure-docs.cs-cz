---
title: Karta virtuálního počítače Marketplace v portál partnerů cloudu pro Azure Marketplace
description: Popisuje kartu Marketplace, která se používá při vytváření nabídky virtuálních počítačů s Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 9bc3c375c5111c144b6b137ca547875225aba58f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824462"
---
# <a name="virtual-machine-marketplace-tab"></a>Karta tržiště virtuálního počítače

Karta **tržiště** na **nové stránce nabídky** vám umožní poskytnout potenciálním zákazníkům marketingové, prodejní a právní informace a smlouvy a spravovat zájemce vygenerované z webu Marketplace. Tato dlouhá forma je rozdělená na čtyři části: **Přehled**, **marketingové artefakty**, **řízení vedoucích**a **právní**.


## <a name="overview-section"></a>Oddíl přehledu
V této části zadáte obecné informace o vaší nabídce Azure Marketplace.  Připojená hvězdička (*) v názvu pole označuje, že je požadovaná.

![Oddíl s přehledem na kartě Marketplace pro virtuální počítače](./media/publishvm_008.png)

Následující tabulka popisuje účel a obsah těchto polí. Požadovaná pole jsou indicted hvězdičkou (*).

|  **Pole**                |     **Popis**                                                          |
|  ---------                |     ---------------                                                          |
| **\* názvu**                 | Název nabídky, často dlouhého a formálního názvu. Tento název se bude zobrazovat na webu Marketplace.  Maximální délka 50 znaků. |
| **Souhrn\***               | Stručný účel nebo funkce řešení.  Maximální délka 100 znaků. |
| **Dlouhý souhrn\***          | Účel nebo funkce řešení.  Maximální délka 256 znaků. |
| **Popis\***           | Popis řešení  Maximální délka 3000 znaků podporuje jednoduché formátování HTML. |
| **\* kanálu prodejců Microsoft CSP** | Výslovný souhlas kanálu pro partnery Cloud Solution Providers (CSP) je teď k dispozici.  Další informace o marketingu vaší nabídky prostřednictvím kanálů partnerů Microsoft CSP najdete v tématu [Poskytovatelé cloudových řešení](../../cloud-solution-providers.md) . |
| **\* identifikátoru marketingu**  | Jedinečná adresa URL, která se má přidružit k této nabídce, obvykle zahrnuje vaši organizaci a název řešení, maximální délka 50 znaků.  Příklad: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Náhled ID předplatných\*** | Přidejte jednu až 100 identifikátorů předplatného pro náhledy. Po publikování těchto bílých předplatných budou mít přístup k této nabídce k dispozici, než budou živá. |
| **Užitečné odkazy**          | Přidejte adresy URL do dokumentů, poznámky k verzi, nejčastější dotazy a tak dále. |
| **Navrhované kategorie (max. 5)\*** | Vícenásobné výběry obchodních a technických kategorií, které nabízejí možnost být nejlépe přidruženy k.  Maximálně pět povolených.  |
|  |  |


## <a name="marketing-artifacts-section"></a>Oddíl marketingových artefaktů

Tato druhá část je rozdělená ve třech pododdílech: **loga**, snímky **obrazovky**a **videa**. Jsou to jedinými požadovanými artefakty marketingu, ale u nich se důrazně doporučuje nejlepšího odvolání od zákazníka. 

![Část marketingové artefakty na kartě Marketplace na novém formuláři nabídky pro virtuální počítače](./media/publishvm_009.png)

Následující tabulka popisuje účel a obsah těchto polí. Požadovaná pole jsou indicted hvězdičkou (*).

|  **Pole**                |     **Popis**                                                          |
|  ---------                |     ---------------                                                          |
| *Značk*  |  |
| **Malá\***                 | 40x40 pixelů. ico – rastrový obrázek                                                      |
| **Střední\***                | 90x90 pixelů. ico – rastrový obrázek                                                      |
| **Velké\***                 | 115x115 pixelů. ico – rastrový obrázek                                                   |
| **Roztažitelné\***                  | 255x115 pixelů. ico – rastrový obrázek                                                    |
| **Hero**                  | 815x290 rastrový obrázek.  Volitelné, ale po nahrání ikony Hero nejde odstranit. |
| *Snímky obrazovky*  | Volitelné, ale maximálně pět snímků obrazovky na SKU. |
| **Název**                  | Název nebo název <!-- TODO - max char length? none specified in UI -->                               |
| **Image**                 | Obrázek snímku obrazovky, 533x324 pixel                                         |
| *Videa*  |  |
| **Název**                  | Název nebo název  <!-- TODO - max char length? -->                              |
| **Odkaz**                  | Adresa URL videa hostovaná na YouTube nebo Vimeo                                        |
| **Miniatuře**             | 533x324 rastrový obrázek                                                               |
|   |   |

### <a name="logo-guidelines"></a>Pokyny pro použití loga

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Všechna loga odeslaná na portál partnerů cloudu by měla postupovat podle pokynů:

*  Design Azure má jednoduchou paletu barev. Ponechte počet primárních a sekundárních barev v logu nízký.
*  Barvy motivu Azure Portal jsou bílé a černé. Proto vyhněte používání těchto barev jako barvy pozadí vašich log. Použijte některé barvy, které by měly být v Azure Portal zvýrazněné jako vaše loga. Doporučujeme použít jednoduché primární barvy. Pokud používáte transparentní pozadí, zajistěte, aby se loga/text nedala bílá ani černá nebo modrá.
*  Nepoužívejte na svém logu pozadí přechodu.
*  Vyhněte se umístění textu, dokonce i názvu vaší společnosti nebo značky – na logo. Vzhled a chování loga by mělo být "ploché" a mělo by se vyhnout přechodu.
*  Nepřizpůsobujte logo.

#### <a name="hero-logo"></a>Hero logo

Logo Hero je volitelné; Po nahrání se ale ikona Hero nedá odstranit.  Ikona loga Hero by měla postupovat podle pokynů:

*  Pro ikony Hero nejsou povoleny černé, bílé a průhledné pozadí.
*  Vyhněte se použití jakékoli světlé barvy jako pozadí ikony Hero.  Zobrazované jméno vydavatele, název plánu a celková doba nabídky se zobrazí v barvě bílého písma a musí se vystavit na pozadí.
*  Nepoužívejte většinu textu při navrhování Hero loga.  Název vydavatele, název plánu, dlouhý souhrn nabídky a tlačítko vytvořit jsou vloženy programově uvnitř ikony Hero, když jsou seznamy nabídek. 
* Zahrňte nepoužitý obdélník na pravé straně ikony Hero, velikost 415x100 pixelů a posun 370 px vlevo.  

Například následující ikona Hero je pro Azure Container Service.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Příklad ikony Hero pro Azure Container Service](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Příklad marketingových informací 

Následující obrázek ukazuje, jak se zobrazují informace o marketingu na hlavní stránce produktu Microsoft Windows Server.

![Ukázková stránka produktu pro Microsoft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Část Správa potenciálních zákazníků

Třetí část vám umožní shromáždit zájemce vygenerované z vašich nabídek Azure Marketplace. Nabízí následující možnosti úložiště (z rozevíracího seznamu) pro tyto informace o zájemci.

* **Žádné** – výchozí informace o zájemci nejsou shromažďovány.
* Tabulka Azure – zapisuje se do tabulky Azure určené připojovacím řetězcem.
* Dynamics CRM Online – zapisuje se do [online instance Microsoft Dynamics 365](https://dynamics.microsoft.com/) určené adresou URL a přihlašovacími údaji ověřování.
* Koncový bod HTTPS – zapisuje do zadaného koncového bodu HTTPS jako datovou část JSON.
* Marketo – zapsané do zadané instance [Marketo](https://www.marketo.com/) , určené ID serveru, ID Munchkin a ID formuláře.
* Salesforce – zapsáno do databáze [Salesforce](https://www.salesforce.com/) určené identifikátorem objektu.

Po úspěšném publikování nabídky se připojení zájemce ověří a vedoucí testu se automaticky pošle do konfigurovaného cíle. Informace o potenciálních zákaznících by se měly průběžně spravovat a tato nastavení by se měla zobrazit výzva k aktualizaci vždy, když provedete změny v architektuře správy zákazníků

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Právní část

V tomto posledním oddílu můžete zadat požadované právní dokumenty vyžadované pro jednotlivé nabídky.  

|  **Pole**                    |     **Popis**                                        |
|  ---------                    |     ---------------                                        |
| **Adresa URL zásad ochrany osobních údajů\***      | Adresa URL vašich publikovaných zásad ochrany osobních údajů                          |
| **Používáte standardní smlouvu?\***  |   |
| **Podmínky použití\***            | zásady jako prostý text nebo jednoduché HTML.                       |
|  |  |


## <a name="next-steps"></a>Další kroky

Na další kartě [podpory](./cpp-support-tab.md) budete poskytovat zdroje informací o technické a uživatelské podpoře pro vaši nabídku.
