---
title: "Řešení problémů migrovat Azure | Microsoft Docs"
description: "Poskytuje přehled o známé problémy ve službě Azure migrovat a Poradce při potížích pro běžné chyby."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: troubleshooting
ms.date: 02/21/2018
ms.author: raynew
ms.openlocfilehash: e1e7a1a57f780ef477379dfb1ceaead0c8654970
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="troubleshoot-azure-migrate"></a>Řešení problémů s Azure Migrate

## <a name="troubleshoot-common-errors"></a>Odstraňování běžných chyb

[Azure migrací](migrate-overview.md) vyhodnocuje místní úlohy pro migraci do Azure. V tomto článku použijte k řešení potíží při nasazení a používání Azure migrovat.


**Kolekce není možné se připojit k Internetu**

To může dojít, pokud je počítač, který používáte za proxy server. Zkontrolujte, zda že je zadat přihlašovací údaje pro autorizaci, pokud potřebovat proxy server.
Pokud používáte žádné brány firewall založená na adresu URL proxy serveru k řízení odchozí připojení, nezapomeňte seznamu povolených IP adres, že následující požadované adresy URL:

**Adresa URL** | **Účel**  
--- | ---
*.portal.azure.com | Vyžaduje se zkontrolovat připojení ke službě Azure a ověřit synchronizaci času problémy.
*.oneget.org | Vyžaduje ke stažení powershell na základě vCenter PowerCLI modulu.

**Kolekce se nemůže připojit k projektu pomocí ID projektu a klíče zkopírovaná z portálu.**

Ujistěte se, že jste se zkopírovat a vložit správné informace. Chcete-li vyřešit, nainstalovat Microsoft Monitoring Agent (MMA) a ověřte, zda může MMA připojit k projektu:

1. V kolekci virtuálních počítačů, stáhněte si [MMA](https://go.microsoft.com/fwlink/?LinkId=828603).
2. Chcete-li spustit instalaci, poklikejte na stažený soubor.
3. V instalačním programu na **úvodní** klikněte na tlačítko **Další**. Na **licenční podmínky** klikněte na tlačítko **souhlasím** tak, aby přijímal licence.
4. V **cílovou složku**, zachovat, nebo upravit výchozí instalační složku > **Další**.
5. V **možnosti instalace agenta**, vyberte **Azure Log Analytics (OMS)** > **Další**.
6. Klikněte na tlačítko **přidat** přidat nový pracovní prostor analýzy protokolů. Vložte projektu ID a klíč, který jste zkopírovali. Pak klikněte na tlačítko **Další**.
7. Ověřte, zda agent může připojit k projektu. Pokud ne, ověřte nastavení. Pokud se mohou připojit agenta však nemůže kolekce, kontaktujte podporu.


**Chyba 802: zobrazí chyba synchronizace datum a čas.**

Hodiny serveru může být se na synchronizaci s aktuálním časem o víc než pět minut. Změňte čas hodiny v kolekci virtuálních počítačů tak, aby odpovídaly aktuálního času, následujícím způsobem:

1. Otevřete příkazový řádek správce ve virtuálním počítači.
2. Chcete-li zkontrolovat časové pásmo, spusťte w32tm /tz.
3. Chcete-li synchronizovat čas, spusťte w32tm/resync.

**Moje projektu klíč má "==" symboly ke konci. Tyto jsou zakódovány na jiné alfanumerických znaků pomocí kolekce. Je to očekávané?**

Ano, každý projekt klíč končí "==". Kolekce zašifruje klíč projektu před zpracováním ho.

**Zobrazuje data výkonu u disků a sítě adaptéry jako nuly**

Tato situace může nastat, pokud úroveň statistiky nastavení na serveru vCenter je nastavena na méně než tři. Na úrovni tři nebo vyšší ukládá vCenter historie výkonu virtuálního počítače pro výpočty, úložiště a sítě. Pro menší než úrovně tři není vCenter uložit úložiště a sítě dat, ale pouze data procesoru a paměti. V tomto scénáři výkonu zobrazí data jako nula v Azure migrovat a migrovat Azure poskytuje velikost doporučení pro disky a na základě metadat shromážděná z počítačů místní sítě.

Chcete-li povolit shromažďování dat výkonu diskových a síťových, změňte úroveň nastavení statistiky na tři. Potom počkejte aspoň jeden den ke zjištění prostředí a vyhodnocení ho. 

**I nainstalovali agenty a vizualizaci závislostí použitý k vytvoření skupin. Nyní post převzetí služeb při selhání, na počítače zobrazit akci "Instalace agenta" místo "Zobrazení závislosti"**
* POST plánované nebo neplánované převzetí služeb při selhání, na místní počítače jsou vypnuté a ekvivalentní počítače jsou spuštěné v Azure. Tyto počítače získat jinou adresu MAC. Se může získat jinou IP adresu založené na tom, jestli se uživatel rozhodl zachovat místní IP adresu, nebo ne. Pokud MAC a IP adresy liší, Azure migrovat žádná data závislostí mapy služeb nepřidruží místní počítače a požádá uživatele k instalaci agentů místo zobrazení závislostí.
* Odeslat testovací převzetí služeb při selhání, místní počítače zůstaly zapnuté podle očekávání. Ekvivalentní počítače spuštěné v Azure získat jinou adresu MAC a mohou získat jinou IP adresu. Pokud uživatel blokuje odchozí provoz OMS z těchto počítačů, Azure migrovat žádná data závislostí mapy služeb nepřidruží místní počítače a požádá uživatele k instalaci agentů místo zobrazení závislostí.


## <a name="troubleshoot-readiness-issues"></a>Řešení potíží s problémy připravenosti

**Problém** | **Oprava**
--- | ---
Nepodporovaný typ spuštění | Azure nepodporuje virtuální počítače s typem spouštěcí EFI. Doporučujeme převést typ spuštění na systému BIOS, před spuštěním migrace. <br/><br/>Můžete použít [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) -li provést migraci takových virtuálních počítačů, protože typ spuštění virtuálního počítače budou převedeny na systému BIOS během migrace.
Počet disků překračuje limit. | Odeberte nepoužité disky z počítače před migrací.
Velikost disku překračuje limit. | Azure podporuje disky s až velikost 4 TB. Zmenšit disky, na méně než 4 TB před migrací. 
V zadaném umístění není k dispozici žádný disk. | Zkontrolujte, zda že je disk v cílovém umístění, před migrací.
Pro zadanou redundanci není k dispozici žádný disk. | Disk musí používat typ redundance úložiště definované v nastavení assessment (LRS ve výchozím nastavení).
Z důvodu interní chyby není možné určit vhodnost disku. | Zkuste vytvořit nové vyhodnocení pro skupinu. 
Virtuální počítač s požadovaný počtem jader a požadovanou pamětí se nenašel. | Azure nelze přesně vhodný typ virtuálního počítače. Před migrací snížit paměť a počet jader na místním počítači. 
Jeden nebo více disků není vhodný. | Zkontrolujte zda místní disky se 4 TB nebo v části před spuštěním migrace.
Jeden nebo více není vhodný síťových adaptérů. | Odeberte nepoužívané síťové adaptéry z počítače před migrací.
Nelze stanovit vhodnosti virtuálních počítačů z důvodu vnitřní chyby. | Zkuste vytvořit nové vyhodnocení pro skupinu. 
Nelze stanovit vhodnosti pro jeden nebo více disků kvůli vnitřní chybě. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Nelze stanovit vhodnosti pro jeden nebo více síťových adaptérů kvůli vnitřní chybě. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Virtuální počítač nebyl nalezen pro výkon vyžaduje úložiště. | Výkon úložiště (IOPS/propustnost) vyžaduje pro počítač překračuje podporu virtuálního počítače Azure. Snižte požadavky na úložiště pro počítač před migrací.
Virtuální počítač nebyl nalezen pro požadované síťový výkon. | Výkon sítě (vstup/výstup) vyžaduje pro počítač překračuje podporu virtuálního počítače Azure. Snižte požadavky na sítě pro počítač. 
Virtuální počítač nebyl nalezen v zadané cenovou úroveň. | Cenová úroveň je nastavené na Standard, vezměte v úvahu downsizing virtuální počítač před migrací do Azure. Pokud úroveň velikost je Basic, vezměte v úvahu při změně cenové úrovně hodnocení na Standard. 
Virtuální počítač nebyl nalezen v zadaném umístění. | Použijte jiné cílové umístění, před migrací.
Neznámý operační systém | Operační systém virtuálního počítače byl zadán jako "Jiné" v systému vCenter Server, kvůli které nelze migrovat Azure identifikovat Azure připravenost virtuálního počítače. Zajistěte, aby operačního systému běžících v rámci počítač [podporované](https://aka.ms/azureoslist) Azure před migrací na počítač.
Podmíněná podporovaný operační systém Windows | Uplynutí jeho koncové datum podpory operačního systému a musí vlastní podporu smlouvy (CSA) pro [podporují v Azure](https://aka.ms/WSosstatement), zvažte upgradování operačního systému před migrací do Azure.
Nepodporované Windows operačního systému | Azure podporuje pouze [vybrané verze operačního systému Windows](https://aka.ms/WSosstatement), zvažte upgradování operačního systému na počítač před migrací do Azure. 
Podmíněná schválené pro operační systém Linux. | Azure nezodpovídá pouze [vybrané verze operačního systému Linux](../virtual-machines/linux/endorsed-distros.md), zvažte upgradování operačního systému na počítač před migrací do Azure.
Unendorsed Linux operačního systému | Tento počítač může spustit v Azure, ale žádná podpora operačního systému je k dispozici v Azure, zvažte upgradování operačního systému a [schválené pro Linux verze](../virtual-machines/linux/endorsed-distros.md) před migrací do Azure
Nepodporovaná bitová verze operačního systému | Virtuální počítače s operačním systémem 32-bit může spustit v Azure, ale doporučuje se upgrade operačního systému virtuálního počítače z 32bitové na 64-bit před migrací do Azure.
Vyžaduje předplatné sady Visual Studio. | Má na počítače klienta Windows operačního systému spuštěné je uvnitř, která je podporována pouze v sadě Visual Studio předplatného.


## <a name="collect-logs"></a>Shromažďování protokolů

**Jak můžu shromáždit protokoly v kolekci virtuálních počítačů?**

Ve výchozím nastavení je povoleno protokolování. Protokoly jsou umístěny následovně:

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Chcete-li shromažďovat trasování událostí pro Windows, postupujte takto:

1. V kolekci virtuálních počítačů Otevřete příkazové okno prostředí PowerShell.
2. Spustit **Get EventLog - název protokolu aplikace | export-csv eventlog.csv**.

**Jak můžu shromáždit protokoly portálu síťových přenosů?**

1. Otevřete prohlížeč a přejděte a přihlaste se [na portál](https://portal.azure.com).
2. Stisknutím klávesy F12 spuštění nástrojů pro vývojáře. V případě potřeby vymazat nastavení **zrušte zaškrtnutí položky na navigační**.
3. Klikněte **sítě** a spusťte zachytávání síťového provozu:
 - V prohlížeči Chrome, vyberte **zachovat protokolu**. Záznamu začněte automaticky. Červené kolečko označuje, že provoz se zachycení. Pokud se nezobrazí, klikněte na černé kruh spuštění
 - Záznam Edge nebo IE měli začít automaticky. Pokud tomu tak není, klikněte na tlačítko Přehrát zelená.
4. Pokuste se tomu chybu reprodukovat.
5. Poté, co jste došlo k chybě při nahrávání, nahrávání zastavit a uložit kopii zaznamenaná aktivity:
 - V prohlížeči Chrome, klikněte pravým tlačítkem myši a klikněte na tlačítko **uložit jako HAR s obsahem**. Tato zpráva prolétne a exportuje protokoly jako soubor .har.
 - V hraniční nebo IE, klikněte na tlačítko **Export zachycení provozu** ikonu. Tato zpráva prolétne a exportuje v protokolu.
6. Přejděte na **konzoly** kartě zkontrolujte všechna upozornění a chyby. Uložit protokol konzoly:
 - V prohlížeči Chrome klikněte pravým tlačítkem na libovolné místo v protokolu konzoly. Vyberte **uložit jako**, abyste mohli exportovat a zip v protokolu.
 - V hraniční nebo IE, klikněte pravým tlačítkem na tyto chyby a vyberte **zkopírujte všechny**. 
7. Zavřete nástroje pro vývojáře.
 

## <a name="vcenter-errors"></a>vCenter chyby

### <a name="error-unhandledexception-internal-error-occured-systemiofilenotfoundexception"></a>Došlo k chybě UnhandledException vnitřní chyba: System.IO.FileNotFoundException

Jedná se o problém jsme si ukazovali tady kolekce verze nižší než 1.0.9.5. Pokud jste na verzi kolekce 1.0.9.2 nebo verze pre-GA jako 1.0.8.59, budete mít potíže. Postupujte podle [propojit danou zde ve fórech pro podrobné odpovědí](https://social.msdn.microsoft.com/Forums/azure/en-US/c1f59456-7ba1-45e7-9d96-bae18112fb52/azure-migrate-connect-to-vcenter-server-error?forum=AzureMigrate).

[Upgradu vaší kolekce vyřešit problém,](https://aka.ms/migrate/col/checkforupdates).

### <a name="error-unabletoconnecttoserver"></a>Error UnableToConnectToServer

Nelze se připojit k systému vCenter Server "Servername.com:9443" kvůli chybě: koncový bod, naslouchá na https://Servername.com:9443 nebo sdk, který může přijmout zprávu.

To se stane, když kolekce počítače se nepodařilo vyřešit zadaný název serveru vCenter nebo speficified portu je nesprávný. Ve výchozím nastavení Pokud není port určen, kolekce se pokusí připojit k číslo portu 443.

1. Zkuste příkaz ping Servername.com z počítače kolekce.
2. Pokud krok 1 nezdaří, zkuste se připojit k serveru vCenter přes IP adresu.
3. Určete číslo správný port pro připojení k systému vCenter.
4. Nakonec zkontrolujte, jestli je vCenter server spuštěná.
 

