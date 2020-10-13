---
title: Povolení Azure Monitor pro virtuální počítače pomocí Azure Policy
description: Popisuje, jak povolíte Azure Monitor pro virtuální počítače pro více virtuálních počítačů Azure nebo sady škálování virtuálních počítačů pomocí Azure Policy.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 240c96016304c009c36485869ac15f5f38076fb7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90088285"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>Povolení Azure Monitor pro virtuální počítače pomocí Azure Policy
Tento článek vysvětluje, jak povolit Azure Monitor pro virtuální počítače pro virtuální počítače Azure nebo k hybridnímu virtuálnímu počítači připojenému pomocí Azure ARC (Preview) pomocí Azure Policy. Azure Policy vám umožní přiřadit definice zásad, které budou instalovat požadované agenty pro Azure Monitor pro virtuální počítače v prostředí Azure a automaticky povolit monitorování pro virtuální počítače při vytvoření každého virtuálního počítače. Azure Monitor pro virtuální počítače poskytuje funkci, která umožňuje zjistit a opravit nekompatibilní virtuální počítače ve vašem prostředí. Místo toho, abyste pracovali přímo s Azure Policy, použijte tuto funkci.

Pokud nejste obeznámeni s Azure Policy, získáte stručný úvod k [nasazení Azure monitor ve velkém měřítku pomocí Azure Policy](../deploy-scale.md).

