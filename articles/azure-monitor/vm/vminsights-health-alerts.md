---
title: Upozornění na stav hosta pro virtuální počítače v Insights (Preview)
description: Popisuje výstrahy vytvořené stavem hosta virtuálních počítačů Insights, včetně způsobu jejich povolení a konfigurace oznámení.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: a32ba9f1c4cf5d6bb9de69e1a6860c858e3ee2a6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707502"
---
# <a name="vm-insights-guest-health-alerts-preview"></a>Upozornění na stav hosta pro virtuální počítače v Insights (Preview)
Stav hosta pro virtuální počítač Insights umožňuje zobrazit stav virtuálního počítače podle definice sady měření výkonu, které jsou v pravidelných intervalech vzorkované. Výstraha se dá vytvořit, když se virtuální počítač nebo monitor změní na stav není v pořádku. Tato upozornění můžete zobrazovat a spravovat pomocí těch, která [jsou vytvořená pomocí pravidel upozornění v Azure monitor](../alerts/alerts-overview.md) , a při vytváření nové výstrahy se rozhodnout, že se budou aktivně informovat.

## <a name="configure-alerts"></a>Konfigurace upozornění
Pokud je tato funkce ve verzi Preview, nemůžete vytvořit pravidlo explicitní výstrahy pro stav hosta služby VM Insights. Ve výchozím nastavení se výstrahy vytvoří pro každý virtuální počítač, ale ne pro každé monitorování.  To znamená, že pokud se monitorování změní na stav, který nemá vliv na aktuální stav virtuálního počítače, nebude vytvořena žádná výstraha, protože stav virtuálního počítače se nezměnil. 

Výstrahy pro konkrétní virtuální počítač nebo konkrétní monitorování můžete na virtuálním počítači zakázat z nastavení **stav výstrahy** v konfiguraci pro virtuální počítač v Azure Portal. Podrobnosti o konfiguraci monitorování v Azure Portal najdete v tématu [Konfigurace sledování ve stavu hosta virtuálních počítačů Insights (Preview)](vminsights-health-configure.md) . Podrobnosti o konfiguraci monitorování v rámci sady virtuálních počítačů najdete v tématu [Konfigurace monitorování ve stavu hosta virtuálních počítačů Insights pomocí pravidel shromažďování dat (Preview)](vminsights-health-configure-dcr.md) .

## <a name="alert-severity"></a>Závažnost výstrahy
Závažnost výstrahy vytvořená stavem hosta je přímo mapována na závažnost virtuálního počítače nebo na monitorování, které aktivuje výstrahu.

| Stav monitorování | Závažnost výstrahy |
|:---|:---|
| Kritické | Sev1 |
| Upozornění  | Sev2 |
| V pořádku  | Sev4 |

## <a name="alert-lifecycle"></a>Životní cyklus výstrahy
Pro každý virtuální počítač se vytvoří [Výstraha Azure](../alerts/alerts-overview.md) , kdykoli se změní na **varování** nebo **kritický** stav. Zobrazení výstrahy z **výstrah** v nabídce **Azure monitor** nebo v nabídce virtuálního počítače v Azure Portal.

Pokud je už výstraha ve stavu **aktivováno** , když se změní stav virtuálního počítače, druhá výstraha se nevytvoří, ale závažnost stejné výstrahy se změní tak, aby odpovídala stavu virtuálního počítače. Například pokud se virtuální počítač změní do **kritického** stavu, když **Upozornění** již bylo ve stavu **aktivováno** , bude Závažnost výstrahy změněna na **Sev1**. Pokud se virtuální počítač změní na stav **výstrahy** , když se už výstraha **Sev1** ve **stavu** vystavení, změní se závažnost výstrahy na **Sev2**. Pokud se virtuální počítač přesune zpátky do stavu **v pořádku** , výstraha se vyřeší se závažností, která se změnila na **Sev4**.

