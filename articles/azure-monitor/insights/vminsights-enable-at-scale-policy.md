---
title: Povolení Azure Monitoru pro virtuální počítače pomocí zásad Azure
description: Tento článek popisuje, jak povolíte Azure Monitor pro virtuální počítače pro více virtuálních počítačů Azure nebo škálovací sady virtuálních počítačů pomocí Zásad y Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 7069f2cc96b8876f5514acfa4ba49274b61be46f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282932"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>Povolení Azure Monitoru pro virtuální počítače pomocí zásad Azure

Tento článek vysvětluje, jak povolit Azure Monitor pro virtuální počítače pro virtuální počítače Azure nebo škálovací sady virtuálních počítačů pomocí Zásad y Azure. Na konci tohoto procesu budete úspěšně nakonfigurovánpovolení log Analytics a závislostí agentů a identifikované virtuální počítače, které nejsou kompatibilní.

Pokud chcete zjistit, spravovat a povolit Azure Monitor pro virtuální počítače pro všechny vaše virtuální počítače Azure nebo škálovací sady virtuálních počítačů, můžete použít azure policy nebo Azure PowerShell. Azure Policy je metoda, kterou doporučujeme, protože můžete spravovat definice zásad efektivně řídit vaše předplatná, abyste zajistili konzistentní dodržování předpisů a automatické povolení nově zřízených virtuálních počítačů. Tyto definice zásad:

* Nasaďte agenta analýzy protokolů a agenta závislostí.
* Zpráva o výsledcích dodržování předpisů.
* Náprava pro nekompatibilní virtuální chod.

Pokud máte zájem o provádění těchto úkolů pomocí Azure PowerShellu nebo šablony Azure Resource Manageru, přečtěte si část [Povolení Azure Monitoru pro virtuální počítače pomocí azure powershellu nebo šablon Azure Resource Manageru](vminsights-enable-at-scale-powershell.md).

## <a name="prerequisites"></a>Požadavky
Před použitím zásad k připojení virtuálních počítačů Azure a škálovacích sad virtuálních počítačů do Azure Monitoring pro virtuální počítače musíte povolit řešení VMInsights v pracovním prostoru, který použijete k ukládání dat monitorování. Tuto úlohu lze dokončit na stránce **Začínáme** v Azure Monitoru na kartě **Ostatní možnosti registrace.**  Vyberte **Konfigurovat pracovní prostor**, který vás vyzve k výběru pracovního prostoru, který má být nakonfigurován.

![Konfigurace pracovního prostoru](media/vminsights-enable-at-scale-policy/configure-workspace.png)

Pracovní prostor můžete také nakonfigurovat tak, že zvolíte **Povolit pomocí zásad** a pak vyberete tlačítko Konfigurovat panel nástrojů **pracovního prostoru.**  Tím nainstalujete řešení VMInsights do vybraného pracovního prostoru, které umožní pracovnímu prostoru ukládat data monitorování odeslaná virtuálními počítači a škálovací sady virtuálních strojů, které povolíte pomocí zásad. 

![Povolit pomocí zásad](media/vminsights-enable-at-scale-policy/enable-using-policy.png)

## <a name="manage-policy-coverage-feature-overview"></a>Spravovat přehled funkcí Pokrytí zásad

Pokrytí zásad Azure Monitor pro virtuální počítače zjednodušuje zjišťování, správu a povolení ve velkém měřítku iniciativu **Povolit Azure Monitor pro virtuální počítače,** která zahrnuje definice zásad uvedené výše. Chcete-li získat přístup k této funkci, vyberte **další možnosti registrace** na kartě **Začínáme** v Azure Monitor u virtuálních počítačů. Výběrem **možnosti Spravovat pokrytí zásad** otevřete stránku **Pokrytí zásad Azure Monitor pro virtuální počítače.**

![Azure Monitor z karty Začínáme virtuálních počítačích](./media/vminsights-enable-at-scale-policy/get-started-page.png)

Tady můžete kontrolovat a spravovat pokrytí iniciativy napříč skupinami pro správu a předplatnými. Můžete pochopit, kolik virtuálních uživatelů existuje v každé skupině pro správu a předplatná a jejich stav dodržování předpisů.

