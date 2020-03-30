---
title: Diagnostika výkonu pro virtuální počítače Azure| Dokumenty společnosti Microsoft
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
ms.openlocfilehash: 16be3d1695608165405a3490b686a01ba6a2a62c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70080605"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Diagnostika výkonu pro virtuální počítače Azure

Nástroj pro diagnostiku výkonu vám pomůže vyřešit problémy s výkonem, které mohou ovlivnit virtuální počítač se systémem Windows nebo Linux . Podporované scénáře řešení potíží zahrnují rychlé kontroly známých problémů a osvědčených postupů a složité problémy, které zahrnují pomalý výkon virtuálního počítače nebo vysoké využití procesoru, místa na disku nebo paměti.

Diagnostiku výkonu můžete spustit přímo z portálu Azure, kde můžete také zkontrolovat přehledy a sestavu různých protokolů, bohatou konfiguraci a diagnostická data. Doporučujeme spustit diagnostiku výkonu a zkontrolovat přehledy a diagnostická data, než se obrátíte na podporu společnosti Microsoft.

> [!NOTE]
> Pro Windows je diagnostika výkonu aktuálně podporována na virtuálních počítačích, které mají nainstalovanou .NET SDK verze 4.5 nebo novější verzi. Postup spuštění diagnostiky výkonu na klasických virtuálních počítačích najdete v tématu [rozšíření virtuálního počítače diagnostiky výkonu Azure](performance-diagnostics-vm-extension.md).

## <a name="supported-operating-systems"></a>Podporované operační systémy

### <a name="windows"></a>Windows

Windows 10, Windows 8, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1, Windows Server 2016, Windows Server 2012, Windows Server 2012 Datacenter, Windows Server 2012 R2, Windows Server 2012 R2 Datacenter, Windows Server 2012 R2 Standard, Windows Server 2012 Standard, Windows Server 2008 R2, Windows Server 2008 R2 Datacenter, Windows Server 2008 R2 Enterprise, Windows Server 2008 R2 Foundation, Windows Server 2008 R2 SP1, Windows Server 2008 R2 Standard.

### <a name="linux"></a>Linux

Oracle Linux Server 6.10 [`*`], 7.3, 7.6, 7.5 (Oracle-Database-Ee 13.8`*`marketplace image), CentOS 6.5 [ ], 7.6, RHEL 7.2, 7.5, 8.0 [`*`], Ubuntu 14.04, 16.04, 18.04, Debian 8, 9, 10 [`*`], SLES 12 SP4 [`*`]

