---
title: Zlepšit dodržování legislativních dodržování předpisů pomocí služby Azure Security Center | Dokumentace Microsoftu
description: 'Kurz: Zjistěte, jak ke zlepšení vaší dodržování legislativních předpisů pomocí služby Azure Security Center.'
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: dbff728c888e9e2f83d9a82ca9819bf9dd0b89e9
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267959"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Kurz: Zlepšit dodržování legislativních předpisů
---

Azure Security Center pomáhá zjednodušit proces pro splnění požadavků na dodržování legislativních předpisů, na řídicím panelu dodržování legislativních předpisů. Na řídicím panelu Security Center poskytuje přehledy o vašich stav dodržování předpisů podle průběžným vyhodnocováním prostředí Azure. Posouzení, která provádí pomocí služby Security Center analyzovat rizikové faktory v hybridním cloudovém prostředí podle osvědčených postupů zabezpečení. Tyto hodnocení jsou namapované na ovládací prvky dodržování předpisů z podporovaných sada standardů. Na řídicím panelu dodržování legislativních předpisů máte jasného zobrazení stavu všech těchto posouzení ve vašem prostředí v kontextu konkrétní standardní nebo nařízení. Jak reagovat na doporučení a snížit rizikové faktory ve vašem prostředí, můžete zobrazit tak stav dodržování předpisů vylepšit.

V tomto kurzu se naučíte, jak:

-   Vyhodnoťte vaše dodržování legislativních předpisů, na řídicím panelu dodržování legislativních předpisů

