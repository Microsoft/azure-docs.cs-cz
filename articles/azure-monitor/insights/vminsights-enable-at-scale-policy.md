---
title: Povolit monitorování Azure pro virtuální počítače s použitím služby Azure Policy | Dokumentace Microsoftu
description: Tento článek popisuje, jak povolit Azure Monitor pro virtuální počítače pro více virtuálních počítačů Azure nebo škálovací sady virtuálních počítačů pomocí Azure Policy.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: magoedte
ms.openlocfilehash: 9664ad5272ffeb55bd85e3d4c4f4b70d05e97702
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524142"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-policy"></a>Povolit monitorování Azure pro virtuální počítače (preview) s použitím služby Azure Policy

Tento článek vysvětluje, jak povolit Azure Monitor pro virtuální počítače (preview) pro virtuální počítače Azure nebo škálovací sady virtuálních počítačů pomocí Azure Policy. Na konci tohoto procesu můžete se úspěšně nakonfigurovali povolení Log Analytics a agenti závislosti a identifikovat virtuální počítače, které nejsou kompatibilní.

Objevovat, spravovat a povolte monitorování Azure pro virtuální počítače pro všechny virtuální počítače Azure nebo škálovací sady virtuálních počítačů, můžete použít zásady Azure nebo Azure Powershellu. Azure Policy je doporučeným způsobem, protože spravujete definice zásad a efektivně řídit vaše předplatná k zajištění dodržování předpisů pro konzistentní a automatické povolení nově zřízený virtuální počítače. Tyto definice zásad:

* Nasaďte agenta Log Analytics a agenta závislostí.
* Sestav o výsledcích dodržování předpisů.
* Opravte nevyhovující virtuálních počítačů.

Pokud vás zajímá provádět to pomocí šablony Azure Powershellu nebo Azure Resource Manageru, najdete v článku [povolit pomocí Azure Powershellu nebo správce prostředků šablony](vminsights-enable-at-scale-powershell.md). 

## <a name="manage-policy-coverage-feature-overview"></a>Správa zásad pokrytí přehled funkcí

Zkušenosti s Azure Policy pro správu a nasazení zásad definice pro monitorování Azure pro virtuální počítače se původně provedla výhradně z Azure Policy. S **spravovat pokrytí zásadami** funkce, umožňuje jednodušší a snadněji zjistit, spravovat a povolit ve velkém měřítku **povolit monitorování Azure pro virtuální počítače** iniciativy, která zahrnuje definice zásad již bylo zmíněno dříve. Můžete použít tuto novou funkci z **Začínáme** karty ve službě Azure Monitor pro virtuální počítače tak, že vyberete **spravovat pokrytí zásadami**. Otevře se stránka pokrytí zásadami virtuálních počítačů. 

