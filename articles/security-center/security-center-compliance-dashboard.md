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
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: b0baa532e8ca986e76cfb938a198d8a8697bd4dd
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757691"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Kurz: Vylepšení dodržování právních předpisů

Azure Security Center pomáhá zjednodušit proces splnění požadavků na dodržování legislativních předpisů pomocí **řídicího panelu dodržování předpisů regulativního předpisu**. 

Security Center provádí nepřetržité vyhodnocení vašeho hybridního cloudového prostředí, aby se analyzovaly rizikové faktory podle kontrol a osvědčených postupů v normách, které se vztahují na vaše předplatná. Řídicí panel odráží stav dodržování předpisů pomocí těchto standardů. 

Když povolíte Security Center v předplatném Azure, automaticky se mu přiřadí [Srovnávací test zabezpečení Azure](../security/benchmarks/introduction.md). Toto široce uznávané sestavování srovnávacích testů v ovládacích prvcích z [centra pro Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) a [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) se soustředí na zabezpečení zaměřené na Cloud.

Na řídicím panelu dodržování předpisů můžete zobrazit stav všech posouzení v rámci vašeho prostředí v kontextu určitého standardu nebo nařízení. Při rozhodování o doporučeních a omezení rizikových faktorů ve vašem prostředí vylepšuje stav dodržování předpisů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vyhodnocení dodržování předpisů pomocí řídicího panelu dodržování předpisů
> * Vylepšete stav dodržování předpisů tím, že v doporučeních vybereme opatření.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Postup pro procházení funkcí popsaných v tomto kurzu:

- Musí být povolený [Azure Defender](azure-defender.md) . Můžete vyzkoušet Azure Defender zdarma po dobu 30 dnů.
- Musíte být přihlášeni pomocí účtu, který má přístup čtenář k datům dodržování zásad (**čtecí modul zabezpečení** není dostatečný). Role **globálního čtecího zařízení** pro předplatné bude fungovat. Musíte mít minimálně přiřazené role **přispěvatele zásad prostředků** a **Správce zabezpečení** .

##  <a name="assess-your-regulatory-compliance"></a>Posouzení dodržování legislativních předpisů

Řídicí panel dodržování předpisů zobrazuje vybrané standardy dodržování předpisů se všemi požadavky, kde jsou podporované požadavky namapované na příslušné posouzení zabezpečení. Stav těchto posouzení odráží dodržování standardu.

Řídicí panel dodržování předpisů vám umožní zaměřit se na pozornost na nedostatky v dodržování standardů a předpisů, na které záleží. Toto zobrazení s fokusem umožňuje také průběžně monitorovat dodržování předpisů v rámci dynamického cloudu a hybridních prostředí.

1. V nabídce Security Center vyberte **dodržování předpisů**.

    V horní části obrazovky je řídicí panel s přehledem stavu dodržování předpisů se sadou podporovaných předpisů pro dodržování předpisů. Můžete si prohlédnout celkové skóre dodržování předpisů a počet průchodů a vyhodnocení souvisejících s každým standardem.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Řídicí panel dodržování předpisů v legislativě":::

1. Vyberte kartu pro standard dodržování předpisů, která je pro vás důležitá (1). Uvidíte, které odběry se standardně aplikují (2), a seznam všech ovládacích prvků pro tento standard (3). Pro příslušné ovládací prvky můžete zobrazit podrobnosti o předávání a selhání hodnocení přidružených k tomuto ovládacímu prvku (4), stejně jako počet ovlivněných prostředků (5). Některé ovládací prvky jsou zobrazeny šedě. K těmto ovládacím prvkům nejsou přidružena žádná Security Center posouzení. Ověřte požadavky těchto hodnot a vyhodnoťte je ve vašem prostředí sami. Některé z nich mohou být související s procesy a nikoli technické.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Zkoumání podrobností o dodržování předpisů pomocí konkrétního standardu":::

