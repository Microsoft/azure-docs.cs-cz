---
title: Povolit Azure Monitor pro virtuální počítače pomocí Azure Policy | Microsoft Docs
description: Tento článek popisuje, jak povolit Azure Monitor pro virtuální počítače pro více virtuálních počítačů Azure nebo služby Virtual Machine Scale Sets pomocí Azure Policy.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 267072b06d936822eae7e7257d62566a020471bb
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656224"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>Povolit Azure Monitor pro virtuální počítače (Preview) pomocí Azure Policy

Tento článek vysvětluje, jak povolit Azure Monitor pro virtuální počítače (Preview) pro virtuální počítače Azure nebo službu Virtual Machine Scale Sets pomocí Azure Policy. Na konci tohoto procesu se úspěšně nakonfigurují povolení agentů Log Analytics a závislostí a identifikovaných virtuálních počítačů, které nedodržují předpisy.

Pokud chcete zjistit, spravovat a povolit Azure Monitor pro virtuální počítače pro všechny virtuální počítače Azure nebo sady škálování virtuálních počítačů, můžete použít buď Azure Policy, nebo Azure PowerShell. Azure Policy je metoda, kterou doporučujeme, protože můžete spravovat definice zásad a efektivně tak řídit vaše předplatná, aby se zajistilo konzistentní dodržování předpisů a automatické povolování nově zřízených virtuálních počítačů. Tyto definice zásad:

* Nasaďte agenta Log Analytics a agenta závislostí.
* Sestav o výsledcích dodržování předpisů.
* Opravit pro virtuální počítače, které nedodržují předpisy

Pokud vás zajímá splnění těchto úkolů s Azure PowerShell nebo šablonou Azure Resource Manager, přečtěte si téma [povolení Azure monitor pro virtuální počítače (Preview) pomocí šablon Azure PowerShell nebo Azure Resource Manager](vminsights-enable-at-scale-powershell.md).

## <a name="manage-policy-coverage-feature-overview"></a>Přehled funkcí spravovat pokrytí zásad

V původním prostředí se zkušenost s Azure Policy pro správu a nasazení definic zásad pro Azure Monitor pro virtuální počítače prováděla výhradně z Azure Policy. Funkce Spravovat pokrytí zásad zjednodušuje a usnadňuje zjišťování, správu a povolování škálování v rámci **povolení Azure monitor pro virtuální počítače** iniciativy, která zahrnuje výše zmíněné definice zásad. K této nové funkci můžete přistupovat **z karty Začínáme** v Azure monitor pro virtuální počítače. Výběrem **možnosti spravovat pokrytí zásad** otevřete stránku **Azure monitor pro virtuální počítače pokrytí zásad** .

![Karta Začínáme s Azure Monitor z virtuálních počítačů](./media/vminsights-enable-at-scale-policy/get-started-page.png)

Odsud můžete v rámci svých skupin a předplatných pro správu kontrolovat a spravovat pokrytí iniciativ. Můžete pochopit, kolik virtuálních počítačů existuje v každé ze skupin a předplatných pro správu a jejich stav dodržování předpisů.