> [!NOTE]
> Pokud chcete používat Azure Policy se službou Azure Virtual Machine Scale Sets nebo pokud chcete pracovat s Azure Policy přímo k povolení virtuálních počítačů Azure, přečtěte si téma [nasazení Azure monitor se škálováním pomocí Azure Policy](../deploy-scale.md#azure-monitor-for-vms).

## <a name="prerequisites"></a>Požadavky
- [Vytvořte a nakonfigurujte Log Analytics pracovní prostor](vminsights-configure-workspace.md).
- V části [podporované operační systémy](vminsights-enable-overview.md#supported-operating-systems) se ujistěte, že je podporovaný operační systém virtuálního počítače nebo sady škálování virtuálních počítačů, které chcete povolit. 


## <a name="azure-monitor-for-vms-initiative"></a>Azure Monitor pro virtuální počítače iniciativa
Azure Monitor pro virtuální počítače poskytuje předdefinované definice zásad pro instalaci agenta Log Analytics a agenta závislostí na virtuálních počítačích Azure. Každá z těchto definic zásad zahrnuje **Azure monitor pro virtuální počítače** v iniciativě. Přiřaďte tuto iniciativu ke skupině pro správu, k předplatnému nebo skupině prostředků, abyste mohli automaticky instalovat agenty na jakékoli virtuální počítače Azure se systémem Windows nebo Linux v daném oboru.

## <a name="open-policy-coverage-feature"></a>Otevřít funkci pokrytí zásad
Pokud chcete získat přístup k **pokrytí zásad Azure monitor pro virtuální počítače**, přejděte na **virtuální počítače** v nabídce **Azure monitor** v Azure Portal. Vyberte **Další možnosti** připojení a pak **Povolit** v části **Povolit používání zásad**.

[![Karta Začínáme s Azure Monitor z virtuálních počítačů](./media/vminsights-enable-at-scale-policy/get-started-page.png)](./media/vminsights-enable-at-scale-policy/get-started-page.png#lightbox)

## <a name="create-new-assignment"></a>Vytvořit nové přiřazení
Pokud ještě nemáte přiřazení, vytvořte ho kliknutím na **přiřadit zásadu**.

[![Vytvořit přiřazení](media/vminsights-enable-at-scale-policy/create-assignment.png)](media/vminsights-enable-at-scale-policy/create-assignment.png#lightbox)

Jedná se o stejnou stránku, abyste přiřadili iniciativu v Azure Policy s tím rozdílem, že je pevně zakódované s rozsahem, který jste vybrali, a s definicí definice iniciativy **Enable Azure monitor pro virtuální počítače** . Volitelně můžete změnit **název přiřazení** a přidat **Popis**. Vyberte **vyloučení** , pokud chcete poskytnout vyloučení do oboru. Rozsahem může být například skupina pro správu a můžete zadat odběr v této skupině pro správu, který bude vyloučen z přiřazení.

[![Přiřadit iniciativu](media/vminsights-enable-at-scale-policy/assign-initiative.png)](media/vminsights-enable-at-scale-policy/assign-initiative.png#lightbox)

Na stránce **parametry** vyberte **pracovní prostor Log Analytics** , který budou používat všechny virtuální počítače v přiřazení. Pokud chcete pro různé virtuální počítače zadat jiné pracovní prostory, musíte vytvořit několik přiřazení, z nichž každý má vlastní rozsah. 

   > [!NOTE]
   > Pokud je tento pracovní prostor nad rámec přiřazení, udělte Log Analytics oprávnění *Přispěvatel* k ID objektu zabezpečení přiřazení zásad. Pokud to neuděláte, může se zobrazit chyba nasazení, například `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`

[![Pracovní prostor](media/vminsights-enable-at-scale-policy/assignment-workspace.png)](media/vminsights-enable-at-scale-policy/assignment-workspace.png#lightbox)

Kliknutím na tlačítko **zkontrolovat + vytvořit** zkontrolujte podrobnosti přiřazení a teprve potom kliknutím na **vytvořit** vytvořte. V tomto okamžiku nevytvářejte úlohu nápravy, protože pravděpodobně budete potřebovat více úloh nápravy, aby bylo možné povolit stávající virtuální počítače. Viz [napravení výsledků dodržování předpisů](#remediate-compliance-results) níže.

## <a name="review-compliance"></a>Kontrola dodržování předpisů
Po vytvoření přiřazení můžete zkontrolovat a spravovat pokrytí pro **Azure monitor pro virtuální počítače** iniciativu v rámci skupin pro správu a předplatných. Tím se zobrazí, kolik virtuálních počítačů existuje v každé ze skupin nebo předplatných pro správu a jejich stav dodržování předpisů.

[![Stránka Azure Monitor pro virtuální počítače Správa zásad](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png#lightbox)


V následující tabulce je uveden popis informací v tomto zobrazení.

| Funkce | Popis | 
|----------|-------------| 
| **Rozsah** | Skupina pro správu a předplatná, ke kterým máte nebo zdědili přístup, mají schopnost přejít k podrobnostem prostřednictvím hierarchie skupiny pro správu.|
| **Role** | Vaše role v oboru, což může být čtenář, vlastník nebo Přispěvatel. Tato akce bude prázdná, pokud máte přístup k předplatnému, ale nikoli ke skupině pro správu, do které patří. Tato role určuje, která data můžete zobrazit, a akce, které můžete provádět s ohledem na přiřazení zásad nebo iniciativ (Vlastník), jejich úpravách nebo zobrazení dodržování předpisů. |
| **Celkový počet virtuálních počítačů** | Celkový počet virtuálních počítačů v daném oboru bez ohledu na jejich stav. Pro skupinu pro správu se jedná o celkový součet virtuálních počítačů vnořených do předplatných nebo podřízených skupin pro správu. |
| **Pokrytí přiřazení** | Procento virtuálních počítačů, na které se vztahuje iniciativa |
| **Stav přiřazení** | **Úspěch** – na všechny virtuální počítače v oboru se nasadí Log Analytics a agenti závislostí.<br>**Upozornění** : předplatné není ve skupině pro správu.<br>**Nespuštěno** – bylo přidáno nové přiřazení.<br>**Lock** – nemáte dostatečná oprávnění ke skupině pro správu.<br>**Prázdné** – neexistují žádné virtuální počítače nebo není přiřazena zásada. |
| **Vyhovující virtuální počítače** | Počet kompatibilních virtuálních počítačů, což je počet virtuálních počítačů, které mají nainstalovaného agenta Log Analytics agent a závislostí. Tato akce bude prázdná, pokud neexistují žádná přiřazení, žádné virtuální počítače v oboru nebo nemáte příslušná oprávnění. |
| **Dodržování předpisů** | Celkové číslo dodržování předpisů je součet různých prostředků, které jsou v souladu s hodnotou součet všech různých prostředků. |
| **Stav dodržování předpisů** | **Kompatibilní** – pro všechny virtuální počítače v oboru virtuálních počítačů se nasadily Log Analytics a agenti závislostí nebo jakékoli nové virtuální počítače v oboru, který je předmětem přiřazení, ještě nejsou vyhodnoceny.<br>**Nekompatibilní** – k dispozici jsou virtuální počítače, které byly vyhodnoceny, ale nejsou povoleny a mohou vyžadovat nápravu.<br>**Nespuštěno** – bylo přidáno nové přiřazení.<br>**Lock** – nemáte dostatečná oprávnění ke skupině pro správu.<br>**Prázdné** – nepřiřazují se žádné zásady.  |


Když přiřadíte iniciativu, může být obor vybraný v přiřazení oborem uvedeným v poli nebo jeho podmnožinou. Například jste mohli vytvořit přiřazení pro předplatné (obor zásad), nikoli skupinu pro správu (rozsah pokrytí). V takovém případě hodnota **pokrytí přiřazení** označuje virtuální počítače v oboru iniciativ dělené virtuálními počítači v oboru pokrytí. V jiném případě jste možná vyloučili některé virtuální počítače, skupiny prostředků nebo předplatné z oboru zásad. Pokud je hodnota prázdná, znamená to, že buď zásada nebo podnět neexistuje, nebo nemáte příslušná oprávnění. Informace jsou k dispozici v části **stav přiřazení**.


## <a name="remediate-compliance-results"></a>Náprava výsledků dodržování předpisů
Iniciativa se použije na virtuální počítače při jejich vytvoření nebo úpravě, ale nebude použita na stávající virtuální počítače. Pokud vaše přiřazení nezobrazuje 100% dodržování předpisů, vytvořte úlohy nápravy k vyhodnocení a povolení existujících virtuálních počítačů, vyberte **Zobrazit dodržování předpisů** tak, že vyberete tři tečky (...).

[![Zobrazit dodržování předpisů](media/vminsights-enable-at-scale-policy/view-compliance.png)](media/vminsights-enable-at-scale-policy/view-compliance.png#lightbox)

Stránka **dodržování předpisů** obsahuje seznam přiřazení odpovídajících zadanému filtru a to, jestli splňují předpisy. Kliknutím na přiřazení zobrazíte jeho podrobnosti.

[![Dodržování zásad pro virtuální počítače Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png#lightbox)

Stránka **dodržování předpisů iniciativ** obsahuje definice zásad v iniciativě a to, jestli jsou v souladu s předpisy.

[![Podrobnosti o kompatibilitě](media/vminsights-enable-at-scale-policy/compliance-details.png)](media/vminsights-enable-at-scale-policy/compliance-details.png#lightbox)

Kliknutím na definici zásady zobrazíte její podrobnosti. Příklady scénářů, které se budou zobrazovat jako nedodržování předpisů, zahrnují následující:

* Agent Log Analytics nebo agent závislostí není nasazený. Vytvořte úlohu nápravy, která se má zmírnit.
* Image virtuálního počítače (operační systém) není v definici zásad identifikovaná. Kritéria zásad nasazení zahrnují jenom virtuální počítače, které jsou nasazené z dobře známých imagí virtuálních počítačů Azure. Podívejte se do dokumentace a zjistěte, jestli je operační systém virtuálního počítače podporovaný.
* Virtuální počítače se neprotokolují do zadaného Log Analytics pracovního prostoru. Některé virtuální počítače v oboru iniciativy jsou připojené k Log Analyticsmu pracovnímu prostoru, který je jiný než ten, který je zadaný v přiřazení zásady.

[![Podrobnosti o dodržování zásad](media/vminsights-enable-at-scale-policy/policy-compliance-details.png)](media/vminsights-enable-at-scale-policy/policy-compliance-details.png#lightbox)

Chcete-li vytvořit úlohu nápravy pro zmírnění problémů s kompatibilitou, klikněte na tlačítko **vytvořit úlohu nápravy**. 

[![Nová úloha nápravy](media/vminsights-enable-at-scale-policy/new-remediation-task.png)](media/vminsights-enable-at-scale-policy/new-remediation-task.png#lightbox)

Klikněte na **opravit** , aby se vytvořila úloha nápravy, a pak ji **napravo** spusťte. Pravděpodobně budete muset vytvořit několik opravných úloh, jednu pro každou definici zásad. Nemůžete vytvořit úlohu nápravy pro iniciativu.

[![Snímek obrazovky znázorňující podokno nápravy zásad pro monitor | Virtual Machines.](media/vminsights-enable-at-scale-policy/remediation.png)](media/vminsights-enable-at-scale-policy/remediation.png#lightbox)


Po dokončení opravných úloh by vaše virtuální počítače měly odpovídat agentům nainstalovaným a povoleným pro Azure Monitor pro virtuální počítače. 

## <a name="next-steps"></a>Další kroky

Teď, když je monitorování povolené pro vaše virtuální počítače, jsou tyto informace k dispozici pro analýzu pomocí Azure Monitor pro virtuální počítače. 

- Pokud chcete zobrazit zjištěné závislosti aplikací, přečtěte si téma [zobrazení Azure monitor pro virtuální počítače mapa](vminsights-maps.md). 
- Pokud chcete zjistit kritické body a celkové využití výkonu vašeho virtuálního počítače, přečtěte si téma [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md). 
