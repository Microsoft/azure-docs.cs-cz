---
title: Scénář hodnocení - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer lze použít v jakékoli situaci, kde můžete vybrat aplikace ravých položek, akce nebo produktu zobrazíte – Pokud chcete vylepšit prostředí, dosahovat lepších obchodních výsledků nebo zvýšit produktivitu.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: b24e3d7fd71ef60cf8ebe5ba2c33889ff518580c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027107"
---
# <a name="where-can-you-use-personalizer"></a>Kde lze Personalizer?

Použití Personalizer v každé situaci, kdy vaše aplikace potřebuje k výběru ravých položek, akce nebo produkt pro zobrazení – aby bylo možné vylepšit prostředí dosažení lepších obchodních výsledků nebo zvýšit produktivitu. 

Personalizer využívá strojové učení a vyberte akci, která se uživateli zobrazí. Výběr může výrazně lišit podle toho, množství, kvality a distribuci dat odesílaných službě.

### <a name="checklist-for-applying-personalizer"></a>Kontrolní seznam pro použití Personalizer


Personalizer můžete použít v situacích, kde:

* Máte podnikový proces nebo použitelnosti cíle pro vaši aplikaci.
* Místo, kde máte ve své aplikaci, ve kterém rozhodování kontextové toho, jak zobrazit uživatelům zlepší tohoto cíle.
* O nejlepší volbu můžete a měly být přeneseny prostřednictvím kolektivní uživatele chování a součet reward skóre.
* Použití služby machine learning pro přizpůsobení následuje [pokyny pro využívání](ethics-responsible-use.md) a možnosti, které jste zvolili.
* Kontextové rozhodnutí může být vyjádřený jako pořadí nejlepší možností (akce) z omezenou sadu možností.
* Jak se dají určit seřazený podle výběru pracoval pro vaši aplikaci pomocí měření některé aspekty chování uživatelů a jeho v vyjádření _oceňujte skóre_. Toto je číslo v rozsahu od -1 a 1.
* Skóre reward nebude umožňuje přinést si příliš mnoho zkreslení nebo externí faktory. Doba trvání experimentu je dostatečně nízko, skóre reward můžete vypočítat, i když je stále relevantní.
* Kontext pro řád můžete vyjádřit jako seznam aspoň 5 [funkce](concepts-features.md) , že si myslíte, že by pomohl učinit správnou volbu a, která nezahrnuje identifikovatelné osobní údaje. (PII).
* Máte informace o jednotlivých obsahu podle vlastní volby, _akce_, jako seznam aspoň 5 [funkce](concepts-features.md) , že si myslíte, že budou nápovědy Personalizer tou správnou volbou.
* Vaše aplikace může uchovávat data dlouho dostatek historie alespoň 100 000 interakcí hromadit.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Strojové učení důležité informace týkající se použití Personalizer

Personalizer vychází zpětnovazební učení, přístup do strojového učení, který je vedená zpětnou vazbu, že jí přiřadit. 

Personalizer se dozvíte, nejlépe v situacích, kde:

* Není k dispozici dostatek události pro získání přehledu optimální individuálního nastavení, pokud se problém drifts v čase (například předvolby v zprávy nebo způsobem). Průběžné změny v reálném světě se bude přizpůsobovat personalizer, ale výsledky nebudou optimální, pokud není k dispozici dostatek události a data z se naučíte, jak vyhledat a vyrovnejte na nová schémata. Měli byste vybrat, ke které dochází často případu použití. Vezměte v úvahu hledáte případy použití, ke kterým dochází aspoň 500 časy za den.
* Kontext a akce dostatek [funkce](concepts-features.md) k usnadnění vzdělávání.
* Nejsou k dispozici méně než 50 akcí na jedno zavolání pořadí.
* Nastavení uchovávání dat povolit Personalizer shromažďovat dostatek dat k provedení offline vyhodnocení a optimalizace zásad. Toto je obvykle nejméně 50 000 datových bodů.

## <a name="monitor-effectiveness-of-personalizer"></a>Monitorování efektivitu Personalizer

Můžete sledovat efektivitu Personalizer pravidelně provedením [offline hodnocení](concepts-offline-evaluation.md).

## <a name="use-personalizer-with-recommendation-engines"></a>Pomocí Personalizer generátory doporučení

Mnoho společností používá doporučíte produkty z velkých katalogu zákazníkům generátory doporučení, marketing a campaigning nástroje, segmentace cílovou skupinu a clusteringu, filtrování založeného na spolupráci a jiným způsobem.

[Úložiště Microsoft Recommenders GitHub ](https://github.com/Microsoft/Recommenders) poskytuje příklady a osvědčené postupy pro vytváření doporučovacích systémů, poznámkové bloky Jupyter ve formě. Pro přípravu dat, vytváření modelů, vaše rozhodnutí vyzkoušet, ladění a zprovozňování generátory doporučení, pro mnoho běžných přístupů xDeepFM, včetně – zvláštní administrativní oblast, ALS, Mechanismů, DKN poskytuje funkčních příkladů.

Personalizer můžete pracovat s doporučovací modul, když je k dispozici.

* Generátory doporučení provést velké množství položek (například 500 000) a doporučujeme ze stovek nebo tisíců možnosti podmnožinu (například prvních 20).
* Personalizer trvá malý počet akcí se velké množství informací o nich a řadí je v reálném čase pro danou zajímavostí, zatímco většina generátory doporučení se používají pouze několik atributů o uživatelích, produkty a jejich interakce.
* Autonomně prozkoumat uživatelských předvoleb celou dobu, která budou poskytovat lepší výsledky, kde obsah se rychle mění, jako jsou novinky, živé události je navržená personalizer live obsah vytvořený komunitou, s denní aktualizací nebo sezónní obsah.

Běžné použití je vzít výstup doporučovací modul (například na top 20 produktů pro určité zákazníka) a použít je jako vstupní akcí pro Personalizer.

## <a name="next-steps"></a>Další postup

[Etickými & odpovídá použití](ethics-responsible-use.md).