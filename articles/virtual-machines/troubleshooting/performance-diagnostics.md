---
title: Diagnostika výkonu pro virtuální počítače Azure | Dokumentace Microsoftu
description: Představuje Diagnostika výkonu Azure pro Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: 67778a269aa962b3184bf55c692154570e8357ba
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49387380"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Diagnostika výkonu pro virtuální počítače Azure

Diagnostika výkonu pomáhá řešit problémy s výkonem, které můžou ovlivnit Windows virtuální počítač (VM). Mezi podporované scénáře řešení potíží patří rychlé kontroly známé problémy a osvědčené postupy a složité problémy, které se týkají výkonem pomalých virtuálních počítačů nebo značným využitím procesoru, místo na disku nebo paměti. 

Diagnostika výkonu můžete spustit přímo z portálu Azure portal, kde můžete také zkontrolovat přehledy a vytváření sestav o různé protokoly, bohaté možnosti konfigurace a diagnostická data. Doporučujeme spustit diagnostiku výkonu a zkontrolujte přehledy a diagnostických dat před Kontaktujte Microsoft Support.

> [!NOTE]
> Diagnostika výkonu je momentálně podporovaný na virtuálních počítačích s Windows, která máte nainstalovaná verze sady SDK rozhraní .NET 4.5 nebo novější. Postup spustit diagnostiku výkonu na klasické virtuální počítače najdete v tématu [rozšíření virtuálního počítače Diagnostika výkonu Azure](performance-diagnostics-vm-extension.md).

