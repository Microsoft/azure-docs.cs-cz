---
title: Povolení monitorování Azure pro virtuální počítače pomocí služby Azure Policy | Dokumentace Microsoftu
description: Tento článek popisuje, jak povolit Azure Monitor pro virtuální počítače pro několik virtuálních počítačů Azure nebo škálovacích sad virtuálních počítačů s využitím zásad Azure.
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
ms.openlocfilehash: cf06004c70609dbea59a47b207e3568299260a82
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594445"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>Povolení monitorování Azure pro virtuální počítače (preview) pomocí služby Azure Policy

Tento článek vysvětluje, jak povolit Azure Monitor pro virtuální počítače (preview) pro virtuální počítače Azure nebo škálovacích sad virtuálních počítačů s využitím zásad Azure. Na konci tohoto procesu se úspěšně nakonfigurovaný povolení Log Analytics a agenty závislostí a identifikovali jsme virtuální počítače, které nedodržují předpisy.

Objevovat, spravovat a povolte monitorování Azure pro virtuální počítače pro všechny virtuální počítače Azure nebo škálovací sady virtuálních počítačů, můžete použít zásady Azure nebo Azure Powershellu. Azure Policy je metoda, doporučujeme, protože spravujete definice zásad a efektivně řídit vaše předplatná k zajištění dodržování předpisů pro konzistentní a automatické povolení nově zřízený virtuální počítače. Tyto definice zásad:

* Nasaďte agenta Log Analytics a agenta závislostí.
* Sestav o výsledcích dodržování předpisů.
* Nápravě nekompatibilních virtuálních počítačů.

Pokud máte zájem o provedení těchto úloh pomocí Azure Powershellu nebo šablony Azure Resource Manageru, přečtěte si téma [povolit monitorování Azure pro virtuální počítače (preview) pomocí šablon Azure Resource Manageru nebo Azure Powershellu](vminsights-enable-at-scale-powershell.md).

## <a name="manage-policy-coverage-feature-overview"></a>Správa zásad pokrytí přehled funkcí

Původně byly zkušenosti s Azure Policy pro správu a nasazení zásad definice pro monitorování Azure pro virtuální počítače bylo provedeno výhradně z Azure Policy. Funkci Správa zásad pokrytí díky jednoduchost a lepší zjišťovat, spravovat a povolit ve velkém měřítku **povolit monitorování Azure pro virtuální počítače** iniciativy, která zahrnuje definice zásad, již bylo zmíněno dříve. Můžete použít tuto novou funkci z **Začínáme** karty ve službě Azure Monitor pro virtuální počítače. Vyberte **spravovat pokrytí zásadami** otevřít **monitorování Azure pro virtuální počítače pokrytí zásadami** stránky.

