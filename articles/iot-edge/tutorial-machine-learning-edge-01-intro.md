---
title: Podrobný průvodce pro Machine Learning v Azure IoT Edge | Dokumentace Microsoftu
description: Podrobný kurz vás provedou různými úkoly, které jsou potřebné k vytvoření end-to-end, strojové učení ve scénáři edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 916e48752431be41ff150c2ac84e66eb1e98e81f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057747"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Kurz: Vaše začátku do konce řešení s využitím Azure Machine Learning a IoT Edge

Aplikace IoT často chtějí využít nabídky inteligentní cloud a inteligentních hraničních zařízení. V tomto kurzu provedeme jednotlivými školení model strojového učení s data shromážděná ze zařízení IoT v cloudu, nasazení modelu na hraničních zařízeních IoT a údržbu a pravidelně upřesnění modelu.

Hlavním cílem tohoto kurzu je zavést zpracování dat IoT s strojového učení, konkrétně na hraničních zařízeních. Když jsme touch mnoho aspektů obecné služby machine learning pracovního postupu, v tomto kurzu není určen jako důkladný Úvod do strojového učení. Jako bod v případě jsme Nepokoušejte se vytvoření modelu vysoce optimalizovaných pro případy použití – stačí na ukazují proces vytvoření a použití modelu přijatelné pro zpracování dat IoT právě uděláme.

## <a name="target-audience-and-roles"></a>Cílové skupiny a role

Tato sada článků je určená pro vývojáře bez předchozích zkušeností ve službě IoT vývoj nebo machine learning. Nasazení machine learningu na hraničních zařízeních vyžaduje znalosti o tom, jak připojit širokou škálu technologií. Proto tento kurz se zaměřuje na celém scénáři začátku do konce k předvedení jedním ze způsobů připojení těchto technologií pro řešení IoT. V reálné prostředí může tyto úlohy distribuovat mezi několik lidí s jinou specializace. Například vývojáři by zaměřte se na zařízení nebo cloudové kód, zatímco odborníci přes data určená analytických modelů. Povolit jednotlivý vývojář pro úspěšné dokončení tohoto kurzu, nabízíme další doprovodné materiály s přehledy a odkazy na další informace, které Věříme, že stačí pochopit, co se provádí, a také proč.

Alternativně může týmové s kolegy z různých rolí a společně, postupujte podle kurzu přináší svou odbornost úplné nést a jako tým, zjistěte, jak věci navzájem propojené.

V obou případech usnadňují orientaci čtecího zařízení, každý článek v tomto kurzu Určuje roli uživatele. Tyto role patří:

* Vývoj pro cloudové prostředí (včetně cloud vývojář pracující v kapacitě DevOps)
* Analýzy dat

## <a name="use-case-predictive-maintenance"></a>Případ použití: Prediktivní údržba

Tento scénář jsme založené na uvedené na konferenci o Prognostics a správa stavu (PHM08) 2008 případu použití. Cílem je předpovídat zbývající dobu životnosti (RUL) sadu turbofan letadle moduly. Tato data se vygeneroval pomocí jazyka C-MAPSS, komerční verzi softwaru MAPSS (modulární Aero Propulsion systému simulace). Tento software poskytuje prostředí flexibilní turbofan engine simulace pohodlně simulace stavu, řízení a parametry modulu.