### <a name="supported-operating-systems"></a>Podporované operační systémy
Windows 10, Windows 8, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1, Windows Server 2016, Windows Server 2012, Windows Server 2012 Datacenter, Windows Server 2012 R2, Windows Server 2012 R2 Datacenter, Windows Server 2012 R2 Standard, Windows Server 2012 Standard, Windows Server 2008 R2, Windows Server 2008 R2 Datacenter, Windows Server 2008 R2 Enterprise, Windows Server 2008 R2 Foundation, Windows Server 2008 R2 SP1, Windows Server 2008 R2 Standard.

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Nainstalujte a spusťte na svém virtuálním počítači Diagnostika výkonu
Diagnostika výkonu nainstaluje rozšíření virtuálního počítače, na kterém běží nástroj diagnostiku, s názvem [PerfInsights](https://aka.ms/perfinsights). Jak nainstalovat a spustit diagnostiku výkonu, postupujte podle těchto kroků:
1.  V levém sloupci příkazů vyberte **virtuálních počítačů**.
1.  Ze seznamu názvy virtuálních počítačů vyberte virtuální počítač, který chcete spustit diagnostiku.
1.  V pravém sloupci příkazy, vyberte **Diagnostika výkonu**.

    ![Snímek obrazovky Azure Portalu, se zvýrazněným tlačítkem Diagnostika výkonu instalace](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > Na tomto snímku obrazovky je skrytá okna názvy virtuálních počítačů.
1. Vyberte účet úložiště (volitelné)

    Pokud chcete použít jeden účet úložiště pro ukládání výsledků diagnostiky výkonu pro několik virtuálních počítačů, můžete vybrat účet úložiště kliknutím **nastavení** tlačítko na panelu nástrojů. Klikněte na tlačítko **OK** tlačítko po výběru účtu úložiště.

    Pokud nezadáte účet úložiště, vytvoří se nový účet úložiště ve výchozím nastavení.

    ![Okně diagnostiky výkonu snímek obrazovky se zvýrazněným tlačítkem nastavení panelu nástrojů](media/performance-diagnostics/settings-button.png)

    ![Snímek obrazovky výběru účtu úložiště v okně nastavení diagnostiky výkonu](media/performance-diagnostics/select-storage-account.png)

1. Vyberte **nainstalovat Diagnostika výkonu** tlačítko.
1. Vyberte **spustit diagnostiku** zaškrtávací políčko, pokud chcete spustit diagnostiku po dokončení instalace. Pokud provedete výběr, budete moct zvolit scénáře pro analýzu výkonu a související možnosti jsou.

    ![Tlačítko Instalovat Diagnostika výkonu snímek obrazovky](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Výběr scénáře pro analýzu ke spuštění

Následující scénáře analýzy jsou k dispozici na webu Azure Portal. Vyberte analýzy, v závislosti na tyto problémy s výkonem, které máte. Vyberte dobu trvání a trasování možnosti podle potřeby analýzy.

* **Rychlý výkon analýzy**  
    Kontroluje známé problémy, analyzuje osvědčené postupy a shromažďuje diagnostická data. Této analýzy trvá několik minut. [Další informace](https://aka.ms/perfinsights/quick)

* **Analýza výkonu**  
    Zahrnuje všechny kontroly v analýze rychlý výkon a monitoruje využití prostředků. Pomocí této verze potíží obecné informace o výkonu, jako je například vysoké využití procesoru, paměti a využití disku. Tato analýza trvá 30 sekund až 15 minut v závislosti na vybrané období. [Další informace](https://aka.ms/perfinsights/vmslow) 
    
* **Analýza výkonu Upřesnit**  
    Obsahuje všechny kontroly v analýzu výkonu a shromažďuje některé z trasování, jak je uvedeno v následující části. Tento scénář se používá k řešení složitých problémů, které vyžadují další trasování. Spuštění v tomto scénáři delší dobu zvýší celkovou velikost diagnostický výstup, v závislosti na velikosti virtuálního počítače a možnosti trasování, které jsou vybrány. Tato analýza trvá 30 sekund až 15 minut ke spuštění, v závislosti na vybrané období. [Další informace](https://aka.ms/perfinsights/advanced) 
    
* **Azure analysis soubory**  
    Obsahuje všechny kontroly v analýzu výkonu a zachytí trasování v síti a čítače SMB. Tento scénář se používá k řešení potíží s výkonem Azure files. Tato analýza trvá 30 sekund až 15 minut ke spuštění, v závislosti na vybrané období. [Další informace](https://aka.ms/perfinsights/azurefiles)


![Snímek obrazovky spustit podokno Diagnostika v okně diagnostiky výkonu](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Poskytují příznaky (volitelné)
Vyberte jakékoli Zkontrolujte předem vybrané příznaky ze seznamu nebo přidejte nové příznaky. Pomůžete nám vylepšit analýzy v budoucnu. 

### <a name="provide-support-request-number-if-available-optional"></a>Zadejte číslo žádosti o podporu, pokud je k dispozici (volitelné)
Pokud pracujete s pracovníkem podpory Microsoftu na existující lístek podpory, zadejte číslo lístku podpory. 

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Zkontrolujte zásady ochrany osobních údajů a právní podmínky a zaškrtněte políčko a potvrďte (povinné)
Pokud chcete spustit diagnostiku, musí souhlas se smluvními podmínkami a přijměte zásady ochrany osobních údajů.

### <a name="select-ok-to-run-the-diagnostics"></a>Kliknutím na tlačítko OK spusťte diagnostiku 
Diagnostika výkonu při spuštění instalace se zobrazí oznámení. Po dokončení instalace se zobrazí oznámení, která označuje, že je instalace úspěšná. Následně se spustí vybranou analýzu během určené doby. To může být vhodný čas pro reprodukci problému výkonu tak, aby diagnostická data se dají zachytit ve správný čas. 

Po dokončení analýzy tyto položky jsou odeslány do tabulky Azure a kontejner binárních rozsáhlých objektů (BLOB) zadaný účet úložiště:

*   Přehledy a související informace o daném spuštění
*   Výstupu komprimovaný soubor (ZIP) (s názvem **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip**), která obsahuje soubory protokolu
*   Zprávu ve formátu HTML

Po dokončení nahrávání je uvedena nová sestava Diagnostika na webu Azure Portal.

![Snímek obrazovky seznam sestav diagnostiky v okně diagnostiky výkonu](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Jak změnit nastavení diagnostiky výkonu
Použití **nastavení** tlačítka panelu nástrojů, chcete-li změnit účet úložiště, kde mohou být uloženy diagnostiky přehledů a výstup. Můžete použít stejný účet úložiště pro víc virtuálních počítačů, které používají Diagnostika výkonu. Pokud změníte účet úložiště, se neodstraní původní sestav a přehledů. Ale už se zobrazí v seznamu sestav diagnostiky. 

## <a name="review-insights-and-performance-diagnostics-report"></a>Kontrola přehledů a sestava Diagnostika výkonu
Každý diagnostiky obsahuje seznam přehledů a doporučení, ovlivněné prostředky, soubory protokolu a dalších shromážděných informací o plnohodnotnou diagnostiku a sestavy pro prohlížení v režimu offline. Úplný seznam všechna shromážděná diagnostická data, najdete v části [jaké informace se shromažďují ve PerfInsights?](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-use-perfinsights#what-kind-of-information-is-collected-by-perfinsights) 

### <a name="select-a-performance-diagnostics-report"></a>Vyberte sestavu Diagnostika výkonu
Seznam sestav diagnostiky můžete použít k vyhledání všech sestav diagnostiky, které byly spuštěny. Seznam obsahuje podrobnosti o analýzy, která byla použita, přehledy, které nebyly nalezeny a úroveň dopadu. Vyberte nějaký řádek zobrazíte další podrobnosti.

![Snímek obrazovky s výběrem sestavy diagnostiky v okně diagnostické nástroje výkonu](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Zkontrolujte sestavu Diagnostika výkonu
Každá sestava Diagnostika výkonu může obsahovat několik přehledů a označení úroveň dopadu vysoká, střední nebo Nízká. Každý insight také obsahuje doporučení, která pomůžou snížit obavy. Přehledy jsou seskupeny pro snadné filtrování. 

Úrovně dopadu představují potenciální problémy s výkonem, na základě faktorů, jako jsou chybné konfigurace, známé problémy, nebo problémů, které jsou hlášeny jinými uživateli. Můžete nemusí se jednat ještě nejméně jeden z uvedených problémy. Například může mít databázové soubory na stejném disku, data a soubory protokolu SQL. Tato podmínka má vysokou potenciální problémová místa a dalších problémů s výkonem, pokud využití databáze je vysoká, že jste si možná problém při nízké využití.

![Diagnostika výkonu snímek obrazovky sestavy s přehledem](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Kontrola výkonu diagnostiky postřehů a doporučení
Můžete vybrat určitý Přehled zobrazíte další podrobnosti o ovlivněné prostředky, doporučené způsoby zmírnění rizik a referenční odkazy. 

![Snímek obrazovky podrobností insight Diagnostika výkonu](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Stáhnout a revidovat diagnostickou zprávu plný výkon
Můžete použít **stáhnout sestavu** tlačítko a stáhněte si zprávu ve formátu HTML, který obsahuje informace o dalších bohatou diagnostikou, jako je například úložiště a konfiguraci sítě, čítače výkonu, trasování, seznam procesů a protokoly. Obsah závisí na vybrané analýzy. Zpráva o případě pokročilého odstraňování problémů, může obsahovat další informace a interaktivních grafů, které se vztahují k vysoké využití procesoru, vysoké míře využití disku a procesy, které spotřebovávají využívala příliš mnoho paměti. Další informace o sestavě Diagnostika výkonu najdete v tématu [zkontrolovat sestavu diagnostiky](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-use-perfinsights#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Správa sestav Diagnostika výkonu
Můžete odstranit jednu nebo více sestav Diagnostika výkonu pomocí **odstranit sestavu** tlačítko.

## <a name="how-to-uninstall-performance-diagnostics"></a>Postup odinstalace Diagnostika výkonu
Diagnostika výkonu můžete odinstalovat z virtuálního počítače. Tato akce odebere rozšíření virtuálního počítače, ale nemá vliv na diagnostická data, která je v účtu úložiště. 

![Snímek obrazovky z panelu nástrojů okno diagnostiky výkonu se zvýrazněným tlačítkem odinstalace](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Pokud se diagnostická data z virtuálního počítače uložené? 
Všechny informace o diagnostiku výkonu a sestavy jsou uložené v účtu úložiště. Insights jsou uložená do tabulky Azure. Komprimovaný soubor sestavy je uložená v kontejneru binárních rozsáhlých objektů (BLOB), který je pojmenován azdiagextnresults.

Pomocí tlačítka nastavení na panelu nástrojů můžete zobrazit informace o účtu úložiště. 

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Jak sdílet data s Zákaznická podpora Microsoftu? 
Diagnostika sestavu sdílet s Microsoftem několika způsoby.

**Možnost 1:** automaticky sdílet na nejnovější sestavu  
Když otevřete lístek podpory s Microsoftem, je potřeba sdílet sestavu Diagnostika výkonu. Pokud jste se rozhodli sdílet tyto informace s Microsoftem při spuštění diagnostiky (tak, že vyberete "**vyjadřuji souhlas s sdílet diagnostické informace s Microsoftem**" zaškrtávací políčko), Microsoft bude mít přístup k sestavě z úložiště účet pomocí odkaz SAS pro výstupní soubor zip po dobu až 30 dnů ode dne spuštění. Jenom nejnovější sestava je k dispozici pro pracovníka podpory. 

**Možnost 2:** vygenerovat sdílený přístupový podpis pro komprimovaný soubor sestavy diagnostiky  
Může sdílet odkaz na komprimovaný soubor sestavy s použitím sdílených přístupových podpisů. Postupujte přitom takto: 
1.  Na webu Azure Portal přejděte do účtu úložiště, ve kterém je uložený diagnostická data.
1.  Vyberte **objekty BLOB** pod **službu Blob service** oddílu. 
1.  Vyberte **azdiagextnresults** kontejneru.
1.  Vyberte možnost výkonu diagnostiky výstup komprimovaný soubor, který chcete sdílet.
1.  Na **generovat SAS** kartu, vyberte kritéria pro sdílení. 
1.  Klikněte na tlačítko **vygenerujte token SAS objektů blob a adresa URL**.
1.  Kopírovat **SAS URL objektu Blob**a sdílet je s pracovníkem technické podpory. 

**Možnost 3:** stáhnout sestavu z účtu úložiště

Diagnostika komprimovaného souboru obsahujícímu sestavu výkonu můžete také vyhledat pomocí kroků 1 – 4 v možnosti 2. Možnost vyberte ke stažení souboru a pak ji sdílet pomocí e-mailu nebo položit pracovníkem technické podpory pokyny pro nahrání souboru.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Jak zachytit diagnostická data ve správný čas?
Každý Diagnostika výkonu spuštění má dvě fáze: 
1.  Instalace nebo aktualizace Diagnostika výkonu rozšíření virtuálního počítače.
1.  Diagnostika během určené doby.

Současné době neexistuje jednoduchý způsob, jak přesně zjistit, kdy nainstalovat rozšíření virtuálního počítače je dokončena. Obvykle trvá přibližně 45 sekund až 1 minuty se nainstalovat rozšíření virtuálního počítače. Po dokončení instalace rozšíření virtuálního počítače můžete spustit kroky pro reprodukci mít Diagnostika výkonu zachycení správné sady dat pro řešení potíží. 

## <a name="next-steps"></a>Další postup
Poté, co si diagnostické informace o výkonu a sestavy, pokud stále nelze zjistit příčinu problému a potřebujete další pomoc, můžete otevřít lístek podpory s zákaznickou podporu Microsoftu. 

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
