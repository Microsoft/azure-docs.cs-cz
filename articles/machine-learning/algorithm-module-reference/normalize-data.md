---
title: 'Normalizovat data: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Normalizovat data v Azure Machine Learning transformovat datovou sadu prostřednictvím *normalizace*..
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: a740c81aa165e221bca19987c7b3c62da56b0402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477523"
---
# <a name="normalize-data-module"></a>Normalizovat datový modul

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k transformaci datové sady prostřednictvím *normalizace*.

Normalizace je technika často používaná jako součást přípravy dat pro strojové učení. Cílem normalizace je změnit hodnoty číselných sloupců v datové sadě tak, aby používaly společné měřítko, aniž by došlo k narušení rozdílů v rozsahu hodnot nebo ztrátě informací. Normalizace je také vyžadována pro některé algoritmy správně modelovat data.

Předpokládejme například, že vstupní datová sada obsahuje jeden sloupec s hodnotami v rozsahu od 0 do 1 a jiný sloupec s hodnotami v rozsahu od 10 000 do 100 000. Velký rozdíl v *měřítku* čísel může způsobit problémy při pokusu o kombinování hodnot jako prvků během modelování.

*Normalizace* se těmto problémům vyhýbá vytvořením nových hodnot, které udržují obecné rozdělení a poměry ve zdrojových datech, při zachování hodnot v měřítku použitém ve všech číselných sloupcích použitých v modelu.

Tento modul nabízí několik možností transformace číselných dat:

- Můžete změnit všechny hodnoty na měřítko 0-1 nebo transformovat hodnoty tak, že je budete reprezentovat jako percentilové, nikoli jako absolutní hodnoty.
- Normalizaci můžete použít na jeden sloupec nebo na více sloupců ve stejné datové sadě.
- Pokud potřebujete opakovat kanál nebo použít stejné kroky normalizace na jiná data, můžete kroky uložit jako normalizační transformaci a použít je na jiné datové sady, které mají stejné schéma.

> [!WARNING]
> Některé algoritmy vyžadují, aby data normalizována před trénování modelu. Jiné algoritmy provádět vlastní škálování dat nebo normalizace. Proto při výběru algoritmu strojového učení pro použití při vytváření prediktivní model, ujistěte se, že zkontrolujte požadavky na data algoritmu před použitím normalizace na trénovací data.

##  <a name="configure-normalize-data"></a>Konfigurace normalizovat data

Pomocí tohoto modulu můžete použít pouze jednu metodu normalizace najednou. Proto je stejná metoda normalizace použita pro všechny sloupce, které vyberete. Chcete-li použít různé metody normalizace, použijte druhou instanci **Normalize Data**.

1. Přidejte modul **Normalizovat data** do kanálu. Modul najdete v Azure Machine Learning v části **Transformace dat**v kategorii **Škálování a Snížit.**

2. Připojte datovou sadu, která obsahuje alespoň jeden sloupec všech čísel.

3. Pomocí voliče sloupců zvolte číselné sloupce, které chcete normalizovat. Pokud nezvolíte jednotlivé sloupce, jsou ve výchozím nastavení zahrnuty **všechny** sloupce číselného typu ve vstupu a stejný proces normalizace se použije na všechny vybrané sloupce. 

    To může vést k podivné výsledky, pokud zahrnete číselné sloupce, které by neměly být normalizovány! Vždy pečlivě zkontrolujte sloupce.

    Pokud nejsou zjištěny žádné číselné sloupce, zkontrolujte metadata sloupce a ověřte, zda datový typ sloupce je podporovaný číselný typ.

    > [!TIP]
    > Chcete-li zajistit, aby sloupce určitého typu byly k dispozici jako vstup, zkuste použít modul [Vybrat sloupce v datové sadě](./select-columns-in-dataset.md) před **normalizací dat**.

4. **Použít 0 pro konstantní sloupce při zaškrtnutí**: Tuto možnost vyberte, pokud libovolný číselný sloupec obsahuje jednu neměnnou hodnotu. Tím je zajištěno, že tyto sloupce nejsou použity v normalizačních operacích.

5. V rozevíracím seznamu **Metoda transformace** zvolte jednu matematickou funkci, která se použije na všechny vybrané sloupce. 
  
    - **Zscore**: Převede všechny hodnoty na z-skóre.
    
      Hodnoty ve sloupci jsou transformovány pomocí následujícího vzorce:  
  
      ![normalizace pomocí z&#45;skóre](media/module/aml-normalization-z-score.png)
  
      Střední hodnota a směrodatná odchylka se vypočítají pro každý sloupec zvlášť. Použije se směrodatná odchylka populace.
  
    - **MinMax**: Min-max normalizátor lineárně změní měřítko každého prvku do intervalu [0,1].
    
      Změna měřítka na interval [0,1] se provádí posunutím hodnot každého prvku tak, aby minimální hodnota byla 0, a poté vydělením novou maximální hodnotou (což je rozdíl mezi původními maximálními a minimálními hodnotami).
      
      Hodnoty ve sloupci jsou transformovány pomocí následujícího vzorce:  
  
      ![normalizace pomocí funkce min&#45;max](media/module/aml-normalization-minmax.png "AML_normalization-minmax")  
  
    - **Logistika**: Hodnoty ve sloupci jsou transformovány podle následujícího vzorce:

      ![vzorec pro normalizaci logistickou funkcí](media/module/aml-normalization-logistic.png "AML_normalization-logistická")  
  
    - **LogNormal**: Tato možnost převede všechny hodnoty na logaritmicko-normální měřítko.
  
      Hodnoty ve sloupci jsou transformovány pomocí následujícího vzorce:
  
      ![protokol vzorců&#45;normální distribuci](media/module/aml-normalization-lognormal.png "AML_normalization-logaritmicko-normální")
    
      Zde jsou parametry rozdělení, vypočtené empiricky z dat jako odhady maximální pravděpodobnosti, pro každý sloupec zvlášť.  
  
    - **TanH**: Všechny hodnoty jsou převedeny na hyperbolickou tečnu.
    
      Hodnoty ve sloupci jsou transformovány pomocí následujícího vzorce:
    
      ![normalizace pomocí funkce tanh](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. Odešlete kanál nebo poklepejte na modul **Normalizovat data** a vyberte **Spustit vybrané**. 

## <a name="results"></a>Výsledky

Modul **Normalize Data** generuje dva výstupy:

- Transformované hodnoty zobrazíte pravým tlačítkem myši na modul a vyberte možnost **Vizualizovat**.

    Ve výchozím nastavení jsou hodnoty transformovány na místě. Pokud chcete porovnat transformované hodnoty s původními hodnotami, použijte modul [Přidat sloupce](./add-columns.md) k opětovnému sloučení datových sad a zobrazení sloupců vedle sebe.

- Chcete-li uložit transformaci, abyste mohli použít stejnou metodu normalizace na jinou datovou sadu, vyberte modul a vyberte **Registrovat datovou sadu** na kartě **Výstupy** v pravém panelu.

    Potom můžete načíst uložené transformace ze **skupiny Transformace** levého navigačního podokna a použít je na datovou sadu se stejným schématem pomocí [použít transformaci](apply-transformation.md).  


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 