---
title: Jak se model stane webovou službou
titleSuffix: ML Studio (classic) - Azure
description: Přehled mechaniky, jak váš model Azure Machine Learning Studio (klasický) postupuje z vývojového experimentu na webovou službu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: e6a8fe7771776a92b3c28a188bc352e3bb105313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217923"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Jak model Machine Learning Studio (klasický) postupuje z experimentu na webovou službu

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Azure Machine Learning Studio (classic) poskytuje interaktivní plátno, které umožňuje vyvíjet, spouštět, testovat a iterovat ***experiment*** představující model prediktivní analýzy. K dispozici je široká škála modulů, které mohou:

* Zadávání dat do experimentu
* Manipulace s daty
* Trénování modelu pomocí algoritmů strojového učení
* Ohodnocení modelu
* Vyhodnocení výsledků
* Konečné hodnoty výstupu

Jakmile budete s experimentem spokojeni, můžete ho nasadit jako ***webovou službu Classic Azure Machine Learning*** nebo novou ***webovou službu Azure Machine Learning,*** aby jim uživatelé mohli odesílat nová data a přijímat zpětné výsledky.

V tomto článku poskytujeme přehled o mechanice, jak váš model strojového učení postupuje z vývojového experimentu na zprovozněnou webovou službu.

> [!NOTE]
> Existují i jiné způsoby, jak vyvíjet a nasazovat modely strojového učení, ale tento článek se zaměřuje na způsob použití Machine Learning Studio (klasické). Chcete-li například přečíst popis, jak vytvořit klasickou prediktivní webovou službu s R, podívejte se na příspěvek blogu [Build & nasazení prediktivních webových aplikací pomocí RStudio a Azure Machine Learning studio](https://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).
>
>

Zatímco Azure Machine Learning Studio (classic) je navržen tak, aby vám pomohl vyvinout a nasadit *model prediktivní analýzy*, je možné použít Studio (klasické) k vývoji experimentu, který neobsahuje model prediktivní analýzy. Experiment může například pouze zadat data, manipulovat s nimi a potom výstup výsledků. Stejně jako experiment prediktivní analýzy můžete tento neprediktivní experiment nasadit jako webovou službu, ale je to jednodušší proces, protože experiment není školení nebo vyhodnocování modelu strojového učení. I když to není typické používat Studio (klasické) tímto způsobem, budeme ji zahrnout do diskuse, takže můžeme poskytnout úplné vysvětlení toho, jak Studio (klasické) funguje.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Vývoj a nasazení prediktivní webové služby
Tady jsou fáze, které typické řešení následuje při vývoji a nasazování pomocí Machine Learning Studio (klasika):

![Tok nasazení](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Obrázek 1 – Fáze typického modelu prediktivní analýzy*

### <a name="the-training-experiment"></a>Tréninkový experiment
***Trénovací experiment*** je počáteční fáze vývoje webové služby v Machine Learning Studio (klasické). Účelem vzdělávacího experimentu je poskytnout vám místo pro vývoj, testování, iterát a nakonec trénovat model strojového učení. Můžete dokonce trénovat více modelů současně, když hledáte nejlepší řešení, ale jakmile budete hotovi experimentovat, vyberete jeden trénovaný model a odstraníte zbytek z experimentu. Příklad vývoje experimentu prediktivní analýzy najdete v [tématu Vývoj řešení prediktivní analýzy pro hodnocení úvěrového rizika v Azure Machine Learning Studio (klasické)](tutorial-part1-credit-risk.md).

### <a name="the-predictive-experiment"></a>Prediktivní experiment
Jakmile máte v trénovaném experimentu trénovaný model, klikněte na **Nastavit webovou službu** a vyberte **Predictive Web Service** v Machine Learning Studio (klasické) a iniciujte proces převodu trénovacího experimentu na ***prediktivní experiment***. Účelem prediktivního experimentu je použít trénovaný model k vykreslování nových dat s cílem, aby se nakonec stal zprovozněn jako webová služba Azure.

Tato konverze se provádí za vás pomocí následujících kroků:

* Převeďte sadu modulů používaných pro školení na jeden modul a uložte ji jako trénovaný model
* Eliminujte všechny cizí moduly, které nesouvisejí s bodováním
* Přidání vstupních a výstupních portů, které bude případná webová služba používat

Může být více změn, které chcete provést, aby byl prediktivní experiment připraven k nasazení jako webová služba. Chcete-li například, aby webová služba vykreslovala pouze podmnožinu výsledků, můžete před výstupní port přidat modul filtrování.

V tomto procesu převodu není zahozen trénovací experiment. Po dokončení procesu máte dvě karty ve studiu (klasické): jeden pro trénovací experiment a jeden pro prediktivní experiment. Tímto způsobem můžete provést změny v trénovacím experimentu před nasazením webové služby a znovu sestavit prediktivní experiment. Nebo můžete uložit kopii trénovacího experimentu a spustit další řadu experimentů.

> [!NOTE]
> Po klepnutí na tlačítko **Prediktivní webová služba** spustíte automatický proces převedení trénovacího experimentu na prediktivní experiment, což ve většině případů funguje dobře. Pokud je váš tréninkový experiment složitý (například máte několik cest pro školení, které spojujete), můžete tento převod provést ručně. Další informace najdete v tématu [Jak připravit model pro nasazení v Azure Machine Learning Studio (klasické)](convert-training-experiment-to-scoring-experiment.md).
>
>

### <a name="the-web-service"></a>Webová služba
Jakmile budete spokojeni s tím, že je prediktivní experiment připravený, můžete svou službu nasadit jako klasickou webovou službu nebo jako novou webovou službu založenou na Správci prostředků Azure. Chcete-li model zprovoznit nasazením jako *webovou službu Classic Machine Learning*, klepněte na **tlačítko Nasadit webovou službu** a vyberte **možnost Nasadit webovou službu [Classic].** Chcete-li nasadit jako *novou webovou službu Machine Learning*, klepněte na tlačítko **Nasadit webovou službu** a vyberte **možnost Nasadit webovou službu [New]**. Uživatelé nyní mohou odesílat data do vašeho modelu pomocí rozhraní REST API webové služby a přijímat zpět výsledky. Další informace najdete v tématu o [využívání webové služby Azure Machine Learning](consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Netypický případ: vytvoření neprediktivní webové služby
Pokud váš experiment netrénuje model prediktivní analýzy, není nutné vytvářet tréninkový experiment i experiment hodnocení – existuje pouze jeden experiment a můžete jej nasadit jako webovou službu. Machine Learning Studio (klasické) zjistí, zda experiment obsahuje prediktivní model analýzou modulů, které jste použili.

Po iterování experimentu a spokojenosti s ním:

1. Klikněte na **Nastavit webovou službu** a vyberte **Možnost Retraining Web Service** – vstupní a výstupní uzly se přidávají automaticky.
2. Klikněte na **Spustit.**
3. Klepněte na **tlačítko Nasadit webovou službu** a v závislosti na prostředí, do kterého chcete nasadit, vyberte **možnost Nasadit webovou službu [Classic]** nebo **Deploy Web Service [New].**

Webová služba je nyní nasazena a můžete k ní přistupovat a spravovat ji stejně jako prediktivní webová služba.

## <a name="updating-your-web-service"></a>Aktualizace webové služby
Nyní, když jste experiment nasadili jako webovou službu, co když ho potřebujete aktualizovat?

To záleží na tom, co je třeba aktualizovat:

**Chcete změnit vstup nebo výstup nebo změnit způsob, jakým webová služba manipuluje s daty**

Pokud neměníte model, ale pouze měníte způsob, jakým webová služba zpracovává data, můžete upravit prediktivní experiment a potom klepnout na tlačítko **Nasadit webovou službu** a znovu vybrat **možnost Nasadit webovou službu [Classic]** nebo **Nasadit webovou službu [New].** Webová služba je zastavena, je nasazen aktualizovaný prediktivní experiment a webová služba je restartována.

Zde je příklad: Předpokládejme, že prediktivní experiment vrátí celý řádek vstupních dat s předpokládaným výsledkem. Můžete se rozhodnout, že chcete, aby webová služba pouze vrátit výsledek. Takže můžete přidat modul **Sloupce projektu** v prediktivní experiment, přímo před výstupní port, chcete-li vyloučit sloupce než výsledek. Po opětovném klepnutí na tlačítko **Nasadit webovou službu** a znovu vyberete **možnost Nasadit webovou službu [Classic]** nebo **Deploy Web Service [New],** bude webová služba aktualizována.

**Chcete přeškolit model s novými daty**

Pokud chcete zachovat model strojového učení, ale chcete jej přeškolit pomocí nových dat, máte dvě možnosti:

1. **Přeškolit model v době, kdy je spuštěna webová služba** – pokud chcete přeškolit model v době, kdy je spuštěna prediktivní webová služba, můžete tak provést provedením několika úprav trénovacího experimentu tak, aby byl ***rekvalifikačním experimentem***, můžete jej nasadit jako ** *rekvalifikační webovou* službu**. Pokyny k tomu, jak to provést, naleznete [v tématu Programové přeškolovací modely strojového učení](/azure/machine-learning/studio/retrain-machine-learning-model).
2. **Vraťte se k původnímu trénovacímu experimentu a použijte k vývoji modelu různá trénovací data** – prediktivní experiment je propojen s webovou službou, ale trénovací experiment není přímo propojen tímto způsobem. Pokud upravíte původní trénovací experiment a klepnete na tlačítko **Nastavit webovou službu**, vytvoří *se nový* prediktivní experiment, který po nasazení vytvoří *novou* webovou službu. Neaktualizuje pouze původní webovou službu.

   Pokud potřebujete upravit tréninkový experiment, otevřete ho a kliknutím na **Uložit jako** vytvořte kopii. To ponechá beze změny původní ho trénovací experiment, prediktivní experiment a webové služby. Nyní můžete vytvořit novou webovou službu se změnami. Po nasazení nové webové služby se pak můžete rozhodnout, zda předchozí webovou službu zastavíte nebo ponecháte spuštěnou vedle nové.

**Chcete trénovat jiný model**

Pokud chcete provést změny původního prediktivního experimentu, například vybrat jiný algoritmus strojového učení, vyzkoušet jinou metodu školení atd., musíte postupovat podle výše popsaného postupu pro přeškolení modelu: otevřete tréninkový experiment, kliknutím na **tlačítko Uložit jako** vytvořte kopii a pak spusťte novou cestu vývoje modelu, vytvoření prediktivního experimentu a nasazení webové služby. Tím se vytvoří nová webová služba, která nesouvisí s původní – můžete se rozhodnout, která nebo obě, chcete-li pokračovat v běhu.

## <a name="next-steps"></a>Další kroky
Další podrobnosti o procesu vývoje a experimentování naleznete v následujících článcích:

* převod experimentu – [jak připravit model pro nasazení v Azure Machine Learning Studio (klasické)](convert-training-experiment-to-scoring-experiment.md)
* nasazení webové služby – [nasazení webové služby Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* rekvalifikace modelu - [Programově přeškolit modely strojového učení](/azure/machine-learning/studio/retrain-machine-learning-model)

Příklady celého procesu naleznete v následujících tématech:

* [Výuka strojového učení: Vytvořte svůj první experiment v Azure Machine Learning Studio (klasické)](create-experiment.md)
* [Návod: Vývoj prediktivního analytického řešení pro hodnocení úvěrového rizika v Azure Machine Learning](tutorial-part1-credit-risk.md)

