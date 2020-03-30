---
title: Kontrolní seznam pro vytváření nabídek SaaS – Komerční tržiště pro Azure
description: Podrobnosti, které můžete poskytnout v procesu vytváření nabídky SaaS. - Komerční tržiště pro Azure
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 60111b6a23c76314383c5f95be3eb4b38f90d3e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281336"
---
# <a name="saas-offer-creation-checklist"></a>Kontrolní seznam k vytváření nabídek SaaS

Proces vytváření nabídky SaaS vás provede několika stránkami. Zde jsou podrobnosti, které můžete poskytnout na každé stránce, s odkazy na další informace o každé položce.

Položky, které jsou povinny poskytnout nebo zadat, jsou uvedeny níže. Některé oblasti jsou volitelné nebo mají k dispozici výchozí hodnoty, které můžete změnit podle potřeby. Na těchto oddílech nemusíte pracovat v uvedeném pořadí.

| **Položka**    | **Účel**  |
| :---------- | :-------------------|
| [**Nová nabídka Modální**](#new-offer-modal) | Shromažďuje informace o identitě nabídky.  |
| [Stránka nastavení nabídky](#offer-setup-page) | Umožňuje přihlásit se k používání klíčových funkcí a zvolit, jak prodávat svou nabídku prostřednictvím společnosti Microsoft.  |
| [Stránka Vlastnosti](#properties-page) | Definujte kategorie a odvětví používaná k seskupení vaší nabídky na tržištích, právní smlouvy podporující vaši nabídku a verzi aplikace. |
| [Stránka nabídky](#offer-listing-page) | Definujte podrobnosti nabídky, které se mají zobrazit na trhu, včetně popisů vašich nabídek a marketingových aktiv. |
| [Náhled stránky](#preview-page) | Definujte omezený okruh uživatelů ve verzi Preview pro uvolnění nabídky před publikováním nabídky živě širšímu publiku na marketplace. |
| [Nabídka stránky technické konfigurace](#technical-configuration-page)  | K dispozici pouze v případě, že se rozhodnete nabídku prodat prostřednictvím společnosti Microsoft. Definujte technické podrobnosti (cesta url, webhook, ID klienta a ID aplikace), které se používají k připojení k vaší nabídce. |
| [**Nový plán Modální**](#plan-identity-modal) | Shromažďuje informace o identitě plánu.  |
| [Stránka s výpoušem plánu](#plan-listing-page)  | K dispozici pouze v případě, že se rozhodnete nabídku prodat prostřednictvím společnosti Microsoft. Definujte podrobnosti použité k uvádění plánu na trhu.  |
| [Stránka dostupnosti & ceny plánu](#plan-pricing--availability-page)  | K dispozici pouze v případě, že se rozhodnete nabídku prodat prostřednictvím společnosti Microsoft.  Shromažďuje obchodní charakteristiky (cenový model), publikum a dostupnost trhu pro každý plán (verzi) vaší nabídky.  |
| [Stránka výpisu testovací jednotky](#test-drive-listing-page)  | K dispozici pouze v případě, že se rozhodnete nabídnout zkušební jízdu pro vaši nabídku. Definujte podrobnosti použité k uzení testovací jednotky na trhu.  |
| Stránka technické konfigurace testovací jednotky  | K dispozici pouze v případě, že se rozhodnete nabídnout zkušební jízdu pro vaši nabídku. Definujte technické podrobnosti pro ukázku (nebo "zkušební jízdu"), které zákazníkům umožní vyzkoušet si vaši nabídku před potvrzením nákupu.  |
| [Stránka Recenze a publikování](#review-and-publish-page)  | Vyberte změny, které chcete publikovat, podívejte se na stav každé stránky a poskytněte poznámky certifikačnímu týmu.  |


## <a name="new-offer-modal"></a>Nová nabídka modální 

První informace, které budete požádáni o poskytnutí, jsou ID a alias pro vaši nabídku. 

| **Název pole**    | **Poznámky**   |  
| :---------------- | :-----------| 
| ID nabídky  | Povinné, nelze změnit po vytvoření. Maximálně 50 znaků a musí se skládat pouze z číselných, alfanumerických znaků, pomlček nebo podtržítek. |
| Alias nabídky  | Povinná hodnota. |

## <a name="offer-setup-page"></a>Stránka nastavení nabídky

Stránka nastavení nabídky je místo, kde se můžete přihlásit do různých kanálů a prodejních pohybů, stejně jako deklarovat použití klíčových funkcí, jako je testovací jízda a zákazník vede. 

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------|  
| Chcete prodávat prostřednictvím společnosti Microsoft?  | Povinná hodnota. Výchozí: Ano |
| Jak chcete, aby potenciální zákazníci komunikovali se seznamem nabídek? (Výzva k akci)  | Povinné, pokud se neprodává prostřednictvím společnosti Microsoft. Výchozí: Bezplatná zkušební verze, možnosti: "Get it now", "Bezplatná zkušební verze", "Kontaktujte mě." |
| Adresa URL zkušební verze  | Povinné, pokud je vybrána možnost "Bezplatná zkušební verze", jako způsob, jakým by zákazníci měli komunikovat se seznamem nabídek. |
| Adresa URL nabídky  | Povinné, pokud je vybrána možnost "Získat nyní", protože způsob, jakým by zákazníci měli komunikovat se seznamem nabídek |
| Kanály  | Nepovinný parametr. Výchozí: Není přihlášen do kanálu CSP (prodejce).  |
| Testovací verze | Nepovinný parametr. Výchozí: Není povolena žádná testovací jednotka.  |
| Typ zkušební jízdy | Povinné, pokud je povolena zkušební jednotka. Výchozí: Není vybráno žádné. Možnosti: Azure Resource Manager, Dynamics 365 pro Business Central, Dynamics 365 pro customer engagement, Dynamics 365 pro operace, aplikace Logika, Power BI.  |
| Vedení Management - připojení k CRM systému | Povinné při prodeji prostřednictvím společnosti Microsoft nebo pokud výpis nabízí jako "Kontaktujte mě". Výchozí: není připojen žádný CRM systém. Možnosti CRM: Tabulka Azure, Objekt blob Azure, Dynamics CRM online, Koncový bod httpů, Marketo, Salesforce  |

## <a name="properties-page"></a>Stránka Vlastnosti

Stránka vlastností je místo, kde definujete kategorie a odvětví používaná k seskupení nabídky na tržištích, právní smlouvy podporující vaši nabídku a verzi aplikace. Nezapomeňte na této stránce uvést úplné a přesné podrobnosti o vaší nabídce, aby byla vhodně zobrazena a nabízena správné sadě zákazníků. 

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------|  
| Kategorie a podkategorie | Povinné 1 a max 3. Výchozí: Není vybráno žádné. |
| Průmyslová odvětví a pododvětví | Nepovinný parametr. max 2 L1 Industries a max 2 subindustries v rámci každého odvětví L1, Výchozí: Žádný |
| verze aplikace  | Nepovinný parametr. Výchozí: Žádné. |
| Použít standardní smlouvu  | Nepovinný parametr. Výchozí: není vybráno.  | |
| Podmínky použití  | Povinné, pokud není vybrána standardní smlouva.  |

## <a name="offer-listing-page"></a>Stránka nabídky

Na stránce nabídky můžete zadat text a obrázky, které zákazníci uvidí při prohlížení nabídky na trhu. 

| **Název pole**    | **Poznámky**   |
| :---------------- | :-----------| 
| Name (Název)  | Povinné, max 50 znaků. |
| Souhrn  | Povinné, max 100 znaků. | 
| Popis  | Povinné, maximálně 3000 znaků. |
| Pokyny začínáme  | Povinné, maximálně 3000 znaků. |
| Pokyny začínáme  | Povinné, maximálně 3000 znaků. |
| Hledat klíčová slova  | Volitelná, doporučená, max 3 klíčová slova. |
| Adresa URL zásad ochrany osobních údajů  | Povinná hodnota. |
| Adresa URL marketingových materiálů programu CSP  | Nepovinný parametr. |
| Užitečné odkazy Název + URL  | Nepovinný parametr. |
| Název podpůrných dokumentů + soubor  | Povinné, min 1 a max 3. Musí být formát souboru PDF. |
| Snímky obrazovek  | Povinné, min 1 screenshot a max 5; čtyři nebo více doporučených. Musí být 1280 X 720 ve formátu PNG. |
| Loga obchodu (malá, střední, velká, široká, hero)  | Je vyžadovánmalý (48 X 48) a velký (216 X 216); jiné velikosti volitelné, ale doporučené: Střední (90 x 90), Široký (255 x 115), Hero (815 x 290). Musí být ve formátu PNG. |
| Název videa + URL + miniatura  | Volitelné, doporučené, max 4 videa. Miniatura musí být 1280 x 720 ve formátu PNG. Video musí být hostováno na YouTube nebo Vimeo. |
| Kontakty (program CSP, inženýrství, podpora)  | Vyžadován kontakt na inženýrskou a podporu (jméno, e-mail a telefonní číslo); CSP Program kontakt volitelné, ale doporučeno. |
| Adresa URL podpory  | Povinná hodnota. |

## <a name="preview-page"></a>Stránka náhledu

Na stránce náhledu zadáte cílovou skupinu, která má přístup k náhledu nabídky, a ověříte, zda nabídka splňuje všechny vaše požadavky před tím, než začne být uvedena do provozu. 

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------| 
| AAD/MSA e-mail + popis | Povinné, min 1 a max 10, pokud je zadán ručně, nebo až 20 při nahrávání souboru CSV. |

## <a name="technical-configuration-page"></a>Stránka technické konfigurace 

Na stránce technické konfigurace zadáte technické podrobnosti, které společnost Microsoft používá k připojení k vaší nabídce. Tato stránka není viditelná pro vás, pokud jste se rozhodli neprodávat prostřednictvím společnosti Microsoft.

| **Název pole**    | **Poznámky**   |  
| :---------------- | :-----------| 
| Adresa URL vstupní stránky | Povinné při prodeji prostřednictvím společnosti Microsoft. |
| Připojení webhooku | Povinné při prodeji prostřednictvím společnosti Microsoft. |
| ID tenanta Azure AD | Povinné při prodeji prostřednictvím společnosti Microsoft. |
| ID aplikace Azure AD | Povinné při prodeji prostřednictvím společnosti Microsoft. |

## <a name="plan-identity-modal"></a>Plánování identity modální

První informace, které budete požádáni o poskytnutí, jsou název a ID plánu. Tato stránka není viditelná pro vás, pokud jste se rozhodli neprodávat prostřednictvím společnosti Microsoft.

| **Název pole**    | **Poznámky**   |  
| :---------------- | :-----------| 
| ID plánu  | Povinné při prodeji prostřednictvím společnosti Microsoft. Po stvoření se to nedá změnit. Maximálně 50 znaků a musí se skládat pouze z číselných, alfanumerických znaků, pomlček nebo podtržítek. |
| Název plánu  | Povinné při prodeji prostřednictvím společnosti Microsoft. Musí být jedinečný ve všech plánech v nabídce. Maximálně 50 znaků. |

## <a name="plan-listing-page"></a>Stránka nabídky plánu

Stránka s výpisem plánu je místo, kde zadáte text, který mohou zákazníci zobrazit při prohlížení plánu na trhu. Tato stránka není viditelná pro vás, pokud jste se rozhodli neprodávat prostřednictvím společnosti Microsoft.

| **Název pole**    | **Poznámky**   |  
| :---------------- | :-----------| 
| Popis plánu   | Povinné při prodeji prostřednictvím společnosti Microsoft. Maximálně 500 znaků. | |

## <a name="plan-pricing--availability-page"></a>Stránka & dostupnosti tarifů

Stránka ceny a dostupnost plánu je místo, kde definujete obchodní charakteristiky, publikum a dostupnost trhu pro každý plán (verzi) vaší nabídky. Tato stránka není viditelná pro vás, pokud jste se rozhodli neprodávat prostřednictvím společnosti Microsoft.

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------| 
| Dostupnost na trhu  | Povinné, min 1 a max 141. |
| Cenový model  | Povinná hodnota. Výchozí: Paušální sazba. Možnosti: Paušální sazba, na uživatele. |
| Minimální a maximální počet sedadel  | Volitelné, k dispozici pouze v případě, že je vybrán cenový model založený na sedadle. |
| Fakturační období  | Povinná hodnota. Výchozí: Měsíčně. Možnosti: Měsíční, Roční. |
| Price  | Povinné USD za měsíc, pokud je vybráno měsíční fakturační období; nebo USD za rok, pokud je zvolena roční fakturační doba. |
| Plánování publika  | Nepovinný parametr. Výchozí: Veřejný plán. Možnosti: Veřejné, Soukromé podle ID klienta |
| Cílová skupina s omezeným plánem (ID klienta + popis)  | Povinné, pokud je vybrán soukromý plán. Min 1 a max 10 ID klienta při ručním zadání. Max 20000, pokud csv import souboru. |

## <a name="test-drive-listing-page"></a>Stránka výpisu testovací jednotky

K dispozici pouze v případě, že se rozhodnete nabídnout zkušební jízdu pro vaši nabídku. Definujte podrobnosti použité k uzení testovací jednotky na trhu.

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------| 
| Popis  | Povinná hodnota. |
| Název uživatelské příručky + soubor  | Povinné, max 1 doc. Musí být ve formátu PDF. |
| Název videa, URL + miniatura  | Nepovinné, doporučené. Miniatura musí být 533 x 324 ve formátu JPGP nebo PNG. Video musí být hostováno na YouTube nebo Vimeo. |

## <a name="review-and-publish-page"></a>Zkontrolovat a publikovat stránku

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------| 
| Poznámky k certifikaci  | Nepovinný parametr. |

## <a name="next-steps"></a>Další kroky

- [Vytvoření nové nabídky SaaS](./create-new-saas-offer.md)
