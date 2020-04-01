---
title: 'Kurz: Kontroly dodržování předpisů – Azure Security Center'
description: 'Kurz: Naučte se, jak zlepšit dodržování předpisů pomocí Azure Security Center.'
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
ms.openlocfilehash: 1a6999c05c0b3dbaf572b376412f666c50c23df7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77604454"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Kurz: Zlepšete dodržování předpisů
---

Azure Security Center pomáhá zefektivnit proces pro splnění požadavků na dodržování předpisů pomocí **řídicího panelu dodržování předpisů**. Centrum zabezpečení na řídicím panelu poskytuje přehled o stavu dodržování předpisů na základě průběžného hodnocení vašeho prostředí Azure. Security Center analyzuje rizikové faktory ve vašem hybridním cloudovém prostředí podle doporučených postupů zabezpečení. Tato posouzení jsou mapována na kontroly dodržování předpisů z podporované sady standardů. Na řídicím panelu dodržování předpisů můžete zobrazit stav všech hodnocení ve vašem prostředí v kontextu konkrétní normy nebo nařízení. Při jednání podle doporučení a snižování rizikových faktorů ve vašem prostředí se vaše dodržování předpisů zlepšuje.

V tomto kurzu se naučíte:

-   Vyhodnoťte dodržování předpisů pomocí řídicího panelu pro dodržování předpisů

