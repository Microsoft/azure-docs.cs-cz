---
title: Kurz reakce na incidenty – Azure Security Center
description: V tomto kurzu se dozvíte, jak řadit výstrahy zabezpečení, jak určit hlavní příčinu & rozsahu incidentu, a data zabezpečení hledání.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: memildin
ms.openlocfilehash: 08e04749eae7158abb501f9a4d127cdd7a89a391
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336271"
---
# <a name="tutorial-respond-to-security-incidents"></a>Kurz: Reakce na incidenty zabezpečení
Security Center nepřetržitě analyzuje vaše hybridní cloudové úlohy pomocí pokročilých analytických algoritmů a inteligentního rozpoznávání hrozeb a upozorní vás na škodlivé aktivity. Kromě toho můžete do Security Center integrovat výstrahy z jiných bezpečnostních produktů a služeb a vytvořit vlastní výstrahy na základě definovaných indikátorů nebo zdroje informací. Jakmile je vygenerována výstraha, je pro prozkoumání a nápravu nutná rychlá akce. V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
> * Analyzovat výstrahy zabezpečení
> * Dalším šetřením určit hlavní příčinu a rozsah incidentu zabezpečení
> * Prohledávat data o zabezpečení v rámci vyšetřování

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky
Pro krokování s funkcemi popsanými v tomto kurzu musíte mít povolený Azure Defender. Azure Defender si můžete vyzkoušet bez jakýchkoli nákladů. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/). Rychlý Start [Začínáme s Security Center](security-center-get-started.md) vás provede upgradem.

## <a name="scenario"></a>Scénář
Společnost Contoso nedávno provedla migraci některých místních prostředků do Azure, včetně některých databází SQL a úloh obchodního systému založených na virtuálních počítačích. Nyní má Hlavní tým reakce na incidenty zabezpečení počítačů (CSIRT) společnosti Contoso problém s vyšetřováním problémů zabezpečení kvůli tomu, že analytické funkce zabezpečení nejsou integrované se současnými nástroji reakce na incidenty. Tato nedostatečná integrace představuje problém během fáze Zjištění (příliš mnoho falešných poplachů) a také během fází Posouzení a Diagnostika. Jako součást této migrace se rozhodli vyjádřit výslovný souhlas s tím, aby jim Security Center pomohl vyřešit tento problém.

První fáze migrace byla dokončena po připojení všech prostředků a vyřešení všech doporučení zabezpečení od Security Center. CSIRT společnosti Contoso je ústředním bodem řešení incidentů zabezpečení počítačů. Tým se skládá ze skupiny lidí, kteří mají odpovědnost za řešení všech incidentů zabezpečení. Členové týmu mají jasně určené povinnosti, aby se zajistilo, že jsou pokryté všechny oblasti reakce.

Pro účely tohoto scénáře se soustředíme na role následujících osob, které jsou součástí týmu CSIRT společnosti Contoso:

![Životní cyklus reakce na incidenty](./media/tutorial-security-incident/security-center-incident-response.png)

Judy pracuje v oddělení zabezpečení. Mezi jejich odpovědnosti patří:

* Nepřetržité monitorování a reagování na ohrožení zabezpečení.
* Předávání řešení ohrožení vlastníkovi cloudových úloh nebo analytikovi zabezpečení (podle potřeby).

Sam je analytikem zabezpečení a jejich zodpovědnosti zahrnují:

* Vyšetřování útoků.
* Napravování výstrah.
* Práce s vlastníky úloh na určování a aplikaci nápravných kroků.

Jak vidíte, Judy a Sam mají rozdílné povinnosti a musí spolupracovat na sdílení informací ze Security Center.

## <a name="triage-security-alerts"></a>Analyzovat výstrahy zabezpečení
Security Center poskytuje jednotné zobrazení všech výstrah zabezpečení. Výstrahy zabezpečení jsou hodnoceny podle závažnosti a je-li to možné, jsou související výstrahy sloučeny do jednoho incidentu zabezpečení. Při analýze výstrah a incidentů byste měli:

- zrušit výstrahy, pro které není nutná žádná další akce, například pokud jde o falešně pozitivní výstrahu,
- zabránit známým útokům, například blokovat síťový provoz ze škodlivé IP adresy,
- určit, které výstrahy vyžadují další zkoumání.


1. V hlavní nabídce služby Security Center vyberte v části **DETEKCE** možnost **Výstrahy zabezpečení**:

   ![Výstrahy zabezpečení](./media/tutorial-security-incident/tutorial-security-incident-fig1.png)

