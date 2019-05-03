---
title: 'Úprava metadat: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu upravit Metadata ve službě Azure Machine Learning, chcete-li změnit metadata, která souvisí s sloupců v datové sadě.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: cfee607aca155b6cf68e5bddc40eb9c752df5e34
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028812"
---
# <a name="edit-metadata-module"></a>Upravit Metadata modulu

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k změnit metadata, která souvisí s sloupců v datové sadě. Po dokončení se změní hodnota a datový typ datové sady **upravit Metadata** modulu. 
 
Typické metadat změny mohou zahrnovat:
  
+ Zpracuje logická nebo číselné sloupce jako kategorických hodnot  
  
+ Určující, který sloupec obsahuje *třídy* popisek nebo hodnoty, které chcete kategorizovat nebo předpověď  
  
+ Označení sloupce jako funkce
  
+ Změna data a času hodnoty na číselnou hodnotu, nebo naopak  
  
+ Přejmenování sloupců
  
 [Upravit Metadata použít kdykoli potřebujete změnit definici sloupce, obvykle podle požadavků pro příjem dat modul. Například může pracovat pouze s konkrétní datové typy některé moduly, nebo vyžadují příznaky u sloupců, jako například `IsFeature` nebo `IsCategorical`.  
  
 Po provedení požadované operace, můžete obnovit metadata do původního stavu. 
  
## <a name="configure-edit-metadata"></a>Konfigurace upravit Metadata
  
1.  Ve službě Azure Machine Learning, přidejte [upravit Metadata](./edit-metadata.md) modulu do experimentu a připojte se datové sady, které chcete aktualizovat. Najdete ho pod **transformace dat**v **manipulovat** kategorie.
  
2.  Klikněte na tlačítko **spusťte selektor sloupců** a zvolte sloupec nebo sadu sloupců pro práci s. Můžete zvolit sloupce jednotlivě podle názvu nebo indexu, nebo můžete použít skupiny sloupců, podle typu.  
  
3.  Vyberte **datový typ** možnost, pokud je potřeba přiřadit jiný datový typ vybraného sloupce. Změna dat typu mohou být potřebné pro určité operace: například, pokud vaše zdrojová datová sada má čísla zpracována jako text, musíte je změnit na číselný datový typ před použitím matematických operací. 

    + Tyto datové typy podporované `String`, `Integer`, `Double`, `Boolean`, `DateTime`. 

    + Pokud se vybere více sloupců, musíte použít změny metadat **všechny** vybrané sloupce. Řekněme například, že zvolíte 2 až 3 číselné sloupce. Můžete je změnit všechny na datový typ string a přejmenovat v rámci jedné operace. Z plovoucí na celé nelze však změnit jeden sloupec pro datový typ řetězce a další sloupec.
  
    + Pokud nezadáte nového datového typu, sloupec metadat se nezmění. 
    
    + Datový typ sloupce a hodnoty se změní po provedení [upravit Metadata](./edit-metadata.md) operace. Kdykoli můžete obnovit původní datový typ pomocí [upravit Metadata](./edit-metadata.md) resetovat datový typ sloupce.  

    > [!NOTE]
    > Pokud změníte jakýkoli typ číslo **data a času** zadejte, ponechte **formátu data a času** prázdné pole. V současné době není možné zadat cílový formát data.  

      
4.  Vyberte **zařazené do kategorií** možnost určit, že hodnoty ve vybraných sloupcích považovat za kategorií. 

    Může například obsahovat sloupec obsahující čísla, 0,1 a 2, ale vědět, že čísla ve skutečnosti rozumí "Smoker", "Non-smoker" a "Neznámý". V takovém případě označením sloupce jako zařazené do kategorií můžete zajistit, že nejsou použity hodnoty v číselné výpočty, pouze data skupiny. 
  
5.  Použití **pole** možnost, pokud chcete změnit způsob, Azure Machine Learning používá data v modelu.

    + **Funkce**: Tuto možnost použijte k nastavení příznaku sloupec jako funkci, pro použití s moduly, které pracují pouze na sloupce. Ve výchozím nastavení jsou považovány za funkce původně všechny sloupce.  
  
    + **Popisek**: Tuto možnost použijte k označení popisku (označované také jako předvídatelný atribut, nebo Cílová proměnná). Mnoho modulů vyžadovat, že nejméně jedna (a pouze jeden) popisek sloupce nacházet v datové sadě. 
    
        V mnoha případech lze odvodit Azure Machine Learning, že sloupec obsahuje popisek třídy, ale tak, že nastavíte tato metadata je zajistit, že se správně identifikuje sloupce. Nastavení této možnosti nezmění data hodnoty pouze způsobem, že některé algoritmů strojového učení zpracovat data.
  

  
    > [!TIP]
    >  Máte dat, který se nevejde do následujících kategorií?  Vaše datová sada může například obsahovat hodnoty, jako je jedinečné identifikátory, které nejsou užitečné jako proměnné. ID v některých případech může způsobovat problémy při použití v modelu. 
    >   
    >  Naštěstí "pod pokličkou" Azure Machine Learning uchovává všechna vaše data, takže není nutné odstranit tyto sloupce z datové sady. Když budete potřebovat k provádění operací na některé speciální sadu sloupců, odeberte všechny ostatní sloupce dočasně pomocí [výběr sloupců v datové sadě](./select-columns-in-dataset.md) modulu. Později můžete sloučit sloupce zpět do datové sady s použitím [přidat sloupce](./add-columns.md) modulu.  
  
6. Pomocí následujících možností obnovení metadat na výchozí hodnoty a vymažte předchozích výběrů.  
  
    + **Vymazat funkce**: Tuto možnost použijte, chcete-li odebrat příznak funkce.  
  
         Vzhledem k tomu, že všechny sloupce jsou zpočátku považovány za funkce pro moduly, které provádějí matematické operace, můžete potřebovat pomocí této možnosti můžete zabránit číselné sloupce jsou považovány za proměnné.
  
    + **Vymazat popisek**: Tuto možnost použijte, chcete-li odebrat **popisek** metadata ze zadaného sloupce.  
  
    + **Vymazat skóre**: Tuto možnost použijte, chcete-li odebrat **skóre** metadata ze zadaného sloupce.  
  
         Schopnost explicitně označit jako skóre aktuálně není k dispozici ve službě Azure Machine Learning. Některé operace však způsobit sloupec se označí jako skóre interně. Vlastní modul R může také výstupní hodnoty skóre.
  
  
7.  Pro **nové názvy sloupců**, zadejte nový název vybraný sloupec nebo sloupce.  
  
    + Názvy sloupců můžete použít jenom znaky, které podporuje UTF-8 kódování. Prázdné řetězce, hodnoty Null nebo názvy, které obsahuje jenom mezery nejsou povolené.  
  
    + Přejmenování více sloupců, zadejte názvy jako čárkami oddělený seznam v pořadí podle indexy sloupců.  
  
    + Všechny vybrané sloupce musí být přejmenován. Nejde vynechat nebo přeskočit sloupce.  
  
  
8.  Spusťte experiment.  

## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 