---
title: Galerie sešitů v Azure Security Center
description: Naučte se vytvářet bohatě interaktivní sestavy vašich Azure Security Center dat pomocí Galerie integrovaných Azure Monitorch sešitů.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 198702f619e490e8000e4430aab23a7f6bfb6d85
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102107483"
---
# <a name="create-rich-interactive-reports-of-security-center-data"></a>Vytváření bohatých interaktivních sestav Security Center dat

[Azure monitor sešity](../azure-monitor/visualize/workbooks-overview.md) poskytují flexibilní plátno pro analýzu dat a vytváření bohatých vizuálních sestav v rámci Azure Portal. Umožňují využívat více zdrojů dat v rámci Azure a kombinovat je do sjednocených interaktivních prostředí.

Pracovní sešity poskytují bohatou sadu funkcí pro vizualizaci dat Azure. Podrobné příklady jednotlivých typů vizualizací najdete v [ukázkách a v dokumentaci k vizualizacím](../azure-monitor/visualize/workbooks-text-visualizations.md). 

V rámci Azure Security Center máte přístup k předdefinovaným sestavám, abyste mohli sledovat stav zabezpečení vaší organizace. Můžete také vytvořit vlastní sestavy pro zobrazení široké škály dat z Security Center nebo jiných podporovaných zdrojů dat.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Sestava zabezpečeného skóre v čase":::

## <a name="availability"></a>Dostupnost

