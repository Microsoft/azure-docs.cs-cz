---
title: 'Kurz: Podrobný návod strojového učení na Azure IoT Edge'
description: Kurz vysoké úrovně, který prochází různé úkoly potřebné k vytvoření komplexního strojového učení na hraničním scénáři.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 965c420fa29c4cf82517148c01e17d6d7dd6ea97
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74106508"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Kurz: Komplexní řešení využívající Azure Machine Learning a IoT Edge

Aplikace IoT často chtějí využívat výhod inteligentního cloudu a inteligentního okraje. V tomto kurzu vás provedeme trénováním modelu strojového učení s daty shromážděnými ze zařízení IoT v cloudu, nasazením tohoto modelu do IoT Edge a pravidelnou údržbou a vylepšováním modelu.

Primárním cílem tohoto kurzu je představit zpracování dat IoT pomocí strojového učení, konkrétně na okraji. I když se dotýkáme mnoha aspektů obecného pracovního postupu strojového učení, tento kurz není určen jako podrobný úvod do strojového učení. Jako příklad se nesnažíme vytvořit vysoce optimalizovaný model pro případ použití – prostě děláme dost pro ilustraci procesu vytváření a používání životaschopného modelu pro zpracování dat IoT.

## <a name="target-audience-and-roles"></a>Cílová skupina a role

Tato sada článků je určena pro vývojáře bez předchozích zkušeností s vývojem IoT nebo strojovým učením. Nasazení strojového učení na hraničních zařízeních vyžaduje znalost toho, jak propojit širokou škálu technologií. Proto tento kurz zahrnuje celý scénář konce na konec k předvedení jednoho způsobu spojení těchto technologií společně pro řešení IoT. V reálném prostředí mohou být tyto úkoly rozděleny mezi několik lidí s různými specializacemi. Vývojáři by se například zaměřili na kód zařízení nebo cloud, zatímco datoví vědci navrhli analytické modely. Abychom umožnili jednotlivým vývojářům úspěšně dokončit tento kurz, poskytli jsme doplňující pokyny s přehledy a odkazy na další informace, o kterých doufáme, že stačí pochopit, co se děje, a proč.

Případně se můžete spojit se spolupracovníky různých rolí, abyste společně sledovali výukový program, přinesli své plné odborné znalosti a jako tým se naučili, jak věci do sebe zapadají.

V obou případech pomoci orientovat čtenáře, každý článek v tomto kurzu označuje roli uživatele. Mezi tyto role patří:

* Vývoj cloudu (včetně vývojáře cloudu pracujícího s kapacitou DevOps)
* Analýzy dat

## <a name="use-case-predictive-maintenance"></a>Případ použití: Prediktivní údržba

Tento scénář jsme založili na případu použití prezentovaném na konferenci o prognózách a řízení zdravotnictví (PHM08) v roce 2008. Cílem je předpovědět zbývající životnost (RUL) sady motorů turbodmychadla. Tato data byla generována pomocí C-MAPSS, komerční verze softwaru MAPSS (Modular Aero-Propulsion System Simulation). Tento software poskytuje flexibilní turbodmychadlo engine simulační prostředí pohodlně simulovat zdraví, řízení a parametry motoru.

