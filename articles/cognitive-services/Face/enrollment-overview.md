---
title: Osvědčené postupy pro přidání uživatelů do služby obličeje
titleSuffix: Azure Cognitive Services
description: Přečtěte si o procesu registrace obličeje pro registraci uživatelů ve službě pro rozpoznávání obličeje.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 04/19/2021
ms.author: pafarley
ms.openlocfilehash: 183983779c09658d8d6f609319a127b1f406452d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728423"
---
# <a name="best-practices-for-adding-users-to-a-face-service"></a>Osvědčené postupy pro přidání uživatelů do služby obličeje

Aby bylo možné používat rozhraní API pro rozpoznávání tváře Cognitive Services pro ověřování nebo identifikaci obličeje, je nutné zaregistrovat plošky do **LargePersonGroup** nebo podobné struktury dat. Tato hluboká podrobně předvádí osvědčené postupy pro shromáždění smysluplného souhlasu uživatelů a ukázková logika pro vytváření vysoce kvalitních registrů, které optimalizují přesnost rozpoznávání. 

## <a name="meaningful-consent"></a>Smysluplný souhlas 

Jedním z klíčových účelů registrační aplikace pro rozpoznávání obličeje je poskytnout uživatelům možnost souhlasit s používáním imagí pro konkrétní účely, jako je například přístup k Worksite. Vzhledem k tomu, že technologie rozpoznávání obličeje můžou být zjištěné jako shromažďování citlivých osobních údajů, je obzvláště důležité požádat o souhlas způsobem, který je transparentní i respectful. Souhlas je pro uživatele smysluplný, když jim dá učinit rozhodnutí, že je pro ně nejvhodnější.   

