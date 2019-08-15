---
title: Kontrolní seznam pro vytváření nabídky SaaS – komerční tržiště pro Azure
description: Podrobnosti, které můžete poskytnout v procesu vytváření nabídky SaaS. – Komerční tržiště pro Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 66c28039f9126ed9e3f56c3ac15b1b3d82279b64
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036377"
---
# <a name="saas-offer-creation-checklist"></a>Kontrolní seznam pro vytváření nabídky SaaS

Proces vytvoření nabídky SaaS vás provede několika stránkami. Tady jsou podrobné informace, které můžete zadat na každé stránce s odkazy na Další informace o jednotlivých položkách.

Položky, které je nutné zadat nebo zadat, jsou uvedeny níže. Některé oblasti jsou volitelné nebo mají zadané výchozí hodnoty, které můžete podle potřeby změnit. Nemusíte pracovat na těchto oddílech v uvedeném pořadí.

| **Položka**    | **Účel**  |
| :---------- | :-------------------|
| [**Nová nabídka – modální**](#new-offer-modal) | Shromažďuje informace o identitě nabídky.  |
| [Stránka nastavení nabídky](#offer-setup-page) | Umožňuje výslovný souhlas s používáním klíčových funkcí a výběr způsobu prodeje vaší nabídky prostřednictvím Microsoftu.  |
| [Stránka vlastností](#properties-page) | Definujte kategorie a odvětví používané k seskupení vaší nabídky na tržištích, právní smlouvy podporující vaši nabídku a verzi vaší aplikace. |
| [Stránka se seznamem nabídek](#offer-listing-page) | Zadejte podrobnosti nabídky, které se budou zobrazovat na webu Marketplace, včetně popisů vaší nabídky a marketingových prostředků. |
| [Stránka náhledu](#preview-page) | Definujte skupinu omezené verze Preview pro uvolnění vaší nabídky před publikováním vaší nabídky do širších cílových skupin Marketplace. |
| [Stránka technické konfigurace nabídky](#technical-configuration-page)  | K dispozici pouze v případě, že vyberete možnost prodávat nabídku prostřednictvím společnosti Microsoft. Definujte technické podrobnosti (cestu URL, Webhook, ID tenanta a ID aplikace), které se používají pro připojení k vaší nabídce. |
| [**Nový plán – modální**](#plan-identity-modal) | Shromažďuje informace o identitě plánu.  |
| [Stránka se seznamem plánů](#plan-listing-page)  | K dispozici pouze v případě, že vyberete možnost prodávat nabídku prostřednictvím společnosti Microsoft. Definujte podrobnosti, které se použijí k vypsání plánu na webu Marketplace.  |
| [Stránka dostupnost & dostupnosti pro ceny plánu](#plan-pricing--availability-page)  | K dispozici pouze v případě, že vyberete možnost prodávat nabídku prostřednictvím společnosti Microsoft.  Shromažďuje obchodní charakteristiky (cenový model), cílovou skupinu a dostupnost trhu pro jednotlivé plány (verze) vaší nabídky.  |
| [Stránka se seznamem testovacích jednotek](#test-drive-listing-page)  | K dispozici pouze v případě, že jste vybrali možnost nabídnout si pro vaši nabídku testovací jednotku. Zadejte podrobnosti, které se použijí k vypsání testovacích jednotek na webu Marketplace.  |
| Stránka technické konfigurace testovacích jednotek  | K dispozici pouze v případě, že jste vybrali možnost nabídnout si pro vaši nabídku testovací jednotku. Definujte technické podrobnosti pro ukázku (nebo "testovací jednotku"), které umožní zákazníkům vyzkoušet si vaši nabídku před tím, než se ji zakoupí.  |
| [Stránka pro kontrolu a publikování](#review-and-publish-page)  | Vyberte změny, které chcete publikovat, zobrazte stav jednotlivých stránek a poskytněte jim poznámky certifikačnímu týmu.  |


## <a name="new-offer-modal"></a>Nová nabídka – modální 

První část informací, které budete požádáni, získáte jako ID a alias pro vaši nabídku. 

| **Název pole**    | **Poznámky**   |  
| :---------------- | :-----------| 
| ID nabídky  | Požadováno, po vytvoření nelze změnit. Maximálně 50 znaků a musí obsahovat jenom malá písmena, alfanumerické znaky, pomlčky nebo podtržítka. |
| Alias nabídky  | Povinný parametr. |

## <a name="offer-setup-page"></a>Stránka nastavení nabídky

Stránka pro nastavení nabídek je místo, kde se můžete přihlásit k různým kanálům a prodávat pohyby, a také deklarovat používání klíčových funkcí, jako je například Test Drive nebo potenciální zákazníci. 

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------|  
| Chtěli byste prodávat přes Microsoft?  | Povinný parametr. Výchozí: Ano |
| Jak chcete potenciálním zákazníkům pracovat se seznamem nabídek? (Volání akce)  | Vyžaduje se, pokud nechcete prodávat prostřednictvím Microsoftu. Výchozí: Bezplatná zkušební verze, možnosti: "Získat hned", "bezplatnou zkušební verzi", "kontaktujte mě." |
| Adresa URL zkušební verze  | Vyžaduje se, pokud je vybrána možnost "bezplatná zkušební verze", jak by zákazníci měli pracovat se seznamem nabídek. |
| Adresa URL nabídky  | Vyžaduje se, pokud je vybraná možnost Get to Now (načíst), jak by zákazníci měli pracovat se seznamem nabídek. |
| Kanály  | Volitelný parametr. Výchozí: Nesouhlasí se s kanálem CSP (prodejce).  |
| Test Drive | Volitelný parametr. Výchozí: Není povolená žádná testovací jednotka.  |
| Typ testovací jednotky | Vyžaduje se, pokud je povolená testovací jednotka. Výchozí: Nic se nevybralo. Nastavení Azure Resource Manager, Dynamics 365 pro firmy Business Central, Dynamics 365 for Customer Engagement, Dynamics 365 for Operations, aplikace logiky, Power BI.  |
| Správa vedoucích – připojení k systému CRM | Požaduje se při prodeji prostřednictvím Microsoftu nebo při výpisu nabídek "kontaktujte mě". Výchozí: není připojený žádný systém CRM. Možnosti CRM: Tabulka Azure, Azure Blob, Dynamics CRM Online, HTTPs ' Endpoint, Marketo, Salesforce  |

## <a name="properties-page"></a>Stránka Vlastnosti

Stránka vlastnosti je místo, kde můžete definovat kategorie a obory používané k seskupení vaší nabídky na tržištích, právní smlouvy podporující vaši nabídku a verzi aplikace. Ujistěte se, že jste na této stránce zadali úplné a přesné podrobnosti o vaší nabídce, aby se zobrazovaly správně a nabízely správné sadě zákazníků. 

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------|  
| Kategorie a podkategorie | Požadováno 1 a max 3. Výchozí: Nic se nevybralo. |
| Odvětví a subodvětví | Volitelný parametr. maximum 2 L1 Industry a maximum 2 subodvětví v rámci každého oboru L1, výchozí: Nevybrán žádný |
| verze aplikace  | Volitelný parametr. Výchozí: Žádné. |
| Použití standardní smlouvy  | Volitelný parametr. Výchozí: není vybráno.  | |
| Podmínky použití  | Povinné, pokud není vybrána standardní smlouva.  |

## <a name="offer-listing-page"></a>Stránka se seznamem nabídek

Stránka výpisu je místo, kde můžete zadat text a obrázky, které se zákazníkům zobrazí při zobrazení seznamu vaší nabídky na webu Marketplace. 

| **Název pole**    | **Poznámky**   |
| :---------------- | :-----------| 
| Name  | Požadováno, max. 50 znaků. |
| Souhrn  | Požadováno, max. 100 znaků. | 
| Popis  | Požadováno, max. 3000 znaků. |
| Pokyny pro Začínáme  | Požadováno, max. 3000 znaků. |
| Pokyny pro Začínáme  | Požadováno, max. 3000 znaků. |
| Hledat klíčová slova  | Volitelné, doporučené, max 3 klíčová slova. |
| Adresa URL zásad ochrany osobních údajů  | Povinný parametr. |
| Adresa URL marketingových materiálů programu CSP  | Volitelný parametr. |
| Název užitečného odkazu + adresa URL  | Volitelný parametr. |
| Název doprovodnéch dokumentů + soubor  | Požadováno, minimum 1 a max 3. Musí se jednat o formát souboru PDF. |
| Snímky obrazovek  | Požadováno, minimální počet 1 snímek obrazovky a maximální 5; doporučuje se čtyři nebo více. Ve formátu PNG musí být 1280 X 720. |
| Ukládat loga (malá, střední, Velká, rozsáhlá, Hero)  | Malá (48 × 48) a velká (216 × 216); jiné velikosti volitelné, ale doporučené: Střední (90 x 90), roztažitelné (255 x 115), Hero (815 x 290). Musí být ve formátu PNG. |
| Název videa + adresa URL + Miniatura  | Volitelné, doporučené, maximální 4 videa. Miniatura musí být ve formátu PNG 1280 x 720. Video musí být hostované na YouTube nebo Vimeo. |
| Kontakty (program CSP, strojírenství, podpora)  | Technické a podpůrné kontaktní požadavky (jméno, e-mail a telefonní číslo); Program CSP kontaktuje volitelnou, ale doporučuje se. |
| Adresa URL podpory  | Povinný parametr. |

## <a name="preview-page"></a>Stránka náhledu

Stránka verze Preview je místo, kde můžete určit cílovou skupinu pro přístup k vaší nabídce Preview, abyste ověřili, že nabídka splňuje všechny vaše požadavky, než bude živá. 

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------| 
| AAD/MSA e-mail + popis | Požadováno, min 1 a Max 10 při zadání ručně nebo až 20 při nahrávání souboru CSV. |

## <a name="technical-configuration-page"></a>Stránka technické konfigurace 

Stránka technická konfigurace je místo, kde zadáte technické podrobnosti, které Microsoft používá pro připojení k vaší nabídce. Pokud se rozhodnete neprodávat přes Microsoft, Tato stránka není viditelná.

| **Název pole**    | **Poznámky**   |  
| :---------------- | :-----------| 
| Adresa URL cílové stránky | Vyžaduje se při prodeji prostřednictvím Microsoftu. |
| Webhook připojení | Vyžaduje se při prodeji prostřednictvím Microsoftu. |
| ID tenanta Azure AD | Vyžaduje se při prodeji prostřednictvím Microsoftu. |
| ID aplikace Azure AD | Vyžaduje se při prodeji prostřednictvím Microsoftu. |

## <a name="plan-identity-modal"></a>Naplánování identity modálního

První část informací, které jste požádali, je název a ID vašeho plánu. Pokud se rozhodnete neprodávat prostřednictvím Microsoftu, Tato stránka není viditelná.

| **Název pole**    | **Poznámky**   |  
| :---------------- | :-----------| 
| ID plánu  | Vyžaduje se při prodeji prostřednictvím Microsoftu. Po vytvoření se nedá změnit. Maximálně 50 znaků a musí obsahovat jenom malá písmena, alfanumerické znaky, pomlčky nebo podtržítka. |
| Název plánu  | Vyžaduje se při prodeji prostřednictvím Microsoftu. Musí být jedinečný ve všech plánech nabídky. Maximálně 50 znaků. |

## <a name="plan-listing-page"></a>Stránka se seznamem plánů

Na stránce seznam plánů můžete zadat text, který se zákazníkům zobrazí při prohlížení plánu na webu Marketplace. Pokud se rozhodnete neprodávat přes Microsoft, Tato stránka není viditelná.

| **Název pole**    | **Poznámky**   |  
| :---------------- | :-----------| 
| Popis plánu   | Vyžaduje se při prodeji prostřednictvím Microsoftu. Maximálně 500 znaků. | |

## <a name="plan-pricing--availability-page"></a>Stránka dostupnost & dostupnosti pro ceny plánu

Stránka plánování cen a dostupnosti vám umožní definovat obchodní charakteristiky, cílovou skupinu a dostupnost trhu pro jednotlivé plány (verze) vaší nabídky. Pokud se rozhodnete neprodávat přes Microsoft, Tato stránka není viditelná.

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------| 
| Dostupnost trhu  | Požadováno, minimum 1 a Max 141. |
| Cenový model  | Povinný parametr. Výchozí: Paušální sazba. Nastavení Paušální sazba na uživatele |
| Minimální a maximální počet sedadel  | Volitelné, dostupné jenom v případě, že je vybraný cenový model založený na pracovních stanicích. |
| Fakturační období  | Povinný parametr. Výchozí: Nadpis. Nastavení Měsíčně, roční. |
| Cena  | Požaduje se USD za měsíc, pokud je vybraný měsíční fakturační termín; nebo USD za rok, pokud je vybraný roční fakturační termín. |
| Cílová skupina plánu  | Volitelný parametr. Výchozí: Veřejný plán. Nastavení Veřejné, privátní podle ID tenanta |
| Skupina omezeného plánu (ID tenanta + popis)  | Vyžaduje se, pokud je vybraný privátní plán. Minimální 1 a maximum 10 ID klientů, pokud jsou zadány ručně. Max 20000 při importu souboru CSV |

## <a name="test-drive-listing-page"></a>Stránka se seznamem testovacích jednotek

K dispozici pouze v případě, že jste vybrali možnost nabídnout si pro vaši nabídku testovací jednotku. Zadejte podrobnosti, které se použijí k vypsání testovacích jednotek na webu Marketplace.

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------| 
| Popis  | Povinný parametr. |
| Uživatelské jméno a soubor ručně  | Požadováno, max 1 doc. Musí být ve formátu PDF. |
| Název videa, adresa URL + Miniatura  | Volitelné, doporučené. Miniatura musí být 533 × 324 ve formátu JPGP nebo PNG. Video musí být hostované na YouTube nebo Vimeo. |

## <a name="review-and-publish-page"></a>Stránka pro kontrolu a publikování

| **Název pole**    | **Poznámky**   | 
| :---------------- | :-----------| 
| Poznámky k certifikaci  | Volitelný parametr. |

## <a name="next-steps"></a>Další postup

- [Vytvořit novou nabídku SaaS](./create-new-saas-offer.md)
