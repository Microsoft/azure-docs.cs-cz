---
title: Diagnostika výkonu pro virtuální počítače Azure | Microsoft Docs
description: Zavádí diagnostiku výkonu Azure pro Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: 857d49fa579e7ea1a6e2c14ae8198cd8ac4fe228
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090631"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Diagnostika výkonu pro virtuální počítače Azure

Nástroj Diagnostika výkonu pomáhá řešit problémy s výkonem, které mohou ovlivnit virtuální počítač se systémem Windows nebo Linux. Mezi podporované scénáře řešení potíží patří rychlé kontroly známých problémů a osvědčených postupů a složité problémy, které zahrnují pomalý výkon virtuálních počítačů nebo vysoké využití procesoru, místa na disku nebo paměti.

Můžete spustit diagnostiku výkonu přímo z Azure Portal, kde můžete také zkontrolovat přehledy a sestavu pro různé protokoly, bohatou konfiguraci a diagnostická data. Před kontaktováním podpora Microsoftu doporučujeme, abyste spustili diagnostiku výkonu a zkontrolovali data o Insights a diagnostice.

> [!NOTE]
> Pro Windows se Diagnostika výkonu aktuálně podporuje na virtuálních počítačích s nainstalovaným rozhraním .NET SDK verze 4,5 nebo novější verzí. Postup, jak spustit diagnostiku výkonu na klasických virtuálních počítačích, najdete v tématu [rozšíření virtuálního počítače Azure Performance Diagnostics](performance-diagnostics-vm-extension.md).

## <a name="supported-operating-systems"></a>Podporované operační systémy

### <a name="windows"></a>Windows

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows 10
* Windows 8.1
* Windows 8

### <a name="linux"></a>Linux

- V současné době jsou podporovány následující distribuce:

    | Distribuce               | Verze                                         |
    |----------------------------|-------------------------------------------------|
    | Server Oracle Linux        | 6,10 [ `*` ], 7,3, 7,6, 7,5 |
    | CentOS                     | 6,5 [ `*` ], 7,6                                    |
    | RHEL                       | 7,2, 7,5, 8,0 [ `*` ]                               |
    | Ubuntu                     | 14,04, 16,04, 18,04, 20,04                               |
    | Debian                     | 8, 9, 10 [ `*` ]                                    |
    | SLES                       | 12 SP4 [ `*` ]                                      |
    |                            |                                                   |