>[!Note]
>[`*`] Viz [známé problémy](how-to-use-perfinsights-linux.md#known-issues)

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Instalace a spuštění diagnostiky výkonu na virtuálním počítači

Diagnostika výkonu nainstaluje rozšíření virtuálního adaptéru, který spouští diagnostický nástroj s názvem PerfInsights. PerfInsights je k dispozici jak pro [Windows,](https://aka.ms/perfinsights) tak pro [Linux](https://aka.ms/perfinsightslinux). Chcete-li nainstalovat a spustit diagnostiku výkonu, postupujte takto:

1. V levém sloupci příkazů vyberte **Virtuální počítače**.
1. Ze seznamu názvů virtuálních počítačů vyberte virtuální počítač, na kterém chcete spustit diagnostiku.
1. V pravém sloupci příkazů vyberte **Diagnostika výkonu**.

    ![Snímek obrazovky portálu Azure se zvýrazněným tlačítkem Diagnostika výkonu instalace](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > Na tomto snímku obrazovky je skrytá čepel názvů virtuálních počítačů.
1. Výběr účtu úložiště (volitelné)

    Pokud chcete k uložení výsledků diagnostiky výkonu pro více virtuálních počítačů použít jeden účet úložiště, můžete vybrat účet úložiště kliknutím na tlačítko **Nastavení** na panelu nástrojů. Po výběru účtu úložiště klikněte na tlačítko **OK.**

    Pokud nezadáte účet úložiště, bude ve výchozím nastavení vytvořen nový účet úložiště.

    ![Snímek obrazovky s panelem Diagnostika výkonu se zvýrazněným tlačítkem panelu nástrojů Nastavení](media/performance-diagnostics/settings-button.png)

    ![Snímek obrazovky s výběrem účtu úložiště z okna Nastavení diagnostiky výkonu](media/performance-diagnostics/select-storage-account.png)

1. Vyberte tlačítko **Instalovat diagnostiku výkonu.**
1. Pokud chcete po dokončení instalace spustit diagnostiku, zaškrtněte políčko **Spustit diagnostiku.** Pokud provedete tento výběr, budete moci zvolit scénář analýzy výkonu a související možnosti.

    ![Snímek obrazovky s tlačítkem Instalace diagnostiky výkonu](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Vyberte scénář analýzy, který chcete spustit.

Následující scénáře analýzy jsou k dispozici na webu Azure Portal. Vyberte analýzu v závislosti na problému s výkonem, který máte. Podle potřeby pro analýzu vyberte možnosti trvání a trasování.

* **Rychlá analýza výkonu**  
    Kontroluje známé problémy, analyzuje osvědčené postupy a shromažďuje diagnostická data. Spuštění této analýzy trvá několik minut. Další informace o [Windows](https://aka.ms/perfinsights/quick) nebo [Linuxu](https://aka.ms/perfinsightslinux/quick)

* **Analýza výkonu**  
    Zahrnuje všechny kontroly v rychlé analýze výkonu a monitoruje vysokou spotřebu prostředků. Tato verze slouží k řešení obecných problémů s výkonem, jako je vysoké využití procesoru, paměti a disku. Tato analýza trvá 30 sekund až 15 minut, v závislosti na zvolené době trvání. Další informace o [Windows](https://aka.ms/perfinsights/vmslow) nebo [Linuxu](https://aka.ms/perfinsightslinux/vmslow)

* **Pokročilá analýza výkonu**`*`  
    Zahrnuje všechny kontroly v analýze výkonu a shromažďuje jeden nebo více trasování, jak je uvedeno v následujících částech. Tento scénář slouží k řešení složitých problémů, které vyžadují další trasování. Spuštění tohoto scénáře pro delší období zvýší celkovou velikost výstupu diagnostiky, v závislosti na velikosti virtuálního počítače a možnosti trasování, které jsou vybrány. Spuštění této analýzy trvá 30 sekund až 15 minut v závislosti na zvolené době trvání. [Další informace](https://aka.ms/perfinsights/advanced)

* **Analýza souborů Azure**`*`  
    Zahrnuje všechny kontroly v analýze výkonu a zachycuje trasování sítě a čítače SMB. Tento scénář slouží k řešení potíží s výkonem souborů Azure. Spuštění této analýzy trvá 30 sekund až 15 minut v závislosti na zvolené době trvání. [Další informace](https://aka.ms/perfinsights/azurefiles)

>[!Note]
>[`*`] Tyto scénáře analýzy jsou podporovány pouze v systému Windows.

![Snímek obrazovky podokna Spustit diagnostiku uvnitř okna Diagnostika výkonu](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Poskytnout příznaky (volitelné)

Vyberte všechny předem vybrané příznaky ze seznamu nebo přidejte nové příznaky. To nám pomáhá zlepšit analýzu v budoucnu.

### <a name="provide-support-request-number-if-available-optional"></a>Poskytnout číslo žádosti o podporu, pokud je k dispozici (volitelné)

Pokud pracujete s pracovníkem podpory společnosti Microsoft na existující lístek podpory, zadejte číslo lístku podpory.

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Zkontrolujte zásady ochrany osobních údajů a právní podmínky a zaškrtněte políčko, které chcete potvrdit (povinné)

Chcete-li spustit diagnostiku, musíte souhlasit s právními podmínkami a přijmout zásady ochrany osobních údajů.

### <a name="select-ok-to-run-the-diagnostics"></a>Chcete-li spustit diagnostiku, vyberte možnost OK.

Oznámení se zobrazí jako diagnostika výkonu začne instalovat. Po dokončení instalace se zobrazí oznámení, které označuje, že instalace byla úspěšná. Vybraná analýza je pak spuštěna po zadanou dobu trvání. To by bylo vhodné reprodukovat problém s výkonem tak, aby diagnostická data mohou být zachyceny ve správný čas.

Po dokončení analýzy se do tabulek Azure nahrají následující položky a binární kontejner s velkým objektem (BLOB) v zadaném účtu úložiště:

* Všechny poznatky a související informace o běhu
* Výstupní komprimovaný soubor (.zip) soubor (s názvem **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip)** v systému Windows a soubor tar ball (s názvem **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.tar.gz** ) na Linuxu, který obsahuje soubory protokolu
* Sestava HTML

Po nahrání je na webu Azure Portal uvedená nová sestava diagnostiky.

![Snímek obrazovky se seznamem sestav diagnostiky v okně Diagnostika výkonu](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Jak změnit nastavení diagnostiky výkonu

Pomocí tlačítka panelu nástrojů **Nastavení** můžete změnit účet úložiště, ve kterém lze uložit přehledy diagnostiky a výstup. Můžete použít stejný účet úložiště pro více virtuálních počítačů, které používají diagnostiku výkonu. Když změníte účet úložiště, staré sestavy a přehledy se neodstraní. V seznamu diagnostických sestav však již nebudou zobrazeny.

## <a name="review-insights-and-performance-diagnostics-report"></a>Kontrola přehledů a sestavy diagnostiky výkonu

Každé diagnostické spuštění obsahuje seznam přehledů a doporučení, ovlivněné prostředky, soubory protokolu a další bohaté diagnostické informace, které jsou shromažďovány, plus sestavu pro prohlížení offline. Úplný seznam všech shromážděných diagnostických dat naleznete v [Windows](how-to-use-perfinsights.md#what-kind-of-information-is-collected-by-perfinsights) [Linux](how-to-use-perfinsights-linux.md#what-kind-of-information-is-collected-by-perfinsights) **tématu Jaký druh informací shromažďuje perfInsights?**

### <a name="select-a-performance-diagnostics-report"></a>Výběr sestavy diagnostiky výkonu

Seznam sestav diagnostiky můžete použít k vyhledání všech diagnostických sestav, které byly spuštěny. Seznam obsahuje podrobnosti o použité analýze, nalezených přehledech a jejich úrovních dopadu. Výběrem řádku zobrazíte další podrobnosti.

![Snímek obrazovky s výběrem diagnostické sestavy z okna Diagnostika výkonu](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Kontrola sestavy diagnostiky výkonu

Každá sestava diagnostiky výkonu může obsahovat několik přehledů a označuje úroveň dopadu vysoké, střední nebo nízké. Každý pohled také obsahuje doporučení, která pomáhají zmírnit obavy. Přehledy jsou seskupeny pro snadné filtrování.

Úrovně dopadu představují potenciál pro problémy s výkonem na základě faktorů, jako je chybná konfigurace, známé problémy nebo problémy, které jsou hlášeny jinými uživateli. Je možné, že ještě nedochází k jednomu nebo více uvedeným problémům. Můžete mít například soubory protokolu SQL a databázové soubory na stejném datovém disku. Tato podmínka má vysoký potenciál pro kritické body a další problémy s výkonem, pokud je vysoké využití databáze, zatímco nemusí všimnout problém, pokud je nízké využití.

![Snímek obrazovky s oknopřehledem sestavy diagnostiky výkonu](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Kontrola přehledů a doporučení diagnostiky výkonu

Můžete vybrat přehled pro zobrazení dalších podrobností o ohrožených prostředcích, navrhovaných opatřeních ke zmírnění rizik a odkazových odkazech.

![Snímek obrazovky s podrobnostmi přehledu diagnostiky výkonu](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Stažení a kontrola úplné sestavy diagnostiky výkonu

Pomocí tlačítka **Stáhnout sestavu** můžete stáhnout sestavu HTML, která obsahuje další bohaté diagnostické informace, jako je konfigurace úložiště a sítě, čítače výkonu, trasování, seznam procesů a protokoly. Obsah závisí na vybrané analýze. Pro pokročilé řešení potíží může sestava obsahovat další informace a interaktivní grafy, které souvisejí s vysokým využitím procesoru, vysokým využitím disku a procesy, které spotřebovávají nadměrnou paměť. Další informace o sestavě diagnostiky výkonu naleznete v [tématu Windows](how-to-use-perfinsights.md#review-the-diagnostics-report) nebo [Linux](how-to-use-perfinsights-linux.md#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Správa sestav diagnostiky výkonu

Pomocí tlačítka **Odstranit sestavu** můžete odstranit jednu nebo více sestav diagnostiky výkonu.

## <a name="how-to-uninstall-performance-diagnostics"></a>Jak odinstalovat diagnostiku výkonu

Diagnostiku výkonu můžete odinstalovat z virtuálního virtuálního aplikace. Tato akce odebere rozšíření virtuálního zařízení, ale nemá vliv na žádná diagnostická data, která je v účtu úložiště.

![Snímek obrazovky panelu nástrojů nástroje Diagnostika výkonu se zvýrazněným tlačítkem Odinstalovat](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Kde jsou uložená diagnostická data z mého virtuálního virtuálního zařízení

Všechny přehledy diagnostiky výkonu a sestavy jsou uloženy ve vašem vlastním účtu úložiště. Přehledy se ukládají v tabulkách Azure. Komprimovaný soubor sestavy je uložen v binárním kontejneru s velkým objektem (BLOB), který je pojmenován azdiagextnresults.

Informace o účtu úložiště můžete zobrazit pomocí tlačítka Nastavení na panelu nástrojů.

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Jak lze sdílet tato data se zákaznickou podporou společnosti Microsoft

Existuje několik způsobů, jak sdílet sestavu diagnostiky se společností Microsoft.

**Možnost 1:** Automatické sdílení nejnovější sestavy  
Při otevření lístku podpory se společností Microsoft je důležité sdílet sestavu diagnostiky výkonu. Pokud jste se při spuštění diagnostiky rozhodli tyto informace sdílet se společností Microsoft (zaškrtnutím políčka Souhlasím**se sdílením diagnostických informací se společností Microsoft,** bude mít společnost Microsoft přístup ke zprávě z vašeho účtu úložiště pomocí odkazu SAS do výstupního souboru zip po dobu až 30 dnů od data spuštění. Pouze nejnovější sestava je k dispozici pracovníkovi podpory.

**Možnost č. 2:** Generovat sdílený přístupový podpis pro komprimovaný soubor sestavy diagnostiky  
Pomocí sdílených přístupových podpisů můžete sdílet odkaz na komprimovaný soubor sestav. Postupujte přitom takto:

1. Na webu Azure Portal přejděte na účet úložiště, ve kterém jsou uložená diagnostická data.
1. V části Služba **objektů Blob** vyberte **objekty BLOB.**
1. Vyberte kontejner **azdiagextnresults.**
1. Vyberte výstupní komprimovaný soubor diagnostiky výkonu, který chcete sdílet.
1. Na kartě **Generovat SAS** vyberte kritéria pro sdílení.
1. Klikněte na **Generovat token a adresu URL objektu blob SAS**.
1. Zkopírujte **adresu URL objektu Blob SAS**a sdílejte ji s pracovníkem podpory.

**Možnost 3:** Stažení sestavy z účtu úložiště

Můžete také vyhledat komprimovaný soubor sestavy diagnostiky výkonu pomocí kroků 1–4 v možnosti 2. Výběrem vyberte, chcete-li soubor stáhnout a pak jej sdílet prostřednictvím e-mailu nebo požádejte pracovníka podpory o pokyny k nahrání souboru.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Jak lze zachytit diagnostická data ve správný čas

Každý spuštění diagnostiky výkonu má dvě fáze:

1. Nainstalujte nebo aktualizujte rozšíření virtuálního aplikace diagnostiky výkonu.
1. Spusťte diagnostiku po zadanou dobu.

V současné době neexistuje žádný snadný způsob, jak přesně vědět, kdy je instalace rozšíření virtuálního zařízení dokončena. Obecně trvá asi 45 sekund až 1 minutu instalace rozšíření virtuálního aplikace. Po instalaci rozšíření virtuálního počítače můžete spustit kroky repro, aby diagnostika výkonu zachytila správnou sadu dat pro řešení potíží.

## <a name="next-steps"></a>Další kroky

Po kontrole přehledy diagnostiky výkonu a sestavy, pokud stále nelze určit příčinu problému a potřebujete další pomoc, můžete otevřít lístek podpory s zákaznickou podporou společnosti Microsoft.

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte Získat **podporu**. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
