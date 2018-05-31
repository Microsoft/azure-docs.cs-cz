---
title: Obrázek klasifikace pomocí CNTK uvnitř Azure Machine Learning Workbench | Microsoft Docs
description: Cvičení, hodnocení a nasadit vlastní image klasifikace model pomocí Azure ML Workbench.
services: machine-learning
documentationcenter: ''
author: PatrickBue
ms.author: pabuehle
manager: mwinkle
ms.reviewer: marhamil, mldocs, garyericson, jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/17/2017
ms.openlocfilehash: 8bf5cd802198cba48a99c029d0c75c25dd5f6d84
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31607858"
---
# <a name="image-classification-using-azure-machine-learning-workbench"></a>Pomocí Azure Machine Learning Workbench klasifikace bitové kopie

Image klasifikace přístupy lze vyřešit velký počet počítače vize problémy.
Mezi ně patří vytváření modelů, které odpovědi na otázky, jako: *se OBJEKT nachází v bitové kopii?* kde objektu může být například *PSA*, *car*, nebo  *dodávat*. Nebo jako složitější dotazy: *jaké třída závažnosti nákazy oko je evinced retinal kontrolou tento pacienta?*.

Tento kurz adresy řešení těchto problémů. Ukážeme, jak pro trénování, hodnocení a nasadit vlastní image klasifikace modelu pomocí [Microsoft kognitivní Toolkit (CNTK) ](https://docs.microsoft.com/cognitive-toolkit/) hloubkové informací.
Příklad bitové kopie jsou k dispozici, ale čtečka může také přineste vlastní datovou sadu a cvičení vlastní vlastní modely.

Řešení vyžaduje tradičně odborných znalostí ručně identifikovat a implementovat takzvané vize počítače *funkce*, který zvýrazněte požadované informace do bitových kopií.
Tento přístup s ručně změnit v 2012 s famous [AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) [1] přímým učení papír a v současné době, hluboké Neuronové sítě (DNN) se používají k automaticky vyhledá tyto funkce.
DNNs vedla k obrovské zlepšování v poli, ne jenom pro klasifikaci bitové kopie, ale také na jiné počítače vize problémy například zjišťování objektu a podobnosti bitové kopie.


## <a name="link-to-the-gallery-github-repository"></a>Propojit s úložišti GitHub Galerie
[https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK](https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK)

## <a name="overview"></a>Přehled

V tomto kurzu je rozdělená do tří částí:

- Část 1 ukazuje, jak pro trénování, hodnocení a nasazení bitové kopie systému klasifikace pomocí předem vyškolení DNN jako featurizer a cvičení SVM na její výstup.
- Část 2 pak ukazuje, jak zlepšit přesnost, například upřesnění DNN než použití jako pevné featurizer.
- Část 3 obsahuje informace o použití vlastní datovou sadu místo zadané příklad bitové kopie, a v případě potřeby, jak vytvořit vlastní datovou sadu podle oškrabávání Image ze sítě.

Při předchozí zkušenosti s machine learning a CNTK není vyžadováno, je užitečné pro pochopení základních zásad. Přesnost čísla, školení čas atd hlášené v tomto kurzu jsou jenom pro referenci a skutečnými hodnotami při spuštění kódu skoro určitě liší.


## <a name="prerequisites"></a>Požadavky

Požadavky na spuštění v tomto příkladu jsou následující:

1. [Účet Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici).
2. [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) následující [Průvodce instalací úvodní](../service/quickstart-installation.md) k instalaci programu a vytvořit pracovní prostor.  
3. Počítače s Windows. Operačního systému Windows je nutné, protože nástroje Workbench podporuje pouze systém Windows a systému MacOS při kognitivní Toolkit společnosti Microsoft (který používáme jako přímý learning knihovny) podporuje pouze systém Windows a Linux.
4. Vyhrazené GPU není nutné provést školení SVM část 1, ale je potřeba pro upřesnění z DNN popsané v části 2. Chybí silné grafického procesoru, chcete cvičení na více grafickými procesory nebo nemají počítače s Windows, můžete použít Azure hloubkové Learning virtuální počítač s operačním systémem Windows. V tématu [sem](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning) průvodce kliknutím 1 nasazení. Po nasazení připojit k virtuálnímu počítači prostřednictvím připojení ke vzdálené ploše, nainstalujte Workbench existuje a spouštění kódu místně z virtuálního počítače.
5. Různé knihovny Python, jako je například OpenCV je potřeba nainstalovat. Klikněte na tlačítko *spusťte příkazový řádek* z *souboru* nabídky na Workbench a spusťte následující příkazy pro instalaci tyto závislosti:  
    - `pip install https://cntk.ai/PythonWheel/GPU/cntk-2.2-cp35-cp35m-win_amd64.whl`  
    - `pip install opencv_python-3.3.1-cp35-cp35m-win_amd64.whl` Po stažení OpenCV hodinových z http://www.lfd.uci.edu/~gohlke/pythonlibs/ (přesný název souboru a verze můžete změnit)
    - `conda install pillow`
    - `pip install -U numpy`
    - `pip install bqplot`
    - `jupyter nbextension enable --py --sys-prefix bqplot`
    - `jupyter nbextension enable --py widgetsnbextension`

### <a name="troubleshooting--known-bugs"></a>Řešení potíží / známé chyby
- Grafického procesoru je potřeba pro část 2 a v opačném případě "Batch normalizaci školení na procesoru není dosud implementována" je vyvolána chyba při pokusu o Upřesnit DNN.
- Chyby z důvodu nedostatku paměti při školení DNN předejít zmenšení velikosti minibatch (proměnná `cntk_mb_size` v `PARAMETERS.py`).
- Kód byl testován pomocí CNTK 2.2 a by také spustit v starší (až v2.0) a novější verze bez nebo pouze malé změny.
- V době psaní Azure Machine Learning Workbench měla problémy s zobrazující poznámkových bloků větší než 5 MB. Poznámkové bloky této velké velikosti může dojít, pokud poznámkového bloku je uložit s všechny buňky zobrazí výstup. Pokud k této chybě dojde, otevřete příkazový řádek z nabídky Soubor uvnitř nástroje Workbench, provést `jupyter notebook`, otevřete Poznámkový blok, zrušte všechny výstup a uložte poznámkového bloku. Po provedení těchto kroků, poznámkového bloku bude správně uvnitř Azure Machine Learning Workbench znovu otevřít.
- Všechny skripty v této ukázce muset provést místně a ne na například docker vzdáleného prostředí. Všech poznámkových bloků je nutné provést s jádra nastavena na jádra místní projektu s názvem "PROJECTNAME místní" (například "myImgClassUsingCNTK místní").

    
## <a name="create-a-new-workbench-project"></a>Vytvoření nového projektu workbench

Chcete-li vytvořit nový projekt v tomto příkladu jako šablona:
1.  Otevřete aplikaci Azure Machine Learning Workbench.
2.  Na **projekty** klikněte na tlačítko **+** přihlásit a vybrat **nový projekt**.
3.  V **vytvořit nový projekt** podokně, vyplňte informace pro nový projekt.
4.  V **šablony projektů vyhledávání** vyhledávacího pole zadejte "Image klasifikaci" a vyberte šablonu.
5.  Klikněte na možnost **Vytvořit**.

Provedením těchto kroků vytvoří strukturu projektu vidíte níže. Adresář projektu je omezen na být méně než 25 MB, protože Azure Machine Learning Workbench vytvoří kopii této složky po každé spuštění (Chcete-li povolit historie spouštění). Proto všechny dočasné soubory a bitové kopie jsou uloženy na a z adresáře *~/Desktop/imgClassificationUsingCntk_data* (označované jako *DATA_DIR* v tomto dokumentu).

  Složka| Popis
  ---|---
  aml_config/|                           Adresář, který obsahuje konfigurační soubory, které Azure Machine Learning Workbench
  knihovny nebo|                              Adresář, který obsahuje všechny podpůrné funkce Python a Jupyter
  poznámkové bloky /|                              Adresář, který obsahuje všechny poznámkové bloky
  prostředky nebo|                              Adresář obsahující všechny prostředky (pro příklad adresy url způsobem obrázků)
  skripty nebo|                              Adresář, který obsahuje všechny skripty.
  PARAMETERS.py|                       Skript v jazyce Python zadání všech parametrů
  Readme.MD|                           Tento dokument readme


## <a name="data-description"></a>Popis dat

Tento kurz používá jako příklad spuštěn dataset texture oblečení horní text tvořený až 428 bitové kopie. Každé bitové kopie je označena jako jednu ze tří různých textury (leopard desítkovém, prokládané,). Jsme udržováno počet bitových kopií malé tak, aby v tomto kurzu lze rychle provést. Kód, ale je dobře otestované a funguje s desítkami tisíc bitové kopie nebo více. Všechny bitové kopie byly oškrábána pomocí hledání bitové kopie Bingu a poznámky popsaná v dolním [část 3](#using-a-custom-dataset). Bitovou kopii adresy URL s jejich příslušné atributy, které jsou uvedeny v */resources/fashionTextureUrls.tsv* souboru.

Skript `0_downloadData.py` stáhne všechny bitové kopie do *DATA_DIR nebo bitové kopie nebofashionTexture/* adresáře. Některé 428 adres URL jsou pravděpodobně poškozená. To není problém a právě znamená, že máme něco menší počet bitových kopií pro trénování a testování. Všechny skripty v této ukázce muset provést místně a ne na například docker vzdáleného prostředí.

Následující obrázek znázorňuje příklady pro atributy s tečkami (vlevo), prokládané (střední) a leopard (vpravo). Poznámky měla provést podle položky oblečení horní textu.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/examples_all.jpg"  alt="alt text" width="700">
</p>


## <a name="part-1---model-training-and-evaluation"></a>Část 1 - Model trénování a hodnocení

V první části tohoto kurzu jsme jsou cvičení systém, který používá, ale ne upravovat, předem vyškolení hluboké neuronové sítě. Tuto předem vyškolení DNN slouží jako featurizer a lineární SVM je vycvičena k předvídání atribut (s tečkami, prokládaný nebo leopard) pro danou bitovou kopii.

Nyní jsme popsané v podrobností, krok za krokem a zobrazení, které skripty muset provést tento postup. Doporučujeme zkontrolovat soubory, které se zapisují a kde se zapisují do po dokončení každého kroku.

Jsou zadány všechny důležité parametry a krátký vysvětlení dostupné na jednom místě: `PARAMETERS.py` souboru.




### <a name="step-1-data-preparation"></a>Krok 1: Příprava dat
`Script: 1_prepareData.py. Notebook: showImages.ipynb`

Poznámkového bloku `showImages.ipynb` lze použít k vizualizaci bitové kopie a opravte jejich poznámky podle potřeby. Pokud chcete spustit poznámkového bloku, otevřete ho v Azure Machine Learning Workbench, klikněte na "spustit poznámkového bloku Server" Pokud tato možnost se zobrazí, změňte jádra místní projektu s názvem "PROJECTNAME místní" (například "myImgClassUsingCNTK místní"), a potom spusťte všechny buňky v Poznámkový blok. Pokud dojde k chybě nesouhlasících Poznámkový blok je příliš velký, který se má zobrazit, najdete v části řešení potíží v tomto dokumentu.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showImages.jpg" alt="alt text" width="700"/>
</p>

Teď spustit skript s názvem `1_prepareData.py`, které přiřadí všechny bitové kopie do buď školení nastavena nebo test. Toto přiřazení se vzájemně vylučují – žádný obrázek školení je také používají pro testování nebo naopak. Ve výchozím nastavení náhodné 75 % bitových kopií z každé třídy atributů přiřazené k školení a zbývající 25 % jsou přiřazeny k testování. Všechna data generované skript se ukládají do *DATA_DIR neboproc/fashionTexture/* složky.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_1_white.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-2-refining-the-deep-neural-network"></a>Krok 2: Upřesnění hluboké Neuronové sítě
`Script: 2_refineDNN.py`

Jak je popsáno jsme v rámci 1 v tomto kurzu, je udržováno předem vyškolení DNN pevné (to znamená, není přesnějších). Ale název skriptu `2_refineDNN.py` je stále načtenou předem vyškolení provedení část 1, [ResNet](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) [2] modelu a změní ho, například umožňující vyšší rozlišení obrázku vstupní. Tento krok je rychlý (v sekundách) a nevyžaduje grafického procesoru.

V části 2 kurzu, změny PARAMETERS.py souboru příčiny `2_refineDNN.py` skript, který i Upřesnit předem vyškolení DNN. Ve výchozím nastavení spustíme 45 epoch školení během vylepšení.

V obou případech je poslední model pak zapsán do souboru *DATA_DIR/proc/fashionTexture/cntk_fixed.model*.

### <a name="step-3-evaluate-dnn-for-all-images"></a>Krok 3: Vyhodnocení DNN pro všechny Image
`Script: 3_runDNN.py`

Jsme teď můžete použít (pravděpodobně přesnějších) DNN z poslední krok k featurize naše bitové kopie. Zadané jako vstup DNN bitovou kopii, výstup je omezena 512 vektoru z předposlední vrstvy modelu. Tento problém je dimenzionální mnohem menší než image sám sebe. Nicméně ji by měl obsahovat (a dokonce zvýrazněte) všechny informace v příslušné rozpoznat atribut obrázku, který je, pokud má položka oblečení desítkovém, rozdělená, image nebo leopard texture.

Všechny bitové kopie reprezentace DNN se uloží do souboru *DATA_DIR/proc/fashionTexture/cntkFiles/features.pickle*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_4_white.jpg" alt="alt text" width="700"/>
</p>


### <a name="step-4-support-vector-machine-training"></a>Krok 4: Support Vector Machine školení
`Script: 4_trainSVM.py`

Obtékaných objektů 512 reprezentace počítaný v posledním kroku jsou nyní použity při cvičení SVM třídění: zadána bitovou kopii jako vstup SVM výstupy skóre pro každý atribut nacházet. V našem příkladu datovou sadu to znamená skóre pro 'prokládané' pro 'desítkovém' a 'leopard'.

Skript `4_trainSVM.py` načte školení bitové kopie, učí SVM pro různé hodnoty parametru regulaci (slack) C a udržuje SVM s nejvyšší přesnost. Přesnost klasifikace se vytiskne na konzole a vykreslena nástroje Workbench. Pro data zadaná texture tyto hodnoty by měly být přibližně 100 % a 88 % v uvedeném pořadí. Nakonec vyškolení SVM je zapsán do souboru *DATA_DIR/proc/fashionTexture/cntkFiles/svm.np*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/vienna_svm_log_zoom.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-5-evaluation-and-visualization"></a>Krok 5: Testování a vizualizaci
`Script: 5_evaluate.py. Notebook: showResults.ipynb`

Přesnost třídění vyškolení bitové kopie lze měřit pomocí skriptu `5_evaluate.py`. Všechny Image test pomocí vyškolení třídění SVM, přiřadí každé bitové kopie atribut s nejvyšší skóre a porovnává předpokládaných atributy s poznámky pravdivosti základů skóre skriptu.

Výstup skriptu `5_evaluate.py` jsou uvedeny níže. Přesnost klasifikace každé jednotlivé třídy je i přesnost pro úplné testovací sada ('celkový přesnost") a průměrnou vypočítán na jednotlivé přesnostmi ('celkový průměr třída přesnost'). 100 % odpovídá nejlepší možný přesnost a 0 % na nejhoršího. Náhodné hádání v průměru vznikna přesnost 1 třída průměrem přes počet atributů: v našem případě by tato přesnost 33,33 %. Tyto výsledky se výrazné zlepšení při použití vyšší vstupní řešení, jako `rf_inputResoluton = 1000`, ale za cenu delší doby DNN výpočty.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_6_white.jpg" alt="alt text" width="700"/>
</p>

Kromě přesnost se vykreslí křivka ROC s příslušnými oblasti v křivky (vlevo); a přehlednosti matice zobrazí (napravo):

<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat.jpg" alt="alt text" width="700"/>
</p>

Nakonec poznámkového bloku `showResults.py` zajišťuje procházení testovací obrázků a vizualizovat jejich skóre příslušné klasifikace. Jak je popsáno v krok 1, musí používat jádra místní projektu s názvem "PROJECTNAME místní" každých poznámkového bloku v této ukázce:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showResults.jpg" alt="alt text" width="700"/>
</p>





### <a name="step-6-deployment"></a>Krok 6: nasazení
`Scripts: 6_callWebservice.py, deploymain.py. Notebook: deploy.ipynb`

Vyškolení systému lze nyní publikovat jako rozhraní REST API. Nasazení je vysvětleno v poznámkovém bloku `deploy.ipynb`a podle funkcionalitu v rámci nástroje Azure Machine Learning Workbench (Nezapomeňte nastavit jako jádra jádra místní projektu s názvem "PROJECTNAME místní"). Viz také části vynikající nasazení [IRIS kurzu](tutorial-classifying-iris-part-3.md) pro další nasazení související informace.

Po nasazení webové služby je možné volat pomocí skriptu `6_callWebservice.py`. Všimněte si, že IP adresa (místní nebo v cloudu) webové služby je třeba nejprve nastavit ve skriptu. Poznámkového bloku `deploy.ipynb` vysvětluje, jak najít tuto IP adresu.








## <a name="part-2---accuracy-improvements"></a>Část 2 – vylepšení přesnost

Část 1 můžeme vám ukázal, jak klasifikovat bitovou kopii podle cvičení lineární Vector Machine podpory na 512 omezena výstup hluboké Neuronové sítě. Tato DNN nebyla předem vyškolení na miliony bitové kopie a vrstvě předposlední vrácena jako funkce vector. Tento přístup je rychlé, protože DNN slouží jako-se, ale přesto často dává dobré výsledky.

Nyní Představujeme několik způsobů, jak zlepšit přesnost modelu ze část 1. Zejména jsme Upřesnit DNN místo zachování ji opravil.

### <a name="dnn-refinement"></a>Upřesnění DNN

Místo SVM jedné můžete udělat klasifikací přímo v neuronové sítě. Toho dosáhnete přidáním nové poslední vrstvy do předem vyškolení DNN, který přebírá obtékaných objektů 512 z předposlední vrstvy jako vstup. Výhodou provádění klasifikace na DNN je, že teď plné síti může být retrained pomocí backpropagation. Tento přístup často vede k mnohem lepší přesnostmi klasifikace ve srovnání s pomocí předem vyškolení DNN jako-se, ale za cenu mnohem delší dobu školení (i s grafickým Procesorem).

Cvičení Neuronové sítě místo SVM se provádí tak, že změníte proměnnou `classifier` v `PARAMETERS.py` z `svm` k `dnn`. Pak jak je popsáno v části 1, všech skriptech s výjimkou přípravy dat (krok 1) a školení SVM (krok 4) potřeba provést znovu. Upřesnění DNN vyžaduje grafického procesoru. Pokud nebyl nalezen žádný GPU nebo pokud je uzamčené na grafický procesor (například podle předchozího spuštění CNTK) pak skript `2_refineDNN.py` vrátí chybu. Školení DNN můžete vyvolat chyba z důvodu nedostatku paměti na některé grafickými procesory, které se vyhnout snížením velikosti minibatch (proměnná `cntk_mb_size` v `PARAMETERS.py`).

Po dokončení školení přesnějších model je uložen na *DATA_DIR/proc/fashionTexture/cntk_refined.model*, a vykreslení vykreslovat, který ukazuje, jak změnit učení a testovací klasifikace chyby během cvičení. Poznámka: v této výkresu, který je mnohem menší než na testovací sada chybu na trénovací sady. Toto chování takzvané přepsání těsně může snížit, například pomocí vyšší hodnoty pro rychlost Odpadlík `rf_dropoutRate`.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_3_plot.png" alt="alt text" height="300"/>
</p>

Jak je vidět v výkresu níže, přesnost pomocí DNN upřesnění zadané sady dat, je 92.35 % a % 88.92 před (část 1). Konkrétně "desítkovém" Image výrazné zlepšení, s oblasti v křivka ROC z 0,98 s upřesnění vs. 0.94 před. Používáme na malou datovou sadu, a proto se liší skutečné přesnostmi spuštění kódu. Tato nesrovnalost je z důvodu stochastického účinky, jako je například náhodné rozdělení bitových kopií do trénování a testování sad.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat_dnn.jpg" alt="alt text" width="700"/>
</p>

### <a name="run-history-tracking"></a>Spustit sledování historie

Úložiště Azure Machine Learning Workbench historii jednotlivých spustit v Azure k porovnání dvou nebo více testů, které jsou povolit i týdnů od sebe. To je podrobně vysvětleny v [Iris kurzu](tutorial-classifying-iris-part-2.md). Také je znázorněno v následující snímky obrazovky, které jsme porovnání dvou spuštění skriptu `5_evaluate.py`, pomocí buď DNN upřesnění tedy `classifier = "dnn"`(spuštění číslo 148) nebo SVM cvičení to znamená, `classifier = "svm"` (spuštění číslo 150).

Na snímku obrazovky první upřesnění DNN vede k lepší přesnostmi než SVM školení pro všechny třídy. Druhý snímek obrazovky ukazuje všechny metriky, které jsou sledovány, včetně toho, co byl třídění. Toto sledování se provádí ve skriptu `5_evaluate.py` voláním Azure Machine Learning Workbench protokolovacího nástroje. Kromě toho skript navíc šetří ROC křivky a přehlednosti matice do *výstupy* složky. To *výstupy* složka je speciální v, že jeho obsah je také sleduje funkci historie Workbench a proto výstupních souborů je přístupný kdykoli bez ohledu na to, jestli máte místní kopie přepsán.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison1.jpg" alt="alt text" width="700"/> </p>

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison2b.jpg" alt="alt text" width="700"/>
</p>


### <a name="parameter-tuning"></a>Parametr ladění
Stejně jako u většina strojového učení projekty, získávání dobré výsledky pro nová datová sada vyžaduje pečlivě parametr ladění a také vyhodnocení různých rozhodnutí. Abyste tyto úlohy jsou zadány všechny důležité parametry a krátký vysvětlení dostupné na jednom místě: `PARAMETERS.py` souboru.

Mezi nejslibnějším cesty pro vylepšení patří:

- Data quality: Zkontrolujte učení a testovací sady mají vysoké kvality. To znamená, jsou obrázky správně opatřen poznámkami, nejednoznačný bitové kopie odebrat (například oblečení položky s rozděluje a tečky) a atributy se vzájemně vylučují (to znamená, vybrali tak, aby každý image patří do přesně jeden atribut).
- Pokud je objekt-vás zajímají malá v bitové kopii jsou známé přístupy klasifikace bitové kopie nepracuje správně. V takových případech zvažte použití přístup zjišťování objektu, jak je popsáno v tomto [kurzu](https://github.com/Azure/ObjectDetectionUsingCntk).
- Upřesnění DNN: pravděpodobně nejdůležitější parametr získat správné je rychlost učení `rf_lrPerMb`. Pokud přesnost na školení nastavený (první obrázek v část 2) se nenachází v blízkosti 0 – 5 %, pravděpodobně je z důvodu nesprávnou hodnotu rychlost učení. Ostatní parametry počínaje `rf_` méně důležité. Chyba školení by měl obvykle snížení exponenciálnímu a být blížící se 0 % po školení.
- Vstupní řešení: výchozí rozlišení obrázku je 224 x 224 pixelů. Pomocí vyšší rozlišení obrázku (parametr: `rf_inputResoluton`), například 448 x 448 nebo 896 x 896 pixelů často významné zlepšuje přesnost, ale zpomaluje DNN vylepšení. **Pomocí vyšší rozlišení obrázku je téměř oběd zdarma a téměř vždy zvyšuje přesnost**.
- Přizpůsobování přečerpání DNN: Vyhněte se velké mezery mezi školení a testovací přesnost během DNN upřesnění (první obrázek v část 2). Během této poměrně lze snížit pomocí Odpadlík sazby `rf_dropoutRate` 0,5 nebo více a zvýšením váhy regularizer `rf_l2RegWeight`. Míra vysoké Odpadlík může být obzvláště užitečné, pokud rozlišení DNN vstupní obrázku je vysoká.
- Zkuste použít hlubší DNNs změnou `rf_pretrainedModelFilename` z `ResNet_18.model` buď `ResNet_34.model` nebo `ResNet_50.model`. Model Resnet – 50 není pouze hlubší, ale jeho výstup předposlední vrstvy je velikost 2048 obtékaných objektů (vs. 512 obtékaných objektů ResNet 18 a ResNet 34 modely). Tato vyšší dimenze může být obzvláště užitečné, když cvičení třídění SVM.

## <a name="part-3---custom-dataset"></a>Část 3 - vlastní datové sady

V rámci 1 a 2 jsme trénují a vyhodnocují model klasifikace bitové kopie používající Image textury oblečení zadaný horní textu. Nyní ukážeme, jak místo toho použít vlastní datovou sadu zadaný uživatelem. Nebo, pokud není k dispozici, jak generovat a opatřit poznámkami, například datové sady pomocí Bingu hledat bitovou kopii.

### <a name="using-a-custom-dataset"></a>Použití vlastní datové sady

Nejprve umožňuje Podíváme se na strukturu složek pro data texture oblečení. Všimněte si, jak všechny bitové kopie pro různé atributy jsou v příslušných podsložky *desítkovém*, * leopard, a *prokládané* v *DATA_DIR nebo bitové kopie nebofashionTexture/*. Všimněte si také, jak název složky bitové kopie také v dojde `PARAMETERS.py` souboru:
```python
datasetName = "fashionTexture"
```

Použití vlastní datové sady je jednoduché, reprodukci tuto strukturu kde jsou všechny bitové kopie v podsložkách podle jejich atributů a zkopírujte tyto podsložky do nového adresáře, který je zadán uživatel *DATA_DIR nebo bitové kopie nebo newDataSetName/*. Je vyžadována pouze změna kódu nastavit `datasetName` proměnnou *newDataSetName*. Skripty 1-5 pak lze spustit v pořadí, a všechny zprostředkující soubory se zapisují do *DATA_DIR neboproc/newDataSetName/*. Nejsou vyžadovány žádné další změny kódu.

Je důležité, aby každé bitové kopie lze přiřadit k přesně jeden atribut. Například by být nesprávné mít atributy pro 'zvíře' a 'leopard', protože bitovou kopii, leopard, by taky patřit do 'zvíře'. Je také vhodné odebrat bitové kopie, které se nejednoznačný a proto obtížné opatřit poznámkami.



### <a name="image-scraping-and-annotation"></a>Obrázek oškrabávání a poznámky

Shromažďování dostatečně velký počet poznámkou bitových kopií pro trénování a testování může být obtížné. Jeden způsob, jak tento problém vyřešit, je scrape bitové kopie z Internetu. Například najdete níže výsledky hledání bitové kopie Bingu pro dotaz *tričko rozdělená*. Podle očekávání, většina Image skutečně jsou rozdělené trička. Několik nesprávný nebo je nejednoznačný bitové kopie (například sloupec 1, řádek 1; nebo sloupec 3, řádek 2) můžete identifikovat a snadno odebrat:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/bing_search_striped.jpg" alt="alt text" width="600"/>
</p>

Pokud chcete vygenerovat velký a různé datové sady, je třeba použít více dotazů. Například 7\*3 = 21 dotazy můžete syntetizované automaticky pomocí všechny kombinace oblečení položky {halenka, hoodie, pulovru, svetr, shirt, tričko, vestě} a atributy {prokládané, desítkovém, leopard}. Stažení bitové kopie prvních 50 na jeden dotaz by vést k maximálně 21 * 50 = 1050 bitové kopie.

Místo ručního stahování bitové kopie z hledání bitové kopie Bingu, je mnohem snazší použijte [kognitivní služby Bing Image vyhledávání API](https://www.microsoft.com/cognitive-services/bing-image-search-api) která vrací sadu adresy URL bitové kopie zadaný řetězec dotazu.

Některé stažené bitových kopií jsou přesný nebo téměř duplicitní položky (například právě lišit image řešení nebo jpg artefakty). Tyto duplikáty byste měli odebrat tak, aby rozdělení učení a testovací neobsahují stejné bitové kopie. Odebírání obrázků duplicitní lze dosáhnout pomocí přístupu na základě algoritmu hash, který funguje ve dvou krocích: (i), nejprve je počítaný řetězce hash pro všechny Image; (ii) v druhé průchodu přes bitové kopie jsou uchovány pouze obrázků s řetězec hash, který nebylo ještě nikdy. Všechny ostatní Image se zahodí. Zjistili jsme `dhash` přístup v knihovně Python `imagehash` a popsané v tomto [blog](http://www.hackerfactor.com/blog/index.php?/archives/529-Kind-of-Like-That.html) k provedení dobře, s parametrem `hash_size` nastaven na hodnotu 16. Je OK nesprávně odebrat některé obrázky jedinečných tak dlouho, dokud získat odebrat většina skutečné duplicitní hodnoty.





## <a name="conclusion"></a>Závěr

Některé klíče označuje tohoto příkladu jsou:
- Kód pro trénování, vyhodnocení a nasazení bitové kopie klasifikační modely.
- Ukázkový Image zadaná, ale snadno přizpůsobitelné (jeden řádek změnit) použít datovou sadu vlastní image.
- Funkce odborné stavu techniky implementována pro učení vysokou přesnost modely v závislosti na přenos učení.
- Interaktivní modelu vývoj pomocí Azure Machine Learning Workbench a Poznámkový blok Jupyter.


## <a name="references"></a>Odkazy

[1] Alex Krizhevsky Ilya Sutskever a Geoffrey E. Hinton, [ _ImageNet klasifikaci s Convolutional hluboké Neuronové sítě_](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf). NIPS 2012.  
[2] použí Kaiming, Xiangyu Potokar, Shaoqing Ren a Jian Sun, [ _hloubky zbytkové učení pro bitovou kopii rozpoznávání_](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf). CVPR 2016.