>[!Note]
>[ `*` ] Podívejte se na [známé problémy](how-to-use-perfinsights-linux.md#known-issues) .

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Nainstalujte a spusťte na svém VIRTUÁLNÍm počítači diagnostiku výkonu.

Nástroj Diagnostika výkonu nainstaluje rozšíření virtuálního počítače, které spouští diagnostický nástroj s názvem PerfInsights. PerfInsights je k dispozici pro [systémy Windows](https://aka.ms/perfinsights) i [Linux](https://aka.ms/perfinsightslinux). Chcete-li nainstalovat a spustit diagnostiku výkonu, postupujte podle následujících kroků:

1. V levém sloupci příkazů vyberte **virtuální počítače**.
1. V seznamu názvů virtuálních počítačů vyberte virtuální počítač, na kterém chcete spustit diagnostiku.
1. V pravém sloupci příkazů vyberte **diagnostiku výkonu**.

    ![Snímek obrazovky Azure Portal s zvýrazněným tlačítkem nainstalovat diagnostiku výkonu](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > Na tomto snímku obrazovky je okno názvů virtuálních počítačů skryté.
1. Vyberte účet úložiště (volitelné).

    Pokud chcete pro ukládání výsledků diagnostiky výkonu pro víc virtuálních počítačů použít jeden účet úložiště, můžete vybrat účet úložiště kliknutím na tlačítko **Nastavení** na panelu nástrojů. Jakmile vyberete účet úložiště, klikněte na tlačítko **OK** .

    Pokud nezadáte účet úložiště, vytvoří se ve výchozím nastavení nový účet úložiště.

    ![Snímek obrazovky okna diagnostiky výkonu s zvýrazněním tlačítka panelu nástrojů nastavení](media/performance-diagnostics/settings-button.png)

    ![Snímek obrazovky s výběrem účtu úložiště z okna nastavení diagnostiky výkonu](media/performance-diagnostics/select-storage-account.png)

1. Vyberte tlačítko **instalovat diagnostiku výkonu** .
1. Zaškrtněte políčko **Spustit diagnostiku** , pokud chcete po dokončení instalace spustit diagnostiku. Pokud provedete tento výběr, budete moci vybrat možnost scénář analýzy výkonu a související možnosti.

    ![Snímek obrazovky s tlačítkem nainstalovat diagnostiku výkonu](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Vyberte scénář analýzy, který se má spustit.

Následující scénáře analýzy jsou k dispozici na Azure Portal. Vyberte analýzu v závislosti na potížích s výkonem, které máte. Vyberte možnosti trvání a trasování podle potřeby pro analýzu.

* **Rychlá analýza výkonu**  
    Kontroluje známé problémy, analyzuje osvědčené postupy a shromažďuje diagnostická data. Spuštění této analýzy trvá několik minut. Další informace pro [Windows](https://aka.ms/perfinsights/quick) nebo [Linux](https://aka.ms/perfinsightslinux/quick)

* **Analýza výkonu**  
    Zahrnuje všechny kontroly v rychlé analýze výkonu a monitorují vysokou spotřebu prostředků. Pomocí této verze můžete řešit běžné problémy s výkonem, jako je například vysoké využití procesoru, paměti a disku. Tato analýza trvá 30 sekund až 15 minut v závislosti na vybrané době trvání. Další informace pro [Windows](https://aka.ms/perfinsights/vmslow) nebo [Linux](https://aka.ms/perfinsightslinux/vmslow)

* **Pokročilá analýza výkonu**`*`  
    Zahrnuje všechny kontroly v analýze výkonu a shromažďuje jedno nebo více trasování, jak je uvedeno v následujících částech. Tento scénář použijte k řešení složitých problémů, které vyžadují další trasování. Spuštění tohoto scénáře po delší dobu zvýší celkovou velikost výstupu diagnostiky v závislosti na velikosti virtuálního počítače a vybraných možnostech trasování. Spuštění této analýzy trvá 30 až 15 minut, a to v závislosti na vybrané době trvání. [Další informace](https://aka.ms/perfinsights/advanced)

* **Analýza souborů Azure**`*`  
    Zahrnuje všechny kontroly v analýze výkonu a zachycuje trasování sítě a čítače protokolu SMB. Tento scénář použijte k odstranění potíží s výkonem služby soubory Azure. Spuštění této analýzy trvá 30 až 15 minut, a to v závislosti na vybrané době trvání. [Další informace](https://aka.ms/perfinsights/azurefiles)

>[!Note]
>[ `*` ] Tyto scénáře analýzy jsou podporovány pouze v systému Windows.

![Snímek obrazovky okna spustit diagnostiku v okně diagnostiku výkonu](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Poskytněte příznaky (volitelné).

Vyberte ze seznamu všechny předvybrané příznaky nebo přidejte nové příznaky. To nám pomáhá vylepšit analýzu v budoucnu.

### <a name="provide-support-request-number-if-available-optional"></a>Poskytnout číslo žádosti o podporu, pokud je k dispozici (volitelné)

Pokud pracujete s pracovníkem technické podpory Microsoftu na stávajícím lístku podpory, zadejte číslo lístku podpory.

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Zkontrolujte zásady ochrany osobních údajů a právní podmínky a zaškrtněte políčko pro potvrzení (povinné).

Pokud chcete spustit diagnostiku, musíte souhlasit s právními podmínkami a přijmout zásady ochrany osobních údajů.

### <a name="select-ok-to-run-the-diagnostics"></a>Výběrem OK spusťte diagnostiku.

Oznámení se zobrazí při zahájení instalace diagnostiky výkonu. Po dokončení instalace se zobrazí oznámení, že instalace proběhla úspěšně. Vybraná analýza se pak spustí po určenou dobu trvání. To by bylo dobrý čas na reprodukování problému s výkonem, aby se diagnostická data mohla zachytit správný čas.

Po dokončení analýzy se do tabulek Azure a kontejneru binárního rozsáhlého objektu (BLOB) v zadaném účtu úložiště nahrají následující položky:

* Všechny přehledy a související informace o běhu
* Výstupní komprimovaný soubor (s příponou. zip) (nazvaný **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip** ) ve Windows a soubor tar míč (nazvaný **PerformanceDiagnostics_yyyy-mm-dd_hh-mm-SS-FFF. tar. gz** ) v systému Linux, který obsahuje soubory protokolu
* Sestava HTML

Po nahrání se v Azure Portal zobrazí nová zpráva o diagnostice.

![Snímek obrazovky se seznamem sestav diagnostiky v okně diagnostiku výkonu](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Postup změny nastavení diagnostiky výkonu

Pomocí tlačítka na panelu nástrojů **Nastavení** můžete změnit účet úložiště, ve kterém se můžou ukládat diagnostické poznatky a výstupy. Stejný účet úložiště můžete použít pro několik virtuálních počítačů, které používají diagnostiku výkonu. Když změníte účet úložiště, staré sestavy a přehledy se neodstraní. Již se však nebudou zobrazovat v seznamu diagnostických sestav.

## <a name="review-insights-and-performance-diagnostics-report"></a>Projděte si sestavu Diagnostika přehledů a výkonu

Každý diagnostický běh obsahuje seznam přehledů a doporučení, ovlivněných prostředků, souborů protokolů a dalších informací o rozšířené diagnostice, které se shromažďují, a také sestavu pro zobrazení v režimu offline. Úplný seznam všech shromážděných diagnostických dat najdete v tématu **Jaké informace jsou shromažďovány pomocí PerfInsights?** v [systému Windows](how-to-use-perfinsights.md#what-kind-of-information-is-collected-by-perfinsights) nebo [Linux](how-to-use-perfinsights-linux.md#what-kind-of-information-is-collected-by-perfinsights).

### <a name="select-a-performance-diagnostics-report"></a>Výběr sestavy diagnostiky výkonu

Seznam diagnostických sestav můžete použít k vyhledání všech diagnostických sestav, které byly spuštěny. Seznam obsahuje podrobné informace o použité analýze, přehledy, které byly nalezeny a jejich úrovně jejich dopadu. Výběrem řádku zobrazíte další podrobnosti.

![Snímek obrazovky s výběrem diagnostické sestavy z okna pro diagnostiku výkonu](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Kontrola sestavy diagnostiky výkonu

Každá Sestava diagnostiky výkonu může obsahovat několik přehledů a označovat úroveň dopadu vysoké, střední nebo nízké. Každý přehled také obsahuje doporučení, která vám pomůžou snížit obavy. Přehledy jsou seskupené pro snadné filtrování.

Úrovně dopadu představují potenciální problémy s výkonem na základě faktorů, jako je chybná konfigurace, známé problémy nebo problémy, které jsou hlášeny jinými uživateli. Možná se vám ještě nevyskytlo některé z uvedených problémů. Můžete mít například soubory protokolu SQL a soubory databáze na stejném datovém disku. Tato podmínka má vysokou potenciál pro kritické body a další problémy s výkonem, pokud je využití databáze vysoké, a pokud je využití nízké, může se stát, že nebudete mít problém.

![Snímek obrazovky s přehledem sestavy diagnostiky výkonu](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Kontrola přehledů a doporučení pro diagnostiku výkonu

Můžete vybrat přehled a zobrazit další podrobnosti o ovlivněných prostředcích, navrhovaných zmírněních a odkazech na odkazy.

![Snímek obrazovky s podrobnostmi o diagnostice přehledu výkonu](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Stažení a kontrola úplné sestavy diagnostiky výkonu

Pomocí tlačítka **Stáhnout sestavu** můžete stáhnout sestavu HTML, která obsahuje další informace s bohatou diagnostikou, jako jsou například úložiště a konfigurace sítě, čítače výkonu, trasování, seznam procesů a protokoly. Obsah závisí na vybrané analýze. V případě pokročilého řešení potíží může sestava obsahovat další informace a interaktivní grafy, které souvisejí s vysokým využitím procesoru, vysokým využitím disku a procesy, které spotřebovávají nadměrné množství paměti. Další informace o sestavě diagnostiky výkonu najdete v tématu [Windows](how-to-use-perfinsights.md#review-the-diagnostics-report) nebo [Linux](how-to-use-perfinsights-linux.md#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Správa sestav diagnostiky výkonu

Jednu nebo více sestav diagnostiky výkonu můžete odstranit pomocí tlačítka **Odstranit sestavu** .

## <a name="how-to-uninstall-performance-diagnostics"></a>Postup odinstalace nástroje Diagnostika výkonu

Z virtuálního počítače můžete odinstalovat diagnostiku výkonu. Tato akce odebere rozšíření virtuálního počítače, ale nemá vliv na žádná diagnostická data, která jsou v účtu úložiště.

![Snímek obrazovky panelu nástrojů okna pro diagnostiku výkonu s zvýrazněným tlačítkem odinstalovat](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Kde jsou uložená diagnostická data z mého virtuálního počítače

Všechny přehledy a sestavy pro diagnostiku výkonu jsou uložené ve vlastním účtu úložiště. Přehledy jsou uložené v tabulkách Azure. Komprimovaný soubor sestav je uložený v kontejneru binárních rozsáhlých objektů (BLOB) s názvem azdiagextnresults.

Informace o účtu úložiště si můžete zobrazit pomocí tlačítka nastavení na panelu nástrojů.

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Návody sdílet tato data s zákaznickou podporou Microsoftu

Existuje několik způsobů, jak sdílet sestavy diagnostiky s Microsoftem.

**Možnost 1:** Automaticky sdílet nejnovější sestavu  
Když otevřete lístek podpory s Microsoftem, je důležité sdílet sestavu diagnostiky výkonu. Pokud jste se rozhodli tyto informace sdílet se společností Microsoft, když spouštíte diagnostiku (zaškrtnutím políčka souhlasím**Sdílet diagnostické informace s Microsoftem**), Microsoft bude moci získat přístup k sestavě z účtu úložiště pomocí odkazu SAS na výstupní soubor zip po dobu až 30 dnů od data spuštění. Pracovníkovi podpory je k dispozici pouze nejnovější sestava.

**Možnost 2:** Generování sdíleného přístupového podpisu pro komprimovaný soubor sestavy diagnostiky  
Můžete sdílet odkaz na komprimovaný soubor sestav pomocí sdílených přístupových podpisů. Postupujte takto:

1. V Azure Portal přejděte k účtu úložiště, ve kterém jsou uložená diagnostická data.
1. V části **BLOB Service** vyberte **objekty blob** .
1. Vyberte kontejner **azdiagextnresults** .
1. Vyberte komprimovaný soubor výstupu diagnostiky výkonu, který chcete sdílet.
1. Na kartě **Generovat SAS** vyberte kritéria pro sdílení.
1. Klikněte na **Generovat token SAS objektu BLOB a adresu URL**.
1. Zkopírujte **adresu URL SAS objektu BLOB**a sdílejte ji s pracovníkem podpory.

**Možnost 3:** Stažení sestavy z účtu úložiště

Komprimovaný soubor sestavy diagnostiky výkonu můžete také najít pomocí kroků 1 – 4 v možnosti 2. Tuto možnost vyberte, pokud chcete soubor stáhnout, a pak ho sdílejte e-mailem nebo požádejte pracovníky podpory o pokyny k nahrání souboru.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Návody zachytit diagnostická data ve správném čase

Každý běh pro diagnostiku výkonu má dvě fáze:

1. Nainstalujte nebo aktualizujte rozšíření virtuálního počítače pro diagnostiku výkonu.
1. Spustí diagnostiku pro zadanou dobu trvání.

V současné době neexistuje jednoduchý způsob, jak přesně zjistit, kdy je instalace rozšíření virtuálních počítačů dokončena. Instalace rozšíření virtuálního počítače obvykle trvá přibližně 45 sekund až 1 minutu. Po instalaci rozšíření virtuálního počítače můžete spustit reprodukci kroky, aby Diagnostika výkonu mohla zachytit správnou sadu dat pro řešení potíží.

## <a name="next-steps"></a>Další kroky

Pokud stále nemůžete zjistit příčinu problému a potřebujete další pomoc, můžete po kontrole přehledů a sestav diagnostiky výkonu otevřít lístek podpory s zákaznickou podporou Microsoftu.

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).
