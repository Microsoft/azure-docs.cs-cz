---
title: 'Kurz: kontroly dodržování předpisů v legislativě – Azure Security Center'
description: 'Kurz: Naučte se, jak vylepšit dodržování legislativních předpisů pomocí Azure Security Center.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2019
ms.author: memildin
ms.openlocfilehash: 7f235f9255df49c096e224e1754ad2bbdf664423
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90895818"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Kurz: zlepšení dodržování legislativních předpisů
---

Azure Security Center pomáhá zjednodušit proces splnění požadavků na dodržování legislativních předpisů pomocí **řídicího panelu dodržování předpisů regulativního předpisu**. Na řídicím panelu Security Center poskytuje přehledy o dodržování předpisů stav na základě průběžného posouzení prostředí Azure. V souladu s osvědčenými postupy zabezpečení Security Center analyzuje rizikové faktory v hybridním cloudovém prostředí. Tato posouzení jsou namapována na řízení dodržování předpisů z podporované sady standardů. Na řídicím panelu dodržování předpisů můžete zobrazit stav všech posouzení v rámci vašeho prostředí v kontextu určitého standardu nebo nařízení. Při rozhodování o doporučeních a omezení rizikových faktorů ve vašem prostředí vylepšuje stav dodržování předpisů.

V tomto kurzu se naučíte, jak:

-   Vyhodnocení dodržování předpisů pomocí řídicího panelu dodržování předpisů

-   Vylepšete stav dodržování předpisů tím, že v doporučeních vybereme opatření.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pro krokování s funkcemi popsanými v tomto kurzu musí být povolený [Azure Defender](azure-defender.md) . Můžete vyzkoušet Azure Defender zdarma po dobu 30 dnů.

##  <a name="assess-your-regulatory-compliance"></a>Posouzení dodržování legislativních předpisů

Security Center nepřetržitě posuzuje konfiguraci vašich prostředků pro identifikaci problémů a ohrožení zabezpečení. Tato posouzení jsou uvedena jako doporučení, která se zaměřují na vylepšení zabezpečení. Na řídicím panelu dodržování předpisů můžete zobrazit sadu standardů dodržování předpisů se všemi požadavky, kde jsou podporované požadavky namapovány na příslušné posouzení zabezpečení. To vám umožní zobrazit stav dodržování předpisů s ohledem na standard na základě stavu těchto posouzení.

Zobrazení řídicího panelu dodržování předpisů může pomáhat zaměřit se na mezery v dodržování předpisů pomocí standardu nebo nařízení, které je pro vás důležité. Toto prioritní zobrazení také umožňuje průběžně monitorovat skóre dodržování předpisů v rámci dynamického cloudu a hybridních prostředí.

>[!NOTE]
> Ve výchozím nastavení Security Center podporuje následující zákonné standardy: Azure CIS, PCI DSS 3,2, ISO 27001 a SOC TSP. 
>
> Funkce [dynamické balíčky s kompatibilitou (Preview)](update-regulatory-compliance-packages.md) umožňuje upgradovat standardy zobrazené na řídicím panelu dodržování předpisů pro nové *dynamické* balíčky. Stejnou funkci ve verzi Preview můžete použít také k přidání nových balíčků dodržování předpisů a k monitorování dodržování předpisů pomocí dalších standardů. 

1.  V hlavní nabídce Security Center v části **zásady & dodržování předpisů** vyberte **dodržování předpisů**. <br>
V horní části obrazovky se zobrazí řídicí panel s přehledem stavu dodržování předpisů se sadou podporovaných předpisů pro dodržování předpisů. Můžete si prohlédnout celkové skóre dodržování předpisů a počet průchodů a vyhodnocení souvisejících s každým standardem.

    ![Popis počítače vysoká důvěra](./media/security-center-compliance-dashboard/compliance-dashboard.png)

