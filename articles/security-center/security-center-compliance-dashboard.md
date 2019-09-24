---
title: Zlepšení dodržování předpisů pomocí Azure Security Center | Microsoft Docs
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
ms.date: 4/30/2019
ms.author: memildin
ms.openlocfilehash: 20842997c5df81835024a6f458cd863b4e4d78b0
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202423"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Kurz: Vylepšené dodržování legislativních předpisů
---

Azure Security Center pomáhá zjednodušit proces splnění požadavků na dodržování legislativních předpisů pomocí řídicího panelu dodržování předpisů regulativního předpisu. Na řídicím panelu Security Center poskytuje přehledy o dodržování předpisů stav na základě průběžného posouzení prostředí Azure. Posouzení prováděné nástrojem Security Center analyzuje rizikové faktory v hybridním cloudovém prostředí v souladu s osvědčenými postupy zabezpečení. Tato posouzení jsou namapována na řízení dodržování předpisů z podporované sady standardů. Na řídicím panelu dodržování předpisů je jasné zobrazení stavu všech těchto posouzení v rámci vašeho prostředí v kontextu určitého standardu nebo nařízení. Když pracujete na doporučeních a omezíte rizikové faktory ve vašem prostředí, můžete si stav zlepšit dodržování předpisů.

V tomto kurzu se naučíte, jak:

-   Vyhodnocení dodržování předpisů pomocí řídicího panelu dodržování předpisů