![Azure Monitor z virtuálních počítačů Začínáme karty](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

Odsud můžete můžete zkontrolovat a spravovat pokrytí pro iniciativy v rámci skupiny pro správu a předplatných, jakož i pochopit, kolik virtuálních počítačů existují ve všech skupin pro správu a předplatná a jejich stav dodržování předpisů.   

![Azure Monitor pro stránky zásad spravovat virtuální počítače](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Tyto informace jsou užitečné pro plánování a provádění váš scénář zásad správného řízení pro monitorování Azure pro virtuální počítače z jednoho centrálního umístění. Zatímco Azure Policy obsahuje zobrazení dodržování předpisů, když zásady nebo iniciativa přiřadí do oboru, pomocí této nové stránce můžete zjistit, kde/iniciativa zásad není přiřazen a přiřaďte ho místně. Všechny akce (přiřazení, zobrazení, úpravy) přesměrovat přímo ke službě Azure Policy. Azure Monitor pro stránku pokrytí zásadami virtuálních počítačů je rozbalený a integrované prostředí pro pouze iniciativu **povolit monitorování Azure pro virtuální počítače**. 

Z této stránky můžete také nakonfigurovat váš pracovní prostor Log Analytics pro monitorování Azure pro virtuální počítače, který provede následující akce:

- Nainstaluje řešení Infrastructure Insights a instalaci mapy služeb
- Povoluje čítače výkonu operačního systému používá grafy výkonu, pracovní sešity a vlastní protokol dotazy a výstrahy.

![Konfigurovat pracovní prostor pro virtuální počítače Azure Monitor](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Tato možnost se nevztahuje na všechny akce zásad a nabízí snadný způsob, jak splnit [požadavky](vminsights-enable-overview.md) vyžaduje k povolení monitorování Azure pro virtuální počítače.  

### <a name="what-information-is-available-on-this-page"></a>Jaké informace jsou k dispozici na této stránce?
Následující tabulka poskytuje přehled toho, jaké informace se zobrazí na stránce zásad pokrytí a jak ho chcete interpretovat.

| Funkce | Popis | 
|----------|-------------| 
| **Rozsah** | Skupina pro správu a předplatná, u kterých máte nebo zděděné přístup k hierarchií skupiny možnost Procházet rozhraní pro správu.|
| **Role** | Vaše role na rozsah, může být čtečky vlastníka nebo přispěvatele. V některých případech může být prázdný k označení, že máte přístup k předplatnému, ale ne ke skupině pro správu patří do. Informace v dalších sloupců se liší v závislosti na vaší roli, jako je klíč při určování, jaká data se zobrazí a akce lze provádět z hlediska přiřazení iniciativy nebo zásady (Vlastník), jejich úpravou nebo zobrazení dodržování předpisů. |
| **Celkový počet virtuálních počítačů** | Počet virtuálních počítačů v rámci tohoto oboru. Pro skupinu pro správu je součet vnořen v souladu s odběry a/nebo podřízené skupiny správy virtuálních počítačů. |
| **Přiřazení pokrytí** | Procento virtuálních počítačů, které jsou zahrnuté do iniciativy nebo zásady. |
| **Stav přiřazení** | V tomto sloupci, můžete najít informace o stavu vašich zásad nebo iniciativ přiřazení. |
| **Kompatibilní virtuální počítače** | Počet virtuálních počítačů, které splňují předpisy v rámci zásad a iniciativy. Pro iniciativu **povolit monitorování Azure pro virtuální počítače** Toto je počet virtuálních počítačů, které mají agenta Log Analytics a agenta závislostí. V některých případech může být prázdný z důvodu žádné přiřazení nebo žádné virtuální počítače nebo nedostatečná oprávnění. Není k dispozici informace o stavu dodržování předpisů. |
| **Dodržování předpisů** | Celkový počet dodržování předpisů je součtem různé prostředky, které jsou kompatibilní rozdělené součet všech různých zdrojů. |
| **Stav dodržování předpisů** | Informace o stavu dodržování předpisů pro vaši zásadu / iniciativy přiřazení.|

Při přiřazení iniciativy nebo zásady, může být oboru zvoleného v přiřazení oboru uvedeny nebo ji podmnožinu. Například jste vytvořili přiřazení pro předplatné (zásady oboru) a ne skupiny pro správu (pokrytí oboru). V takovém případě hodnota **přiřazení pokrytí** naznačují rozsah virtuálních počítačů v zásady (nebo iniciativa) rozdělené podle virtuálních počítačů v oboru pokrytí. V jiném případě jste možná vyloučili některé virtuální počítače, skupiny prostředků nebo předplatného z obor zásady. Pokud je hodnota prázdná, znamená to, že buď/iniciativa zásad neexistuje nebo nemáte oprávnění (informace najdete v stav přiřazení).

## <a name="enable-using-azure-policy"></a>Povolení s využitím Azure Policy

Povolení monitorování Azure pro virtuální počítače s využitím zásad Azure ve vašem tenantovi:

- Přiřadit iniciativu obor: skupina pro správu, předplatné nebo skupinu prostředků
- Zkontrolujte a opravte výsledků kontroly kompatibility

Další informace o přiřazování zásad Azure najdete v tématu [Přehled služby Azure Policy](../../governance/policy/overview.md#policy-assignment) a zkontrolujte [přehled skupin pro správu](../../governance/management-groups/index.md) předtím, než budete pokračovat.

### <a name="policies-for-azure-vms"></a>Zásady pro virtuální počítače Azure

Definice zásad pro virtuální počítač Azure jsou uvedeny v následující tabulce:

|Název |Popis |Type |
|-----|------------|-----|
|[Preview]: Povolit monitorování Azure pro virtuální počítače |Povolte monitorování Azure pro virtuální počítače (VM) v zadaném oboru (skupiny pro správu, předplatné nebo skupinu prostředků). Jako parametr používá pracovní prostor Log Analytics. |Iniciativa |
|[Preview]: Nasazení agenta závislostí auditu – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu |Virtuální počítače ohlásí jako nedodržující předpisy, pokud Image virtuálního počítače (OS) není definována v seznamu a agent není nainstalovaný. |Zásada |
|[Preview]: Auditovat nasazení agenta Log Analytics – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu |Virtuální počítače ohlásí jako nedodržující předpisy, pokud Image virtuálního počítače (OS) není definována v seznamu a agent není nainstalovaný. |Zásada |
|[Preview]: Nasazení agenta závislosti pro virtuální počítače s Linuxem |Nasazení agenta závislostí pro virtuální počítače s Linuxem, pokud Image virtuálního počítače (OS) je definován v seznamu a agent není nainstalovaný. |Zásada |
|[Preview]: Nasazení agenta závislosti pro virtuální počítače s Windows |Nasazení agenta závislosti pro Windows VMs Pokud Image virtuálního počítače (OS) je definován v seznamu a agent není nainstalovaný. |Zásada |
|[Preview]: Nasadit agenta Log Analytics pro virtuální počítače s Linuxem |Nasaďte agenta Log Analytics pro virtuální počítače s Linuxem, když v seznamu je definována Image virtuálního počítače (OS) a agent není nainstalovaný. |Zásada |
|[Preview]: Nasadit agenta Log Analytics pro virtuální počítače s Windows |Nasaďte agenta Log Analytics pro Windows VMs, když v seznamu je definována Image virtuálního počítače (OS) a agent není nainstalovaný. |Zásada |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Zásady pro virtuální počítač Azure škálovací sady

Definice zásad pro sadu škálování virtuálního počítače Azure jsou uvedeny v následující tabulce:

|Název |Popis |Type |
|-----|------------|-----|
|[Preview]: Povolit monitorování Azure VM Scale Sets (VMSS) |Povolte monitorování Azure pro škálovací sady virtuálních počítačů v zadaném oboru (správu skupiny, předplatné nebo skupinu prostředků). Pracovní prostor Log Analytics přijímá jako parametr. Poznámka: Pokud se hodnota upgradePolicy vaše škálovací sada je nastaven na ručně, budete muset použijte rozšíření pro všechny virtuální počítače v sadě voláním upgrade na ně. V rozhraní příkazového řádku by to byl az vmss update-instances. |Iniciativa |
|[Preview]: Auditovat nasazení agenta závislostí v VMSS – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu |Sestavy virtuálního počítače škálovací sady jako nevyhovující, pokud se Image virtuálního počítače (OS) v seznamu a agent není nainstalován. |Zásada |
|[Preview]: Auditovat nasazení agenta Log Analytics v VMSS – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu |Sestavy virtuálního počítače škálovací sady jako nevyhovující, pokud se Image virtuálního počítače (OS) v seznamu a agent není nainstalován. |Zásada |
|[Preview]: Nasazení agenta závislosti pro Škálovací sady virtuálních počítačů s Linuxem (VMSS) |Nasazení agenta závislosti pro Linux virtual machine scale sets s Pokud Image virtuálního počítače (OS) je definován v seznamu a agent není nainstalovaný. |Zásada |
|[Preview]: Nasazení agenta závislosti pro Škálovací sady virtuálních počítačů Windows (VMSS) |Nasaďte závislosti agenta pro Windows virtual machine scale sets s Pokud Image virtuálního počítače (OS) je definován v seznamu a agent není nainstalovaný. |Zásada |
|[Preview]: Nasadit agenta Log Analytics pro Škálovací sady virtuálních počítačů s Linuxem (VMSS) |Nasaďte agenta Log Analytics pro Linux virtual machine scale sets s Pokud Image virtuálního počítače (OS) je definován v seznamu a agent není nainstalovaný. |Zásada |
|[Preview]: Nasadit agenta Log Analytics pro Škálovací sady virtuálních počítačů Windows (VMSS) |Nasaďte Log Analytics agenta pro Windows virtual machine scale sets s Pokud Image virtuálního počítače (OS) je definován v seznamu a agent není nainstalovaný. |Zásada |

Samostatné zásady (není zahrnutá v iniciativě) je popsaný tady:

|Název |Popis |Type |
|-----|------------|-----|
|[Preview]: Pracovní prostor Log Analytics auditu pro virtuální počítač – neshoda sestavy |Sestavy virtuálních počítačů jako nedodržující předpisy, pokud nejsou protokolování do pracovního prostoru Log Analytics zadaná v přiřazení zásady nebo iniciativa. |Zásada |

### <a name="assign-the-azure-monitor-initiative"></a>Přiřadit iniciativu Azure Monitor
K vytvoření přiřazení zásady ze služby Azure Monitor pro stránku pokrytí zásadami virtuální počítače, proveďte následující kroky. Jak provést tyto kroky najdete v tématu [vytvoření přiřazení zásady z portálu Azure portal](../../governance/policy/assign-policy-portal.md).

Při přiřazení iniciativy nebo zásady, může být oboru zvoleného v přiřazení oboru tady nebo podmnožinu. Například může vytvoříte přiřazení pro předplatné (zásady oboru) a není skupinou pro správu (rozsah pokrytí) ve kterém by označoval % případů pokrytí rozsahu virtuálních počítačů v zásady (nebo iniciativa) rozdělené podle virtuálních počítačů v oboru pokrytí. V některých případech je může vyloučili některé virtuální počítače nebo RGs nebo předplatné z obor zásady.  V případě, že je pole prázdné, to znamená, že buď zásady / iniciativy neexistuje nebo nemáte oprávnění (informace o přiřazení stavu k dispozici)  

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Na webu Azure Portal, vyberte **monitorování**. 

3. Zvolte **virtuálních počítačů (preview)** v **Insights** oddílu.
 
4. Vyberte **Začínáme** kartu a na stránce vyberte **spravovat pokrytí zásadami**.

5. Vyberte skupinu pro správu nebo předplatného z tabulky a vyberte **oboru** kliknutím na tlačítko se třemi tečkami (...).      V našem příkladu omezuje obor přiřazení zásady pro seskupení virtuálních počítačů pro vynucení.

6. Na **přiřazení zásady Azure Policy** stránky se předem vyplní iniciativy **povolit monitorování Azure pro virtuální počítače**. 
    **Název přiřazení** pole se automaticky vyplní název iniciativy, ale můžete ho změnit. Můžete také přidat volitelný popis. **Přiřadil** pole se vyplní automaticky, podle který je přihlášen, a tato hodnota je volitelná.

7. (Volitelné) Chcete-li odebrat jednu nebo více prostředků z oboru, vyberte **vyloučení**.

8. V **pracovní prostor Log Analytics** rozevírací seznam pro podporované oblasti, vyberte pracovní prostor.

   > [!NOTE]
   > Pokud pracovní prostor je mimo rozsah přiřazení, udělit *Přispěvatel Log Analytics* oprávnění ID přiřazení zásady instanční objekt. Pokud to neuděláte, může se zobrazit selhání nasazení, jako: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` Pokud chcete udělit přístup, zkontrolujte [jak ručně nakonfigurovat spravovanou identitu](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  **Identity spravované** zaškrtávací políčko je zaškrtnuto, protože obsahuje přiřazení iniciativy zásad s *deployIfNotExists* vliv.
    
9. V **spravovat Identity umístění** rozevíracího seznamu vyberte příslušnou oblast.

10. Vyberte **Přiřadit**.

Jakmile vytvoříte přiřazení monitorování Azure pro virtuální počítače pokrytí zásadami stránka se aktualizuje přiřazení pokrytí, stav přiřazení, virtuálních počítačů dodržování předpisů a stavu dodržování předpisů tak, aby odrážely změny. 

Následující matice mapuje každý stav shody možné pro iniciativy.  

| Stav shody | Popis | 
|------------------|-------------|
| **Vyhovující** | Všechny virtuální počítače v rámci mít nasazené agenty Log Analytics a závislostí.|
| **Nedodržující předpisy** | Ne všechny virtuální počítače v rámci mají Log Analytics a agenti závislosti nasazené a může vyžadovat nápravu.|
| **Nezahájeno** | Nové přiřazení se přidala. |
| **Zámek** | Nemáte dostatečná oprávnění ke skupině pro správu. <sup>1</sup> | 
| **Prázdné** | Nemají přiřazené žádné zásady. | 

<sup>1</sup> Pokud nemáte přístup ke skupině pro správu, požádejte vlastníka k poskytnutí přístupu nebo dodržování předpisů umožňuje zobrazit a spravovat přiřazení podřízené skupiny pro správu nebo předplatných. 

Následující tabulka mapuje každý stav možné přiřazení iniciativy.

| Stav přiřazení | Popis | 
|------------------|-------------|
| **Úspěch** | Všechny virtuální počítače v rámci mít nasazené agenty Log Analytics a závislostí.|
| **Upozornění** | Předplatné není v rámci skupiny pro správu.|
| **Nezahájeno** | Nové přiřazení se přidala. |
| **Zámek** | Nemáte dostatečná oprávnění ke skupině pro správu. <sup>1</sup> | 
| **Prázdné** | Neexistují žádné virtuální počítače nebo není přiřazena zásada. | 
| **Akce** | Přiřadit zásady ani upravit přiřazení | 

<sup>1</sup> Pokud nemáte přístup ke skupině pro správu, požádejte vlastníka k poskytnutí přístupu nebo dodržování předpisů umožňuje zobrazit a spravovat přiřazení podřízené skupiny pro správu nebo předplatných. 

## <a name="review-and-remediate-the-compliance-results"></a>Zkontrolujte a opravte výsledků kontroly kompatibility

V následujícím příkladu je pro virtuální počítač Azure, ale platí také pro škálovací sady virtuálních počítačů. Můžete zjistěte, jak zkontrolovat výsledky kontroly dodržování předpisů načtením [identifikovat nedodržení předpisů výsledky](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Na Azure monitoru pro stránku pokrytí zásadami virtuální počítače, vyberte skupinu pro správu nebo předplatného z tabulky a vyberte **zobrazit dodržování zásad** kliknutím na tlačítko se třemi tečkami (...).   

![Zásady dodržování předpisů pro virtuální počítače Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

Na základě výsledků Zásady iniciativy je součástí virtuálních počítačů označené jako nedodržující předpisy v následujících scénářích:

* Log Analytics nebo agenta závislostí není nasazený.  
    Tento scénář je typický pro obor s existující virtuální počítače. Jak ji zmírnit, nasazení požadovaných agentů podle [vytváření úloh nápravy](../../governance/policy/how-to/remediate-resources.md) nekompatibilní zásady.  
    - [Preview]: Deploy Dependency Agent for Linux VMs
    - [Preview]: Deploy Dependency Agent for Windows VMs
    - [Preview]: Deploy Log Analytics Agent for Linux VMs
    - [Preview]: Deploy Log Analytics Agent for Windows VMs

* Virtuální počítač Image operačního systému není identifikované v definici zásad.  
    Kritéria zásad nasazení zahrnovat jenom virtuální počítače, které jsou nasazeny z dobře známé imagí virtuálních počítačů Azure. Vyhledejte v dokumentaci najdete v článku, zda je podporován operační systém virtuálního počítače. Pokud není podporován, duplicitní zásady nasazení a aktualizace nebo upravte ho tak, aby image kompatibilní.  
    - [Preview]: Nasazení agenta závislostí auditu – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu
    - [Preview]: Auditovat nasazení agenta Log Analytics – obrázku (operačního systému virtuálního počítače) neuvedené v seznamu

* Virtuální počítače nejsou přihlášení k zadanému pracovnímu prostoru Log Analytics.  
    Je možné, že některé virtuální počítače v rámci iniciativy jsou přihlášení k pracovnímu prostoru Log Analytics, než je ten, který je zadaná v přiřazení zásady. Tato zásada je nástroj, který určíte, které virtuální počítače se hlásí nekompatibilní pracovního prostoru.  
    - [Preview]: Audit Log Analytics Workspace for VM - Report Mismatch

## <a name="edit-initiative-assignment"></a>Upravit přiřazení iniciativy

Kdykoli po přiřazení iniciativy do skupiny pro správu nebo předplatného můžete upravit ho upravit následující vlastnosti:

- Název přiřazení
- Popis
- Přiřadil
- Pracovní prostor Log Analytics
- Výjimky

## <a name="next-steps"></a>Další postup

Teď, když je zapnuté monitorování pro vaše virtuální počítače, tyto informace jsou dostupné pro analýzy a monitorování Azure pro virtuální počítače. Další informace o použití funkce stavu, najdete v článku [zobrazení monitorování Azure pro virtuální počítače stav](vminsights-health.md). Chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md). Pokud chcete identifikovat problémová místa a celkové využití výkonu vašich virtuálních počítačů, přečtěte si téma [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md), nebo chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md).