![Stránka Spravovat zásady Azure Monitor pro virtuální počítače](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Tyto informace jsou užitečné, které vám pomůžou naplánovat a spustit scénář zásad správného řízení pro Azure Monitor pro virtuální počítače z jednoho centrálního umístění. Zatímco Azure Policy poskytuje zobrazení dodržování předpisů, když je zásada nebo iniciativa přiřazena k oboru, s touto novou stránkou můžete zjistit, kde není zásada nebo iniciativa přiřazena, a přiřadit ji na místě. Všechny akce, jako je přiřazení, zobrazení a úpravy přesměrování na Azure Policy přímo. Stránka **Pokrytí zásad Azure Monitor pro virtuální počítače** je rozšířené a integrované prostředí pro jenom iniciativu **Povolit Azure Monitor pro virtuální počítače**.

Na této stránce můžete také nakonfigurovat pracovní prostor Analýzy protokolů pro Azure Monitor pro virtuální počítače, který:

- Nainstaluje řešení mapy služeb.
- Umožňuje čítače výkonu operačního systému používané grafy výkonu, sešity a vlastní dotazy protokolu a výstrahy.

![Azure Monitor pro virtuální počítače konfigurace pracovního prostoru](media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Tato možnost nesouvisí s žádnými akcemi zásad. Je k dispozici pro poskytování snadný způsob, jak splnit [požadavky](vminsights-enable-overview.md) potřebné pro povolení Azure Monitor pro virtuální počítače.  

### <a name="what-information-is-available-on-this-page"></a>Jaké informace jsou k dispozici na této stránce?

V následující tabulce je uveden rozpis informací, které jsou uvedeny na stránce pokrytí zásad a jak je interpretovat.

| Funkce | Popis | 
|----------|-------------| 
| **Rozsah** | Skupina pro správu a předplatná, ke kterým máte nebo ke kterým jste zdědili přístup, se schopností přejít k podrobnostem v hierarchii skupinpro správu.|
| **Role** | Vaše role k oboru, který může být čtenář, vlastník nebo přispěvatel. V některých případech se může zdát prázdné označuje, že můžete mít přístup k předplatnému, ale ne do skupiny pro správu, do které patří. Informace v jiných sloupcích se liší v závislosti na vaší roli. Role je klíčová při určování toho, jaká data můžete zobrazit, a akce, které můžete provádět, pokud jde o přiřazování zásad nebo iniciativ (vlastníka), jejich úpravy nebo zobrazení dodržování předpisů. |
| **Celkem virtuálních můek** | Počet virtuálních discích v rámci tohoto oboru. Pro skupinu pro správu je to součet virtuálních aplikací vnořených pod předplatnými nebo skupinou pro správu podřízených. |
| **Pokrytí přiřazení** | Procento virtuálních společností, které jsou zahrnuty do zásady nebo iniciativy. |
| **Stav přiřazení** | Informace o stavu přiřazení zásad nebo iniciativy. |
| **Kompatibilní virtuální ms** | Počet virtuálních disponovačů, které jsou kompatibilní v rámci zásady nebo iniciativy. Pro iniciativu **Povolit Azure Monitor pro virtuální počítače**, to je počet virtuálních počítače, které mají agenta Log Analytics a závislost agenta. V některých případech se může zobrazit prázdné z důvodu žádné přiřazení, žádné virtuální chodů nebo nedostatek oprávnění. Informace jsou poskytovány v rámci **stavu dodržování předpisů**. |
| **Dodržování předpisů** | Celkové číslo dodržování předpisů je součet různých prostředků, které jsou kompatibilní děleno součtem všech různých prostředků. |
| **Stav dodržování předpisů** | Informace o stavu dodržování předpisů pro přiřazení zásad nebo iniciativy.|

Při přiřazení zásady nebo iniciativy může být oborem vybraným v přiřazení uvedený obor nebo jeho podmnožinou. Například jste pravděpodobně vytvořili přiřazení pro předplatné (obor zásady) a ne skupinu pro správu (obor pokrytí). V tomto případě hodnota **pokrytí přiřazení** označuje virtuální chody v oboru zásad nebo iniciativy děleno virtuálními virtuálními sítěmi v oboru disponibility. V jiném případě jste pravděpodobně vyloučili některé virtuální uživatele, skupiny prostředků nebo předplatné z oboru zásad. Pokud je hodnota prázdná, znamená to, že zásada nebo iniciativa neexistuje nebo nemáte oprávnění. Informace jsou uvedeny v části **Stav přiřazení**.

## <a name="enable-by-using-azure-policy"></a>Povolit pomocí zásad Azure

Povolení Azure Monitoru pro virtuální počítače pomocí Azure Policy ve vašem tenantovi:

- Přiřaďte iniciativu k oboru: skupině pro správu, předplatnému nebo skupině prostředků.
- Zkontrolujte a najakte výsledky dodržování předpisů.

Další informace o přiřazování zásad Azure najdete v [tématu Přehled zásad Azure](../../governance/policy/overview.md#policy-assignment) a zkontrolujte [přehled skupin pro správu,](../../governance/management-groups/overview.md) než budete pokračovat.

### <a name="policies-for-azure-vms"></a>Zásady pro virtuální počítače Azure

Definice zásad pro virtuální počítač Azure jsou uvedeny v následující tabulce.

|Name (Název) |Popis |Typ |
|-----|------------|-----|
|Povolení Azure Monitoru pro virtuální počítače |Povolte Azure Monitor pro virtuální počítače v zadaném oboru (skupina pro správu, předplatné nebo skupina prostředků). Jako parametr převezme pracovní prostor Log Analytics. |Iniciativy |
|Nasazení agenta závislostí auditu – image virtuálního počítače (OS) není uvedena |Hlásí virtuální chod jako nekompatibilní, pokud image virtuálního zařízení (OS) není definována v seznamu a agent není nainstalovaný. |Zásada |
|Nasazení agenta analýzy protokolů auditu – image virtuálního počítače (OS) není uveden |Hlásí virtuální chod jako nekompatibilní, pokud image virtuálního zařízení (OS) není definována v seznamu a agent není nainstalovaný. |Zásada |
|Nasazení agenta závislostí pro virtuální počítače s Linuxem |Nasazení agenta závislostí pro virtuální počítače s Linuxem, pokud je image virtuálního počítače (OS) definována v seznamu a agent není nainstalovaný. |Zásada |
|Nasazení agenta závislostí pro virtuální servery Windows |Nasazení agenta závislostí pro virtuální servery Windows, pokud je image virtuálního zařízení (OS) definována v seznamu a agent není nainstalovaný. |Zásada |
|Nasazení agenta Log Analytics pro virtuální počítače s Linuxem |Nasazení agenta Log Analytics pro virtuální počítače s Linuxem, pokud je image virtuálního počítače (OS) definována v seznamu a agent není nainstalovaný. |Zásada |
|Nasazení agenta Analýzy protokolů pro virtuální servery Windows |Nasazení agenta Log Analytics pro virtuální uživatele windows, pokud je image virtuálního zařízení (OS) definována v seznamu a agent není nainstalovaný. |Zásada |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Zásady pro škálovací sady virtuálních strojů Azure

Definice zásad pro škálovací sadu virtuálních strojů Azure jsou uvedeny v následující tabulce.

|Name (Název) |Popis |Typ |
|-----|------------|-----|
|Povolení Azure Monitoru pro škálovací sady virtuálních strojů |Povolte Azure Monitor pro škálovací sady virtuálních strojů v zadaném oboru (skupina pro správu, předplatné nebo skupina prostředků). Jako parametr převezme pracovní prostor Log Analytics. Poznámka: Pokud je zásada upgradu škálovací sady nastavená na ruční, použijte rozšíření na všechny virtuální počítače v sadě voláním upgrade na ně. V zapisování/li střižové míchaná, klitova. `az vmss update-instances` |Iniciativy |
|Nasazení agenta závislostí auditu ve škálovacích sadách virtuálních strojů – image virtuálního počítače (OS) není uveden |Sestavy škálování virtuálního počítače jako nekompatibilní, pokud image virtuálního počítače (OS) není definována v seznamu a agent není nainstalován. |Zásada |
|Nasazení agenta analýzy protokolů auditu ve škálovacích sadách virtuálních strojů – image virtuálního počítače (OS) není uveden |Sestavy škálování virtuálního počítače jako nekompatibilní, pokud image virtuálního počítače (OS) není definována v seznamu a agent není nainstalován. |Zásada |
|Nasazení agenta závislostí pro škálovací sady virtuálních strojů pro Linux |Nasazení agenta závislostí pro škálovací sady virtuálních strojů v Linuxu, pokud je image virtuálního počítače (OS) definována v seznamu a agent není nainstalovaný. |Zásada |
|Nasazení agenta závislostí pro škálovací sady virtuálních strojů windows |Nasazení agenta závislostí pro škálovací sady virtuálních strojů systému Windows, pokud je bitová kopie virtuálního počítače (OS) definována v seznamu a agent není nainstalovaný. |Zásada |
|Nasazení agenta Log Analytics pro škálovací sady virtuálních strojů pro Linux |Nasazení agenta Log Analytics pro škálovací sady virtuálních strojů v Linuxu, pokud je image virtuálního počítače (OS) definována v seznamu a agent není nainstalovaný. |Zásada |
|Nasazení agenta Log Analytics pro škálovací sady virtuálních strojů windows |Nasazení agenta Log Analytics pro škálovací sady virtuálních strojů windows, pokud je bitová kopie virtuálního počítače (OS) definována v seznamu a agent není nainstalovaný. |Zásada |

Samostatné zásady (nejsou součástí iniciativy) jsou popsány zde:

|Name (Název) |Popis |Typ |
|-----|------------|-----|
|Pracovní prostor analýzy protokolu auditu pro virtuální hosti – neshoda sestavy |Sestavy virtuálních stránek jako nedodržující předpisy, pokud nejsou protokolování do pracovního prostoru Log Analytics zadané v zásadách nebo přiřazení iniciativy. |Zásada |

### <a name="assign-the-azure-monitor-initiative"></a>Přiřazení iniciativy Azure Monitor

Pokud chcete vytvořit přiřazení zásad ze stránky **Pokrytí zásad Azure Monitor pro virtuální počítače,** postupujte takto. Informace o tom, jak tyto kroky provést, najdete [v tématu Vytvoření přiřazení zásad z webu Azure Portal](../../governance/policy/assign-policy-portal.md).

Při přiřazení zásady nebo iniciativy může být oborem vybraným v přiřazení zde uvedený obor nebo jeho podmnožinou. Například jste pravděpodobně vytvořili přiřazení pro předplatné (obor zásady) a ne pro skupinu pro správu (obor pokrytí). V takovém případě procento pokrytí by znamenalo virtuální chody v oblasti zásad nebo iniciativy vydělené virtuálními sítěmi v rozsahu disponibility. V jiném případě jste pravděpodobně vyloučili některé virtuální uživatele nebo skupiny prostředků nebo předplatné z oboru zásad. Pokud je prázdná, znamená to, že zásada nebo iniciativa neexistuje nebo nemáte oprávnění. Informace jsou uvedeny v části **Stav přiřazení**.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. Na webu Azure Portal vyberte **Monitor**. 

3. V části **Přehledy** zvolte **Virtuální počítače.**
 
4. Vyberte kartu **Začínáme.** Na stránce vyberte **Spravovat pokrytí zásad**.

5. V tabulce vyberte skupinu pro správu nebo předplatné. **Výběrem** tři tečky (...). V příkladu obor omezuje přiřazení zásad na seskupení virtuálních počítačů pro vynucení.

6. Na stránce **přiřazení zásad Azure** je předem vyplněná iniciativou **Povolit Azure Monitor pro virtuální počítače**. 
    Pole **Název přiřazení** je automaticky vyplněno názvem iniciativy, ale můžete jej změnit. Můžete také přidat volitelný popis. Pole **Přiřazeno podle** je automaticky vyplněno podle toho, kdo je přihlášen. Tato hodnota je nepovinná.

7. (Nepovinné) Chcete-li z oboru odebrat jeden nebo více prostředků, vyberte **možnost Vyloučení**.

8. V rozevíracím **seznamu pracovního prostoru Log Analytics** pro podporovanou oblast vyberte pracovní prostor.

   > [!NOTE]
   > Pokud je pracovní prostor nad rámec přiřazení, udělte *přispěvateli analýzy protokolů* oprávnění k ID jistiny přiřazení zásad. Pokud tak neučiníte, může se zobrazit `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` selhání nasazení, jako je Udělení přístupu, přečtěte si, [jak ručně nakonfigurovat spravovanou identitu](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  Zaškrtávací políčko **Spravovaná identita** je zaškrtnuto, protože přiřazená iniciativa obsahuje zásadu s efektem *deployIfNotExists.*
    
9. V rozevíracím seznamu **Spravovat umístění identity** vyberte příslušnou oblast.

10. Vyberte **Přiřadit**.

Po vytvoření přiřazení aktualizuje stránka **Azure Monitor for VMs Policy Coverage** pokrytí pokrytí pokrytí **přiřazení**, **stav přiřazení**, kompatibilní virtuální **počítače**a stav **dodržování předpisů** tak, aby odrážely změny. 

Následující matice mapuje každý možný stav dodržování předpisů pro iniciativu.  

| Stav dodržování předpisů | Popis | 
|------------------|-------------|
| **Odpovídající** | Všechny virtuální společnosti v oboru mají agenty analýzy protokolů a závislostí nasazené.|
| **Není kompatibilní** | Ne všechny virtuální společnosti v oboru mají agenty analýzy protokolů a závislostí nasazené do nich a může vyžadovat nápravu.|
| **Nespuštěno** | Bylo přidáno nové přiřazení. |
| **Zámek** | Nemáte dostatečná oprávnění pro skupinu pro správu. <sup>1.</sup> | 
| **Prázdné** | Není přiřazena žádná zásada. | 

<sup>1</sup> Pokud nemáte přístup ke skupině pro správu, požádejte vlastníka o poskytnutí přístupu. Nebo můžete zobrazit dodržování předpisů a spravovat přiřazení prostřednictvím podřízených skupin pro správu nebo předplatných. 

Následující tabulka mapuje každý možný stav přiřazení pro iniciativu.

| Stav přiřazení | Popis | 
|------------------|-------------|
| **Úspěch** | Všechny virtuální společnosti v oboru mají agenty analýzy protokolů a závislostí nasazené.|
| **Upozornění** | Předplatné není pod skupinou pro správu.|
| **Nespuštěno** | Bylo přidáno nové přiřazení. |
| **Zámek** | Nemáte dostatečná oprávnění pro skupinu pro správu. <sup>1.</sup> | 
| **Prázdné** | Neexistují žádné virtuální společnosti nebo není přiřazena zásada. | 
| **Akce** | Přiřaďte zásadu nebo upravte přiřazení. | 

<sup>1</sup> Pokud nemáte přístup ke skupině pro správu, požádejte vlastníka o poskytnutí přístupu. Nebo můžete zobrazit dodržování předpisů a spravovat přiřazení prostřednictvím podřízených skupin pro správu nebo předplatných.

## <a name="review-and-remediate-the-compliance-results"></a>Kontrola a náprava výsledků dodržování předpisů

Následující příklad je pro virtuální počítač Azure, ale platí také pro škálovací sady virtuálních strojů. Informace o kontrole výsledků dodržování předpisů naleznete v [tématu Identifikace výsledků nedodržování předpisů](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Na stránce **Azure Monitor for VMs Policy Coverage** vyberte skupinu pro správu nebo předplatné z tabulky. **Výběrem** tři tečky (...).   

![Dodržování zásad pro virtuální počítače Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

Na základě výsledků zásad zahrnutých do iniciativy jsou virtuální počítači v následujících scénářích vykázány jako nevyhovující:

* Agent analýzy protokolů nebo agent závislostí není nasazený.  
    Tento scénář je typický pro obor s existujícími virtuálními počítačemi. Chcete-li ji zmírnit, nasaďte požadované agenty [vytvořením nápravných úloh](../../governance/policy/how-to/remediate-resources.md) v zásadách, které nedodržují předpisy.  
    - Nasazení agenta závislostí pro virtuální počítače s Linuxem
    - Nasazení agenta závislostí pro virtuální servery Windows
    - Nasazení agenta Log Analytics pro virtuální počítače s Linuxem
    - Nasazení agenta Analýzy protokolů pro virtuální servery Windows

* Image virtuálního zařízení (OS) není v definici zásad y identifikována.  
    Kritéria zásad nasazení zahrnují jenom virtuální počítače, které jsou nasazené z dobře známých ibi virtuálních počítačích Azure. Zkontrolujte dokumentaci a zjistěte, jestli je operační systém v om podporován. Pokud není podporována, duplikujte zásady nasazení a aktualizujte je nebo upravte tak, aby byla bitová kopie kompatibilní.  
    - Nasazení agenta závislostí auditu – image virtuálního počítače (OS) není uvedena
    - Nasazení agenta analýzy protokolů auditu – image virtuálního počítače (OS) není uveden

* Virtuální hotely se nepřihlašují k určenému pracovnímu prostoru Log Analytics.  
    Je možné, že některé virtuální společnosti v oboru iniciativy se přihlašují k jinému pracovnímu prostoru Log Analytics, než který je zadán v přiřazení zásad. Tato zásada je nástroj k identifikaci, které virtuální chody jsou vykazování do pracovního prostoru nesplňující požadavky.  
    - Pracovní prostor analýzy protokolu auditu pro virtuální hosti – neshoda sestavy

## <a name="edit-an-initiative-assignment"></a>Úprava přiřazení iniciativy

Kdykoli po přiřazení iniciativy skupině pro správu nebo předplatnému ji můžete upravit a upravit tak následující vlastnosti:

- Název přiřazení
- Popis
- Přiřazeno
- Pracovní prostor služby Log Analytics
- Výjimky

## <a name="next-steps"></a>Další kroky

Teď, když je monitorování povolené pro vaše virtuální počítače, jsou tyto informace dostupné pro analýzu pomocí Azure Monitoru pro virtuální počítače. 

- Informace o zjištěných závislostech aplikací najdete [v tématu Zobrazení programu Azure Monitor for VMS .](vminsights-maps.md) 

- Informace o problémových místech a celkovém využití s výkonem virtuálního počítače najdete v [tématu Zobrazení výkonu virtuálních počítačích Azure](vminsights-performance.md). 