Data použitá v tomto kurzu jsou převzata ze [sady dat simulace degradace modulu Turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Ze souboru readme:

***Experimentální scénář***

*Datové sady se skládají z více časových řad s více odchylkami. Každá datová sada je dále rozdělena na trénovací a testovací podmnožiny. Každá časová řada pochází z jiného motoru – tj. Každý motor začíná s různým stupněm počátečního opotřebení a výrobní chudiny, které uživatel i není znám. Toto opotřebení a kolísání se považuje za normální, tj. Existují tři provozní nastavení, která mají podstatný vliv na výkon motoru. Tato nastavení jsou také zahrnuty v datech. Data jsou kontaminována šumem senzoru.*

*Motor pracuje normálně na začátku každé časové řady a v určitém okamžiku série se v yvine závada. V trénovací sadě se chyba zvětšuje až do selhání systému. V testovací sadě časová řada končí nějaký čas před selháním systému. Cílem soutěže je předpovědět počet zbývajících provozních cyklů před poruchou ve zkušební sadě, tj. Také za předpokladu, vektor true Zbývající životnost (RUL) hodnoty pro testovací data.*

Vzhledem k tomu, že data byla publikována pro soutěž, několik přístupů k odvození modelů strojového učení byly publikovány nezávisle. Zjistili jsme, že studium příkladů je užitečné při pochopení procesu a uvažování, které se podílejí na vytvoření konkrétního modelu strojového učení. Viz například:

[Model predikce selhání motoru letadla](https://github.com/jancervenka/turbofan_failure) uživatelem GitHub jancervenka.

[Degradace turbodmychadla uživatelem](https://github.com/hankroark/Turbofan-Engine-Degradation) GitHub hankroark.

## <a name="process"></a>Proces

Obrázek níže ilustruje hrubé kroky, které sledujeme v tomto tutoriálu:

![Diagram architektury pro kroky procesu](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Shromažďování trénovacích dat**: Proces začíná shromažďováním trénovacích dat. V některých případech již byla data shromážděna a jsou k dispozici v databázi nebo ve formě datových souborů. V ostatních případech, zejména pro scénáře IoT, je třeba data shromažďovat ze zařízení IoT a senzorů a ukládat je v cloudu.

   Předpokládáme, že nemáte kolekci turbodmychadel, takže soubory projektu obsahují jednoduchý simulátor zařízení, který odesílá data zařízení NASA do cloudu.

1. **Připravte data**. Ve většině případů budou nezpracovaná data shromážděná ze zařízení a senzorů vyžadovat přípravu na strojové učení. Tento krok může zahrnovat vyčištění dat, přeformátování dat nebo předběžné zpracování pro vložení dalších informací, které může strojové učení vypnout.

   Pro naše data stroje motoru letadla příprava dat zahrnuje výpočet explicitní čas-k-selhání časy pro každý datový bod ve vzorku na základě skutečných pozorování na data. Tyto informace umožňují algoritmu strojového učení najít korelace mezi skutečnými vzorky dat senzoru a očekávanou zbývající životností motoru. Tento krok je vysoce specifický pro doménu.

1. **Vytvořte model strojového učení**. Na základě připravených dat nyní můžeme experimentovat s různými algoritmy strojového učení a parametrizací, abychom trénovali modely a porovnávali výsledky mezi sebou.

   V tomto případě pro testování porovnáme předpovídaný výsledek vypočítaný modelem se skutečným výsledkem pozorovaným na sadě motorů. V Azure Machine Learning můžeme spravovat různé iterace modelů, které vytvoříme v registru modelů.

1. **Nasazení modelu**. Jakmile budeme mít model, který splňuje naše kritéria úspěchu, můžeme přejít k nasazení. To zahrnuje zabalení modelu do aplikace webové služby, která může být podávána s daty pomocí volání REST a výsledky analýzy vrácení. Aplikace webové služby je pak zabalena do kontejneru dockeru, který zase můžete nasadit buď v cloudu, nebo jako modul IoT Edge. V tomto příkladu se zaměřujeme na nasazení na IoT Edge.

1. **Udržovat a zpřesnit model**. Naše práce není provedena po nasazení modelu. V mnoha případech chceme pokračovat ve shromažďování dat a pravidelně je nahrávat do cloudu. Tato data pak můžeme použít k přeškolení a zpřesnění našeho modelu, který pak můžeme znovu nasadit do IoT Edge.

## <a name="prerequisites"></a>Požadavky

K dokončení kurzu, budete potřebovat přístup k předplatnému Azure, ve kterém máte práva k vytváření prostředků. Některé služby použité v tomto kurzu budou účtovány poplatky Azure. Pokud ještě nemáte předplatné Azure, můžete začít s [bezplatným účtem Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).

Potřebujete také počítač s nainstalovaným PowerShellem, kde můžete spouštět skripty a nastavit virtuální počítač Azure jako vývojový počítač.

V tomto dokumentu používáme následující sadu nástrojů:

* Azure IoT hub pro sběr dat

* Poznámkové bloky Azure jako hlavní front-end pro přípravu dat a experimentování se strojovým učením. Spuštění kódu pythonu v poznámkovém bloku na podmnožině ukázkových dat je skvělý způsob, jak získat rychlý iterativní a interaktivní obrat během přípravy dat. Jupyter notebooky lze také připravit skripty pro spuštění ve velkém měřítku v rámci výpočetního back-endu.

* Azure Machine Learning jako back-end pro strojové učení ve velkém měřítku a pro generování bitových obrázků strojového učení. Back-end Azure Machine Learning řídíme pomocí skriptů připravených a testovaných v poznámkových blocích Jupyteru.

* Azure IoT Edge pro off-cloudové aplikace image strojového učení

Je zřejmé, že existují i jiné možnosti k dispozici. V některých scénářích, například IoT Central lze použít jako alternativu bez kódu pro sběr počáteční trénovací data ze zařízení IoT.

## <a name="next-steps"></a>Další kroky

Tento kurz je rozdělen do následujících částí:

1. Nastavte si vývojový počítač a služby Azure.
2. Vygenerujte trénovací data pro modul strojového učení.
3. Trénování a nasazování modulu strojového učení.
4. Nakonfigurujte zařízení IoT Edge tak, aby působilo jako transparentní brána.
5. Vytvořte a nasaďte moduly IoT Edge.
6. Odesílejte data do zařízení IoT Edge.

Pokračujte k dalšímu článku a nastavte vývojový počítač a zřizujete prostředky Azure.

> [!div class="nextstepaction"]
> [Nastavení prostředí pro strojové učení na IoT Edge](tutorial-machine-learning-edge-02-prepare-environment.md)