2.  Vyberte kartu standardu dodržování předpisů, který je pro vás relevantní. Zobrazí se seznam všech ovládacích prvků pro daný standard. V případě použitelných ovládacích prvků můžete zobrazit podrobnosti o úspěšných a neúspěšných posouzeních přidružených k danému ovládacímu prvku. Některé ovládací prvky jsou zobrazeny šedě. K těmto ovládacím prvkům nejsou přidružena žádná Security Center posouzení. Ověřte požadavky těchto hodnot a vyhodnoťte je ve vašem prostředí sami. Některé z nich mohou být v souvislosti s procesy a nikoli technického typu.

    ![karta dodržování předpisů](./media/security-center-compliance-dashboard/compliance-pci.png)

1. Pokud chcete vygenerovat a stáhnout sestavu PDF shrnující aktuální stav dodržování předpisů pro určitý standard, klikněte na **Stáhnout sestavu**.

    Tato sestava poskytuje přehled o stavu dodržování předpisů pro vybrané standardní údaje na základě Security Center dat posouzení a organizuje se podle ovládacích prvků tohoto standardu. Tuto sestavu lze sdílet s příslušnými zúčastněnými stranami a může sloužit k poskytování důkazů pro interní a externí auditory.

    ![stáhnout](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>Zlepšení stav dodržování předpisů

S ohledem na informace na řídicím panelu dodržování předpisů můžete vylepšit stav dodržování doporučení přímo v řídicím panelu.

1.  Kliknutím na kterékoli z neúspěšných posouzení, která se zobrazí na řídicím panelu, zobrazíte podrobnosti o tomto doporučení. Každé doporučení zahrnuje sadu nápravných kroků, které by měly být dodrženy k vyřešení tohoto problému.

1.  Pokud chcete zobrazit další podrobnosti a vyřešit doporučení pro daný prostředek, můžete vybrat konkrétní prostředek. <br>Například na kartě **Standard Azure SNS** můžete kliknout na doporučení **Vyžadovat zabezpečený přenos do účtu úložiště**.

    ![Výběr doporučení * * vyžadovat zabezpečený přenos do účtu úložiště * *](./media/security-center-compliance-dashboard/compliance-recommendation.png)

1. Po kliknutí na informace o doporučeních a výběru prostředku, který není v pořádku, vás zavede přímo k možnosti povolení **přenosu zabezpečeného úložiště** v rámci Azure Portal.

    Další informace o tom, jak používat doporučení, najdete [v tématu Implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).

    ![Konfigurace účtu úložiště](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

1.  Po provedení akce k vyřešení doporučení uvidíte dopad v sestavě řídicího panelu dodržování předpisů, protože se zvyšuje skóre dodržování předpisů.

    > [!NOTE]
    > Posouzení se spouští přibližně každých 12 hodin, takže se akce projeví u vašich dat dodržování předpisů až po spuštění posouzení.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o použití řídicího panelu pro dodržování předpisů Security Center pro:

-   Můžete zobrazit a monitorovat stav dodržování předpisů vzhledem ke standardům a předpisům, které jsou pro vás důležité.

-   Vylepšete svůj stav dodržování předpisů tím, že vyřešte relevantní doporučení a sledujte skóre dodržování předpisů.

Řídicí panel dodržování předpisů může významně zjednodušit proces dodržování předpisů a významně zkrátit dobu potřebnou ke shromažďování důkazů dodržování předpisů pro vaše prostředí Azure a hybridní prostředí.

Další informace najdete tady:

-   [Aktualizace na dynamické balíčky dodržování předpisů na řídicím panelu dodržování předpisů (Preview)](update-regulatory-compliance-packages.md) – Přečtěte si o této funkci ve verzi Preview, která vám umožní aktualizovat standardy zobrazené na řídicím panelu dodržování předpisů na nové *dynamické* balíčky. Stejnou funkci ve verzi Preview můžete také přidat nové balíčky dodržování předpisů a monitorovat dodržování předpisů pomocí dalších standardů. 

-   [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Zjistěte, jak monitorovat stav svých prostředků Azure.

-   [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Přečtěte si, jak používat doporučení v Azure Security Center k ochraně vašich prostředků Azure.

-   [Vylepšete Vaše zabezpečené skóre v Azure Security Center](secure-score-security-controls.md) – Zjistěte, jak upřednostnit ohrožení zabezpečení a doporučení zabezpečení, aby se zlepšila bezpečnost stav.