-   Zlepšit tak stav dodržování předpisů provedením akce v doporučení

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li si vyzkoušet postupy popsané v tomto kurzu, musíte mít cenovou úroveň Security Center Standard. Security Center úrovně Standard můžete vyzkoušet zdarma.
Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/). Článek Rychlý Start: [Onboarding předplatného Azure na Security Center Standard](https://docs.microsoft.com/en-us/azure/security-center/security-center-get-started) vás provede postupem upgradu na úroveň Standard.

##  <a name="assess-your-regulatory-compliance"></a>Vyhodnocení dodržování předpisů

Security Center průběžně posuzuje konfiguraci vašich prostředků identifikovat problémy se zabezpečením a ohrožení zabezpečení. Tyto posouzení se zobrazí jako doporučení, která se zaměřují na zlepšení vaší kontrolu zabezpečení. Na řídicím panelu dodržování legislativních předpisů můžete zobrazit sadu standardy pro dodržování předpisů s jejich požadavky a kde jsou požadavky na podporované mapované na posouzení použít zabezpečení. To umožňuje zobrazit váš stav dodržování předpisů s ohledem na standard, na základě stavu těchto posouzení.

Zobrazení řídicího panelu pro dodržování předpisů pomáhají zaměřit pozornost na mezery v souladu s standard nebo nařízení, která je pro vás důležité. Tento zaměřené zobrazení také umožňuje průběžně monitorovat vaše skóre dodržování předpisů v čase v rámci dynamických cloudové a hybridní prostředí.

>[!NOTE]
> Aktuálně se podporují zákonných norem jsou: Azure CIS, PCI DSS 3.2, ISO 27001 a SOC TSP. Další standardů se projeví v řídicím panelu se vyvíjí.

1.  V zabezpečení Center hlavní nabídky v části **zásady a dodržování předpisů** vyberte **řídicího panelu pro dodržování legislativních předpisů**. <br>
V horní části obrazovky uvidíte řídicí panel přehled o stavu dodržování předpisů pomocí podporovaných dodržování předpisů. Zobrazí se vaše celkové skóre dodržování předpisů a počtu předávání vs. selhání posouzení spojené s každou standard.

    ![počítač popis jistotou](./media/security-center-compliance-dashboard/compliance-dashboard.png)


2.  Vyberte kartu pro standardy dodržování předpisů, která je pro vás relevantní. Zobrazí se seznam všech ovládacích prvků pro tuto úroveň standard. U příslušných ovládacích prvků můžete zobrazit podrobnosti o úspěšným a neúspěšným posouzení přidružený ovládací prvek. Některé ovládací prvky zobrazují šedě. Tyto ovládací prvky nemají žádné posouzení Security Center k nim má přiřazené. Musíte analyzovat požadavky pro tyto a vyhodnoťte jejich ve vašem prostředí vlastními silami. Některé z nich může být vztahující se k procesu a ne technickou.

    ![Karta dodržování předpisů](./media/security-center-compliance-dashboard/compliance-pci.png)

3.  Vyberte **všechny** kartu k zobrazení všechna relevantní doporučení služby Security Center a jejich přidružené standardy. Tento náhled může být užitečné pro identifikaci různých standardů vliv na konkrétní doporučení. <br> Toto zobrazení potenciálně slouží k určení priority doporučení, které je třeba vyřešit. Například, pokud uvidíte, že doporučení **povolit MFA pro účty s oprávněními vlastníka předplatného** selhává na několika prostředcích a souvisí s více standardů vyřešením tohoto doporučení může mít vysoký dopad na vaše celkové skóre dodržování předpisů.

    ![dopad skóre dodržování předpisů](./media/security-center-compliance-dashboard/compliance-all-tabs.png)

## <a name="improve-your-compliance-posture"></a>Zlepšit tak stav dodržování předpisů


Uvedené informace na řídicím panelu dodržování legislativních předpisů, můžete zlepšit tak stav dodržování předpisů pomocí řešení doporučení přímo z řídicího panelu.

1.  Proklikejte se prostřednictvím všech selhání hodnocení, které se zobrazují na řídicím panelu zobrazíte podrobnosti o doporučení. Každé doporučení obsahuje sadu nápravných kroků, které byste měli dodržet, k vyřešení daného problému.

2.  Můžete vybrat konkrétní prostředků k zobrazení dalších podrobností a řešení doporučení pro daný prostředek. <br>Například v **Azure CIS standard** kartu, můžete kliknout na doporučení **vyžadovat zabezpečený přenos pro účet úložiště**.

    ![doporučení dodržování předpisů](./media/security-center-compliance-dashboard/compliance-recommendation.png)

3. Proklikejte se k informacím o doporučení, a vyberte prostředek není v pořádku, ho vás přímo pro práci s povolením **zabezpečený přenos úložiště** na webu Azure portal.<br>Další informace o tom, jak používat doporučení, najdete v části [implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).

    ![doporučení dodržování předpisů](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

4.  Až podniknete opatření, jak řešit doporučení, zobrazí se dopad na řídicí panel sestavu dodržování předpisů, protože zvyšuje vaše skóre dodržování předpisů.

    > [!NOTE]
    > Posouzení se spouštějí přibližně každých 12 hodin, tak až po spuštění posouzení, která se zobrazí dopad na vaše data o dodržování předpisů.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o použití řídicího panelu Security Center dodržování legislativních předpisů pro:

-   Zobrazovat a monitorovat tak stav dodržování předpisů, vzhledem k standardy a nařízeními, které jsou pro vás důležité.

-   Stav dodržování předpisů můžete Zlepšete řešení relevantní doporučení a sledování na zvýšení skóre dodržování předpisů.

Řídicí panel dodržování legislativních předpisů mohou výrazně usnadňují proces dodržování předpisů a výrazně snížili čas potřebný k shromáždění důkazy o dodržování předpisů pro Azure a hybridní prostředí.

Další informace o službě Security Center najdete v tématu:

-   [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.

-   [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md)– zjistěte, jak používat doporučení ve službě Azure Security Center k ochraně vašich prostředků Azure.

-   [Zlepšení hodnotíte zabezpečení ve službě Azure Security Center](security-center-secure-score.md)– zjistěte, jak určit prioritu ohrožení zabezpečení a doporučení týkající se zabezpečení a nejvíce zlepšit tak stav zabezpečení.

-   [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
