---
title: Karta Marketplace virtuálních strojů na portálu cloudových partnerů pro Azure Marketplace
description: Popisuje kartu Marketplace používanou při vytváření nabídky virtuálního počítače Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 156a405d93aeac27f27fe617e1a5485044309846
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273813"
---
# <a name="virtual-machine-marketplace-tab"></a>Karta Marketplace virtuálního počítače

> [!IMPORTANT]
> dubna 2020 začneme přesouvat správu nabídek virtuálního počítače Azure do Centra partnerů. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Postupujte podle pokynů v [části Vytvoření nabídky virtuálního počítače Azure](https://aka.ms/CreateAzureVMoffer) ke správě migrovaných nabídek.

Karta **Marketplace** na stránce **Nová nabídka** umožňuje poskytovat potenciálním zákazníkům marketingové, prodejní a právní informace a smlouvy a spravovat potenciální zákazníky generované z tržiště. Tato dlouhá forma je rozdělena do čtyř částí: **Přehled**, **Marketingové artefakty**, **Správa zájemců**a **Právní**.


## <a name="overview-section"></a>Oddíl přehledu
V této části zadáte obecné informace o nabídce Azure Marketplace.  Připojená hvězdička (*) k názvu pole označuje, že je požadována.

![Část Přehled karty Marketplace pro virtuální počítače](./media/publishvm_008.png)

Následující tabulka popisuje účel a obsah těchto polí. Povinná pole jsou obžalována hvězdičkou (*).

|  **Pole**                |     **Popis**                                                          |
|  ---------                |     ---------------                                                          |
| **Název\***                 | Název nabídky, často dlouhý, formální název. Tento titul se zobrazí prominentně na trhu.  Maximální délka 50 znaků. |
| **Souhrn\***               | Stručný účel nebo funkce řešení.  Maximální délka 100 znaků. |
| **Dlouhý souhrn\***          | Účel nebo funkce řešení.  Maximální délka 256 znaků. |
| **Popis\***           | Popis roztoku.  Maximální délka 3000 znaků, podporuje jednoduché formátování HTML. |
| **Kanál prodejce CSP společnosti Microsoft\*** | Přihlášení partnerského kanálu zprostředkovatelů cloudových řešení (CSP) je nyní k dispozici.  Další informace o marketingu vaší nabídky prostřednictvím partnerských kanálů Microsoft CSP najdete v tématu [Poskytovatelé cloudových řešení.](../../cloud-solution-providers.md) |
| **Identifikátor marketingu\***  | Jedinečná adresa URL, kterou je třeba přidružit k této nabídce, obvykle zahrnuje název organizace a řešení, maximální délka 50 znaků.  Příklad: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Náhled ID předplatného\*** | Přidejte jeden až 100 identifikátorů předplatného pro předběžné recenzenty. Tato předplatná uvedená na seznamu povolených budou mít přístup k nabídce, jakmile bude zveřejněna, než začne být uvedena v provozu. |
| **Užitečné odkazy**          | Přidejte adresy URL do dokumentace, poznámek k verzi, nejčastějších dotazů a tak dále. |
| **Navrhované kategorie\*** | Vyberte až dvě (2) kategorie, včetně primární a sekundární kategorie (volitelné). Vyberte až dvě (2) podkategorie pro každou primární nebo sekundární kategorii. Pokud není vybrána žádná podkategorie, nabídka bude stále zjistitelná pouze pro vybranou kategorii. |
|  |  |


## <a name="marketing-artifacts-section"></a>Sekce Marketingové artefakty

Tato druhá část je rozdělena do tří podsekcí: **Loga**, **Snímek obrazovky**a **Videa**. Loga jsou pouze požadované marketingové artefakty, nicméně všechny jsou vysoce doporučeno pro nejlepší odvolání zákazníka. 

![Část Marketingové artefakty na kartě Marketplace ve formuláři Nová nabídka pro virtuální počítače](./media/publishvm_009.png)

Následující tabulka popisuje účel a obsah těchto polí. Povinná pole jsou obžalována hvězdičkou (*).

|  **Pole**                |     **Popis**                                                          |
|  ---------                |     ---------------                                                          |
| *Loga*  |  |
| **Krátkodobé používání\***                 | Bitmapa .ico o rozměrech 40 x 40 pixelů                                                      |
| **Medium\***                | Bitmapa .ico o rozměrech 90 x 90 pixelů                                                      |
| **Dlouhodobé používání\***                 | Bitmapa .ico 115 x 115 pixelů                                                   |
| **Široký\***                  | Bitmapa .ico o rozměrech 255 x 115 pixelů                                                    |
| **Hrdina**                  | 815x290 bitmapa.  Volitelné, ale po nahrání nelze ikonu hrdiny odstranit. |
| *Snímky obrazovek*  | Volitelné, ale maximálně pět snímků obrazovky na skladovou položku. |
| **Název**                  | Jméno nebo název <!-- TODO - max char length? none specified in UI -->                               |
| **Image**                 | Snímek obrazovky, 533x324 pixelů                                         |
| *Videa*  |  |
| **Název**                  | Jméno nebo název  <!-- TODO - max char length? -->                              |
| **Odkaz**                  | Adresa URL videa hostovaná na YouTube nebo Vimeo                                        |
| **Miniaturu**             | Bitmapa 533x324                                                               |
|   |   |

### <a name="logo-guidelines"></a>Pokyny pro použití loga

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Všechna loga nahraná na portál partnerů cloudu by se měla řídit pokyny:

*  Design Azure má jednoduchou paletu barev. Udržujte nízký počet primárních a sekundárních barev na logu.
*  Barvy motivu portálu Azure jsou bílé a černé. Proto se vyhněte použití těchto barev jako barvu pozadí loga. Použijte nějakou barvu, která by vaše loga prominentní na webu Azure portal. Doporučujeme použít jednoduché primární barvy. Pokud používáte průhledné pozadí, ujistěte se, že loga/text nejsou bílé nebo černé nebo modré.
*  Nepoužívejte pozadí přechodu na logo.
*  Neumisťujte na logo text – dokonce i vaši společnost nebo značku. Vzhled a dojem z vašeho loga by měl být "plochý" a měl by se vyhnout přechodům.
*  Neprotahování loga.

#### <a name="hero-logo"></a>Hero logo

Logo Hero je volitelné; po nahrání však nelze ikonu hrdiny odstranit.  Ikona loga Hero by se měla řídit pokyny:

*  Černé, bílé a průhledné pozadí nejsou povoleny pro ikony hrdiny.
*  Nepoužívejte jako pozadí ikony hrdiny jakoukoli světlou barvu.  Zobrazovaný název aplikace Publisher, název plánu a dlouhý souhrn nabídky jsou zobrazeny v bílé barvě písma a musí vyniknout na pozadí.
*  Při navrhování loga hrdiny nepoužívejte většinu textu.  Název vydavatele, název plánu, dlouhé shrnutí nabídky a tlačítko vytvořit jsou při seznamu nabídek programově vloženy do ikony hrdiny. 
* Nepoužívaný obdélník zahrňte na pravé straně ikony hrdiny o velikosti 415 x 100 pixelů a odsazení o 370 pixelů zleva.  

Jako příklad je následující ikona hrdiny pro službu Azure Container Service.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Příklad ikony hrdiny služby Azure Container Service](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Příklad marketingových informací 

Následující obrázek ukazuje, jak jsou marketingové informace zobrazeny na stránce hlavního produktu systému Microsoft Windows Server.

![Příklad stránky produktu pro systém Microsoft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Oddíl Správa potenciálních zákazníků

Třetí část umožňuje shromažďovat zákazníky, kteří jsou potenciální zákazníci vygenerovaní z vašich nabídek na Azure Marketplace. Nabízí následující možnosti úložiště (z rozevíracího seznamu) pro tyto informace o zájemcích.

* **Žádný** - výchozí informace o zájemcích nejsou shromažďovány.
* Azure Table – zapsáno do tabulky Azure určené připojovacím řetězcem.
* Dynamics CRM Online – zapsáno do instance [Aplikace Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) určené adresou URL a pověřením pro ověřování.
* HTTPS Koncový bod - zapsán do zadaného koncového bodu HTTPS jako datová část JSON.
* Marketo - zapsáno do zadané instance [Marketo,](https://www.marketo.com/) určené ID serveru, id munchkin a ID formuláře.
* Salesforce - zapsándo databáze [Salesforce](https://www.salesforce.com/) určené identifikátorem objektu.

Po úspěšném publikování nabídky je připojení zájemce ověřeno a testovací zájemce je automaticky odeslán do nakonfigurovaného cíle. Informace o potenciálních zákaznících by měly být průběžně spravovány a tato nastavení by měla být okamžitě aktualizována při každé změně architektury správy zákazníků.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Právní sekce

Tato poslední část umožňuje poskytnout požadované právní dokumenty potřebné pro každou nabídku.  

|  **Pole**                    |     **Popis**                                        |
|  ---------                    |     ---------------                                        |
| **Adresa URL zásad ochrany osobních údajů\***      | Adresa URL vašich zveřejněných zásad ochrany osobních údajů                          |
| **Použít standardní smlouvu?\***  |   |
| **Podmínky použití\***            | jako prostý text nebo jednoduchý KÓD HTML.                       |
|  |  |


## <a name="next-steps"></a>Další kroky

Na další kartě [Podpora](./cpp-support-tab.md) poskytnete technické a uživatelské zdroje podpory pro vaši nabídku.
