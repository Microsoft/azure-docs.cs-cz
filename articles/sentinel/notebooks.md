---
title: Možnosti typu pomocí poznámkových bloků ve verzi Preview Sentinelu Azure | Dokumentace Microsoftu
description: Tento článek popisuje postup použití poznámkových bloků s funkcemi Azure Sentinelu typu.
services: sentinel
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: c6202f3e945d817c5e8fd7caa340a3e237bc34bd
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993108"
---
# <a name="use-notebooks-to-hunt-for-anomalies"></a>Použití poznámkových bloků na dost možná nejlepší anomálií

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel využívá výkon interaktivní poznámkové bloky Jupyter k poskytování přehledů a akce k prošetření nebo hunt bezpečnostních anomálií ve vašem prostředí. Azure Sentinel obsahuje předem pomocí poznámkových bloků, které byly vyvinuty analytikům zabezpečení od Microsoftu. Každý Poznámkový blok je sestavován samostatná pracovní postup pro případ použití konkrétní účel. Vizualizace jsou součástí každého poznámkového bloku pro rychlejší zkoumání dat a před internetovými útoky typu. Přizpůsobení integrovaných poznámkových bloků vašim potřebám, vytvoření zcela nové poznámkových bloků nebo naimportovat poznámkové bloky od Azure Sentinelu "komunitě GitHub. Poznámkové bloky Jupyter se importují jako projekt na stránce poznámkových bloků Azure – to umožňuje uživatelům přístup ke všem jejich poznámkových bloků Azure Sentinelu na jednom místě. Poznámkové bloky můžete spustit kliknutím na tlačítko nebo se dá nakonfigurovat uživatele tak, aby odpovídaly jejich prostředí.

Plně integrované prostředí umožňuje poznámkových bloků a spouštět na cloudové výpočty a úložiště s žádné základní režijní náklady na údržbu. Možnost využívat stávající ekosystému poznámkové bloky Jupyter vám umožní skupina lidí zdrojových modelů, bez sdílení dat zákazníků. 


Každý Poznámkový blok je hostovaná na poznámkových bloků Azure (aktuálně ve verzi preview), interaktivní vývoj prostředí v cloudu Azure. Poznámkové bloky jsou vždycky dostupné, z libovolného prohlížeče, kdekoli ve světě vždy k dispozici.  Azure Sentinelu "integrovaných poznámkových bloků pro šetření a typu se klonují do projektu, který patří, a který můžete upravit a přizpůsobit pro vaše prostředí. Zde jsou některé z nejoblíbenějších integrovaných poznámkových bloků:

- **Výstrahy vyšetřování a myslivost**: Tento interaktivní Poznámkový blok umožňuje rychlé třídění různých tříd výstrah tak, že načítání související aktivity a rozšíření se přidružená aktivita a data, ze které byla výstraha vygenerována výstraha.

- **Hostitel koncového bodu na základě typu**: Umožňuje hunt známky narušení zabezpečení podle procházení k podrobnostem relevantní aktivity související se zabezpečením přidružený k hostiteli, který koncový bod.  

- **Office přihlásit s asistencí myslivost**: Umožňuje pro podezřelé přihlášení možná nejlepší vizualizaci geografické umístění podezřelé protokolů, stejně jako zobrazení neobvyklé vzory přihlašování odvozený z dat Office 365.

## <a name="run-a-notebook"></a>Spuštění poznámkového bloku
V následujícím příkladu zajišťuje každodenní provoz integrované poznámkového bloku k vyhledání hloubkové všemi zúčastněnými stranami anomálie umístění zobrazíte, pokud kdokoli ve neočekávané umístění je něco v síti.

1. Na portálu Azure Sentinelu, klikněte na tlačítko **poznámkových bloků** a pak vyberte některou z integrovaných poznámkových bloků.
  
   ![Vyberte poznámkového bloku](./media/notebooks/select-notebook.png)

3.  Klikněte na tlačítko **Import** naklonujte úložiště GitHub do svého projektu poznámkových bloků Azure.
  ![Import poznámkového bloku](./media/notebooks/import1.png)
4.  Každý Poznámkový blok vás provede kroky pro provádění hunt nebo šetření. Modely, knihovny a další závislosti a konfigurace pro připojení k Azure Sentinelu je automaticky naimportován do povolit spuštění jedním kliknutím. Všechny kódu a knihoven, které jsou potřebné ke spuštění poznámkového bloku se načítají předem. Můžete okamžitě začít spuštěným Poznámkový blok pro váš pracovní prostor Log Analytics bez konfigurace.

   ![Import úložiště](./media/notebooks/import2.png)

5.  Prozkoumejte, upravit a spustit všechny ukázkové poznámkové bloky k dispozici. Toto může sloužit jako stavební bloky pro řadu různých scénářů.

  ![Vyberte poznámkového bloku](./media/notebooks/import3.png)



## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak spustit šetření typu pomocí poznámkových bloků v Azure Sentinelu. Další informace o Azure Sentinelu, naleznete v následujících článcích:

- [Proaktivně hunt hrozby](hunting.md)
- [Použití záložek ke uložit zajímavé informace při typu](bookmarks.md)