-   Vylepšete stav dodržování předpisů tím, že v doporučeních vybereme opatření.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pro krokování s funkcemi popsanými v tomto kurzu musíte mít standardní cenovou úroveň Security Center. Můžete vyzkoušet Security Center Standard bez jakýchkoli nákladů.
Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/). Článek Rychlý Start: [Onboarding předplatného Azure na Security Center Standard](https://docs.microsoft.com/azure/security-center/security-center-get-started) vás provede postupem upgradu na úroveň Standard.

##  <a name="assess-your-regulatory-compliance"></a>Posouzení dodržování legislativních předpisů

Security Center nepřetržitě posuzuje konfiguraci vašich prostředků pro identifikaci problémů a ohrožení zabezpečení. Tato posouzení jsou uvedena jako doporučení, která se zaměřují na vylepšení zabezpečení. Na řídicím panelu dodržování předpisů můžete zobrazit sadu standardů dodržování předpisů se všemi požadavky, kde jsou podporované požadavky namapovány na příslušné posouzení zabezpečení. To vám umožní zobrazit stav dodržování předpisů s ohledem na standard na základě stavu těchto posouzení.

Zobrazení řídicího panelu dodržování předpisů může pomáhat zaměřit se na mezery v dodržování předpisů pomocí standardu nebo nařízení, které je pro vás důležité. Toto prioritní zobrazení také umožňuje průběžně monitorovat skóre dodržování předpisů v rámci dynamického cloudu a hybridních prostředí.

>[!NOTE]
> V současné době jsou podporované zákonné normy: Azure SNS, PCI DSS 3,2, ISO 27001 a SOC TSP. Další standardy se na řídicím panelu odrazí s tím, jak se bude vyvíjet.
1.  V hlavní nabídce Security Center v části **zásady &AMP; dodržování předpisů** vyberte **dodržování předpisů**. <br>
V horní části obrazovky se zobrazí řídicí panel s přehledem stavu dodržování předpisů se sadou podporovaných předpisů pro dodržování předpisů. Můžete si prohlédnout celkové skóre dodržování předpisů a počet průchodů a vyhodnocení souvisejících s každým standardem.

    ![Popis počítače vysoká důvěra](./media/security-center-compliance-dashboard/compliance-dashboard.png)


2.  Vyberte kartu standardu dodržování předpisů, který je pro vás relevantní. Zobrazí se seznam všech ovládacích prvků pro daný standard. V případě použitelných ovládacích prvků můžete zobrazit podrobnosti o úspěšných a neúspěšných posouzeních přidružených k danému ovládacímu prvku. Některé ovládací prvky jsou neaktivní. K takovým ovládacím prvkům nejsou přidružená žádná posouzení služby Security Center. Potřebujete tyto požadavky analyzovat a vyhodnotit je ve vašem prostředí sami. Některé z nich mohou být v souvislosti s procesy a nikoli technického typu.

    ![karta dodržování předpisů](./media/security-center-compliance-dashboard/compliance-pci.png)

3. Výběrem karty **vše** zobrazíte zobrazení všech relevantních doporučení Security Center a jejich přidružených standardů. Toto zobrazení může být užitečné k identifikaci všech různých standardů, na které má vliv konkrétní doporučení. <br> Toto zobrazení můžete použít k určení priorit doporučení, která potřebujete k vyřešení. Pokud například zjistíte, že doporučení **Povolit MFA pro účty s oprávněním vlastníka u vašeho** předplatného není u více prostředků k dispozici a je přidruženo k několika standardům, pak řešení tohoto doporučení bude mít vysoký dopad na celkové skóre dodržování předpisů.

    ![dopad skóre shody](./media/security-center-compliance-dashboard/compliance-all-tabs.png)

1. Pokud chcete vygenerovat a stáhnout sestavu PDF shrnující aktuální stav dodržování předpisů pro určitý standard, klikněte na **Stáhnout sestavu**.

    Tato sestava poskytuje přehled o stavu dodržování předpisů pro vybrané standardní údaje na základě Security Center dat posouzení a organizuje se podle ovládacích prvků tohoto standardu. Tuto sestavu lze sdílet s příslušnými zúčastněnými stranami a může sloužit k poskytování důkazů pro interní a externí auditory.

    ![download](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>Zlepšení stav dodržování předpisů

S ohledem na informace na řídicím panelu dodržování předpisů můžete vylepšit stav dodržování doporučení přímo v řídicím panelu.

1.  Kliknutím na kterékoli z neúspěšných posouzení, která se zobrazí na řídicím panelu, zobrazíte podrobnosti o tomto doporučení. Každé doporučení zahrnuje sadu nápravných kroků, které by měly být dodrženy k vyřešení tohoto problému.

2.  Pokud chcete zobrazit další podrobnosti a vyřešit doporučení pro daný prostředek, můžete vybrat konkrétní prostředek. <br>Například na kartě **Standard Azure SNS** můžete kliknout na doporučení **Vyžadovat zabezpečený přenos do účtu úložiště**.

    ![doporučení pro dodržování předpisů](./media/security-center-compliance-dashboard/compliance-recommendation.png)

3. Po kliknutí na informace o doporučeních a výběru prostředku, který není v pořádku, vás zavede přímo k možnosti povolení **přenosu zabezpečeného úložiště** v rámci Azure Portal.<br>Další informace o tom, jak používat doporučení, najdete v části [implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).

    ![doporučení pro dodržování předpisů](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

4.  Po provedení akce k vyřešení doporučení uvidíte dopad v sestavě řídicího panelu dodržování předpisů, protože se zvyšuje skóre dodržování předpisů.

    > [!NOTE]
    > Posouzení se spouští přibližně každých 12 hodin, takže se akce projeví u vašich dat dodržování předpisů až po spuštění posouzení.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o použití řídicího panelu pro dodržování předpisů Security Center pro:

-   Můžete zobrazit a monitorovat stav dodržování předpisů vzhledem ke standardům a předpisům, které jsou pro vás důležité.

-   Vylepšete svůj stav dodržování předpisů tím, že vyřešte relevantní doporučení a sledujte skóre dodržování předpisů.

Řídicí panel dodržování předpisů může významně zjednodušit proces dodržování předpisů a významně zkrátit dobu potřebnou ke shromažďování důkazů dodržování předpisů pro vaše prostředí Azure a hybridní prostředí.

Další informace o Security Center najdete v těchto tématech:

-   [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.

-   [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md)– Přečtěte si, jak používat doporučení v Azure Security Center k ochraně vašich prostředků Azure.

-   [Vylepšete zabezpečené skóre v Azure Security Center](security-center-secure-score.md)– Zjistěte, jak nastavit prioritu ohrožení zabezpečení a doporučení zabezpečení, aby se zlepšila bezpečnost stav.

-   [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
