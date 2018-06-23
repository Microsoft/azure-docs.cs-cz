---
title: Dokumentace rozhraní API překlad řeči | Microsoft Docs
titleSuffix: Cognitive Services
description: Pomocí rozhraní API služby Microsoft překladač řeči překlad přidat řeči na rozpoznávání řeči a převod řeči na text překlad, aby vaše aplikace.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jansko
ms.openlocfilehash: 15f27e6b5b2fd7384958a660156855fc65f4e558
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342714"
---
# <a name="microsoft-translator-speech-api"></a>Microsoft Translator Speech API
Rozhraní API pro rozpoznávání řeči překladač Microsoft slouží k přidání začátku do konce, v reálném čase, řeči překlady do aplikace, nástroje nebo jakéhokoli řešení vyžádání překladu vícejazyčné řeči bez ohledu na cílové operačního systému nebo programovacích jazyků. Rozhraní API můžete použít pro obě převod řeči na rozpoznávání řeči a převod řeči na text překlad.

Rozhraní API služby Microsoft překladač textu je služba Azure, část [kognitivní API služby Microsoft kolekce](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive) strojové učení a algoritmy AI v cloudu, snadno použití ve vašich projektů vývoj.

Klientské aplikace s rozhraním API Microsoft překladač řeči stream řeči zvuk ke službě a zobrazí zpět na základě text a zvuk výsledků, které zahrnují rozpoznaný text v source jazyk a jeho překlad v jazyce cílový datový proud s. Textové výsledky vytváří použitím automatické rozpoznávání řeči rozpoznávání (ASR) používá technologii hluboké neuronové sítě pro příchozí datový proud zvuku. Nezpracovaná automatické obnovení systému výstup další lepší pomocí nové počítačového názvem PravdivýText tak, aby odrážela přesněji záměru uživatele. Například PravdivýText odebere disfluencies (hmms a coughs), opakovaná slova a správné interpunkce obnovení a velkých písmen. Možnost maskování nebo vyloučit profanities je rovněž obsažena. Moduly rozpoznávání a překlad probíhá Trénink speciálně pro zpracování konverzačního řeči. 

Služba překladu řeči používá interval detekce určit konec utterance. Po pozastavení hlasové aktivity bude služba zpět stream konečný výsledek pro dokončené utterance. Službu můžete také odeslat zpět částečné výsledky, která poskytnou zprostředkující uznání a překladů pro utterance v průběhu. 

Pro překlad řeči na rozpoznávání řeči služba poskytuje možnost syntetizovat rozpoznávání řeči (převod textu na řeč) z mluvené textu v jazycích cíl. Převod textu na řeč zvuk se vytvoří ve formátu určeném klientem. Formáty WAV a MP3 nejsou k dispozici.

Rozhraní API pro překlad řeči používá protokol WebSocket k poskytnutí plně duplexní komunikační kanál mezi klientem a serverem. 

## <a name="about-microsoft-translator"></a>O překladač Microsoft
Microsoft Translator je služba založená na cloudu strojového překladu. Jádrem této služby se [Text API překladač] (https://www.microsoft.com/en-us/translator/translatorapi.aspx) a překladač řeči rozhraní API což spotřeby různých produktů a služeb Microsoftu a používá tisíce firmy po celém světě v jejich aplikace a pracovní postupy, že jejich obsahu dosažení po celém světě cílovou skupinu.

Další informace o [služby Microsoft Translator](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="microsoft-translator-neural-machine-translation-nmt"></a>Překladač Microsoft Neural strojový překlad (NMT)
Rozhraní API pro rozpoznávání řeči překladač Microsoft používá k poskytnutí překlady starší verze statistické strojový překlad (SMT) i novější neuronové strojový překlad (NMT).

Statistické strojový překlad, bylo dosaženo plateau z hlediska výkonu v přírůstcích. Překlad kvality již nebudou zlepšují žádným způsobem významné pro obecné systémy s SMT. Novou technologií na základě AI překlad je získání výkonnosti založené na Neuronové sítě (NN).

NMT poskytuje lepší překlady nejen z nezpracovaná překlad kvality, ale i vyhodnocování hlediska, protože zvukových více fluent, více lidí, než SMT ty. Z důvodu klíče pro tuto přenositelnost je, že NMT používá kontext úplné věty překládat slova. SMT pouze trvá bezprostřednímu kontextu pár slov před a po jednotlivých slov.

NMT modely jsou jádrem rozhraní API a nejsou viditelné pro koncové uživatele. Pouze znatelné rozdíly jsou:
* Vylepšené překlad kvality, zejména pro jazyky, jako je například čínštiny, japonštiny a arabské
* Nekompatibilita s stávajících funkcí přizpůsobení centra (pro použití s rozhraním API pro Microsoft překladač Text)

Všechny jazyky podporované řeči překlad jsou zapnuté podle NMT. Proto všechny překlad řeči na rozpoznávání řeči používá NMT. 

Převod řeči na text překlad může použít kombinaci NMT a SMT v závislosti na pár jazyk. Pokud cílový jazyk je podporovaná NMT, úplná překlad je NMT zapnuté. Pokud cílový jazyk nepodporuje NMT, překlad je hybridní NMT a SMT pomocí angličtina jako "pivot" mezi nimi. 

Zobrazení podporované jazyky na [Microsoft.com](https://www.microsoft.com/en-us/translator/languages.aspx). 

Další informace o [fungování NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Registrace](translator-speech-how-to-signup.md)

> [!div class="nextstepaction"]
> [Psaní](quickstarts/csharp.md)

## <a name="see-also"></a>Další informace najdete v tématech
- [Kognitivní stránky dokumentace služby](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive)
- [Kognitivní stránka služby produktu](https://azure.microsoft.com/services/cognitive-services/)
- [Řešení a informace o cenách](https://www.microsoft.com/en-us/translator/home.aspx) 