V závislosti na výzkumu uživatelů Microsoftu, který je zodpovědný za systém souborů AI a [externí výzkum](ftp://ftp.cs.washington.edu/tr/2000/12/UW-CSE-00-12-02.pdf), jsme zjistili, že toto udělení souhlasu je smysluplné, když pro uživatele, kteří registrují v technologii, nabízí tyto zásady:

* Povědomí: uživatelé by neměli mít pochybnosti, když budou požádáni o poskytnutí své vlastní šablony nebo fotografií pro zápis. 
* Vysvětlení: uživatelé by měli být schopni přesně popsat svá vlastní slova, o kterých byli požádáni, o koho, o o tom, k čemu a s jakými zárukami. 
* Volnost výběru: uživatelé by neměli mít možnost být při rozhodování o souhlasu a registraci v rozpoznávání obličeje zavázáni nebo manipulováni. 
* Řízení: uživatelé by měli být schopni odvolat svůj souhlas a kdykoli odstranit svá data. 

Tato část nabízí pokyny k vývoji registrační aplikace pro rozpoznávání obličeje. Tyto pokyny se vyvinuly na základě výzkumu uživatelů Microsoftu v souvislosti s registrací jednotlivců v oblasti rozpoznávání obličeje pro vytváření záznamů. Proto se tato doporučení nemusí vztahovat na všechna řešení pro rozpoznávání obličeje. Odpovědnost za použití rozhraní API pro rozpoznávání tváře závisí na konkrétním kontextu, ve kterém je integrovaná, takže stanovení priorit a uplatnění těchto doporučení by se mělo přizpůsobit vašemu scénáři. 

> [!NOTE]
> Vaše žádost o aplikaci pro registraci se dá v rámci své jurisdikce zarovnat s platnými zákonnými požadavky a přesně odrážet všechny postupy shromažďování a zpracování dat.

## <a name="application-development"></a>Vývoj aplikací 

Než začnete navrhovat tok registrace, zamyslete se nad tím, jak aplikace, kterou vytváříte, umožňuje příslibů uživatelům, jak jsou jejich data chráněná. Následující doporučení vám mohou pomoci při vytváření registračního prostředí, které zahrnuje zodpovědné přístupy k zabezpečení osobních údajů, správě ochrany osobních údajů uživatelů a zajištění, že aplikace je přístupná pro všechny uživatele.  

|Kategorie | Doporučení |
|---|---|
|Hardware | Vezměte v úvahu kvalitu kamery registračního zařízení. |
|Doporučené funkce pro registraci | Zahrňte do aplikace Multi-Factor Authentication krok přihlášení. </br></br>Propojte informace o uživateli, jako je alias nebo identifikační číslo s ID šablony obličeje z rozhraní API pro rozpoznávání tváře (označované jako ID osoby). Toto mapování je nezbytné pro načtení a správu registrace uživatele. Poznámka: ID osoby by mělo být v aplikaci považováno za tajné.</br></br>Nastavte automatizovaný proces odstraňování všech registračních dat, včetně profesionálových šablon a fotografií pro zápis uživatelů, kteří už nejsou uživateli techniky rozpoznávání obličeje, jako jsou bývalé zaměstnanci. </br></br>Vyhněte se automatické registraci, protože nedává uživateli informace o povědomí, porozumění, svobodě výběru nebo řízení, které se doporučuje pro získání souhlasu. </br></br>Požádejte uživatele o oprávnění k uložení imagí používaných k registraci. To je užitečné v případě, že dojde k aktualizaci modelu, protože nové fotografie registrace se budou muset v novém modelu znovu zaregistrovat každých 10 měsíců. Pokud se původní image neukládají, uživatelé budou muset projít procesem registrace od začátku.</br></br>Umožňuje uživatelům odhlásit se z ukládání fotek v systému. Chcete-li nastavit možnost Clear, můžete přidat druhou obrazovku žádosti o souhlas pro uložení fotografií zápisu. </br></br>Pokud jsou fotky uložené, vytvořte automatizovaný proces pro opětovné registraci všech uživatelů, když dojde k aktualizaci modelu. Uživatelé, kteří si uložili své fotky k registraci, se nemusí znovu zaregistrovat. </br></br>Vytvořte funkci aplikace, která umožňuje určeným správcům přepsat určité filtry kvality, pokud má uživatel potíže s registrací. |
|Zabezpečení | Cognitive Services dodržujte [osvědčené postupy](../cognitive-services-virtual-networks.md?tabs=portal) pro šifrování neaktivních a přenosových dat uživatelů. Níže jsou uvedené další postupy, které vám pomůžou zajistit, aby se zabezpečení příslibů při registraci uživatelů. </br></br>Vezměte v úvahu bezpečnostní opatření, abyste zajistili, že nikdo nemá přístup k ID osoby v jakémkoli okamžiku při registraci. Poznámka: PersonID by měl být v systému zápisu považován za tajný klíč. </br></br>Použijte [řízení přístupu na základě role](../../role-based-access-control/overview.md) s Cognitive Services. </br></br>Pro přístup k prostředkům, jako jsou databáze, použijte ověřování založené na tokenech a/nebo sdílené přístupové podpisy (SAS) prostřednictvím klíčů a tajných klíčů. Pomocí tokenů Request nebo SAS můžete udělit omezený přístup k datům, aniž byste museli narušit klíče účtu, a můžete zadat čas vypršení platnosti tokenu. </br></br>Nikdy neukládejte do vaší aplikace žádné tajné klíče, klíče ani hesla. |
|Ochrana osobních údajů uživatele |Poskytněte řadu možností registrace, které řeší různé úrovně otázek ochrany osobních údajů. Nepoužívejte žádné pověření, aby se uživatelé mohli zaregistrovat do systému pro rozpoznávání tváře, pomocí svých osobních zařízení. </br></br>Umožněte uživatelům, aby se znovu zaregistrovali, odvolali souhlas a odstranili data z aplikace pro zápis kdykoli a z jakéhokoli důvodu. |
|Usnadnění |Sledujte standardy přístupnosti (například [Ada](https://www.ada.gov/regs2010/2010ADAStandards/2010ADAstandards.htm) nebo [W3C](https://www.w3.org/TR/WCAG21/)), abyste zajistili, že je aplikace použitelná pro lidi s mobilitou nebo zrakovým postižením. |

## <a name="next-steps"></a>Další kroky  

Postupujte podle pokynů průvodce [vytvořením aplikace pro zápis](build-enrollment-app.md) a začněte s ukázkovou registrační aplikací. Pak ho Přizpůsobte nebo napište vlastní aplikaci tak, aby vyhovovala potřebám vašeho produktu.