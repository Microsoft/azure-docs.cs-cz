---
title: 'Kurz: podrobný návod Machine Learning v Azure IoT Edge'
description: Kurz vysoké úrovně, který projde různými úkoly potřebnými k vytvoření komplexního strojového učení ve scénáři Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 67cc470b4f7f119b7f5b86bcb82ea284ab662dfe
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463234"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Kurz: ucelené řešení využívající Azure Machine Learning a IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Aplikace IoT často chtějí využívat inteligentní Cloud a inteligentní hraniční zařízení. V tomto kurzu Vás provedeme školením modelu strojového učení s daty shromážděnými ze zařízení IoT v cloudu, nasazením tohoto modelu IoT Edge a pravidelným udržováním a úpravami modelu.

>[!NOTE]
>Koncepty v této sadě kurzů se vztahují na všechny verze IoT Edge, ale ukázkové zařízení, které vytvoříte k vyzkoušení scénářů, se spouští IoT Edge verze 1,1.

Hlavním cílem tohoto kurzu je zavést zpracování dat IoT pomocí strojového učení, konkrétně na hraničních zařízeních. I když jsme se dotkli mnoha aspektů obecného pracovního postupu strojového učení, tento kurz není určený jako podrobný Úvod do strojového učení. V takovém případě se nepokoušíme vytvořit vysoce optimalizovaný model pro případ použití – stačí, abyste ilustraci procesu vytváření a používání životaschopného modelu pro zpracování dat IoT.

V této části kurzu se zabývá:

> [!div class="checklist"]
>
> * Požadavky na dokončení dalších částí tohoto kurzu.
> * Cílová skupina tohoto kurzu.
> * Případ použití, který kurz simuluje.
> * Celkový proces, který tento kurz sleduje, aby splnil případ použití.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete přístup k předplatnému Azure, ve kterém máte práva k vytváření prostředků. Některé ze služeb používaných v tomto kurzu se účtují za Azure. Pokud ještě nemáte předplatné Azure, možná budete moct začít s [bezplatným účtem Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).

Budete také potřebovat počítač s nainstalovaným prostředím PowerShell, kde můžete spouštět skripty pro nastavení virtuálního počítače Azure jako vývojového počítače.

V tomto dokumentu používáme následující sadu nástrojů:

* Azure IoT Hub pro zachycení dat

* Azure Notebooks jako náš hlavní front-end pro přípravu dat a experimentování ve strojovém učení. Spuštění kódu Pythonu v poznámkovém bloku u podmnožiny ukázkových dat představuje skvělý způsob, jak rychle a interaktivní vyřízení při přípravě dat. Pomocí poznámkových bloků Jupyter můžete také připravovat skripty pro spouštění ve velkém měřítku v back-endu.

* Azure Machine Learning jako back-end pro strojové učení ve velkém měřítku a pro generování imagí machine learningu. Azure Machine Learning back-end pomocí skriptů připravených a testovaných v poznámkových blocích Jupyter.

* Azure IoT Edge pro necloudovou aplikaci image strojového učení

V některých případech jsou k dispozici další možnosti. V některých scénářích můžete například IoT Central použít jako alternativu bez kódu k zachycení počátečních dat školení ze zařízení IoT.

## <a name="target-audience-and-roles"></a>Cílová skupina a role

Tato sada článků je určená pro vývojáře bez předchozího prostředí ve vývoji IoT nebo strojovém učení. Nasazení strojového učení na hraničních zařízeních vyžaduje znalost způsobu připojení široké škály technologií. V tomto kurzu se proto zabývá celým kompletním scénářem, který předvádí jeden způsob, jak tyto technologie spojit do řešení IoT. V reálných prostředích můžou být tyto úlohy rozdělené mezi několik lidí s různými specializacemi. Vývojáři se například zaměřují na zařízení nebo na kód v cloudu, zatímco odborníci na data navrhli analytické modely. Pokud chcete, aby jednotliví vývojáři úspěšně dokončili tento kurz, poskytli jsme doplňkové pokyny k přehledům a odkazům na Další informace, které doufáme, že je k dispozici dostatek informací, abychom zjistili, co se děje, a také proč.

Alternativně se můžete spojit s spolupracovníky různých rolí, které vám pomůžou postupovat podle tohoto kurzu a dodržet si plné znalosti a Naučte se, jak se vejdou do sebe.

V obou případech, které vám pomůžou s orientací čtenářů, každý článek v tomto kurzu označuje roli uživatele. Mezi tyto role patří:

* Vývoj pro Cloud (včetně cloudového vývojáře pracujícího v kapacitě DevOps)
* Analýza dat

## <a name="use-case-predictive-maintenance"></a>Případ použití: prediktivní údržba

Tento scénář vychází z případu použití prezentovaného na konferenci Prognostics and Health Management (PHM08) v 2008. Cílem je předpovědět zbývající dobu života (RUL) sady turbofanch motorů pro letadlo. Tato data byla vygenerována pomocí map jazyka C, komerční verze map (modulární Aero-Propulsion systém simulace). Tento software poskytuje flexibilní prostředí pro simulaci turbofan Engine pro pohodlné simulaci parametrů stavu, řízení a stroje.

