---
title: 'ML Studio (Classic): způsob, jakým se model stal webovou službou – Azure'
description: Přehled mechanismu způsobu, jakým model Azure Machine Learning Studio (klasický) postupuje z experimentu vývoje na webovou službu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: 4e0f5786047977a319825aae9f3c7b89c0aa118b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "100518619"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Způsob, jakým model Machine Learning Studio (klasický) postupuje z experimentu na webovou službu

**platí pro:** ![ Toto je značka zaškrtnutí, což znamená, že se tento článek týká Machine Learning Studio (Classic). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ Toto je X, což znamená, že se tento článek týká Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Azure Machine Learning Studio (Classic) poskytuje interaktivní plátno, které umožňuje vyvíjet, spouštět, testovat a iterovat ***experiment*** , který představuje model prediktivní analýzy. K dispozici je celá řada modulů, které mohou:

* Zadávání dat do experimentu
* Manipulace s daty
* Výuka modelu pomocí algoritmů strojového učení
* Určení skóre modelu
* Vyhodnocení výsledků
* Výstup finálních hodnot

Až budete s experimentem spokojeni, můžete ho nasadit jako ***klasický Azure Machine Learning webové služby** _ nebo _ *_nové Azure Machine Learning webové služby_**, aby si ji uživatelé mohli poslat nová data a získat výsledky zpět.

V tomto článku poskytujeme přehled o tom, jak model Machine Learning postupuje z experimentu vývoje do provozní webové služby.

> [!NOTE]
> Existují i jiné způsoby, jak vyvíjet a nasazovat modely strojového učení, ale tento článek se zaměřuje na použití Machine Learning Studio (Classic). Pokud například chcete přečíst popis postupu vytvoření klasické prediktivní webové služby pomocí jazyka R, přečtěte si Blogový příspěvek [Build & nasazení prediktivních Web Apps pomocí RStudio a Azure Machine Learning Studio](/archive/blogs/machinelearning/build-deploy-predictive-web-apps-using-rstudio-and-azure-ml).
>
>

I když je Azure Machine Learning Studio (Classic) navržený tak, aby vám usnadnil vývoj a nasazení *modelu prediktivní analýzy*, je možné použít Studio (Classic) k vývoji experimentu, který nezahrnuje model prediktivní analýzy. Experiment může například pouze zadat data, manipulovat ho a pak výstup výsledků. Stejně jako u experimentu s prediktivní analýzou můžete tento neprediktivní experiment nasadit jako webovou službu, ale je to jednodušší proces, protože experiment není v výuce nebo bodování modelu strojového učení. I když se tímto způsobem nejedná o běžné použití studia (Classic), zahrneme ho do diskuze, abychom mohli poskytnout kompletní vysvětlení toho, jak Studio (Classic) funguje.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Vývoj a nasazení prediktivní webové služby
Tady jsou fáze, které typické řešení sleduje při vývoji a nasazování pomocí Machine Learning Studio (Classic):

![Tok nasazení](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Obrázek 1 – fáze typického modelu prediktivní analýzy*

### <a name="the-training-experiment"></a>Experiment školení
***Školicí experiment*** je počáteční fází vývoje webové služby v Machine Learning Studio (Classic). Účelem školení experimentu je poskytnout místo pro vývoj, testování, iteraci a nakonec výuku modelu strojového učení. Můžete dokonce prokládat více modelů současně při hledání nejlepšího řešení, ale až budete hotovi, vyberete jeden vyškolený model a odstraníte zbytek z experimentu. Příklad vývoje experimentování s prediktivní analýzou najdete [v tématu vývoj řešení prediktivní analýzy pro posuzování úvěrového rizika v Azure Machine Learning Studio (Classic)](tutorial-part1-credit-risk.md).

### <a name="the-predictive-experiment"></a>Prediktivní experiment
Jakmile budete mít školicí model v experimentu pro školení, klikněte na **nastavit webovou službu** a vyberte **prediktivní webovou službu** v Machine Learning Studio (Classic), abyste zahájili proces převodu školicího experimentu na **_prediktivní experiment_**. Účelem prediktivního experimentu je vyzkoušení nových dat pomocí vámi vyučeného modelu s cílem nakonec se stát, že bude fungovat jako webová služba Azure.

Tento převod se provádí v následujících krocích:

* Převeďte sadu modulů používaných pro školení do jednoho modulu a uložte ho jako trained model.
* Eliminujte všechny nadbytečné moduly, které nesouvisí s bodování
* Přidat vstupní a výstupní porty, které bude webová služba používat

Může se stát, že budete mít k dispozici další změny, které vám pomůžou udělat prediktivní experiment k nasazení jako webovou službu. Například pokud chcete, aby webová služba vyoutput pouze podmnožinu výsledků, můžete přidat modul filtrování před výstupním portem.

V tomto procesu převodu nedojde k zahození školicího experimentu. Po dokončení procesu máte v studiu dvě karty (klasické): jeden pro školicí experiment a druhý pro prediktivní experiment. Tímto způsobem můžete provést změny v experimentu školení před nasazením webové služby a opětovným sestavením prediktivního experimentu. Můžete také uložit kopii zkušebního experimentu a zahájit další řadu experimentů.

> [!NOTE]
> Když kliknete na **prediktivní webovou službu** , spustíte automatický proces, který převede školicí experiment na prediktivní experiment. ve většině případů to funguje dobře. Pokud je váš školicí experiment složitý (například máte k dispozici více cest pro školení, které spojíte dohromady), můžete tento převod preferovat ručně. Další informace najdete v tématu [Příprava modelu pro nasazení v Azure Machine Learning Studio (Classic)](deploy-a-machine-learning-web-service.md).
>
>

### <a name="the-web-service"></a>Webová služba
Jakmile budete spokojeni s připraveným experimentem, můžete službu nasadit jako klasickou webovou službu nebo novou webovou službu založenou na Azure Resource Manager. Pokud zprovoznění svůj model nasazením jako *klasický Machine Learning webové služby*, klikněte na **nasadit webovou službu** a vyberte **nasadit webovou službu [Classic]**. Pokud chcete nasadit jako *novou webovou službu Machine Learning*, klikněte na **nasadit webovou službu** a vyberte **nasadit webovou službu [New]**. Uživatelé teď mohou odesílat data do modelu pomocí webové služby REST API a získat zpět výsledky. Další informace najdete v tématu o [využívání webové služby Azure Machine Learning](consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Netypický případ: vytvoření neprediktivní webové služby
Pokud váš experiment nevýukový model prediktivní analýzy, nemusíte vytvářet školicí experimenty i experimenty pro bodování – existuje jen jeden experiment a můžete ho nasadit jako webovou službu. Machine Learning Studio (Classic) zjistí, zda experiment obsahuje prediktivní model analýzou modulů, které jste použili.

Po iteraci na experimentu a jejich splnění:

1. Klikněte na **nastavit webovou službu** a vyberte možnost **přeškolení webové služby** – vstupní a výstupní uzly se přidají automaticky.
2. Klikněte na **Spustit** .
3. Klikněte na **nasadit webovou službu** a vyberte **nasadit webovou službu [Classic]** nebo **nasadit webovou službu [New]** v závislosti na prostředí, do kterého chcete nasadit.

Vaše webová služba je teď nasazená a Vy ji můžete používat a spravovat stejně jako prediktivní webové služby.

## <a name="updating-your-web-service"></a>Aktualizace webové služby
Teď, když jste nasadili experiment jako webovou službu, co když ho potřebujete aktualizovat?

To závisí na tom, co potřebujete aktualizovat:

**Chcete změnit vstup nebo výstup nebo chcete změnit způsob, jakým webová služba pracuje s daty**

Pokud model neměníte, ale právě měníte, jak webová služba zpracovává data, můžete upravit prediktivní experiment a pak kliknout na **nasadit webovou** službu a vybrat nasadit webovou službu **[Classic]** nebo **nasadit webovou službu [New]** znovu. Webová služba je zastavena, je nasazen aktualizovaný prediktivní experiment a webová služba je restartována.

Tady je příklad: Předpokládejme, že prediktivní experiment vrátí celý řádek vstupních dat s předpokládaným výsledkem. Můžete se rozhodnout, že chcete, aby webová služba vracela pouze výsledek. Takže můžete přidat modul **projektové sloupce** do prediktivního experimentu přímo před výstupním portem pro vyloučení jiných sloupců, než je výsledek. Když kliknete na **nasadit webovou službu** a vyberte **nasadit webovou službu [Classic]** nebo **nasadit webovou službu [New]** znovu, Webová služba se aktualizuje.

**Chcete přeškolit model s novými daty**

Pokud chcete zachovat model strojového učení, ale chcete ho znovu naučit s novými daty, máte dvě možnosti:

1. Převeďte **model, když je spuštěná webová služba** – Pokud chcete svůj model předávat i v době, kdy je spuštěná prediktivní webová služba, můžete to udělat tak, že provedete několik úprav školicích experimentů, abyste se ***přesadili experiment _, a pak ho můžete nasadit jako*_webovou službu pro rekurzi_**. Pokyny k tomu, jak to provést, najdete v tématu [přeučení Machine Learningch modelů programově](./retrain-machine-learning-model.md).
2. **Vraťte se na původní školicí experiment a pomocí různých školicích dat vytvořte model** – prediktivní experiment je propojen s webovou službou, ale školicí experiment není přímo propojen tímto způsobem. Pokud upravíte původní školicí experiment a kliknete na **nastavit webovou službu**, vytvoří se *Nový*     prediktivní experiment, který při nasazení vytvoří *novou* webovou službu. Neaktualizuje jenom původní webovou službu.

   Pokud potřebujete upravit zkušební experiment, otevřete ho a kliknutím na **Uložit jako** vytvořte kopii. Tato akce ponechá původní zkušební experiment, prediktivní experiment a webovou službu. Nyní můžete vytvořit novou webovou službu se změnami. Po nasazení nové webové služby se můžete rozhodnout, zda chcete zastavit předchozí webovou službu nebo ponechat spuštěnou současně s novým.

**Chcete si vyškolit jiný model**

Pokud chcete provést změny v původním prediktivním experimentu, jako je třeba vybrat jiný algoritmus strojového učení, vyzkoušet jinou výukovou metodu atd., pak musíte postupovat podle druhého postupu popsaného výše v tématu o přeškolení modelu: Otevřete zkušební experiment, klikněte na **Uložit jako** a vytvořte kopii a pak začněte novou cestou k vývoji modelu, Vytvoření prediktivního experimentu a nasazení webové služby. Tím se vytvoří nová webová služba, která nesouvisí s původní verzí, kterou si můžete vybrat, nebo obojí.

## <a name="next-steps"></a>Další kroky
Další informace o procesu vývoje a experimentování najdete v následujících článcích:

* převod experimentu – [jak připravit model pro nasazení v Azure Machine Learning Studio (Classic)](deploy-a-machine-learning-web-service.md)
* nasazení webové služby – [nasazení webové služby Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* přeškolování modelů modelu [Machine Learning pomocí kódu programu](./retrain-machine-learning-model.md)

Příklady celého procesu najdete v těchto tématech:

* [Kurz strojového učení: vytvoření prvního experimentu v Azure Machine Learning Studio (Classic)](create-experiment.md)
* [Návod: vývoj řešení prediktivní analýzy pro posuzování úvěrového rizika v Azure Machine Learning](tutorial-part1-credit-risk.md)