Data použitá v tomto kurzu je převzata z [Turbofan engine snížení simulace datovou sadu](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Ze souboru readme:

***Experimentální scénář***

*Datové sady se skládají z více s množstvím proměnných časové řady. Každou sadu dat je dále rozdělen do podmnožiny trénování a testování. Každá Časová řada se jiný modul – to znamená, data lze považovat za z park modulů stejného typu. Každý motor začíná u různých stupňů počáteční wear a vývoje a výrobního změna, kterou Neznámý pro uživatele. Tento wear a variace se považuje za normální, to znamená, není to považováno za selhání podmínku. Existují tři provozní nastavení, které mají podstatný dopad na výkon modulu. Tato nastavení jsou také zahrnuté v datech. Data je znečištěné. senzor šumu.*

*Modul normálně na začátku každé časové řady a rozvíjet chybu v určitém okamžiku během této série. V sadě školení roste chyby v rozsahu do selhání systému. V testovací sadě skončí časové řady nějakou dobu před selháním systému. Cílem soutěž je předpovědět počet zbývajících provozní cykly před selháním v sadě testů, například počet cyklů provozní po poslední cyklus, který modul bude i nadále fungovat. Vektor hodnot true zbývající dobu životnosti (RUL) k dispozici také pro testovací data.*

Vzhledem k tomu, že data byla publikována do soutěže, několik přístupů k odvození modelů strojového učení jsou publikovaná nezávisle na sobě. Zjistili jsme, že je užitečné vysvětlující Princip fungování procesu a důvody účastnící se vytváření modelem konkrétní machine learning studiu příklady. Viz například:

[Leteckého motoru selhání prediktivního modelu](https://github.com/jancervenka/turbofan_failure) podle uživatele jancervenka Githubu.

[Snížení turbofan engine](https://github.com/hankroark/Turbofan-Engine-Degradation) podle uživatele hankroark Githubu.

## <a name="process"></a>Proces

Následující obrázek znázorňuje hrubý kroky, které v tomto kurzu budeme postupovat podle:

![Diagram architektury pokyny procesu](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Shromažďování výukových dat**: Proces začíná shromažďováním trénovací data. V některých případech se data se už shromážděná a je k dispozici v databázi nebo v podobě datových souborů. V ostatních případech, zejména pro scénáře IoT je třeba data ze senzorů a zařízení IoT shromažďují a ukládají do cloudu.

   Předpokládáme, že není nutné kolekci modulů turbofan, tak soubory projektu zahrnují simulátor jednoduché zařízení, která odesílá data NASA zařízení do cloudu.

1. **Příprava dat**. Ve většině případů se nezpracovaná data jako shromážděných ze zařízení a senzorů bude vyžadovat Příprava pro službu machine learning. Tento krok může zahrnovat data vyčistit, přeformátování dat nebo předzpracování se vložit další informace o machine learning můžete klíč vypnout.

   Pro naše data počítače motoru letadla přípravu dat zahrnuje výpočet explicitní čas selhání časy pro každý datový bod v ukázce podle skutečné připomínky na data. Tyto informace umožňují strojového učení algoritmů a hledání korelací mezi vzorky dat skutečných senzorů a očekávaný zbývající dobu životnosti motoru. Tento krok je velmi specifický pro doménu.

1. **Sestavit model strojového učení**. Na základě dat připravené jsme můžete nyní experimentovat s algoritmů různých strojového učení a parameterizations k trénování modelů a porovnejte výsledky mezi sebou.

   V takovém případě pro testování porovnáme predikované výsledky počítají tak, že model se skutečný výsledek pozorovaný na sadu modulů. Ve službě Azure Machine Learning spravujeme různých iterací modelů, kterou jsme vytvořili v registru modelu.

1. **Nasazení modelu**. Jakmile model, který splňuje požadavky našich kritéria úspěchu, můžeme přesunout k nasazení. Budete muset obtékání modelu do webové aplikace služby, který lze vkládat s daty pomocí volání REST a vrátí výsledky analýzy. Webové aplikace služby se následně zabalené do kontejneru dockeru, které lze následně nasadit v cloudu nebo jako modul IoT Edge. V tomto příkladu se zaměříme na nasazení do hraničních zařízení IoT.

1. **Udržovat a zpřesnit modelu**. Naše práce se provádí po nasazení modelu. V mnoha případech chcete pokračovat, shromažďování dat a pravidelně nahrávání dat do cloudu. Tato data jsme pak můžete použít k přetrénujete a vylepšit náš model, který jsme pak můžete znovu nasadit na hraničních zařízeních IoT.

## <a name="prerequisites"></a>Požadavky

K absolvování tohoto kurzu potřebujete přístup k předplatnému Azure, ve kterém máte oprávnění k vytváření prostředků. Některé služby využité v tomto kurzu vám budou účtovány poplatky za Azure. Pokud ještě nemáte předplatné Azure, možná vám umožní začít [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).

Také musíte na počítači s prostředím PowerShell nainstalovaný, kde můžete spouštět skripty pro nastavení se virtuální počítač Azure jako vývojovém počítači.

V tomto dokumentu můžeme použít následující sady nástrojů:

* Služby Azure IoT hub pro sběr dat

* Azure poznámkových bloků jako naší hlavní front-endu pro přípravu dat a strojové učení, experimentování. Spustit jazyk python kódu v poznámkovém bloku na podmnožinu ukázkových dat je skvělý způsob, jak získat rychlý iterativní a interaktivní vyřízení při přípravě dat. Poznámkové bloky Jupyter lze také přípravné skripty, které chcete spouštět ve velkém měřítku v back-end výpočetní prostředky.

* Služba Azure Machine Learning jako back-endu pro strojové učení ve velkém měřítku a za machine learning generování bitových kopií. Můžeme vám zajistí pomocí skriptů připravit a otestovat v poznámkových blocích Jupyter back-endu Azure Machine Learning.

* Azure IoT Edge pro vypnutí cloudovou aplikaci machine learning bitové kopie

Samozřejmě nejsou k dispozici další možnosti. V některých scénářích například IoT Central umožňuje jako bez kódu alternativní zachycení počáteční trénovacích dat ze zařízení IoT.

## <a name="next-steps"></a>Další postup

V tomto kurzu je rozdělen do následujících částí:

1. Nastavení vývojového počítače a služby Azure.
2. Vygenerujte trénovací data pro počítač modulu learning module.
3. Trénování a nasazení modulu machine learning.
4. Nakonfigurujte zařízení tak, aby fungoval jako transparentní brána IoT Edge.
5. Vytvořit a nasadit moduly IoT Edge.
6. Odesílání dat do zařízení IoT Edge.

Pokračujte v dalším článku se nastavení vývojového počítače a zřízení prostředků Azure.

> [!div class="nextstepaction"]
> [Nastavení prostředí pro machine learningu na hraničních zařízeních IoT](tutorial-machine-learning-edge-02-prepare-environment.md)
