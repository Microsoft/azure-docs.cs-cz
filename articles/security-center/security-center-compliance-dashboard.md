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
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 20a464011e5a8d37a6215b222323ca989e02ac04
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550904"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Kurz: Vylepšení dodržování právních předpisů

Azure Security Center pomáhá zjednodušit proces splnění požadavků na dodržování legislativních předpisů pomocí **řídicího panelu dodržování předpisů regulativního předpisu**. 

Security Center nepřetržitě posuzuje vaše hybridní cloudové prostředí, aby se analyzovaly rizikové faktory podle ovládacích prvků a osvědčených postupů ve standardech, které se pro vaše předplatné nastavily. Řídicí panel odráží stav dodržování předpisů pomocí těchto standardů. 

Když povolíte Security Center v předplatném Azure, automaticky se mu přiřadí [Srovnávací test zabezpečení Azure](../security/benchmarks/introduction.md). Toto široce uznávané sestavování srovnávacích testů v ovládacích prvcích z [centra pro Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) a [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) se soustředí na zabezpečení zaměřené na Cloud.

Řídicí panel dodržování předpisů zobrazuje stav všech posouzení v rámci vašeho prostředí podle zvolených standardů a předpisů. Při rozhodování o doporučeních a omezení rizikových faktorů ve vašem prostředí vylepšuje stav dodržování předpisů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vyhodnocení dodržování předpisů pomocí řídicího panelu dodržování předpisů
> * Vylepšete stav dodržování předpisů tím, že v doporučeních vybereme opatření.
> * Nastavit výstrahy na změny stav dodržování předpisů
> * Exportujte data o dodržování předpisů jako souvislý datový proud a jako týdenní snímky.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Postup pro procházení funkcí popsaných v tomto kurzu:

- Musí být povolený [Azure Defender](azure-defender.md) . Můžete vyzkoušet Azure Defender zdarma po dobu 30 dnů.
- Musíte být přihlášeni pomocí účtu, který má přístup čtenář k datům dodržování zásad (**čtecí modul zabezpečení** není dostatečný). Role **globálního čtecího zařízení** pro předplatné bude fungovat. Musíte mít minimálně přiřazené role **přispěvatele zásad prostředků** a **Správce zabezpečení** .

##  <a name="assess-your-regulatory-compliance"></a>Posouzení dodržování legislativních předpisů

Řídicí panel dodržování předpisů zobrazuje vybrané standardy dodržování předpisů se všemi požadavky, kde jsou podporované požadavky namapované na příslušné posouzení zabezpečení. Stav těchto posouzení odráží dodržování standardu.

Řídicí panel dodržování předpisů vám umožní zaměřit se na pozornost mezer v souladu s vašimi zvolenými normami a předpisy. Toto zobrazení s fokusem umožňuje také průběžně monitorovat dodržování předpisů v rámci dynamického cloudu a hybridních prostředí.

1. V nabídce Security Center vyberte **dodržování předpisů**.

    V horní části obrazovky je řídicí panel s přehledem stavu dodržování předpisů se sadou podporovaných předpisů pro dodržování předpisů. Zobrazí se celkové skóre dodržování předpisů a počet průchodů a neúspěšných vyhodnocení přidružených ke každému standardu.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Řídicí panel dodržování předpisů v legislativě" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Vyberte kartu pro standard dodržování předpisů, která je pro vás důležitá (1). Uvidíte, které odběry se standardně aplikují (2), a seznam všech ovládacích prvků pro tento standard (3). Pro příslušné ovládací prvky můžete zobrazit podrobnosti o předávání a selhání hodnocení přidružených k tomuto ovládacímu prvku (4), stejně jako počet ovlivněných prostředků (5). Některé ovládací prvky jsou zobrazeny šedě. K těmto ovládacím prvkům nejsou přidružena žádná Security Center posouzení. Ověřte požadavky těchto hodnot a vyhodnoťte je ve vašem prostředí sami. Některé z nich mohou být související s procesy a nikoli technické.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Zkoumání podrobností o dodržování předpisů pomocí konkrétního standardu":::

1. Pokud chcete vygenerovat sestavu PDF se souhrnem aktuálního stavu dodržování předpisů pro určitý standard, vyberte **Stáhnout sestavu**.

    Tato sestava poskytuje přehled o stavu dodržování předpisů pro vybrané standardní údaje na základě Security Center dat posouzení a organizuje se podle ovládacích prvků tohoto standardu. Tuto sestavu lze sdílet s příslušnými zúčastněnými stranami a může poskytovat důkazy pro interní a externí auditory.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Stáhnout sestavu dodržování předpisů":::

