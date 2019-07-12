---
title: Nabízí kontrolní seznam k vytvoření – komerční Marketplace pro Azure
description: Informace, které zadáte v procesu vytváření nabídky. – Komerční Marketplace pro Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: eb824eb67e84ec4bdb93bc355ac6a6afa844ceb9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67701152"
---
# <a name="offer-creation-checklist"></a>Kontrolní seznam k vytvoření nabídky

Proces vytvoření nabídky se dostanete pomocí více stránek. Tady jsou podrobnosti, které poskytnete na každé stránce, s odkazy na další informace o jednotlivých položkách.

Položky, které je nutné zadat nebo zadejte uvedený níže. Některé oblasti jsou nepovinné nebo výchozí hodnoty zadali, že můžete změnit podle potřeby. Není nutné pro práci na těchto oddílů v určeném pořadí.

| **Položka**    | **Účel**  |
| :---------- | :-------------------|
| [**Nová nabídka modální okno**](#new-offer-modal) | Shromažďuje nabízí informace o identitě.  |
| [Stránka nastavení nabídky](#offer-setup-page) | Můžete vyjádřit výslovný souhlas s pomocí klíčových funkcích a zvolte, jak prodej vaší nabídky Microsoftu.  |
| [Stránky vlastností](#properties-page) | Definování kategorií a průmysl používá k seskupení vaší nabídky na Tržiště, právní smlouvy podporou vaší nabídky a verze vaší aplikace. |
| [Výpis stránku nabídek](#offer-listing-page) | Definujte podrobnosti nabídky, který se má zobrazit na webu Marketplace, včetně popisu vaší nabídky a marketingové prostředky. |
| [Stránka ve verzi Preview](#preview-page) | Definování omezený okruh uživatelů ve verzi Preview pro uvolnění vaši nabídku před publikováním nabídky za provozu pro širší cílovou skupinu webu marketplace. |
| [Technické konfigurační stránku nabídek](#technical-configuration-page)  | K dispozici pouze pokud jste vybrali k prodeji tuto nabídku prostřednictvím Microsoft. Definujte technické podrobnosti (cesta adresy URL webhooku, ID tenanta a ID aplikace) použít pro připojení k vaší nabídce. |
| [**Nový plán modální okno**](#plan-identity-modal) | Naplánujte shromažďuje informace o identitě.  |
| [Plánování stránce](#plan-listing-page)  | K dispozici pouze pokud jste vybrali k prodeji tuto nabídku prostřednictvím Microsoft. Zadejte podrobnosti používané k výpisu plán na webu Marketplace.  |
| [Ceny plánu a na stránce Dostupnost](#plan-pricing--availability-page)  | K dispozici pouze pokud jste vybrali k prodeji tuto nabídku prostřednictvím Microsoft.  Shromažďuje charakteristiky firmy (cenový model), cílovou skupinu a na trhu k dispozici pro každý plán (verze) o vaší nabídce.  |
| [Test jednotky stránce](#test-drive-listing-page)  | K dispozici pouze pokud vyberete nabízí testovací verze pro vaši nabídku. Zadejte podrobnosti používá seznam test drive na webu Marketplace.  |
| Stránka technické konfigurace testů jednotky  | K dispozici pouze pokud vyberete nabízí testovací verze pro vaši nabídku. Definujte technické podrobnosti pro demonstraci (nebo "testovací verze"), které umožní zákazníkům vyzkoušet vaši nabídku před potvrzením ji zakoupit.  |
| [Projděte si stránku a publikovat](#review-and-publish-page)  | Zvolte změny, které chcete publikovat, zobrazit stav jednotlivých stránek a zadejte poznámky, které tým certifikace.  |


## <a name="new-offer-modal"></a>Nová nabídka modální okno 

První řadu informací, které budete požádáni o zadání představují ID a alias pro vaši nabídku. 

| **Název pole**    | **Poznámky**   |  
| :---------------- | :-----------| 
| ID nabídky  | Povinné, po vytvoření nelze změnit. Max. 50 znaků a musí obsahovat jenom malá písmena, alfanumerické znaky, pomlčky nebo podtržítka. |
| Nabídka alias  | Povinný parametr. |

## <a name="offer-setup-page"></a>Stránka nastavení nabídky

Na stránce nastavení nabídky je kde je můžete začít používat různé kanály a prodejní pohyby, stejně jako deklaraci že vede použití klíčových funkcí, jako jsou testovací jednotky a zákazníků. 

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------|  
| Chcete prodávat prostřednictvím společnosti Microsoft?  | Povinný parametr. Výchozí hodnota: Ano |
| Jak chcete potenciálních zákazníků pro interakci s nabídkou výpis? (Výzva k akci)  | Vyžaduje, pokud není prodej prostřednictvím společnosti Microsoft. Výchozí hodnota: Bezplatnou zkušební verzi, možnosti: "Stáhnout", "Bezplatné zkušební verze", "Mě kontaktovat." |
| Adresa URL zkušební verze  | Vyžaduje, pokud je vybrána "Bezplatné zkušební verze", jak zákazníci způsob, jak by měl pracovat s výpis nabídky. |
| Adresa URL nabídky  | Vyžaduje-li "Získat ji nyní" je vybráno, jako způsob, jak zákazníci měli pracovat s výpis nabídky |
| Kanály  | Volitelné. Výchozí hodnota: Není zapojen do kanálu CSP (prodejce).  |
| Testovací verze | Volitelné. Výchozí hodnota: Není povoleno žádné testovací verze.  |
| Typ testu jednotky | Vyžaduje, pokud je povoleno si testovací jízdu. Výchozí hodnota: Nic nevybráno. Možnosti: Azure Resource Manageru, Dynamics 365 pro střední firmy, Dynamics 365 for Customer Engagement, Dynamics 365 pro operace, aplikace logiky, Power BI.  |
| Správa zájemců – připojení k systému CRM | Požadováno pokud prodej prostřednictvím společnosti Microsoft, nebo pokud výpis nabízí "Mě kontaktovat." Výchozí hodnota: žádný systému CRM připojen. Možnosti CRM: Tabulky Azure, objektů blob v Azure, Dynamics CRM online, koncový bod HTTPs', Marketo, Salesforce  |

## <a name="properties-page"></a>Stránka Vlastnosti

Na stránce vlastností je tady můžete definovat kategorie a průmysl používá k seskupení vaší nabídky na Tržiště, právní smlouvy podporou vaší nabídky a verze vaší aplikace. Nezapomeňte uvést úplných a přesných podrobnosti o vaší nabídky na této stránce tak, aby se správně zobrazeny a nabízí správnou sadu zákazníků. 

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------|  
| Kategorie a podkategorie | Vyžaduje 1 a maximální velikosti 3. Výchozí hodnota: Nic nevybráno. |
| Odvětví a subindustries | Volitelné. Max. 2 L1 odvětví a max. 2 subindustries v rámci každé L1 odvětví, výchozí: Nic není vybrané |
| verze aplikace  | Volitelné. Výchozí hodnota: Žádné. |
| Použít standardní kontraktu  | Volitelné. Výchozí hodnota: není vybraný.  | |
| Podmínky použití  | Povinné, pokud není vybrána standardní kontraktu.  |

## <a name="offer-listing-page"></a>Stránce nabídky

Stránce je, ve kterém můžete zadat text a obrázky, které zákazníkům zobrazovat při prohlížení seznamu vaší nabídky na webu Marketplace. 

| **Název pole**    | **Poznámky**   |
| :---------------- | :-----------| 
| Name  | Povinné, max. 50 znaků. |
| Souhrn  | Povinné, max. 100 znaků. | 
| Popis  | Povinné, max 3000 znaků. |
| Začínáme se službou pokyny  | Povinné, max 3000 znaků. |
| Začínáme se službou pokyny  | Povinné, max 3000 znaků. |
| Klíčová slova pro vyhledávání  | Volitelné, doporučujeme maximální 3 klíčová slova. |
| Adresa URL zásad ochrany osobních údajů  | Povinný parametr. |
| URL materiály Marketing programu CSP  | Volitelné. |
| Užitečné odkazy, název a adresu URL  | Volitelné. |
| Podklady titul + souboru  | Vyžaduje min 1 a max 3. Musí být ve formátu souboru PDF. |
| Snímky obrazovek  | Snímek obrazovky s min 1 a max 5; vyžadováno čtyři nebo více. Musí být ve formátu PNG 1280 × 720. |
| Logo pro Store (malá, střední, velké, celou, Hero)  | Malý (48 X 48) a velké (216 X 216) nevyžaduje. Další velikosti volitelný, ale doporučené: Střední (90 x 90) širokou (255 x 115) a Hero (815 x 290). Musí být ve formátu PNG. |
| Název videa + adresa URL miniatury  | Volitelný parametr, doporučeno maximálně 4 videa. Miniatura musí být 1280 × 720 ve formátu PNG. Video musí být hostovaný ve YouTube nebo Vimeo. |
| Kontakty (programu CSP, inženýrství, podpora)  | Konstrukce a kontakt podpory nevyžaduje (jméno, e-mailu a telefonní číslo). Kontakt programu CSP je sice volitelný, ale doporučený. |
| Adresa URL podpory  | Povinný parametr. |

## <a name="preview-page"></a>Stránka ve verzi Preview

Stránku s náhledem je, kde můžete určit cílovou skupinu přístup k vaší nabídky ve verzi preview, můžete ověřit, že nabídky splňuje všechny požadavky na dříve, než přejde za provozu. 

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------| 
| E-mailu AAD/MSA + popis | Povinné, min 1 a max 10 Pokud zadali ručně, nebo až na 20, pokud chcete nahrát soubor CSV. |

## <a name="technical-configuration-page"></a>Stránka technické konfigurace 

Na stránce technické konfigurace se zadávají technické podrobnosti, které používá společnost Microsoft pro připojení k vaší nabídce. Tato stránka není viditelné pro vás, pokud jste se rozhodli prodávat prostřednictvím společnosti Microsoft.

| **Název pole**    | **Poznámky**   |  
| :---------------- | :-----------| 
| Cílová adresa URL stránky | Vyžaduje, pokud prodej prostřednictvím společnosti Microsoft. |
| Připojení webhooku | Vyžaduje, pokud prodej prostřednictvím společnosti Microsoft. |
| ID tenanta Azure AD | Vyžaduje, pokud prodej prostřednictvím společnosti Microsoft. |
| ID aplikace Azure AD | Vyžaduje, pokud prodej prostřednictvím společnosti Microsoft. |

## <a name="plan-identity-modal"></a>Plán identity modální okno

První řadu informací, které budete vyzváni k zadání jsou název a ID pro váš plán. Tato stránka není viditelné pro vás, pokud jste se rozhodli prodávat prostřednictvím společnosti Microsoft.

| **Název pole**    | **Poznámky**   |  
| :---------------- | :-----------| 
| ID plánu  | Vyžaduje, pokud prodej prostřednictvím společnosti Microsoft. Po vytvoření nelze změnit. Max. 50 znaků a musí obsahovat jenom malá písmena, alfanumerické znaky, pomlčky nebo podtržítka. |
| Název plánu  | Vyžaduje, pokud prodej prostřednictvím společnosti Microsoft. Musí být jedinečný ve všech plánů v nabídce. Max. 50 znaků. |

## <a name="plan-listing-page"></a>Plánování stránce

V plánu zobrazení stránky je, ve kterém můžete zadat text pro zákazníky a zjistěte, kdy zobrazení plánu na webu Marketplace. Tato stránka není viditelné pro vás, pokud jste se rozhodli prodávat prostřednictvím společnosti Microsoft.

| **Název pole**    | **Poznámky**   |  
| :---------------- | :-----------| 
| Popis plánu   | Vyžaduje, pokud prodej prostřednictvím společnosti Microsoft. Max. 500 znaků. | |

## <a name="plan-pricing--availability-page"></a>Stránka cen a dostupnost plánu

Na stránce ceny a dostupnost plánu je tady můžete definovat obchodní charakteristiky, cílovou skupinu a dostupnost trhu pro každý plán (verze) o vaší nabídce. Tato stránka není viditelné pro vás, pokud jste se rozhodli prodávat prostřednictvím společnosti Microsoft.

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------| 
| Dostupnost na trhu  | Povinné, min 1 a max 141. |
| Cenový model  | Povinný parametr. Výchozí hodnota: Paušální sazba. Možnosti: Paušální sazba za uživatele. |
| Minimální a maximální licencovaných míst  | Volitelné, dostupné, pouze pokud založené na pracovní stanici ceny vybraného modelu. |
| Fakturační období  | Povinný parametr. Výchozí hodnota: Měsíční. Možnosti: Měsíční, roční. |
| Cena  | Požadované USD za měsíc, pokud měsíční fakturace termín zaškrtnuto. nebo USD za rok Pokud roční fakturace vybrané období. |
| Plánování cílové skupiny  | Volitelné. Výchozí hodnota: Veřejný plán. Možnosti: Public, Private podle ID tenanta |
| Omezené plánování cílová skupina (ID tenanta a popis)  | Povinné, pokud vybraná privátní plánu. Min 1 a max 10 ID tenanta, je-li zadat ručně. Maximální počet 20000, je-li import souboru CSV. |

## <a name="test-drive-listing-page"></a>Test jednotky stránce

K dispozici pouze pokud vyberete nabízí testovací verze pro vaši nabídku. Zadejte podrobnosti používá seznam test drive na webu Marketplace.

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------| 
| Popis  | Povinný parametr. |
| Uživatelské jméno ruční + souboru  | Povinné, max. 1 dokumentu. Musí být ve formátu PDF. |
| Název videa, adresa URL + miniaturu  | Volitelné, doporučujeme. Miniatura musí být 533 x 324 ve formátu JPGP nebo PNG. Video musí být hostovaný ve YouTube nebo Vimeo. |

## <a name="review-and-publish-page"></a>Projděte si stránku a publikovat

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------| 
| Poznámky k certifikaci  | Volitelné. |

## <a name="next-steps"></a>Další postup

- [Vytvoření nové nabídky SaaS](./create-new-saas-offer.md)