Data použitá v tomto kurzu jsou pořízena ze [sady dat simulace degradace modulu Turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Ze souboru Readme:

***Experimentální scénář***

*Sady dat se skládají z několika lineární časových řad. Každá sada dat je dále rozdělena na školicí a testovací podmnožiny. Každá časová řada pochází z jiného modulu, tj. je možné, že se data považují za z loďstva motorů stejného typu. Každý stroj začíná různými stupni počátečního opotřebení a výrobní variací, které uživatel nezná. Tento opotřebení a variace se považují za normální, tj. není považována za stav selhání. Existují tři provozní nastavení, která mají podstatný vliv na výkon motoru. Tato nastavení jsou také obsažena v datech. Data jsou kontaminována pomocí snímače hluku.*

*Modul pracuje normálně na začátku každé časové řady a v určitém okamžiku během řady vyvíjí chybu. V sadě školení se chyba zvětšuje až do selhání systému. V sadě testů časová řada ukončí určitou dobu před selháním systému. Cílem konkurence je předpovědět počet zbývajících provozních cyklů před selháním v sadě testů, tj. počet provozních cyklů po posledním cyklu, kdy bude modul fungovat i nadále. K dispozici je také vektor hodnot true zbylé životnosti (RUL) pro testovací data.*

Vzhledem k tomu, že data byla publikována pro konkurenci, je několik přístupů k odvozeným modelům strojového učení Publikováno nezávisle. Zjistili jsme, že příklady studijních příkladů jsou užitečné pro porozumění procesu a důvodům při vytváření konkrétního modelu strojového učení. Podívejte se například na:

[Model předpovědi selhání leteckého motoru](https://github.com/jancervenka/turbofan_failure) podle Jancervenka uživatele GitHubu.

[Snížení výkonu stroje Turbofan](https://github.com/hankroark/Turbofan-Engine-Degradation) uživatelem Hankroark na GitHubu.

## <a name="process"></a>Proces

Následující obrázek znázorňuje přibližné kroky, které sledujeme v tomto kurzu:

![Diagram architektury pro kroky procesu](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Shromažďování školicích dat**: proces začíná shromažďováním školicích dat. V některých případech již byla data shromážděna a k dispozici v databázi nebo ve formě datových souborů. V jiných případech, zejména u scénářů IoT, se data musí shromažďovat ze zařízení IoT a senzorů a ukládají se do cloudu.

   Předpokládáme, že nemáte kolekci turbofan modulů, takže soubory projektu zahrnují jednoduchý simulátor zařízení, který odesílá data zařízení NASA do cloudu.

1. **Připravte data**. Ve většině případů nezpracovaná data shromážděná ze zařízení a senzorů vyžadují přípravu na strojové učení. Tento krok může zahrnovat vyčištění dat, přeformátování dat nebo předzpracování pro vkládání dalších informací strojového učení.

   Pro naše data strojového stroje v letadle zahrnuje Příprava dat výpočet explicitních časů pro všechny datové body v ukázce na základě skutečných pozorování dat. Tyto informace umožňují algoritmu strojového učení vyhledat korelace mezi skutečnými vzorci dat senzorů a očekávaným zbývajícím životním časem motoru. Tento krok je vysoce specifický pro doménu.

1. **Sestavte model strojového učení**. Na základě připravených dat teď můžeme experimentovat s různými algoritmy strojového učení a parameterizations se naučit modely a porovnat výsledky.

   V tomto případě pro účely testování porovnáváme předpokládaný výsledek vypočítaný modelem se skutečným výsledkem zjištěným u sady motorů. V Azure Machine Learning můžeme spravovat různé iterace modelů, které vytvoříme v registru modelu.

1. **Nasaďte model**. Jakmile budeme mít model, který splňuje naše kritéria úspěchu, můžeme přejít na nasazení. To zahrnuje zabalení modelu do aplikace webové služby, kterou lze doplňovat s daty pomocí volání REST a výsledků analýz. Aplikace webové služby se pak zabalí do kontejneru Docker, který pak můžete nasadit buď v cloudu, nebo jako modul IoT Edge. V tomto příkladu se zaměříme na nasazení na IoT Edge.

1. **Udržujte a upřesněte model**. Naše práce se neprovádí po nasazení modelu. V mnoha případech chceme pokračovat ve shromažďování dat a pravidelné nahrávání těchto dat do cloudu. Tato data pak můžeme použít k reučení a upřesnění našeho modelu, který potom můžeme znovu nasadit do IoT Edge.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz je součástí sady, kde každý článek sestaví na práci, která se provádí v předchozích verzích. Počkejte prosím na vyčištění všech prostředků, dokud nedokončíte finální kurz.

## <a name="next-steps"></a>Další kroky

Tento kurz je rozdělen do následujících částí:

1. Nastavte svůj vývojový počítač a služby Azure.
2. Vygenerujte školicí data pro modul Machine Learning.
3. Proveďte výuku a nasaďte modul Machine Learning.
4. Nakonfigurujte zařízení IoT Edge tak, aby fungovalo jako transparentní brána.
5. Vytvořte a nasaďte IoT Edge moduly.
6. Odešlete data do zařízení IoT Edge.

Pokračujte dalším článkem a nastavte vývojový počítač a zřiďte prostředky Azure.

> [!div class="nextstepaction"]
> [Nastavení prostředí pro strojové učení na IoT Edge](tutorial-machine-learning-edge-02-prepare-environment.md)