## <a name="viewing-alerts"></a>Zobrazení výstrah
Zobrazit výstrahy vytvořené stavem hosta virtuálních počítačů Insights s dalšími [výstrahami v Azure Portal](../platform/alerts-overview.md#alerts-experience). Můžete vybrat **výstrahy** z nabídky **Azure monitor** , abyste zobrazili výstrahy pro všechny monitorované prostředky, nebo vybrat **výstrahy** z nabídky virtuálního počítače, abyste zobrazili výstrahy jenom pro tento virtuální počítač.

## <a name="alert-properties"></a>Vlastnosti výstrahy

### <a name="properties-in-the-azure-portal"></a>Vlastnosti v Azure Portal
Vlastnosti výstrahy, když ji zobrazíte v Azure Portal, jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:---|:---|
| Stav monitorování před vytvořením výstrahy | Stav monitorování nebo virtuálního počítače před tím, než se tato výstraha vyvolala poprvé. |
| Stav monitorování při vytvoření výstrahy | Stav monitorování nebo virtuálního počítače při prvním spuštění výstrahy. Toto je stav, který způsobil, že se výstraha aktivuje. |
| Chcete-li získat další informace o přechodu stavu při vytvoření výstrahy | Odkaz na stránku stavu virtuálního počítače, kde vidíte přesný přechod stavu. Tento přechod stavu představuje instanci, když se monitor nejdřív nachází ze stavu **v pořádku** do stavu, který není v pořádku. |

Například monitorování **přejde z provozu** do **kritického** v čase t0 a k vyvolání nové výstrahy se **Sev1**. Pak přejde z **kritického** na **varování** v čase T1 a závažnost výstrahy se aktualizuje na **Sev2**. Bude v **čase** T2 a výstraha se vyřeší.

Vlastnosti výstrahy budou mít tyto hodnoty během celé sekvence.

- Stav monitorování před vytvořením výstrahy: v pořádku
- Stav monitorování při vytvoření výstrahy: kritické
- Další informace o přechodu stavu při vytvoření výstrahy: navigační odkaz na přechod stavu nastal v čase t0.


### <a name="properties-in-notifications"></a>Vlastnosti v oznámeních
Vlastnosti výstrahy obsažené v oznámeních jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:---|:---|
| Předchozí stav monitorování | Stav monitorování nebo virtuálního počítače před změnou stavu. Pokud aktualizace závažnosti výstrahy aktivuje toto oznámení, tato vlastnost představuje stav, který byl těsně před aktualizací závažnosti. |
| Aktuální stav monitorování | Stav monitorování nebo virtuálního počítače, když změnil stav. Pokud aktualizace závažnosti výstrahy spustí toto oznámení, tato vlastnost představuje nový stav. |
| Chcete-li získat další informace o tomto přechodu stavu | Odkaz na stránku stavu virtuálního počítače, kde vidíte přesný přechod stavu. Tento přechod stavu představuje instanci při monitorování změněného stavu, který aktivoval toto oznámení. |

Pomocí výše uvedeného příkladu budou oznámení mít v každém okamžiku následující vlastnosti.

Oznámení obdrženo v čase t0
- Stav předchozího monitorování: v pořádku
- Aktuální stav monitorování: kritický
- Další informace o přechodu tohoto stavu: navigační odkaz na přechod stavu nastal v čase t0.

Oznámení přijaté v čase T1
- Předchozí stav monitorování: kritický
- Aktuální stav monitorování: upozornění
- Další informace o přechodu tohoto stavu: navigační odkaz na přechod stavu nastal v čase t1.

Oznámení přijato v čase T2
- Předchozí stav monitorování: upozornění
- Aktuální stav monitorování: v pořádku
- Chcete-li získat další informace o tomto přechodu stavu: navigační odkaz na přechod stavu se nastal v čase T2.

## <a name="configure-notifications"></a>Konfigurace oznámení
Chcete-li být aktivně upozorněni na výstrahu aktivované stavem hosta, vytvořte [skupinu akcí](../alerts/action-groups.md) pro definování různých akcí, které mají být provedeny, například odeslání zprávy SMS nebo spuštění aplikace logiky. Pak vytvořte [pravidlo akce](../alerts/alerts-action-rules.md) , které určuje rozsah monitorování a virtuálních počítačů a používá tuto skupinu akcí.

V nabídce **monitorování** v Azure Portal vyberte **výstrahy**.  Vyberte **Spravovat akce** a pak **pravidla akce (Preview)**. 

![Nové pravidlo akce](media/vminsights-health-alerts/action-rule-new.png)

Klikněte na **Nová akce pravidlo** a vytvořte nové pravidlo. Klikněte na **Vybrat** další pro obor a vyberte předplatné, skupinu prostředků nebo jeden nebo několik konkrétních virtuálních počítačů. Oznámení se aktivuje jenom pro virtuální počítače, které spadají do oboru.

![Rozsah pravidla akce](media/vminsights-health-alerts/action-rule-scope.png)

Klikněte na tlačítko **Přidat** další pro **Filtr**. Vytvořte filtr, ve kterém **se služba monitorování rovná stavu VM Insights**. Přidejte další filtry a určete tak konkrétní výstrahy, které by měly aktivovat oznámení. Pomocí **závažnosti** můžete například vyhledat výstrahy ze všech monitorování, které odpovídají určité závažnosti.

![Filtr pravidla akce](media/vminsights-health-alerts/action-rule-filter.png)

V části **definovat v tomto oboru** vyberte **Skupina akcí** a potom vyberte skupinu akcí, která se má k monitorování přidružit. Zadejte název pravidla a vyberte skupinu prostředků, ve které se má uložit. Kliknutím na **vytvořit** vytvořte pravidlo.

![Pravidlo akce](media/vminsights-health-alerts/action-rule.png)


## <a name="next-steps"></a>Další kroky

- [Povolit stav hosta v agentech VM Insights a integrovaných agentech.](vminsights-health-enable.md)
- [Nakonfigurujte monitorování pomocí Azure Portal.](vminsights-health-configure.md)
- [Nakonfigurujte monitorování pomocí pravidel shromažďování dat.](vminsights-health-configure-dcr.md)