1. Pokud chcete vygenerovat sestavu PDF se souhrnem aktuálního stavu dodržování předpisů pro určitý standard, vyberte **Stáhnout sestavu**.

    Tato sestava poskytuje přehled o stavu dodržování předpisů pro vybrané standardní údaje na základě Security Center dat posouzení a organizuje se podle ovládacích prvků tohoto standardu. Tuto sestavu lze sdílet s příslušnými zúčastněnými stranami a může poskytovat důkazy pro interní a externí auditory.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Stáhnout sestavu dodržování předpisů":::

## <a name="improve-your-compliance-posture"></a>Zlepšení stav dodržování předpisů

S ohledem na informace na řídicím panelu dodržování předpisů můžete vylepšit stav dodržování doporučení přímo v řídicím panelu.

1.  Kliknutím na kterékoli z neúspěšných posouzení, která se zobrazí na řídicím panelu, zobrazíte podrobnosti o tomto doporučení. Každé doporučení zahrnuje sadu nápravných kroků, které by měly být dodrženy k vyřešení tohoto problému.

1.  Pokud chcete zobrazit další podrobnosti a vyřešit doporučení pro daný prostředek, můžete vybrat konkrétní prostředek. <br>Například ve standardu **Azure CIS 1.1.0** můžete vybrat, že se **má na virtuálních počítačích použít šifrování disku** s doporučeními.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Výběr doporučení od standardních potenciálních zákazníků přímo na stránku s podrobnostmi o doporučeních":::

1. Když v tomto příkladu vyberete **provést akci** ze stránky podrobnosti doporučení, přijdete na stránky virtuálního počítače Azure v Azure Portal, kde můžete otevřít kartu **zabezpečení** a povolit šifrování:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Tlačítko akce na stránce s podrobnostmi o doporučení vede k možnostem nápravy":::

    Další informace o tom, jak používat doporučení, najdete [v tématu Implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).

1.  Po provedení akce k vyřešení doporučení uvidíte dopad v sestavě řídicího panelu dodržování předpisů, protože se zvyšuje skóre dodržování předpisů.

    > [!NOTE]
    > Posouzení se spouští přibližně každých 12 hodin, takže se vám bude zobrazovat dopad na data o dodržování předpisů až po dalším spuštění příslušného posouzení.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o použití řídicího panelu pro dodržování předpisů Security Center pro:

-   Můžete zobrazit a monitorovat stav dodržování předpisů vzhledem ke standardům a předpisům, které jsou pro vás důležité.
-   Vylepšete svůj stav dodržování předpisů tím, že vyřešte relevantní doporučení a sledujte skóre dodržování předpisů.

Řídicí panel dodržování předpisů může významně zjednodušit proces dodržování předpisů a významně zkrátit dobu potřebnou ke shromažďování důkazů dodržování předpisů pro vaše prostředí Azure a hybridní prostředí.

Další informace najdete v těchto souvisejících článcích:

-   [Aktualizace na dynamické balíčky dodržování předpisů na řídicím panelu dodržování předpisů (Preview)](update-regulatory-compliance-packages.md) – Přečtěte si o této funkci ve verzi Preview, která vám umožní aktualizovat standardy zobrazené na řídicím panelu dodržování předpisů na nové *dynamické* balíčky. Stejnou funkci ve verzi Preview můžete také přidat nové balíčky dodržování předpisů a monitorovat dodržování předpisů pomocí dalších standardů. 
-   [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Zjistěte, jak monitorovat stav svých prostředků Azure.
-   [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Přečtěte si, jak používat doporučení v Azure Security Center k ochraně vašich prostředků Azure.
-   [Vylepšete Vaše zabezpečené skóre v Azure Security Center](secure-score-security-controls.md) – Zjistěte, jak upřednostnit ohrožení zabezpečení a doporučení zabezpečení, aby se zlepšila bezpečnost stav.