![Azure Monitor z virtuálních počítačů Začínáme karty](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

Z tohoto místa můžete zkontrolovat a spravovat pokrytí pro iniciativy v rámci skupiny pro správu a předplatných. Kolik virtuálních počítačů vám umožní pochopit existovat v každém ze skupin pro správu a předplatná a jejich stav dodržování předpisů.

![Azure Monitor pro stránky zásad spravovat virtuální počítače](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Tyto informace jsou užitečné při plánování a spusťte váš scénář zásad správného řízení pro monitorování Azure pro virtuální počítače z jednoho centrálního místa. Zatímco Azure Policy obsahuje zobrazení dodržování předpisů, když zásady nebo iniciativa přiřadí do oboru, pomocí této nové stránce můžete zjistit, kde zásady nebo iniciativa není přiřazen a přiřaďte ho na místě. Všechny akce, jako jsou přiřazení, zobrazovat a upravovat přímo přesměrování ke službě Azure Policy. **Monitorování Azure pro virtuální počítače pokrytí zásadami** stránka je rozbalený a integrované prostředí pro pouze iniciativu **povolit monitorování Azure pro virtuální počítače**.

Z této stránky můžete také konfigurovat pracovního prostoru Log Analytics pro monitorování Azure pro virtuální počítače, které:

- Nainstaluje řešení Infrastructure Insights a instalaci mapy služeb.
- Povoluje čítače výkonu operačního systému používá grafy výkonu, pracovní sešity a vlastní protokol dotazy a výstrahy.

![Konfigurovat pracovní prostor pro virtuální počítače Azure Monitor](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Tato možnost se nevztahuje na všechny akce zásad. Je k dispozici snadný způsob, jak splnit [požadavky](vminsights-enable-overview.md) vyžaduje k povolení monitorování Azure pro virtuální počítače.  

### <a name="what-information-is-available-on-this-page"></a>Jaké informace jsou k dispozici na této stránce?
Následující tabulka obsahuje přehled informací, které se zobrazí na stránce pokrytí zásad a jak ho chcete interpretovat.

| Funkce | Popis | 
|----------|-------------| 
| **Rozsah** | Skupina pro správu a předplatná, u kterých máte nebo dědí přístup k se možnost Procházet hierarchii skupiny správy.|
| **Role** | Vaše role do oboru, což může být čtečky, vlastník nebo Přispěvatel. V některých případech se může zobrazit prázdné, pokud chcete určit, že můžete mít přístup k předplatnému, ale ne ke skupině pro správu patří do. Informace v dalších sloupců se liší v závislosti na vaší roli. Role je klíč při určování, jaká data se zobrazí a akce lze provádět z hlediska přiřazení zásad nebo iniciativ (Vlastník), jejich úpravou nebo zobrazení dodržování předpisů. |
| **Celkový počet virtuálních počítačů** | Počet virtuálních počítačů v rámci tohoto oboru. Pro skupinu pro správu je součet vnořen v rámci předplatného nebo podřízené skupiny správy virtuálních počítačů. |
| **Přiřazení pokrytí** | Procento virtuálních počítačů, které jsou zahrnuté do zásady nebo iniciativa. |
| **Stav přiřazení** | Informace o stavu přiřazení zásady nebo iniciativa. |
| **Kompatibilní virtuální počítače** | Počet virtuálních počítačů, které splňují předpisy v rámci zásady nebo iniciativa. Pro iniciativu **povolit monitorování Azure pro virtuální počítače**, toto je počet virtuálních počítačů, které mají agenta Log Analytics a agenta závislostí. V některých případech se může zobrazovat prázdný z důvodu žádné přiřazení, žádné virtuální počítače nebo nedostatečná oprávnění. Informace najdete v části **stavu dodržování předpisů**. |
| **Dodržování předpisů** | Celkový počet dodržování předpisů je součtem různé prostředky, které jsou kompatibilní s rozdělené součet všech různých zdrojů. |
| **Stav dodržování předpisů** | Informace o stavu dodržování předpisů pro přiřazení zásady nebo iniciativa.|

Když přiřadíte zásady nebo iniciativa, může být oboru zvoleného v přiřazení oboru uvedeny nebo určité podmnožiny. Například může vytvoříte přiřazení pro předplatné (zásady oboru) a ne skupiny pro správu (pokrytí oboru). V takovém případě hodnota **přiřazení pokrytí** označuje virtuální počítače v zásad nebo iniciativ oboru dělený virtuální počítače v oboru pokrytí. V jiném případě jste možná vyloučili některé virtuální počítače, skupiny prostředků nebo předplatného z obor zásady. Pokud je hodnota prázdná, znamená to, že zásady nebo iniciativa neexistuje nebo nemáte oprávnění. Informace najdete v části **stav přiřazení**.

## <a name="enable-by-using-azure-policy"></a>Povolení s využitím zásad Azure

Povolení monitorování Azure pro virtuální počítače s využitím zásad Azure ve vašem tenantovi:

- Přiřadit iniciativu obor: skupina pro správu, předplatné nebo skupinu prostředků.
- Zkontrolujte a opravte výsledků kontroly kompatibility.

Další informace o přiřazování zásad Azure najdete v tématu [Přehled služby Azure Policy](../../governance/policy/overview.md#policy-assignment) a zkontrolujte [přehled skupin pro správu](../../governance/management-groups/index.md) předtím, než budete pokračovat.

### <a name="policies-for-azure-vms"></a>Zásady pro virtuální počítače Azure

V následující tabulce jsou uvedeny definice zásad pro virtuální počítač Azure.

|Name |Popis |type |
|-----|------------|-----|
|\[Preview\]: Povolit monitorování Azure pro virtuální počítače |Povolte monitorování Azure pro virtuální počítače v zadaném oboru (skupiny pro správu, předplatné nebo skupinu prostředků). Jako parametr používá pracovní prostor Log Analytics. |Iniciativa |
|\[Preview\]: Auditovat nasazení agenta závislostí – image virtuálního počítače (OS) neuvedené v seznamu |Sestavy virtuálních počítačů jako nedodržující předpisy, pokud se image virtuálního počítače (OS) není definována v seznamu a agent není nainstalovaný. |Zásada |
|\[Preview\]: Auditovat nasazení agenta Log Analytics – image virtuálního počítače (OS) neuvedené v seznamu |Sestavy virtuálních počítačů jako nedodržující předpisy, pokud se image virtuálního počítače (OS) není definována v seznamu a agent není nainstalovaný. |Zásada |
|\[Preview\]: Nasazení agenta závislosti pro virtuální počítače s Linuxem |Nasazení agenta závislosti pro virtuální počítače s Linuxem, pokud image virtuálního počítače (OS) je definován v seznamu a agent není nainstalovaný. |Zásada |
|\[Preview\]: Nasazení agenta závislosti pro virtuální počítače s Windows |Nasaďte agenta závislosti pro virtuální počítače s Windows, když v seznamu je definována image virtuálního počítače (OS) a agent není nainstalovaný. |Zásada |
|\[Preview\]: Nasadit agenta Log Analytics pro virtuální počítače s Linuxem |Nasaďte agenta Log Analytics pro virtuální počítače s Linuxem, když v seznamu je definována image virtuálního počítače (OS) a agent není nainstalovaný. |Zásada |
|\[Preview\]: Nasadit agenta Log Analytics pro virtuální počítače s Windows |Nasaďte agenta Log Analytics pro virtuální počítače s Windows, když v seznamu je definována image virtuálního počítače (OS) a agent není nainstalovaný. |Zásada |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Zásady pro virtuální počítač Azure škálovací sady

Definice zásad pro sadu škálování virtuálního počítače Azure jsou uvedeny v následující tabulce.

|Name |Popis |type |
|-----|------------|-----|
|\[Preview\]: Povolit monitorování Azure pro škálovací sady virtuálních počítačů |Povolte monitorování Azure pro škálovací sady virtuálních počítačů v zadaném oboru (skupiny pro správu, předplatné nebo skupinu prostředků). Jako parametr používá pracovní prostor Log Analytics. Poznámka: Pokud vaše zásady upgradu škálovací sady je nastaven na ručně, použijte rozšíření pro všechny virtuální počítače v sadě voláním upgrade na ně. V rozhraní příkazového řádku je to az vmss update-instances. |Iniciativa |
|\[Preview\]: Auditovat nasazení agenta závislostí ve škálovacích sadách virtuálních počítačů – image virtuálního počítače (OS) neuvedené v seznamu |Sestavy virtuálního počítače škálovací sady jako nedodržující předpisy, pokud v seznamu není definována image virtuálního počítače (OS) a agent není nainstalovaný. |Zásada |
|\[Preview\]: Auditovat nasazení agenta Log Analytics ve škálovacích sadách virtuálních počítačů – image virtuálního počítače (OS) neuvedené v seznamu |Sestavy virtuálního počítače škálovací sady jako nedodržující předpisy, pokud v seznamu není definována image virtuálního počítače (OS) a agent není nainstalovaný. |Zásada |
|\[Preview\]: Nasazení agenta závislosti pro škálovací sady virtuálních počítačů Linux |Nasazení agenta závislosti pro Linux virtual machine scale sets s Pokud image virtuálního počítače (OS) je definován v seznamu a agent není nainstalovaný. |Zásada |
|\[Preview\]: Nasazení agenta závislosti pro škálovací sady virtuálních počítačů Windows |Nasazení agenta závislosti pro Windows virtual machine scale sets s Pokud image virtuálního počítače (OS) je definován v seznamu a agent není nainstalovaný. |Zásada |
|\[Preview\]: Nasadit agenta Log Analytics pro škálovací sady virtuálních počítačů Linux |Nasaďte agenta Log Analytics pro Linux virtual machine scale sets s Pokud Image virtuálního počítače (OS) je definován v seznamu a agent není nainstalovaný. |Zásada |
|\[Preview\]: Nasadit agenta Log Analytics pro škálovací sady virtuálních počítačů Windows |Nasaďte agenta Log Analytics pro Windows virtual machine scale sets s Pokud image virtuálního počítače (OS) je definován v seznamu a agent není nainstalovaný. |Zásada |

Samostatné zásady (není zahrnutá v iniciativě) je popsaný tady:

|Název |Popis |type |
|-----|------------|-----|
|\[Preview\]: Auditovat pracovní prostor Log Analytics pro virtuální počítač – neshoda sestavy |Sestavy virtuálních počítačů jako nedodržující předpisy, pokud nejsou protokolování do pracovního prostoru Log Analytics zadaná v přiřazení zásady nebo iniciativa. |Zásada |

### <a name="assign-the-azure-monitor-initiative"></a>Přiřadit iniciativu Azure Monitor
K vytvoření přiřazení zásady z **monitorování Azure pro virtuální počítače pokrytí zásadami** stránce, postupujte podle těchto kroků. Jak provést tyto kroky najdete v tématu [vytvoření přiřazení zásady z portálu Azure portal](../../governance/policy/assign-policy-portal.md).

Při přiřazení zásady nebo iniciativa, může být oboru zvoleného v přiřazení oboru tady nebo podmnožinu. Například může vytvoříte přiřazení předplatného (zásady oboru), a ne skupiny pro správu (pokrytí rozsahu). Procento pokrytí v takovém případě by označoval, virtuální počítače v zásad nebo iniciativ oboru dělený virtuálních počítačů v rámci pokrytí. V jiném případě jste možná vyloučili některé virtuální počítače nebo skupiny prostředků nebo předplatného z oboru zásad. Pokud není zadán, znamená, že zásady nebo iniciativa neexistuje nebo nemáte příslušná oprávnění. Informace najdete v části **stav přiřazení**.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Na webu Azure Portal, vyberte **monitorování**. 

3. Zvolte **virtuálních počítačů (preview)** v **Insights** oddílu.
 
4. Vyberte **Začínáme** kartu. Na stránce vybrat **spravovat pokrytí zásadami**.

5. Vyberte předplatné nebo skupinu pro správu z tabulky. Vyberte **oboru** tak, že vyberete tři tečky (...). V tomto příkladu omezuje obor přiřazení zásady pro seskupení virtuálních počítačů pro vynucení.

6. Na **přiřazení zásady Azure Policy** stránky, ji má předem vyplněné k iniciativě **povolit monitorování Azure pro virtuální počítače**. 
    **Název přiřazení** pole se automaticky vyplní název iniciativy, ale můžete ho změnit. Můžete přidat také volitelný popis. **Přiřadil** pole se vyplní automaticky, podle který je přihlášen. Tato hodnota je volitelná.

7. (Volitelné) Chcete-li odebrat jednu nebo více prostředků z oboru, vyberte **vyloučení**.

8. V **pracovní prostor Log Analytics** rozevírací seznam pro podporované oblasti, vyberte pracovní prostor.

   > [!NOTE]
   > Pokud pracovní prostor je mimo rozsah přiřazení, udělit *Přispěvatel Log Analytics* oprávnění ID přiřazení zásady instanční objekt. Pokud to neuděláte, může se zobrazit selhání nasazení jako `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` Pokud chcete udělit přístup, zkontrolujte [jak ručně nakonfigurovat spravovanou identitu](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  **Identity spravované** zaškrtávací políčko je zaškrtnuto, protože obsahuje přiřazení iniciativy zásad s *deployIfNotExists* vliv.
    
9. V **spravovat Identity umístění** rozevíracího seznamu vyberte příslušnou oblast.

10. Vyberte **Přiřadit**.

Po vytvoření tohoto přiřazení, **monitorování Azure pro virtuální počítače pokrytí zásadami** aktualizace stránky **přiřazení pokrytí**, **stav přiřazení**, **dodržujících předpisy Virtuální počítače**, a **stavu dodržování předpisů** tak, aby odrážely změny. 

Následující matice mapuje každý stav shody možné pro iniciativy.  

| Stav dodržování předpisů | Popis | 
|------------------|-------------|
| **Vyhovující** | Všechny virtuální počítače v rámci mít nasazené agenty Log Analytics a závislostí.|
| **Nedodržující předpisy** | Ne všechny virtuální počítače v rámci mají Log Analytics a agenti závislosti nasazené a může vyžadovat nápravy.|
| **Nezahájeno** | Nové přiřazení se přidal. |
| **Zámek** | Nemáte dostatečná oprávnění ke skupině pro správu. <sup>1</sup> | 
| **Prázdné** | Není přiřazena žádná zásada. | 

<sup>1</sup> Pokud nemáte přístup ke skupině pro správu, požádejte vlastníka a zajistit tak přístup. Zobrazit dodržování předpisů nebo spravovat přiřazení podřízené skupiny pro správu nebo předplatných. 

Následující tabulka mapuje každý stav možné přiřazení iniciativy.

| Stav přiřazení | Popis | 
|------------------|-------------|
| **Úspěch** | Všechny virtuální počítače v rámci mít nasazené agenty Log Analytics a závislostí.|
| **Upozornění** | Předplatné není v rámci skupiny pro správu.|
| **Nezahájeno** | Nové přiřazení se přidal. |
| **Zámek** | Nemáte dostatečná oprávnění ke skupině pro správu. <sup>1</sup> | 
| **Prázdné** | Neexistují žádné virtuální počítače nebo není přiřazená zásada. | 
| **Akce** | Přiřazení zásady nebo upravte přiřazení. | 

<sup>1</sup> Pokud nemáte přístup ke skupině pro správu, požádejte vlastníka a zajistit tak přístup. Zobrazit dodržování předpisů nebo spravovat přiřazení podřízené skupiny pro správu nebo předplatných.

## <a name="review-and-remediate-the-compliance-results"></a>Zkontrolujte a opravte výsledků kontroly kompatibility

V následujícím příkladu je virtuální počítač Azure, ale platí také pro škálovací sady virtuálních počítačů. Zjistěte, jak zkontrolovat výsledky kontroly dodržování předpisů, najdete v článku [identifikovat výsledky při nedodržení předpisů](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Na **monitorování Azure pro virtuální počítače pokrytí zásadami** vyberte skupinu pro správu nebo předplatného z tabulky. Vyberte **zobrazit dodržování zásad** tak, že vyberete tři tečky (...).   

![Zásady dodržování předpisů pro virtuální počítače Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

Na základě výsledků Zásady iniciativy je součástí virtuálních počítačů označené jako nedodržující předpisy v následujících scénářích:

* Není nasadit agenta log Analytics nebo agenta závislostí.  
    Tento scénář je typický pro obor s existující virtuální počítače. Jak ji zmírnit, nasazení požadovaných agentů podle [vytváření úloh nápravy](../../governance/policy/how-to/remediate-resources.md) nekompatibilní zásady.  
    - \[Preview\]: Nasazení agenta závislosti pro virtuální počítače s Linuxem
    - \[Preview\]: Nasazení agenta závislosti pro virtuální počítače s Windows
    - \[Preview\]: Nasadit agenta Log Analytics pro virtuální počítače s Linuxem
    - \[Preview\]: Nasadit agenta Log Analytics pro virtuální počítače s Windows

* Image virtuálního počítače (OS) není identifikované v definici zásad.  
    Kritéria zásad nasazení zahrnovat jenom virtuální počítače, které jsou nasazeny z dobře známé imagí virtuálních počítačů Azure. Vyhledejte v dokumentaci najdete v článku, zda je podporován operační systém virtuálního počítače. Pokud není podporován, duplicitní zásady nasazení a aktualizace nebo upravte ho tak, aby image kompatibilní.  
    - \[Preview\]: Auditovat nasazení agenta závislostí – image virtuálního počítače (OS) neuvedené v seznamu
    - \[Preview\]: Auditovat nasazení agenta Log Analytics – image virtuálního počítače (OS) neuvedené v seznamu

* Virtuální počítače nejsou přihlášení k zadanému pracovnímu prostoru Log Analytics.  
    Je možné, že některé virtuální počítače v rámci iniciativy jsou přihlášení k pracovnímu prostoru Log Analytics, než je ten, který je zadaná v přiřazení zásady. Tato zásada je nástroj, který určíte, které virtuální počítače se hlásí nekompatibilní pracovního prostoru.  
    - \[Preview\]: Auditovat pracovní prostor Log Analytics pro virtuální počítač – neshoda sestavy

## <a name="edit-an-initiative-assignment"></a>Upravit přiřazení iniciativy

Kdykoli po přiřazení iniciativy do skupiny pro správu nebo předplatného, můžete upravit ho upravit následující vlastnosti:

- Název přiřazení
- Popis
- Přiřadil
- Pracovní prostor log Analytics
- Výjimky

## <a name="next-steps"></a>Další postup

Teď, když je zapnuté monitorování pro vaše virtuální počítače, tyto informace jsou dostupné pro analýzy a monitorování Azure pro virtuální počítače. 

- Další informace o použití funkce stavu, najdete v článku [zobrazení monitorování Azure pro virtuální počítače stav](vminsights-health.md). 
- Chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md). 
- Identifikujte kritické body a celkové využití s výkonem Virtuálního počítače, naleznete v tématu [virtuálního počítače Azure zobrazení výkonu](vminsights-performance.md). 
- Chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md).
