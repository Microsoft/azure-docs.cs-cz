---
title: Vyšetřování incidentů a výstrah v Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vám pomůže používat funkci šetření v Azure Security Center k vyšetřování výstrah a incidentů zabezpečení.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: a8e894a9-8781-4749-ae8f-8c8e01658566
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: memildin
ms.openlocfilehash: ef1684f11349696c22a290c90469a8d94f922e58
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603993"
---
# <a name="investigate-incidents-and-alerts-in-azure-security-center-retired"></a>Vyšetřování incidentů a výstrah v Azure Security Center (vyřazeno)
Tento dokument vám pomůže s používáním funkce šetření (Preview) v Azure Security Center k prozkoumání incidentů a výstrah zabezpečení.

> [!NOTE]
> Prostředí pro šetření výstrah a incidentů (Preview) bylo vyřazeno od 31. července 2019. Další informace a alternativní služby najdete v tématu [vyřazení funkcí Security Center (červenec 2019)](security-center-features-retirement-july2019.md#menu_investigate).

## <a name="what-is-investigation-in-security-center"></a>Co je šetření ve službě Security Center?
Funkce šetření ve službě Security Center umožňuje třídit, porozumět rozsahu a vysledovat původní příčinu potenciálního [incidentu zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-incident).

Cílem je zjednodušit proces šetření propojením všech entit ([výstrah zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), uživatelů, počítačů a incidentů) souvisejících s incidentem, který vyšetřujete.  Služba Security Center to dokáže díky korelaci relevantních dat se všemi souvisejícími entitami a zveřejnění této korelace v podobě interaktivního grafu, který pomáhá procházet objekty a vizualizovat relevantní informace.


> [!NOTE]
> * Ve vyšetřovací funkci Security Center se nepodporují [vlastní výstrahy](security-center-custom-alert.md) .
> * Šetření se podporuje jenom pro výstrahy založené na datech shromážděných ze serverů Windows.


## <a name="how-investigation-works"></a>Jak šetření funguje?
Šetření je tvořeno grafem, který se nachází v centrální oblasti řídicího panelu šetření. Graf je vždy zaměřený na konkrétní entitu a zobrazuje další entity, které jsou s ní spojené. Entitou může být výstraha zabezpečení, uživatel, počítač nebo incident.

![Mapa](./media/security-center-investigation/security-center-investigation-fig1.png)

Uživatel může mezi jednotlivými entitami přecházet kliknutím na příslušnou entitu v grafu. Graf automaticky přesune zaměření na vybranou entitu a entity, které s ní souvisí. Entity, které už nejsou relevantní, je možné z grafu odebrat.

### <a name="investigation-path"></a>Cesta šetření
Zatímco uživatel prochází různé entity, cesta šetření pomáhá udržovat přehled o kontextu šetření a umožňuje rychlou navigaci. Incident, který obsahuje výsledky šetření, je v cestě šetření vždy úplně vlevo.

![Cesta](./media/security-center-investigation/security-center-investigation-fig2.png)

### <a name="general-information"></a>Obecné informace
Když je entita zobrazena v grafu, na kartách se zobrazují další informace o této entitě. Karta **Informace** uvádí obecné informace o entitě z různých dostupných zdrojů informací.

![Obecné informace](./media/security-center-investigation/security-center-investigation-fig3.png)

Karta Informace zobrazuje informace relevantní pro incident vybraný na mapě. Incident je kontejner, který obsahuje výsledky šetření. Každé šetření probíhá v kontextu incidentu.

Incident se vytvoří pouze v případě, že uživatel klikne na tlačítko **Spustit šetření** u konkrétní výstrahy. Základní schopností, kterou má vyšetřovatel k dispozici, je označování entit, jako je uživatel, počítač nebo výstraha. Při označení entity jako související se uvede důvod. Od této chvíle se tato entita bude v grafu i v seznamu entit incidentu zobrazovat přímo pod incidentem.

### <a name="entities"></a>Entity

Karta **Entity** zobrazuje všechny související entity seskupené podle typu. To je užitečné ve dvou případech: když existuje příliš mnoho entit a není možné je znázornit v grafu nebo když jsou názvy entit příliš dlouhé a je jednodušší je zobrazit v tabulce.

![Entity](./media/security-center-investigation/security-center-investigation-fig4.png)

### <a name="search"></a>Hledat

Karta **Vyhledávání** uvádí všechny typy protokolů, které jsou pro entitu k dispozici. U každého typu protokolu můžete zobrazit počet dostupných záznamů. Kliknutím na jednotlivé typy protokolů přejdete na obrazovku vyhledávání. Na obrazovce vyhledávání můžete upřesnit vaše hledání a použít různé vyhledávací funkce, například nastavení výstrah. V aktuální verzi je karta Vyhledávání dostupná pouze pro entity uživatelů a počítačů.

![Hledat](./media/security-center-investigation/security-center-investigation-fig5.png)

### <a name="exploration"></a>Průzkum

Karta **Průzkum** umožňuje vyšetřovateli zkoumat data související s různými problémy, které s entitou souvisí. Například když je počítač prověřený, zobrazí se na kartě průzkumu seznam procesů, které jsou na něm provedeny. V některých případech karta průzkumu zobrazuje data, která by mohla poukazovat na podezřelý problém. Vyšetřovatel může data zkoumat na kartě nebo je otevřít na obrazovce vyhledávání, která umožňuje zkoumání velkých sad dat a použití pokročilých možností vyhledávání, jako je filtrování a export do aplikace Excel.

![Průzkum](./media/security-center-investigation/security-center-investigation-fig6.png)

### <a name="timeline"></a>Časová osa

Většina dat znázorněných v grafu a na různých kartách je relevantní pro konkrétní časové období. Tento časový rozsah je možné nastavit pomocí selektoru časového rozsahu v levé horní části grafu. Vyšetřovatel může časový rozsah vybrat různými způsoby.

![Časová osa](./media/security-center-investigation/security-center-investigation-fig7.png)

Následující položky jsou citlivé na časový rozsah:

- Vztah uživatel-počítač: v grafu se zobrazí pouze uživatelé, kteří se k počítači přihlásili v tomto časovém rozsahu.
- Zobrazené výstrahy při zkoumání počítačů a uživatelů: zobrazí se pouze výstrahy, ke kterým došlo v tomto časovém rozsahu.
- Karta entit se řídí stejnou logikou jako graf.

Následující položky se budou zobrazovat bez ohledu na vybraný časový rozsah:

- Když je zobrazená výstraha, vždy jsou zobrazeny i všechny entity (například uživatelé a počítače), které obsahuje.
- Pokud incident obsahuje entitu, zobrazí se i tato entita.

> [!NOTE]
> Na kartách **Vyhledávání** a **Průzkum** se zobrazují pouze záznamy z tohoto časového rozsahu.

## <a name="how-to-perform-an-investigation"></a>Jak se provádí šetření?

S šetřením můžete začít od incidentu zabezpečení nebo výstrahy. Zvolená možnost se bude lišit podle vašich potřeb. Následující kroky se používají ke spuštění šetření z výstrahy:

1.  Otevřete řídicí panel **Security Center**.
2.  Klikněte na **Výstrahy zabezpečení** a vyberte incident, který chcete vyšetřit.
3.  Na stránce incidentu klikněte na tlačítko **Spustit šetření** a zobrazí se řídicí panel **Šetření**.

    ![Výstraha](./media/security-center-investigation/security-center-investigation-fig8.png)

4. Na tomto řídicím panelu můžete na mapě vybrat entitu a na pravé straně obrazovky se zobrazí relevantní informace o této entitě.

    ![Řídicí panel Šetření](./media/security-center-investigation/security-center-investigation-fig9.png)

Z tohoto bodu můžete zkoumat entity, které byly spojeny s tímto incidentem, a o každé z nich zkoumat další podrobnosti.

## <a name="see-also"></a>Viz také:
V tomto dokumentu jste zjistili, jak používat funkci šetření ve službě Security Center. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení ve službě Security Center.
* [Monitorování stavu zabezpečení ve službě Azure Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Principy výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Seznamte se s dalšími typy výstrah zabezpečení.