-   Zlepšete své dodržování předpisů tím, že podniknete kroky na základě doporučení

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li procházet funkce uvedené v tomto kurzu, musíte mít Standard cenové úrovně Security Center. Můžete zkusit Security Center Standard zdarma.
Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/). Článek Rychlý Start: [Onboarding předplatného Azure na Security Center Standard](https://docs.microsoft.com/azure/security-center/security-center-get-started) vás provede postupem upgradu na úroveň Standard.

##  <a name="assess-your-regulatory-compliance"></a>Vyhodnoťte dodržování předpisů

Security Center průběžně vyhodnocuje konfiguraci prostředků k identifikaci problémů se zabezpečením a slabých míst. Tato hodnocení jsou prezentována jako doporučení, která se zaměřují na zlepšení vaší bezpečnostní hygieny. Na řídicím panelu dodržování předpisů můžete zobrazit sadu standardů dodržování předpisů se všemi jejich požadavky, kde jsou podporované požadavky mapovány na příslušná posouzení zabezpečení. To vám umožní zobrazit stav dodržování předpisů s ohledem na standard, na základě stavu těchto hodnocení.

Zobrazení řídicího panelu pro dodržování předpisů může pomoci zaměřit vaši pozornost na mezery v souladu se standardem nebo předpisem, které jsou pro vás důležité. Toto cílené zobrazení také umožňuje průběžně sledovat skóre dodržování předpisů v průběhu času v rámci dynamického cloudu a hybridních prostředí.

>[!NOTE]
> Ve výchozím nastavení security center podporuje následující regulační standardy: Azure CIS, PCI DSS 3.2, ISO 27001 a SOC TSP. 
>
> Funkce [dynamické balíčky dodržování předpisů (preview)](update-regulatory-compliance-packages.md) umožňuje upgradovat standardy uvedené na řídicím panelu dodržování předpisů na nové *dynamické* balíčky. Stejnou funkci náhledu můžete také použít k přidání nových balíčků dodržování předpisů a sledování dodržování dalších standardů. 

1.  V hlavní nabídce Centra zabezpečení vyberte v části **ZÁSADY dodržování předpisů v části ZÁSADY & dodržování předpisů** **dodržování předpisů**. <br>
V horní části obrazovky se zobrazí řídicí panel s přehledem stavu dodržování předpisů se sadou podporovaných předpisů pro dodržování předpisů. Můžete zobrazit celkové skóre dodržování předpisů a počet absolvování vs selhání hodnocení spojené s každou normu.

    ![popis počítače vysoká důvěra](./media/security-center-compliance-dashboard/compliance-dashboard.png)

2.  Vyberte kartu standardu dodržování předpisů, který je pro vás relevantní. Zobrazí se seznam všech ovládacích prvků pro daný standard. V případě použitelných ovládacích prvků můžete zobrazit podrobnosti o úspěšných a neúspěšných posouzeních přidružených k danému ovládacímu prvku. Některé ovládací prvky jsou šedé. Tyto ovládací prvky nemají žádné hodnocení Centra zabezpečení s nimi spojené. Zkontrolujte požadavky na tyto a posoudit je ve vašem prostředí na vlastní pěst. Některé z nich mohou souviset s procesem a nikoli technické.

    ![karta dodržování předpisů](./media/security-center-compliance-dashboard/compliance-pci.png)

1. Chcete-li vygenerovat a stáhnout zprávu PDF shrnující aktuální stav dodržování předpisů pro určitý standard, klepněte na **tlačítko Stáhnout zprávu**.

    Sestava poskytuje souhrn vysoké úrovně stavu dodržování předpisů pro vybraný standard na základě dat hodnocení Centra zabezpečení a je uspořádána podle ovládacích prvků daného standardu. Zpráva může být sdílena s příslušnými zúčastněnými stranami a může sloužit k poskytnutí důkazů interním a externím auditorům.

    ![stáhnout](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>Zlepšete své dodržování předpisů

Vzhledem k informacím na řídicím panelu dodržování předpisů můžete zlepšit stav dodržování předpisů řešením doporučení přímo na řídicím panelu.

1.  Kliknutím na libovolné neúspěšné hodnocení, které se zobrazí na řídicím panelu, zobrazíte podrobnosti pro toto doporučení. Každé doporučení obsahuje sadu nápravných kroků, které by měly být dodržovány k vyřešení problému.

1.  Můžete vybrat konkrétní zdroj, chcete-li zobrazit další podrobnosti a vyřešit doporučení pro tento prostředek. <br>Například na kartě **Standard Azure CIS** můžete kliknout na doporučení **Vyžadovat zabezpečený přenos do účtu úložiště**.

    ![doporučení pro dodržování předpisů](./media/security-center-compliance-dashboard/compliance-recommendation.png)

1. Když kliknete na informace o doporučení a vyberete prostředek není v pořádku, vede vás přímo k prostředí **umožňujícímu přenos zabezpečeného úložiště** v rámci portálu Azure.

    Další informace o tom, jak použít doporučení, najdete [v tématu Implementace doporučení zabezpečení v Centru zabezpečení Azure](security-center-recommendations.md).

    ![doporučení pro dodržování předpisů](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

1.  Po přijetí opatření k vyřešení doporučení se v sestavě řídicího panelu pro dodržování předpisů zobrazí dopad, protože se zlepšuje skóre dodržování předpisů.

    > [!NOTE]
    > Posouzení se spouští přibližně každých 12 hodin, takže se akce projeví u vašich dat dodržování předpisů až po spuštění posouzení.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o použití řídicího panelu dodržování předpisů Centra zabezpečení k:

-   Zobrazení a sledování stavu dodržování předpisů ve vztahu k normám a předpisům, které jsou pro vás důležité.

-   Zlepšete svůj stav dodržování předpisů tím, že vyřešíte příslušná doporučení a sledujete, jak se skóre dodržování předpisů zlepšuje.

Řídicí panel dodržování předpisů může výrazně zjednodušit proces dodržování předpisů a výrazně zkrátit čas potřebný ke shromažďování důkazů o dodržování předpisů pro vaše prostředí Azure a hybridní prostředí.

Další informace najdete na:

-   [Aktualizace dynamických balíčků dodržování předpisů na řídicím panelu dodržování předpisů (Preview)](update-regulatory-compliance-packages.md) – Přečtěte si o této funkci náhledu, která vám umožní aktualizovat standardy uvedené na řídicím panelu dodržování předpisů na nové *dynamické* balíčky. Můžete také stejnou funkci náhledu přidat nové balíčky dodržování předpisů a sledovat dodržování dalších standardů. 

-   [Monitorování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak sledovat stav prostředků Azure.

-   [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak používat doporučení v Azure Security Center k ochraně prostředků Azure.

-   [Vylepšete své skóre zabezpečení v Azure Security Center](security-center-secure-score.md) – zjistěte, jak upřednostnit chyby zabezpečení a doporučení zabezpečení, abyste většinu zlepšili stav zabezpečení.