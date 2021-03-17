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
ms.date: 02/10/2021
ms.author: memildin
ms.openlocfilehash: fb8dc22c923b7b53a6263baa43046862af4d2f04
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370256"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Kurz: Vylepšení dodržování právních předpisů

Azure Security Center pomáhá zjednodušit proces splnění požadavků na dodržování legislativních předpisů pomocí **řídicího panelu dodržování předpisů regulativního předpisu**. 

Security Center nepřetržitě posuzuje vaše hybridní cloudové prostředí, aby se analyzovaly rizikové faktory podle ovládacích prvků a osvědčených postupů ve standardech, které se pro vaše předplatné nastavily. Řídicí panel odráží stav dodržování předpisů pomocí těchto standardů. 

Když povolíte Security Center v předplatném Azure, automaticky se k tomuto předplatnému přiřadí [Srovnávací test zabezpečení Azure](../security/benchmarks/introduction.md) . Toto široce uznávané sestavování srovnávacích testů v ovládacích prvcích z [centra pro Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) a [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) se soustředí na zabezpečení zaměřené na Cloud.

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

1. Vyberte kartu pro standard dodržování předpisů, která je pro vás důležitá (1). Uvidíte, které odběry se standardně aplikují (2), a seznam všech ovládacích prvků pro tento standard (3). Pro příslušné ovládací prvky můžete zobrazit podrobnosti o předávání a selhání hodnocení přidružených k tomuto ovládacímu prvku (4) a počet ovlivněných prostředků (5). Některé ovládací prvky jsou zobrazeny šedě. K těmto ovládacím prvkům nejsou přidružena žádná Security Center posouzení. Ověřte jejich požadavky a vyhodnoťte je ve vašem prostředí. Některé z nich mohou být související s procesy a nikoli technické.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Zkoumání podrobností o dodržování předpisů pomocí konkrétního standardu":::

1. Pokud chcete vygenerovat sestavu PDF se souhrnem aktuálního stavu dodržování předpisů pro určitý standard, vyberte **Stáhnout sestavu**.

    Tato sestava poskytuje souhrnný přehled stavu dodržování předpisů pro vybrané standardní údaje na základě dat Security Center posouzení. Sestava je uspořádána podle ovládacích prvků tohoto konkrétního standardu. Tuto sestavu lze sdílet s příslušnými zúčastněnými stranami a může poskytovat důkazy pro interní a externí auditory.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Stáhnout sestavu dodržování předpisů":::

## <a name="improve-your-compliance-posture"></a>Zlepšení stav dodržování předpisů

Pomocí informací na řídicím panelu dodržování předpisů Vylepšete stav dodržování doporučení přímo v řídicím panelu.

1.  Vyberte některé z neúspěšných posouzení, která se zobrazí na řídicím panelu, a zobrazte podrobnosti o tomto doporučení. Každé doporučení zahrnuje sadu nápravných kroků pro vyřešení tohoto problému.

1.  Pokud chcete zobrazit další podrobnosti a vyřešit doporučení k tomuto prostředku, vyberte konkrétní prostředek. <br>Například ve standardu **Azure CIS 1.1.0** vyberte na **virtuálních počítačích použití šifrování disku** s doporučeními.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Výběr doporučení od standardních potenciálních zákazníků přímo na stránku s podrobnostmi o doporučeních":::

1. Pokud v tomto příkladu vyberete možnost **provést akci** na stránce s podrobnostmi doporučení, přijdete na stránky virtuálního počítače Azure v Azure Portal, kde můžete povolit šifrování na kartě **zabezpečení** :

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Tlačítko akce na stránce s podrobnostmi o doporučení vede k možnostem nápravy":::

    Další informace o tom, jak používat doporučení, najdete [v tématu Implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).

1.  Po provedení akce k vyřešení doporučení se výsledek zobrazí v sestavě řídicího panelu dodržování předpisů, protože vaše skóre dodržování předpisů vylepšuje.

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




## <a name="faq---regulatory-compliance-dashboard"></a>Nejčastější dotazy – řídicí panel dodržování předpisů