2. V seznamu výstrah vyberte incident zabezpečení, což je kolekce výstrah, abyste se dozvěděli víc o tomto incidentu. Otevře se stránka **Byl zjištěn incident zabezpečení**.

   ![Zjištěn incident zabezpečení](./media/tutorial-security-incident/tutorial-security-incident-fig2.png)

3. Na této obrazovce uvidíte incident zabezpečení nahoře a pod ním seznam výstrah, které jsou jeho součástí. Klikněte na výstrahu, kterou chcete prošetřit – zobrazí se další informace.

   ![Podrobnosti výstrahy od incidentu](./media/tutorial-security-incident/tutorial-security-incident-fig3.png)

   Výstrahy mohou být různých typů, další podrobnosti o typech výstrah a možnostech nápravy si přečtěte v článku [Význam výstrah zabezpečení ve službě Azure Security Center](security-center-alerts-type.md). U výstrah, které je možné bezpečně zrušit, klikněte pravým tlačítkem na výstrahu a vyberte možnost **Zavřít**:

   ![Výstrahy](./media/tutorial-security-incident/tutorial-security-incident-fig4.png)

4. Pokud neznáte hlavní příčinu a rozsah škodlivé aktivity, pokračujte dalším krokem ve vyšetřování.

## <a name="investigate-an-alert-or-incident"></a>Šetření výstrahy nebo incidentu
1. Na stránce **Výstraha zabezpečení** klikněte na tlačítko **Zahájit šetření** (pokud jste už začali, tlačítko se změní na **Pokračovat v šetření**).

   ![Šetření](./media/tutorial-security-incident/tutorial-security-incident-fig5.png)

   Mapa šetření je grafická reprezentace entit, které jsou spojené s touto výstrahou nebo incidentem zabezpečení. Kliknutím na entitu v mapě zobrazíte informace o dané a mapa se rozšíří. Typ entity vybrané v mapě má své vlastnosti zvýrazněné v podokně na pravé straně stránky. Informace dostupné na každé kartě se liší podle vybrané entity. Během procesu šetření zkontrolujte všechny relevantní informace, abyste lépe pochopili pohyb útočníka.

2. Pokud potřebujete další fakta nebo podrobnější zkoumání entit, na které jste narazili během šetření, přejděte k dalšímu kroku.

## <a name="search-data-for-investigation"></a>Vyhledávání dat při šetření

Funkce vyhledávání ve službě Security Center vám pomůže najít další fakta o ohrožených systémech a další podrobnosti o entitách, které jsou součástí šetření.

Vyhledávání se provádí na řídicím panelu **Security Center** – klikněte na **Vyhledávání** v levém navigačním podokně, vyberte pracovní prostor obsahující entity, které chcete hledat, zadejte vyhledávací dotaz a klikněte na tlačítko hledání.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud máte v úmyslu pokračovat v práci s dalšími rychlými starty a kurzy, nechte Automatické zřizování a povolený program Azure Defender. Pokud nechcete pokračovat nebo chcete zakázat Azure Defender:

1. Vraťte se do hlavní nabídky Security Center a vyberte **ceny a nastavení**.
1. Vyberte předplatné, které chcete downgradovat.
1. Nastavte v **Azure Defenderu** na vypnuto.
1. Vyberte **Uložit**.

Pokud chcete vypnout automatické zřizování:

1. Vraťte se do hlavní nabídky Security Center a vyberte **zásady zabezpečení**.
2. Vyberte předplatné, pro které chcete vypnout automatické zřizování.
3. V části **Zásady zabezpečení – shromažďování dat** výběrem možnosti **Vypnuto** u volby **Onboarding** vypnete automatické zřizování.
4. Vyberte **Uložit**.

>[!NOTE]
> Při vypnutí automatického zřizování se agent Log Analytics neodebere z virtuálních počítačů Azure, ve kterých se agent zřídil. Vypnutí automatického zřizování omezí sledování zabezpečení pro vaše prostředky.
>

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se dozvěděli o funkcích služby Security Center, které můžete použít při reakci na incident zabezpečení, například:

> [!div class="checklist"]
> * Incident zabezpečení, který je agregací souvisejících výstrah pro určitý prostředek
> * Mapa šetření, což je grafická reprezentace entit spojených s danou výstrahou nebo incidentem zabezpečení
> * Možnosti vyhledávání, které umožňují najít další fakta o ohrožených systémech