| Aspekt                          | Podrobnosti                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| Stav vydaných verzí:                  | Preview<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                       |
| Stanov                        | Free                                                                                                                                         |
| Požadované role a oprávnění: | Chcete-li uložit sešity, musíte mít alespoň oprávnění Přispěvatel sešitu pro cílovou skupinu prostředků.                                      |
| Cloud                         | ![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![Yes](./media/icons/yes-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov) |
|                                 |                                                                                                                                              |

## <a name="workbooks-gallery-in-azure-security-center"></a>Galerie sešitů v Azure Security Center

Díky integrovaným funkcím sešitů Azure Azure Security Center usnadňuje vytváření vlastních interaktivních sestav. Security Center také obsahuje galerii sešitů s následujícími sestavami, které jsou připraveny k přizpůsobení:

- **Zabezpečení skóre v čase** – sledování skóre předplatných a změn doporučení pro vaše prostředky
- **Aktualizace systému** – zobrazení chybějících aktualizací systému podle prostředků, operačního systému, závažnosti a dalších
- **Zjištění ohrožení zabezpečení** – zobrazení výsledků kontroly ohrožení zabezpečení vašich prostředků Azure

:::image type="content" source="media/custom-dashboards-azure-workbooks/workbooks-gallery-security-center.png" alt-text="Galerie předdefinovaných sešitů v Azure Security Center":::

Vyberte jednu z dodaných sestav nebo vytvořte vlastní.

> [!TIP]
> Pomocí tlačítka **Upravit** můžete přizpůsobit některou z dodaných sestav, aby byly vaší spokojenosti. Až budete hotovi, vyberte **Uložit** a změny se uloží do nového sešitu.
> 
> :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-supplied-workbooks.png" alt-text="Úpravy dodaných sešitů, abyste je přizpůsobili konkrétním potřebám":::

### <a name="use-the-secure-score-over-time-report"></a>Použití sestavy zabezpečený počet skóre v čase

Tato sestava využívá bezpečná data skóre z pracovního prostoru Log Analytics. Tato data musí být exportována z nástroje pro průběžný export, jak je popsáno v tématu [Konfigurace průběžného exportu z Security Center stránek v Azure Portal](continuous-export.md?tabs=azure-portal).

Při nastavování průběžného exportu nastavte četnost exportu pro **streamování aktualizací** i **snímků**.

:::image type="content" source="media/custom-dashboards-azure-workbooks/export-frequency-both.png" alt-text="V případě sešitu zabezpečeného skóre v čase budete muset v konfiguraci průběžného exportu vybrat obě tyto možnosti z nastavení četnosti exportu.":::

> [!NOTE]
> Snímky se exportují týdně, takže budete muset před zobrazením dat v této sestavě počkat aspoň jeden týden pro export prvního snímku.

> [!TIP]
> Ke konfiguraci průběžného exportu napříč vaší organizací použijte dodané Azure Policy zásady DeployIfNotExist popsané v tématu [Konfigurace průběžného exportu ve velkém měřítku](continuous-export.md?tabs=azure-policy).

Sestava zabezpečeného skóre v čase má pět grafů pro vytváření sestav předplatných pro vybrané pracovní prostory:


|Graph  |Příklad  |
|---------|---------|
|**Trendy skóre pro poslední týden a měsíc**<br>V této části můžete monitorovat aktuální skóre a obecné trendy skóre vašich předplatných.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-1.png" alt-text="Trendy pro bezpečné skóre na předdefinované sestavě":::|
|**Agregované skóre pro všechna vybraná předplatná**<br>Najeďte myší na libovolný bod v trendové přímce, abyste viděli agregované skóre v jakémkoli časovém intervalu.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-2.png" alt-text="Agregované skóre pro všechna vybraná předplatná":::|
|**Doporučení s největším stavem prostředků**<br>Tato tabulka vám pomůže posoudit doporučení, u kterých se většina prostředků změnila v nesprávném stavu za vybrané období.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-3.png" alt-text="Doporučení s největším stavem prostředků":::|
|**Skóre pro konkrétní ovládací prvky zabezpečení**<br>Ovládací prvky zabezpečení Security Center jsou logické skupiny doporučení. V tomto grafu vidíte na první pohled týdenní skóre všech vašich ovládacích prvků.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-4.png" alt-text="Skóre pro kontrolu zabezpečení v průběhu vybraného časového období":::|
|**Změny prostředků**<br>V tomto seznamu jsou uvedena doporučení s největším stavem prostředků, které se změnily (v pořádku, není v pořádku nebo nepoužito). Výběrem všech doporučení ze seznamu otevřete novou tabulku se seznamem konkrétních prostředků.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-5.png" alt-text="Doporučení s nejvíc prostředky, u kterých došlo ke změně stavu":::|

### <a name="use-the-system-updates-report"></a>Použít sestavu aktualizace systému

Tato sestava vychází z doporučení zabezpečení, které by se měly nainstalovat na vaše počítače.

Tato sestava vám pomůže identifikovat počítače s nezpracovanými aktualizacemi.

Situaci pro vybraná předplatná můžete zobrazit v závislosti na:

- Seznam prostředků s nezpracovanými aktualizacemi
- Seznam aktualizací, které ve vašich prostředcích chybí

:::image type="content" source="media/custom-dashboards-azure-workbooks/system-updates-report.png" alt-text="Sestava aktualizací systému Security Center na základě doporučení zabezpečení chybějící aktualizace":::

### <a name="use-the-vulnerability-assessment-findings-report"></a>Použít sestavu zjištění posouzení ohrožení zabezpečení

Security Center zahrnuje skenery ohrožení zabezpečení pro vaše počítače, kontejnery v registrech kontejnerů a SQL servery.

Další informace o používání těchto skenerů:

- [Skenování počítačů pomocí integrovaného skeneru VA](deploy-vulnerability-assessment-vm.md)
- [Kontrola ohrožení zabezpečení v imagích registrů](defender-for-container-registries-usage.md)
- [Prohledat chyby zabezpečení v prostředcích SQL](defender-for-sql-on-machines-vulnerability-assessment.md)

Zjištění pro každý z těchto skenerů se hlásí v samostatných doporučeních:

- Ohrožení zabezpečení ve vašich virtuálních počítačích by se mělo opravit.
- Ohrožení zabezpečení v Azure Container Registry imagí by se mělo opravit (používá se Qualys).
- U vašich databází SQL by se měly opravit výsledky posouzení ohrožení zabezpečení.
- Na počítačích s SQL serverem by se měly opravit výsledky posouzení ohrožení zabezpečení.

Tato sestava shromažďuje tyto závěry a organizuje je podle závažnosti, typu prostředku a kategorie.

:::image type="content" source="media/custom-dashboards-azure-workbooks/vulnerability-assessment-findings-report.png" alt-text="Sestava zjištění posouzení ohrožení zabezpečení Security Center":::


## <a name="import-workbooks-from-other-workbook-galleries"></a>Import sešitů z jiných galerií sešitů

Pokud jste vytvořili sešity v jiných službách Azure a chcete je přesunout do galerie Azure Security Centerch sešitů:

1. Otevřete cílový sešit.

1. Na panelu nástrojů vyberte **Upravit**.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks.png" alt-text="Úprava Azure Monitor sešitu":::

1. Na panelu nástrojů vyberte **</>** a zadejte rozšířený editor.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-advanced-editor.png" alt-text="Spuštění rozšířeného editoru pro získání kódu JSON šablony galerie":::

1. Zkopírujte JSON šablony v sešitu.

1. Otevře galerii sešitů v Security Center a na řádku nabídek vyberte **Nový**.
1. Vyberte **</>** Rozšířený editor a zadejte.
1. Vložte do celé šablony JSON pro celou galerii.
1. Vyberte **Použít**.
1. Na panelu nástrojů vyberte **Uložit jako**.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-save-as.png" alt-text="Uložení sešitu do galerie v Security Center":::

1. Zadejte požadované podrobnosti pro uložení sešitu:
   1. Název sešitu
   2. Požadovaná oblast
   3. Podle potřeby předplatné, skupinu prostředků a sdílení.

Uložený sešit najdete v kategorii **sešity nedávno upraveno** .


## <a name="next-steps"></a>Další kroky

Tento článek popisuje Security Center integrovaná Azure Monitor sešity s předdefinovanými sestavami a možnost vytvářet vlastní interaktivní sestavy.

- Další informace o [Azure monitor sešitech](../azure-monitor/visualize/workbooks-overview.md)
- Předdefinované sestavy vyžádají svá data z doporučení Security Center. Přečtěte si o mnoha doporučeních zabezpečení v [doporučeních zabezpečení – referenční příručka](recommendations-reference.md)