---
title: Hlavní řídicí panel nebo stránka přehled Azure Security Center
description: Přečtěte si o funkcích Security Center stránce Přehled
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: cc52610eacc3916b7a8978cba17a1db3f3d50686
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447680"
---
# <a name="azure-security-centers-overview-page"></a>Stránka s přehledem Azure Security Center

Když otevřete Azure Security Center, na první stránce se zobrazí stránka přehled. 

:::image type="content" source="media/overview-page/overview.png" alt-text="Stránka s přehledem služby Security Center":::

Vyhodnoťte a zhodnoťte zabezpečení vašich úloh a identifikujte a zmírnit rizika pomocí stránky s přehledem Security Center.

Přehled nabízí jednotný pohled na stav zabezpečení vašich hybridních cloudových úloh. Kromě toho zobrazuje výstrahy zabezpečení, informace o pokrytí a další.


## <a name="features-of-the-overview-page"></a>Funkce stránky přehled

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Stránka s přehledem služby Security Center":::

**Horní panel nabídek** nabízí:
- **Předplatné** – výběrem tohoto tlačítka můžete zobrazit a filtrovat seznam předplatných. Security Center upraví displej tak, aby odrážel stav zabezpečení vybraných předplatných.
- **Co je nového** – otevře se zpráva k [vydání verze](release-notes.md) , abyste mohli průběžně udržovat nové funkce, opravy chyb a zastaralé funkce.
- **Čísla vysoké úrovně** připojených cloudových účtů, aby se zobrazil kontext informací v hlavních dlaždicích níže. A také počet aktivních doporučení a upozornění.
    Přečtěte si další informace o připojení [účtů AWS](quickstart-onboard-aws.md) a vašich [projektů GCP](quickstart-onboard-gcp.md).


Ve středu stránky jsou **čtyři střední dlaždice**, pro které se pro další podrobnosti odkazuje na vyhrazený řídicí panel:
- Řešení **Secure skore** – Security Center průběžně vyhodnocuje vaše prostředky, odběry a organizace pro problémy se zabezpečením. Pak agreguje všechna zjištění do jediného skóre, abyste na první pohled mohli sdělit aktuální situaci zabezpečení: čím vyšší je skóre, tím se sníží zjištěná úroveň rizika. [Přečtěte si další informace](secure-score-security-controls.md).
- **Dodržování předpisů** – Security Center poskytuje přehledy o vašich stav dodržování předpisů na základě průběžného posouzení prostředí Azure. V souladu s osvědčenými postupy zabezpečení Security Center analyzuje rizikové faktory v hybridním cloudovém prostředí. Tato posouzení jsou namapována na řízení dodržování předpisů z podporované sady standardů. [Další informace](security-center-compliance-dashboard.md)
- **Azure Defender** – to je platforma ochrany zatížení cloudu (CWPP) integrovaná v rámci Security Center pro pokročilou, inteligentní a chráněnou službu Azure a hybridní úlohy. Tato dlaždice znázorňuje pokrytí vašich připojených prostředků (pro aktuálně vybraná předplatná) a nedávné výstrahy, barevně kódované podle závažnosti. [Přečtěte si další informace](azure-defender.md).
- **Inventář** – na této dlaždici se zobrazuje počet nemonitorovaných virtuálních počítačů a jednoduché barometery prostředků monitorovaných pomocí CenterBen zabezpečení. [Přečtěte si další informace](asset-inventory.md).


Podokno **Přehled** nabízí vlastní položky pro vaše prostředí, mezi které patří:
- Vaše nejvíc napadených prostředků
- Vaše ovládací prvky zabezpečení, které mají nejvyšší potenciál zvýšit vaše zabezpečené skóre
- Aktivní doporučení s největším dopadem na prostředky
- Poslední příspěvky na blogu Azure Security Center odborníky

## <a name="next-steps"></a>Další kroky

Tato stránka představila stránku Přehled Security Center. Související informace najdete v těchto tématech:

- [Prozkoumejte a spravujte svoje prostředky pomocí inventáře prostředků a nástrojů pro správu.](asset-inventory.md)
- [Bezpečné skóre v Azure Security Center](secure-score-security-controls.md)