- [Jaké standardy jsou podporovány na řídicím panelu dodržování předpisů?](#what-standards-are-supported-in-the-compliance-dashboard)
- [Proč se některé ovládací prvky zobrazují šedě?](#why-do-some-controls-appear-grayed-out)
- [Jak můžu z řídicího panelu odebrat integrovanou standardní hodnotu, jako je PCI-DSS, ISO 27001 nebo SOC2 TSP?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [Navrhovaná změna na základě doporučení se ještě neprojevila na řídicím panelu](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [Jaká oprávnění potřebuji pro přístup k řídicímu panelu dodržování předpisů?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [Řídicí panel dodržování předpisů se pro mě nenačítá](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [Jak si můžu v řídicím panelu zobrazit sestavu o ovládacích prvcích a jejich selhání na standard?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [Jak můžu stáhnout sestavu s daty o dodržování předpisů v jiném formátu než PDF?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [Jak můžu vytvořit výjimky pro některé zásady na řídicím panelu dodržování předpisů?](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [Jaké plány nebo licence v programu Azure Defender potřebuji k používání řídicího panelu dodržování předpisů?](#what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>Jaké standardy jsou podporovány na řídicím panelu dodržování předpisů?
Ve výchozím nastavení se na řídicím panelu dodržování předpisů zobrazuje srovnávací test zabezpečení Azure. Srovnávací test zabezpečení Azure je Microsoftem vytvořeným pravidlem pro zabezpečení a osvědčenými postupy pro dodržování předpisů, které jsou založené na běžných architekturách dodržování předpisů. Další informace najdete v [úvodu k zabezpečení Azure Security test](../security/benchmarks/introduction.md).

Chcete-li sledovat dodržování předpisů u jakékoli jiné úrovně Standard, je nutné je explicitně přidat na řídicí panel.
 
Můžete přidat standardy, jako je Azure CIS 1.1.0 (nový), NIST SP 800-53 R4, NIST SP 800-171 R2, SWIFT CSP CSCF-v2020, UK oficiální a UK NHS, HIPAA HITRUST, Kanada – federální PBMM, ISO 27001, SOC2-TSP a PCI-DSS 3.2.1.  
 
Do řídicího panelu budou přidány další standardy a jsou součástí informací o [přizpůsobení sady standardů na řídicím panelu dodržování předpisů](update-regulatory-compliance-packages.md).

### <a name="why-do-some-controls-appear-grayed-out"></a>Proč se některé ovládací prvky zobrazují šedě?
Pro každý Standard dodržování předpisů na řídicím panelu je k dispozici seznam ovládacích prvků Standard. Pro příslušné ovládací prvky můžete zobrazit podrobnosti o předávání a selhání posouzení.

Některé ovládací prvky jsou zobrazeny šedě. K těmto ovládacím prvkům nejsou přidružena žádná Security Center posouzení. Některé mohou být procedury nebo související s procesem, a proto je nelze ověřit pomocí Security Center. Některé ještě nemají žádné implementované automatizované zásady ani posouzení, ale budou v budoucnu. A některé ovládací prvky můžou být zodpovědností platformy, jak je vysvětleno v tématu [sdílená odpovědnost v cloudu](../security/fundamentals/shared-responsibility.md). 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>Jak můžu z řídicího panelu odebrat integrovanou standardní hodnotu, jako je PCI-DSS, ISO 27001 nebo SOC2 TSP? 
Pokud chcete přizpůsobit řídicí panel dodržování předpisů a soustředit se jenom na standardy, které se vám týkají, můžete odebrat všechny zobrazené zákonné standardy, které nejsou relevantní pro vaši organizaci. Pokud chcete odebrat Standard, postupujte podle pokynů v tématu [Odebrání standardu z řídicího panelu](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>Navrhovaná změna na základě doporučení se ještě neprojevila na řídicím panelu
Po provedení akce pro vyřešení doporučení počkejte 12 hodin, než se zobrazí změny dat o dodržování předpisů. Posouzení se spouští přibližně každých 12 hodin, takže se vám bude zobrazovat vliv na data o dodržování předpisů až po spuštění posouzení.
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>Jaká oprávnění potřebuji pro přístup k řídicímu panelu dodržování předpisů?
Chcete-li zobrazit data o dodržování předpisů, je nutné mít k datům dodržování zásad přístup aspoň **Čtenář** . Proto nestačí jen čtenář zabezpečení. Pokud jste globální čtenář v rámci předplatného, bude to stačit.

Minimální sada rolí pro přístup k řídicímu panelu a správě standardů je **Přispěvatel zásad prostředků** a **Správce zabezpečení**.


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>Řídicí panel dodržování předpisů se pro mě nenačítá
Aby bylo možné použít řídicí panel dodržování předpisů, Azure Security Center musí mít povolený program Azure Defender na úrovni předplatného. Pokud se řídicí panel nenačítá správně, zkuste následující kroky:

1. Vymažte mezipaměť prohlížeče.
1. Vyzkoušejte si jiný prohlížeč.
1. Zkuste otevřít řídicí panel z jiného umístění v síti.


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>Jak si můžu v řídicím panelu zobrazit sestavu o ovládacích prvcích a jejich selhání na standard?
Na hlavním řídicím panelu se zobrazí zpráva s oznámením a neúspěšnými ovládacími prvky pro (1) nejnižšími standardy dodržování předpisů na řídicím panelu v horní části 4. Chcete-li zobrazit všechny stavové ovládací prvky pro předání a selhání, vyberte (2) **Zobrazit vše *x*** (kde x je počet sledovaných standardů). Plocha kontextu zobrazuje stav dodržování předpisů pro každou ze sledovaných standardů.

:::image type="content" source="media/security-center-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="Část s přehledem řídicího panelu dodržování předpisů regulativní předpisy":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>Jak můžu stáhnout sestavu s daty o dodržování předpisů v jiném formátu než PDF?
Když vyberete možnost **Stáhnout sestavu**, vyberte standardní a formát (PDF nebo CSV). Výsledná sestava bude odpovídat aktuální sadě předplatných, které jste vybrali ve filtru portálu.

- Sestava PDF zobrazuje souhrnný stav vybraného standardu.
- Sestava CSV poskytuje podrobné výsledky pro jednotlivé prostředky, protože souvisí se zásadami přidruženými k jednotlivým ovládacím prvkům.

V současné době není k dispozici podpora pro stažení sestavy pro vlastní zásady. jenom pro dodané regulativní standardy.


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>Jak můžu vytvořit výjimky pro některé zásady na řídicím panelu dodržování předpisů?
V případě zásad, které jsou integrované v Security Center a zahrnuté do zabezpečeného skóre, můžete vytvořit výjimky pro jeden nebo více prostředků přímo na portálu, jak je vysvětleno v tématu [vyloučení prostředků a doporučení ze zabezpečeného skóre](exempt-resource.md).

Pro jiné zásady můžete vytvořit výjimku přímo v samotné zásadě, a to podle pokynů v tématu [struktura Azure Policy osvobození](../governance/policy/concepts/exemption-structure.md).


### <a name="what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>Jaké plány nebo licence v programu Azure Defender potřebuji k používání řídicího panelu dodržování předpisů?
Pokud máte v některém z vašich typů prostředků Azure povolené nějaké balíčky Azure Defenderu, máte v Security Center přístup k řídicímu panelu dodržování předpisů, který obsahuje všechna jeho data.





## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o použití řídicího panelu pro dodržování předpisů Security Center pro:

> [!div class="checklist"]
> * Prohlédněte si a sledujte stav dodržování předpisů týkajících se standardů a předpisů, které jsou pro vás důležité.
> * Vylepšete svůj stav dodržování předpisů tím, že vyřešte relevantní doporučení a sledujte skóre dodržování předpisů.

Řídicí panel dodržování předpisů může významně zjednodušit proces dodržování předpisů a významně zkrátit dobu potřebnou ke shromažďování důkazů dodržování předpisů pro prostředí Azure, hybridního a více cloudových prostředí.

Další informace najdete v těchto souvisejících stránkách:

- [Přizpůsobení sady standardů na řídicím panelu dodržování předpisů](update-regulatory-compliance-packages.md) – Naučte se, jak vybrat, které standardy se zobrazí na řídicím panelu dodržování předpisů regulativního předpisu. 
- [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Přečtěte si, jak používat doporučení v Security Center k ochraně vašich prostředků Azure.