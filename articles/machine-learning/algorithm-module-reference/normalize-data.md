---
title: 'Normalizovat data: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul Normalized data v Azure Machine Learning k transformaci datové sady prostřednictvím *normalizace*.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: de0a23ca9dea210d91fe259b06622226549ba3b2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90890446"
---
# <a name="normalize-data-module"></a>Normalizovat datový modul

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Tento modul použijte k transformaci datové sady prostřednictvím *normalizace*.

Normalizace je technika, která se často používá jako součást přípravy dat pro strojové učení. Cílem normalizace je změnit hodnoty číselného sloupce v datové sadě tak, aby používaly společný rozsah, aniž by došlo k narušení rozdílů v rozsahu hodnot nebo ztrátě informací. Normalizace je také nutná pro některé algoritmy pro správné modelování dat.

Předpokládejme například, že vaše vstupní datová sada obsahuje jeden sloupec s hodnotami od 0 do 1 a další sloupec s hodnotami od 10 000 do 100 000. Velký rozdíl v *rozsahu* čísel může způsobit problémy při pokusu o kombinování hodnot jako funkcí během modelování.

*Normalizace* zabraňuje těmto problémům vytvořením nových hodnot, které udržují obecnou distribuci a poměry ve zdrojových datech, a přitom udržuje hodnoty v rámci měřítka aplikované napříč všemi číselnými sloupci používanými v modelu.

Tento modul nabízí několik možností transformace číselných dat:

- Můžete změnit všechny hodnoty na 0-1ový rozsah nebo transformovat hodnoty tak, že je zastupuje jako percentil, nikoli jako absolutní hodnoty.
- Normalizaci můžete aplikovat na jeden sloupec nebo na více sloupců ve stejné datové sadě.
- Pokud potřebujete kanál opakovat nebo použít stejný postup normalizace na jiná data, můžete tyto kroky Uložit jako transformaci normalizace a použít ji pro jiné datové sady, které mají stejné schéma.

> [!WARNING]
> Některé algoritmy vyžadují, aby byla data normalizována před školením modelu. Jiné algoritmy provádějí vlastní škálování dat nebo normalizaci. Proto když zvolíte algoritmus strojového učení, který se použije při vytváření prediktivního modelu, nezapomeňte si projít požadavky na data algoritmu před použitím normalizace na školicí data.

##  <a name="configure-normalize-data"></a>Konfigurace normalizování dat

Pomocí tohoto modulu můžete najednou použít jenom jednu metodu normalizace. Proto se stejná metoda normalizace použije na všechny sloupce, které vyberete. Chcete-li použít jiné metody normalizace, použijte druhou instanci **normalizovaná data**.

1. Přidejte modul **dat Normalize** do vašeho kanálu. Modul můžete najít v Azure Machine Learning v části **transformace dat** v kategorii **škálování a snížení** .

2. Připojte datovou sadu, která obsahuje alespoň jeden sloupec se všemi čísly.

3. Pomocí voliče sloupců vyberte číselné sloupce, které se mají normalizovat. Pokud si nejste zvolili jednotlivé sloupce, jsou ve výchozím nastavení zahrnuty **všechny** sloupce číselného typu ve vstupu a stejný proces normalizace se použije na všechny vybrané sloupce. 

    Pokud zahrnete číselné sloupce, které by neměly být normalizovány, může to vést k neobvyklým výsledkům. Vždy pečlivě zkontrolujte sloupce.

    Pokud nejsou zjištěny žádné číselné sloupce, zkontrolujte metadata sloupce a ověřte, zda je datovým typem sloupce podporovaný numerický typ.

    > [!TIP]
    > Chcete-li zajistit, aby jako vstup byly zadány sloupce určitého typu, zkuste použít modul [Výběr sloupců v datové sadě](./select-columns-in-dataset.md) a teprve potom **normalizovat data**.

4. **Při zaškrtnutí použít 0 pro konstantní sloupce**: tuto možnost vyberte, pokud libovolný číselný sloupec obsahuje jedinou nezměněnou hodnotu. Tím se zajistí, že se tyto sloupce nepoužijí v normalizaci operací.

5. V rozevíracím seznamu **Metoda transformace** vyberte jednu matematickou funkci, která se použije pro všechny vybrané sloupce. 
  
    - **Zscore**: převede všechny hodnoty na z-skóre.
    
      Hodnoty ve sloupci jsou transformovány pomocí následujícího vzorce:  
  
      ![normalizace s využitím skóre z&#45;](media/module/aml-normalization-z-score.png)
  
      Střední a směrodatná odchylka se počítá pro každý sloupec samostatně. Použije se standardní odchylka populace.
  
    - **MinMax**: normalizace min-max lineárně mění škálu každé funkce na interval [0, 1].
    
      Změna měřítka intervalu [0, 1] se provádí posunutím hodnot každé funkce tak, aby minimální hodnota byla 0 a pak se vydělí novou maximální hodnotou (což je rozdíl mezi původní maximální a minimální hodnotou).
      
      Hodnoty ve sloupci jsou transformovány pomocí následujícího vzorce:  
  
      ![normalizace pomocí funkce min&#45;max](media/module/aml-normalization-minmax.png "AML_normalization – minmax")  
  
    - **Logistické**: hodnoty ve sloupci se transformují pomocí následujícího vzorce:

      ![vzorec pro normalizaci pomocí logistické funkce](media/module/aml-normalization-logistic.png "AML_normalization – logistické")  
  
    - **Normální**: Tato možnost převede všechny hodnoty na logaritmicko-normální škálování.
  
      Hodnoty ve sloupci jsou transformovány pomocí následujícího vzorce:
  
      ![protokol vzorců&#45;normální distribuce](media/module/aml-normalization-lognormal.png "AML_normalization – normální")
    
      V této části jsou parametry distribuce vypočítané z dat jako maximální pravděpodobnost odhadu pro každý sloupec samostatně.  
  
    - **Tanh –**: všechny hodnoty jsou převedeny na hyperbolický tangens.
    
      Hodnoty ve sloupci jsou transformovány pomocí následujícího vzorce:
    
      ![normalizace pomocí funkce tanh –](media/module/aml-normalization-tanh.png "AML_normalization – tanh –")

6. Odešlete kanál, nebo poklikejte na modul **Normalized data** a vyberte **Spustit vybrané**. 

## <a name="results"></a>Výsledky

Modul **Normalize data** generuje dva výstupy:

- Chcete-li zobrazit transformované hodnoty, klikněte pravým tlačítkem na modul a vyberte **vizualizovat**.

    Ve výchozím nastavení se hodnoty transformují na místě. Chcete-li porovnat transformované hodnoty s původními hodnotami, použijte modul [Přidat sloupce](./add-columns.md) pro rekombinaci datových sad a zobrazení sloupců vedle sebe.

- Chcete-li transformaci uložit, abyste mohli použít stejnou metodu normalizace na jinou datovou sadu, vyberte modul a na kartě **výstup** v pravém panelu vyberte možnost **Registrovat datovou sadu** .

    Uloženou transformaci pak můžete načíst ze skupiny **transformes** v levém navigačním podokně a použít ji pro datovou sadu se stejným schématem pomocí [transformace použít](apply-transformation.md).  


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 