## <a name="improve-your-compliance-posture"></a>Zlepšení stav dodržování předpisů

Pomocí informací na řídicím panelu dodržování předpisů Vylepšete stav dodržování doporučení přímo v řídicím panelu.

1.  Kliknutím na kterékoli z neúspěšných posouzení, která se zobrazí na řídicím panelu, zobrazíte podrobnosti o tomto doporučení. Každé doporučení zahrnuje sadu nápravných kroků, které by měly být dodrženy k vyřešení tohoto problému.

1.  Pokud chcete zobrazit další podrobnosti a vyřešit doporučení k tomuto prostředku, vyberte konkrétní prostředek. <br>Například ve standardu **Azure CIS 1.1.0** vyberte na **virtuálních počítačích použití šifrování disku** s doporučeními.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Výběr doporučení od standardních potenciálních zákazníků přímo na stránku s podrobnostmi o doporučeních":::

1. Pokud v tomto příkladu vyberete možnost **provést akci** na stránce s podrobnostmi doporučení, přijdete na stránky virtuálního počítače Azure v Azure Portal, kde můžete povolit šifrování na kartě **zabezpečení** :

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Tlačítko akce na stránce s podrobnostmi o doporučení vede k možnostem nápravy":::

    Další informace o tom, jak používat doporučení, najdete [v tématu Implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).

1.  Po provedení akce pro řešení doporučení uvidíte dopad v sestavě řídicího panelu dodržování předpisů, protože se zvyšuje skóre dodržování předpisů.

    > [!NOTE]
    > Posouzení se spouští přibližně každých 12 hodin, takže se vám bude zobrazovat dopad na data o dodržování předpisů až po dalším spuštění příslušného posouzení.


## <a name="export-your-compliance-status-data"></a>Vyexportujte data o stavu dodržování předpisů.

Pokud chcete sledovat stav dodržování předpisů pomocí dalších monitorovacích nástrojů ve vašem prostředí, Security Center obsahuje mechanismus exportu, který to uděláte. Nakonfigurujte **průběžný export** pro odesílání vybraných dat do centra událostí Azure nebo do pracovního prostoru Log Analytics.

Použijte průběžný export dat do centra událostí Azure nebo do pracovního prostoru Log Analytics:

- Exportovat všechna legislativní data do **souvislého datového proudu**:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="Průběžně exportujte proud dat dodržování předpisů regulativními předpisy" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- Exportujte **týdenní snímky** dat dodržování předpisů, které jsou v souladu s předpisy:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="Průběžný export týdenního snímku dat dodržování předpisů" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

**Sestavu PDF/CSV** dat o dodržování předpisů můžete také exportovat přímo z řídicího panelu dodržování předpisů regulativního předpisu:

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="Exportujte data dodržování předpisů jako sestavu PDF nebo CSV." lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

Další informace najdete v [průběžném exportu Security Center dat](continuous-export.md).


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Spustit automatizaci pracovního postupu, když dojde ke změnám v dodržování předpisů

Funkce automatizace pracovního postupu Security Center může aktivovat Logic Apps vždy, když některý ze stavů pro vyhodnocení dodržování předpisů dodržuje předpisy.

Můžete třeba chtít, aby se v případě neúspěšného vyhodnocení dodržování předpisů Security Center e-mailem určitému uživateli e-mail. Nejprve musíte vytvořit aplikaci logiky (pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md)) a potom nastavit Trigger v novém automatizaci pracovního postupu, jak je vysvětleno v tématu [automatizace odpovědí na Security Center triggery](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Použití změn pro vyhodnocení dodržování předpisů v legislativě pro aktivaci automatizace pracovního postupu" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o použití řídicího panelu pro dodržování předpisů Security Center pro:

- Prohlédněte si a sledujte stav dodržování předpisů týkajících se standardů a předpisů, které jsou pro vás důležité.
- Vylepšete svůj stav dodržování předpisů tím, že vyřešte relevantní doporučení a sledujte skóre dodržování předpisů.

Řídicí panel dodržování předpisů může významně zjednodušit proces dodržování předpisů a významně zkrátit dobu potřebnou ke shromažďování důkazů dodržování předpisů pro prostředí Azure, hybridního a více cloudových prostředí.

Další informace najdete v těchto souvisejících stránkách:

- [Přizpůsobení sady standardů na řídicím panelu dodržování předpisů](update-regulatory-compliance-packages.md) – Naučte se, jak vybrat, které standardy se zobrazí na řídicím panelu dodržování předpisů regulativního předpisu. 
- [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Zjistěte, jak monitorovat stav svých prostředků Azure.
- [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Přečtěte si, jak používat doporučení v Azure Security Center k ochraně vašich prostředků Azure.