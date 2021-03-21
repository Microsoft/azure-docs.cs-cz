---
title: Hlavní řídicí panel nebo stránka přehled Azure Security Center
description: Přečtěte si o funkcích Security Center stránce Přehled
author: memildin
ms.author: memildin
ms.date: 03/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 768d69b626a870910d6734e1a1ddc29871f96afb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099773"
---
# <a name="azure-security-centers-overview-page"></a>Stránka s přehledem Azure Security Center

Když otevřete Azure Security Center, na první stránce se zobrazí stránka přehled. 

Tento interaktivní řídicí panel poskytuje jednotný pohled na stav zabezpečení vašich hybridních cloudových úloh. Kromě toho zobrazuje výstrahy zabezpečení, informace o pokrytí a další.

Chcete-li získat podrobnější informace, můžete vybrat libovolný prvek na stránce.

:::image type="content" source="media/overview-page/overview.png" alt-text="Stránka s přehledem služby Security Center":::

## <a name="features-of-the-overview-page"></a>Funkce stránky přehled

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Horní panel stránky s přehledem Security Center":::

**Horní panel nabídek** nabízí:
- **Předplatné** – výběrem tohoto tlačítka můžete zobrazit a filtrovat seznam předplatných. Security Center upraví displej tak, aby odrážel stav zabezpečení vybraných předplatných.
- **Co je nového** – otevře se zpráva k [vydání verze](release-notes.md) , abyste mohli průběžně udržovat nové funkce, opravy chyb a zastaralé funkce.
- **Čísla vysoké úrovně** připojených cloudových účtů, aby se zobrazil kontext informací v hlavních dlaždicích níže. A také počet vyhodnocených prostředků, aktivních doporučení a výstrah zabezpečení. Vyberte vyhodnocené číslo prostředků pro přístup k [inventáři assetů](asset-inventory.md). Přečtěte si další informace o připojení [účtů AWS](quickstart-onboard-aws.md) a vašich [projektů GCP](quickstart-onboard-gcp.md).


Ve středu stránky jsou **čtyři střední dlaždice**, pro které se pro další podrobnosti odkazuje na vyhrazený řídicí panel:
- Řešení **Secure skore** – Security Center průběžně vyhodnocuje vaše prostředky, odběry a organizace pro problémy se zabezpečením. Pak agreguje všechna zjištění do jediného skóre, abyste na první pohled mohli sdělit aktuální situaci zabezpečení: čím vyšší je skóre, tím se sníží zjištěná úroveň rizika. [Další informace](secure-score-security-controls.md).
- **Dodržování legislativních předpisů** – Security Center poskytuje přehledy o dodržování předpisů, které se stav na základě průběžného posouzení prostředí Azure. V souladu s osvědčenými postupy zabezpečení Security Center analyzuje rizikové faktory v hybridním cloudovém prostředí. Tato posouzení jsou namapována na řízení dodržování předpisů z podporované sady standardů. [Další informace](security-center-compliance-dashboard.md).
- **Azure Defender** – to je platforma ochrany zatížení cloudu (CWPP) integrovaná v rámci Security Center pro pokročilou, inteligentní ochranu vašich Azure a hybridních úloh. Tato dlaždice znázorňuje pokrytí vašich připojených prostředků (pro aktuálně vybraná předplatná) a nedávné výstrahy, barevně kódované podle závažnosti. [Další informace](azure-defender.md).
- **Správce brány firewall** – na této dlaždici se zobrazuje stav Center a sítí od [správce Azure firewall](../firewall-manager/overview.md). 


Podokno **Přehled** nabízí vlastní položky pro vaše prostředí, mezi které patří:
- Vaše nejvíc napadených prostředků
- Vaše ovládací prvky zabezpečení, které mají nejvyšší potenciál zvýšit vaše zabezpečené skóre
- Aktivní doporučení s největším dopadem na prostředky
- Poslední příspěvky na blogu Azure Security Center odborníky

## <a name="next-steps"></a>Další kroky

Tato stránka představila stránku Přehled Security Center. Související informace najdete v těchto tématech:

- [Prozkoumejte a spravujte svoje prostředky pomocí inventáře prostředků a nástrojů pro správu.](asset-inventory.md)
- [Skóre zabezpečení ve službě Azure Security Center](secure-score-security-controls.md)