![Stránka Azure Monitor pro virtuální počítače Správa zásad](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Tyto informace vám pomůžou naplánovat a spustit scénář zásad správného řízení pro Azure Monitor pro virtuální počítače z jednoho centrálního umístění. I když Azure Policy poskytuje zobrazení dodržování předpisů v případě, že se k oboru přiřadí zásada nebo iniciativa s touto novou stránkou, můžete zjistit, kde zásada nebo iniciativa není přiřazená, a přiřadit ji na místo. Všechny akce, jako je přiřazení, zobrazení a úpravy přesměrování, Azure Policy přímo. Stránka **pokrytí zásad Azure monitor pro virtuální počítače** je rozšířené a integrované prostředí, které **umožňuje Azure monitor pro virtuální počítače povolit**jenom iniciativu.

Na této stránce můžete také nakonfigurovat pracovní prostor Log Analytics pro Azure Monitor pro virtuální počítače, který:

- Nainstaluje řešení Service Map.
- Povolí čítače výkonu operačního systému používané grafy výkonu, sešity a vlastními dotazy a výstrahami protokolu.

![Azure Monitor pro virtuální počítače nakonfigurovat pracovní prostor](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Tato možnost není v relaci s žádnými akcemi zásad. Je k dispozici pro zajištění snadného způsobu, jak splnit [požadavky](vminsights-enable-overview.md) potřebné k povolení Azure monitor pro virtuální počítače.  

### <a name="what-information-is-available-on-this-page"></a>Jaké informace jsou na této stránce k dispozici?

Následující tabulka uvádí rozpis informací, které jsou uvedeny na stránce pokrytí zásad a jejich interpretaci.

| Funkce | Popis | 
|----------|-------------| 
| **Rozsah** | Skupina pro správu a předplatná, ke kterým máte nebo zdědili přístup, mají schopnost přejít k podrobnostem prostřednictvím hierarchie skupiny pro správu.|
| **Role** | Vaše role v oboru, což může být čtenář, vlastník nebo Přispěvatel. V některých případech se může zdát, že je prázdná, aby označoval, že máte přístup k předplatnému, ale ne ke skupině pro správu, ke které patří. Informace v dalších sloupcích se liší v závislosti na vaší roli. Role je klíč, který určuje, jaká data můžete zobrazit, a akce, které můžete provádět v souvislosti s přiřazováním zásad nebo iniciativ (Vlastník), jejich úprav nebo dodržování předpisů. |
| **Celkový počet virtuálních počítačů** | Počet virtuálních počítačů v daném oboru. Pro skupinu pro správu se jedná o součet virtuálních počítačů vnořených v rámci předplatných nebo podřízených skupin pro správu. |
| **Pokrytí přiřazení** | Procento virtuálních počítačů, na které se vztahuje zásada nebo podnět. |
| **Stav přiřazení** | Informace o stavu přiřazení zásady nebo iniciativy |
| **Vyhovující virtuální počítače** | Počet virtuálních počítačů, které vyhovují zásadám nebo iniciativám. Pro účely iniciativy **Azure monitor pro virtuální počítače**je toto počet virtuálních počítačů, které mají agenta Log Analytics a agenta závislostí. V některých případech se může zdát, že je prázdný, protože není k dispozici žádné přiřazení, žádné virtuální počítače nebo nemáte dostatečná oprávnění. Informace jsou k dispozici v části **stav dodržování předpisů**. |
| **Dodržování předpisů** | Celkové číslo dodržování předpisů je součet různých prostředků, které jsou v souladu s hodnotou součet všech různých prostředků. |
| **Stav dodržování předpisů** | Informace o stavu dodržování předpisů pro přiřazení zásady nebo iniciativy.|

Když přiřadíte zásadu nebo iniciativu, může být obor vybraný v přiřazení oborem uvedeným v poli nebo jeho podmnožinou. Například jste mohli vytvořit přiřazení pro předplatné (obor zásad), nikoli skupinu pro správu (rozsah pokrytí). V takovém případě hodnota **pokrytí přiřazení** označuje virtuální počítače v oboru zásad nebo iniciativy dělené virtuálními počítači v oboru pokrytí. V jiném případě jste možná vyloučili některé virtuální počítače, skupiny prostředků nebo předplatné z oboru zásad. Pokud je hodnota prázdná, znamená to, že buď zásada nebo podnět neexistuje, nebo nemáte příslušná oprávnění. Informace jsou k dispozici v části **stav přiřazení**.

## <a name="enable-by-using-azure-policy"></a>Povolení s využitím zásad Azure

Povolení monitorování Azure pro virtuální počítače s využitím zásad Azure ve vašem tenantovi:

- Přiřaďte iniciativu k oboru: skupina pro správu, předplatné nebo skupinu prostředků.
- Kontrola a náprava výsledků dodržování předpisů.

Další informace o přiřazování Azure Policy naleznete v tématu [Azure Policy Overview](../../governance/policy/overview.md#policy-assignment) a Projděte si [Přehled skupin pro správu](../../governance/management-groups/overview.md) , než budete pokračovat.

### <a name="policies-for-azure-vms"></a>Zásady pro virtuální počítače Azure

Definice zásad pro virtuální počítač Azure jsou uvedené v následující tabulce.

|Název |Popis |Typ |
|-----|------------|-----|
|\[Preview\]: Povolit Azure Monitor pro virtuální počítače |Povolí Azure Monitor pro virtuální počítače v zadaném oboru (skupina pro správu, předplatné nebo skupina prostředků). Jako parametr používá pracovní prostor Log Analytics. |Iniciativa |
|\[Preview\]: nasazení agenta závislostí auditu – image virtuálního počítače (OS) není v seznamu. |Hlásí virtuální počítače jako nedodržující předpisy, pokud image virtuálního počítače (OS) není v seznamu definovaná a Agent není nainstalovaný. |Zásady |
|\[Preview\]: nasazení agenta Log Analytics auditu – image virtuálního počítače (OS) bez seznamu |Hlásí virtuální počítače jako nedodržující předpisy, pokud image virtuálního počítače (OS) není v seznamu definovaná a Agent není nainstalovaný. |Zásady |
|\[Preview\]: nasazení agenta závislostí pro virtuální počítače se systémem Linux |Nasaďte agenta závislostí pro virtuální počítače se systémem Linux, pokud je image virtuálního počítače definovaná v seznamu a Agent není nainstalovaný. |Zásady |
|\[Preview\]: nasazení agenta závislostí pro virtuální počítače s Windows |Nasaďte agenta závislostí pro virtuální počítače s Windows, pokud je image virtuálního počítače definovaná v seznamu a Agent není nainstalovaný. |Zásady |
|\[Preview\]: nasazení Log Analytics agenta pro virtuální počítače se systémem Linux |Nasaďte agenta Log Analytics pro virtuální počítače se systémem Linux, pokud je image virtuálního počítače definovaná v seznamu a Agent není nainstalovaný. |Zásady |
|\[Preview\]: nasazení Log Analytics agenta pro virtuální počítače s Windows |Nasaďte agenta Log Analytics pro virtuální počítače s Windows, pokud je image virtuálního počítače definovaná v seznamu a Agent není nainstalovaný. |Zásady |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Zásady pro Azure Virtual Machine Scale Sets

Definice zásad pro sadu škálování virtuálního počítače Azure jsou uvedené v následující tabulce.

|Název |Popis |Typ |
|-----|------------|-----|
|\[Preview\]: povolení Azure Monitor pro Virtual Machine Scale Sets |Povolte Azure Monitor pro sadu škálování virtuálního počítače v zadaném oboru (skupina pro správu, předplatné nebo skupinu prostředků). Jako parametr používá pracovní prostor Log Analytics. Poznámka: Pokud je vaše zásada upgradu sady škálování nastavená na ruční, použijte rozšíření na všechny virtuální počítače v sadě voláním upgradu. V rozhraní příkazového řádku je to `az vmss update-instances`. |Iniciativa |
|\[Preview\]: nasazení agenta závislostí auditu ve Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu. |Hlásí sadu virtuálních počítačů s měřítkem jako nedodržující předpisy, pokud image virtuálního počítače není v seznamu definovaná a Agent není nainstalovaný. |Zásady |
|\[Preview\]: nasazení agenta Log Analytics auditu ve Virtual Machine Scale Sets – image virtuálního počítače (OS) není v seznamu. |Hlásí sadu virtuálních počítačů s měřítkem jako nedodržující předpisy, pokud image virtuálního počítače není v seznamu definovaná a Agent není nainstalovaný. |Zásady |
|\[Preview\]: nasazení agenta závislostí pro virtuální počítače se systémem Linux Scale Sets |Nasaďte agenta závislostí pro sadu virtuálních počítačů se systémem Linux, pokud je image virtuálního počítače definovaná v seznamu a Agent není nainstalovaný. |Zásady |
|\[Preview\]: nasazení agenta závislostí pro sadu škálování virtuálních počítačů s Windows |Nasaďte agenta závislostí pro sadu Windows Virtual Machine Scale Sets, pokud je v seznamu definovaná image virtuálního počítače a Agent není nainstalovaný. |Zásady |
|\[Preview\]: nasazení agenta Log Analytics pro Linux Virtual Machine Scale Sets |Nasazení agenta Log Analytics pro Linux Virtual Machine Scale Sets, pokud je image virtuálního počítače definovaná v seznamu a Agent není nainstalovaný. |Zásady |
|\[Preview\]: nasazení agenta Log Analytics pro Windows Virtual Machine Scale Sets |Nasaďte agenta Log Analytics pro Virtual Machine Scale Sets, pokud je image virtuálního počítače definovaná v seznamu a Agent není nainstalovaný. |Zásady |

Samostatné zásady (není zahrnutá v iniciativě) je popsaný tady:

|Název |Popis |Typ |
|-----|------------|-----|
|\[Preview\]: pracovní prostor pro audit Log Analytics pro virtuální počítač – neshoda sestavy |Vykázat virtuální počítače jako nedodržující předpisy, pokud se neprotokolují do Log Analyticsho pracovního prostoru zadaného v přiřazení zásady nebo iniciativy. |Zásady |

### <a name="assign-the-azure-monitor-initiative"></a>Přiřadit iniciativu Azure Monitor

Pokud chcete vytvořit přiřazení zásad na stránce **Azure monitor pro virtuální počítače pokrytí zásad** , postupujte podle těchto kroků. Pokud chcete zjistit, jak tento postup provést, přečtěte si téma [vytvoření přiřazení zásady z Azure Portal](../../governance/policy/assign-policy-portal.md).

Když přiřadíte zásadu nebo iniciativu, může být obor vybraný v přiřazení oborem uvedeným zde nebo podmnožinou. Například jste mohli vytvořit přiřazení pro předplatné (rozsah zásad), nikoli skupinu pro správu (rozsah pokrytí). V takovém případě procento pokrytí by znamenalo, že virtuální počítače v oboru zásad nebo iniciativy jsou dělené virtuálními počítači v oboru pokrytí. V jiném případě jste možná vyloučili některé virtuální počítače nebo skupiny prostředků nebo předplatné z oboru zásad. Pokud je prázdný, znamená to, že buď zásada nebo podnět neexistuje, nebo nemáte oprávnění. Informace jsou k dispozici v části **stav přiřazení**.

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).

2. V Azure Portal vyberte **monitorovat**. 

3. V části **přehledy** vyberte **Virtual Machines (Preview)** .
 
4. Vyberte kartu **Začínáme** . Na stránce vyberte **Spravovat pokrytí zásad**.

5. V tabulce vyberte buď skupinu pro správu, nebo předplatné. Vyberte **Rozsah** výběrem tři tečky (...). V tomto příkladu obor omezuje přiřazení zásad na seskupení virtuálních počítačů pro vynucení.

6. Na stránce **přiřazení Azure Policy** je předem vyplněný s iniciativou **Enable Azure monitor pro virtuální počítače**. 
    Pole **název přiřazení** se automaticky vyplní názvem iniciativy, ale můžete ho změnit. Můžete také přidat volitelný popis. Pole **přiřazeno podle** je automaticky vyplněno na základě toho, kdo je přihlášen. Tato hodnota je volitelná.

7. Volitelné Chcete-li odebrat jeden nebo více prostředků z oboru, vyberte možnost **vyloučení**.

8. V rozevíracím seznamu **Log Analytics pracovní prostor** pro podporovanou oblast vyberte pracovní prostor.

   > [!NOTE]
   > Pokud je tento pracovní prostor nad rámec přiřazení, udělte Log Analytics oprávnění *Přispěvatel* k ID objektu zabezpečení přiřazení zásad. Pokud to neuděláte, může se zobrazit chyba nasazení, například `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` pro udělení přístupu, přečtěte si, [Jak ručně nakonfigurovat spravovanou identitu](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  Je zaškrtnuto políčko **spravovaná identita** , protože přiřazená iniciativa zahrnuje zásadu s *deployIfNotExists* účinkem.
    
9. V rozevíracím seznamu **spravovat umístění identity** vyberte příslušnou oblast.

10. Vyberte **Přiřadit**.

Po vytvoření přiřazení aktualizuje stránka **pokrytí zásad Azure monitor pro virtuální počítače** aktualizace **rozsahu přiřazení**, **stavu přiřazení**, **odpovídajících virtuálních počítačů**a **stavu dodržování předpisů** , aby odrážely změny. 

Následující matice mapuje všechny možné stavy dodržování předpisů pro iniciativu.  

| Stav dodržování předpisů | Popis | 
|------------------|-------------|
| **Kompatibilní** | Na všechny virtuální počítače v oboru jsou nasazeni Log Analytics a agenti závislostí.|
| **Nedodržující předpisy** | Ne všechny virtuální počítače v oboru nemají Log Analytics a v nich jsou nasazeni agenti závislostí a mohou vyžadovat nápravu.|
| **Nezahájeno** | Bylo přidáno nové přiřazení. |
| **Získáte** | Nemáte dostatečná oprávnění ke skupině pro správu. <sup>1</sup> | 
| **Prázdné** | Nepřiřadily se žádné zásady. | 

<sup>1</sup> Pokud ke skupině pro správu nemáte přístup, požádejte vlastníka o poskytnutí přístupu. Nebo si prohlédněte dodržování předpisů a spravujte přiřazení prostřednictvím podřízených skupin nebo předplatných pro správu. 

Následující tabulka namapuje všechny možné stavy přiřazení pro iniciativu.

| Stav přiřazení | Popis | 
|------------------|-------------|
| **Nástup** | Na všechny virtuální počítače v oboru jsou nasazeni Log Analytics a agenti závislostí.|
| **Upozornění** | Předplatné není ve skupině pro správu.|
| **Nezahájeno** | Bylo přidáno nové přiřazení. |
| **Získáte** | Nemáte dostatečná oprávnění ke skupině pro správu. <sup>1</sup> | 
| **Prázdné** | Neexistují žádné virtuální počítače, nebo není zásada přiřazena. | 
| **Akce** | Přiřaďte zásadu nebo upravte přiřazení. | 

<sup>1</sup> Pokud ke skupině pro správu nemáte přístup, požádejte vlastníka o poskytnutí přístupu. Nebo si prohlédněte dodržování předpisů a spravujte přiřazení prostřednictvím podřízených skupin nebo předplatných pro správu.

## <a name="review-and-remediate-the-compliance-results"></a>Zkontrolujte a opravte výsledků kontroly kompatibility

Následující příklad je pro virtuální počítač Azure, ale vztahuje se také na službu Virtual Machine Scale Sets. Informace o tom, jak zkontrolovat výsledky dodržování předpisů, najdete v tématu [určení výsledků nedodržování předpisů](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Na stránce **Azure monitor pro virtuální počítače pokrytí zásad** vyberte buď skupinu pro správu, nebo předplatné z tabulky. Kliknutím na tlačítko se třemi tečkami (...) vyberte **Zobrazit dodržování předpisů** .   

![Zásady dodržování předpisů pro virtuální počítače Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

Na základě výsledků zásad, které jsou součástí iniciativy, se virtuální počítače hlásí jako nedodržující předpisy v následujících scénářích:

* Agent Log Analytics nebo agent závislostí není nasazený.  
    Tento scénář je typický pro obor s existující virtuální počítače. Pokud ho chcete zmírnit, nasaďte požadované agenty tím, že se [vytvoří úlohy oprav](../../governance/policy/how-to/remediate-resources.md) při nekompatibilních zásadách.  
    - \[Preview\]: nasazení agenta závislostí pro virtuální počítače se systémem Linux
    - \[Preview\]: nasazení agenta závislostí pro virtuální počítače s Windows
    - \[Preview\]: nasazení Log Analytics agenta pro virtuální počítače se systémem Linux
    - \[Preview\]: nasazení Log Analytics agenta pro virtuální počítače s Windows

* Image virtuálního počítače (operační systém) není v definici zásad identifikovaná.  
    Kritéria zásad nasazení zahrnovat jenom virtuální počítače, které jsou nasazeny z dobře známé imagí virtuálních počítačů Azure. Vyhledejte v dokumentaci najdete v článku, zda je podporován operační systém virtuálního počítače. Pokud není podporován, duplicitní zásady nasazení a aktualizace nebo upravte ho tak, aby image kompatibilní.  
    - \[Preview\]: nasazení agenta závislostí auditu – image virtuálního počítače (OS) není v seznamu.
    - \[Preview\]: nasazení agenta Log Analytics auditu – image virtuálního počítače (OS) bez seznamu

* Virtuální počítače nejsou přihlášení k zadanému pracovnímu prostoru Log Analytics.  
    Je možné, že některé virtuální počítače v rámci iniciativy jsou přihlášení k pracovnímu prostoru Log Analytics, než je ten, který je zadaná v přiřazení zásady. Tyto zásady představují nástroj, který určuje, které virtuální počítače se hlásí do pracovního prostoru nedodržující předpisy.  
    - \[Preview\]: pracovní prostor pro audit Log Analytics pro virtuální počítač – neshoda sestavy

## <a name="edit-an-initiative-assignment"></a>Upravit přiřazení iniciativy

Kdykoli poté, co přiřadíte iniciativu ke skupině nebo předplatnému pro správu, můžete ji upravit a upravit následující vlastnosti:

- Název přiřazení
- Popis
- Přiřadil
- Pracovní prostor Log Analytics
- Výjimky

## <a name="next-steps"></a>Další kroky

Teď, když je monitorování povolené pro vaše virtuální počítače, jsou tyto informace k dispozici pro analýzu pomocí Azure Monitor pro virtuální počítače. 

- Pokud chcete zobrazit zjištěné závislosti aplikací, přečtěte si téma [zobrazení Azure monitor pro virtuální počítače mapa](vminsights-maps.md). 

- Pokud chcete zjistit kritické body a celkové využití výkonu vašeho virtuálního počítače, přečtěte si téma [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md). 
