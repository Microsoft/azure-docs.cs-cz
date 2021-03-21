---
title: Trénování modelu Vowpal Wabbit
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí modulu výuka pro dostupné model vytvořit model strojového učení s použitím instance pro dostupné.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 70d0fc456b3697e3c74a5ec45cc936a02b77e591
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657650"
---
# <a name="train-vowpal-wabbit-model"></a>Trénování modelu Vowpal Wabbit
Tento článek popisuje, jak pomocí modulu **pro (Learning dostupné model** ) v Návrháři Azure Machine Learning vytvořit model strojového učení pomocí pro dostupné.  

Pokud chcete používat pro dostupné pro Machine Learning, naformátujte zadání podle požadavků pro dostupné a připravte data v požadovaném formátu. Pomocí tohoto modulu můžete zadat argumenty příkazového řádku pro dostupné. 

Při spuštění kanálu je instance pro dostupné načtena do doby běhu experimentu spolu se zadanými daty. Po dokončení školení je model serializován zpátky do pracovního prostoru. Model můžete použít hned k určení skóre dat. 

Chcete-li postupně vyškolit stávající model pro nová data, připojte uložený model k **předem připravenému portu Input dostupné modelu** **pro dostupné** a přidejte nová data do druhého vstupního portu.  

## <a name="what-is-vowpal-wabbit"></a>Co je pro dostupné?  

Pro dostupné (VW) je rychlé paralelní rozhraní Machine Learning, které bylo vyvinuto pro distribuované výpočetní prostředí pomocí Yahoo! Výzkum. Později bylo přenesené do systému Windows a přizpůsobené Jan Langford (Microsoft Research) pro vědecké výpočty v paralelních architekturách.  

Mezi funkce služby pro dostupné, které jsou důležité pro strojové učení, patří průběžné učení (online učení), snižování dimenzionálního a interaktivní učení. Pro dostupné je také řešení problémů, když nemůžete přizpůsobit modelová data do paměti.  

Hlavní uživatelé pro dostupné jsou odborníci na data, kteří dříve používali architekturu pro úlohy strojového učení, jako je klasifikace, regrese, modelování tématu nebo vytváření matic. Obálka Azure pro pro dostupné má velmi podobné charakteristiky výkonu pro místní verzi, takže můžete využívat výkonné funkce a nativní výkon pro dostupné a snadno publikovat školený model jako provozní službu.  

Modul [hash funkcí](feature-hashing.md) obsahuje také funkce, které poskytuje pro dostupné, která umožňuje transformovat textové datové sady do binárních funkcí pomocí algoritmu hash.  

## <a name="how-to-configure-vowpal-wabbit-model"></a>Jak nakonfigurovat model dostupné pro  

Tato část popisuje, jak vytvořit nový model a jak přidat nová data do existujícího modelu.

Na rozdíl od jiných modulů v Návrháři tento modul určuje parametry modulu a navlakuje model. Pokud máte existující model, můžete ho přidat jako volitelný vstup pro přírůstkové učení modelu.

+ [Příprava vstupních dat v jednom z požadovaných formátů](#prepare-the-input-data)
+ [Výuka nového modelu](#create-and-train-a-vowpal-wabbit-model)
+ [Přírůstkové učení existujícího modelu](#retrain-an-existing-vowpal-wabbit-model)

### <a name="prepare-the-input-data"></a>Příprava vstupních dat

Aby bylo možné vytvořit model pomocí tohoto modulu, vstupní datová sada musí obsahovat jeden textový sloupec v jednom ze dvou podporovaných formátů: **SVMLight** nebo **VW**. To neznamená, že pro dostupné analyzuje pouze textová data, a proto musí být v požadovaném formátu textového souboru připraveny jenom tyto funkce a hodnoty.  

Data lze číst ze dvou typů datových sad, datové sady souborů nebo tabulkové sady dat. Obě tyto datové sady musí být ve formátu SVMLight nebo VW. Datový formát pro dostupné má výhodu, že nepotřebuje sloupcový formát, který šetří místo při práci s zhuštěnými daty. Další informace o tomto formátu najdete na [stránce wikiwebu pro dostupné](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format).  

### <a name="create-and-train-a-vowpal-wabbit-model"></a>Vytvoření a výuka modelu pro dostupné

1. Do experimentu přidejte modul **dostupné modelu výuky pro** . 
  
2. Přidejte datovou sadu školení a připojte ji k **školicím datům**. Pokud je datová sada školení adresářem, který obsahuje soubor školicích dat, zadejte název souboru školicích dat s **názvem souboru školicích** dat. Pokud je datová sada školení jedním souborem, ponechte **název souboru školicích dat** prázdný.

3. Do textového pole **argumenty VW** zadejte argumenty příkazového řádku pro spustitelný soubor pro dostupné.

     Můžete například přidat *`–l`* , chcete-li určit míru učení, nebo *`-b`* Označit počet bitů hash.  

     Další informace najdete v části [parametry dostupné pro](#supported-and-unsupported-parameters) .  

4. **Název souboru školicích dat**: zadejte název souboru, který obsahuje vstupní data. Tento argument se používá pouze v případě, že datová sada školení je adresář.

5. **Zadejte typ souboru**: Určete, který formát vaše školicí data používá. Pro dostupné podporuje tyto dva formáty vstupních souborů:  

    - **VW** představuje interní formát používaný pro dostupné. Podrobnosti najdete na [stránce wiki pro dostupné](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) . 
    - **SVMLight** je formát používaný některými jinými nástroji pro strojové učení. 

6. **Výstupní čitelný soubor modelu**: tuto možnost vyberte, pokud chcete, aby modul uložil čitelný model do záznamů spuštění. Tento argument odpovídá `--readable_model` parametru v příkazovém řádku VW.  

7. **Výstupní invertovaná hodnota hash**: tuto možnost vyberte, pokud chcete, aby modul ukládal inverzní funkci hash do jednoho souboru v záznamech spuštění. Tento argument odpovídá `--invert_hash` parametru v příkazovém řádku VW.  

8. Odešlete kanál.

### <a name="retrain-an-existing-vowpal-wabbit-model"></a>Přeučení stávajícího modelu dostupné pro

Pro dostupné podporuje přírůstkové školení přidáním nových dat do existujícího modelu. Existují dva způsoby, jak získat existující model pro přeškolení:

+ Použijte výstup jiného modulu **dostupné modelu pro** ve stejném kanálu.  
  
+ Vyhledejte uložený model v kategorii **datové sady** v levém navigačním podokně návrháře a přetáhněte ho do svého kanálu.  

1. Přidejte do svého kanálu modul **dostupné modelu výuky pro** .  
2. Propojte dříve vyškolený model s předem vyškoleným vstupním portem **pro dostupné modelu** modulu.
3. Připojte nové školicí údaje ke vstupnímu portu **školicích dat** modulu.
4. V podokně Parametry **modelu pro dostupné pro analýzu** zadejte formát nových školicích dat a také název souboru dat školení, pokud je vstupní datovou sadou adresář.
5. Vyberte **výstupní soubor čitelného modelu** a **výstupní možnosti souboru hodnoty hash** , pokud je potřeba uložit odpovídající soubory do záznamů spuštění.

6. Odešlete kanál.  
7. Vyberte modul a v pravém podokně vyberte **Registrovat datovou sadu** na kartě **výstupy a protokoly** , abyste zachovali aktualizovaný model v pracovním prostoru Azure Machine Learning.  Pokud nezadáte nový název, aktualizovaný model přepíše existující uložený model.

## <a name="results"></a>Výsledky

+ K vygenerování skóre z modelu použijte [model skóre pro dostupné](score-vowpal-wabbit-model.md).

> [!NOTE]
> Pokud potřebujete nasadit model vyškolený v návrháři, ujistěte se, že je místo **modelu skóre** připojen ke vstupu [výstupního modulu webové služby](web-service-input-output.md) v kanálu odvození [pro model dostupné](score-vowpal-wabbit-model.md) .

## <a name="technical-notes"></a>Technické poznámky

Tato část obsahuje podrobné informace o implementaci, tipy a odpovědi na nejčastější dotazy.

### <a name="advantages-of-vowpal-wabbit"></a>Výhody pro dostupné

Pro dostupné poskytuje extrémně rychlé učení nad nelineárními funkcemi, jako je n-gram.  

Pro dostupné využívá techniky *učení online* , jako je například stochastického gradient klesání (SGD), aby se vešel na jeden záznam v jednom okamžiku. Proto provádí iteraci velmi rychle nad nezpracovanými daty a může vyvíjet dobrý předpověď rychleji než většinu ostatních modelů. Tento přístup také zabraňuje nutnosti číst všechna školicí data do paměti.  

Pro dostupné převádí všechna data na hodnoty hash, nikoli jenom textová data, ale i jiné proměnné kategorií. Použití hodnot hash usnadňuje vyhledávání regresních vah, což je klíčové pro efektivní klesání gradientu stochastického.  

###  <a name="supported-and-unsupported-parameters"></a>Podporované a nepodporované parametry 

Tato část popisuje podporu pro parametry příkazového řádku pro dostupné v Návrháři Azure Machine Learning. 

Obecně se podporují všechny, ale jenom omezené sady argumentů. Úplný seznam argumentů získáte pomocí [stránky wikiwebu pro dostupné](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).    

Následující parametry nejsou podporovány:

-   Možnosti vstupu a výstupu zadané v [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     Tyto vlastnosti jsou již modulem konfigurovány automaticky.  
  
-   Kromě toho je zakázána jakákoli možnost, která generuje více výstupů nebo přijímá více vstupů. Mezi ně patří *`--cbt`* , *`--lda`* a *`--wap`* .  
  
-   Podporují se jenom kontrolní algoritmy pro učení pod dohledem. Proto tyto možnosti nejsou podporovány:, atd *`–active`* `--rank` *`--search`* . 

### <a name="restrictions"></a>Omezení

Vzhledem k tomu, že cílem služby je podpora zkušených uživatelů pro dostupné, musí být vstupní data připravená předem, a to pomocí formátu pro dostupné Native text, nikoli pomocí formátu DataSet používaného jinými moduly.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
