---
title: Řešení potíží s Azure Migrate | Dokumentace Microsoftu
description: Poskytuje základní informace o známých problémech ve službě Azure Migrate a Poradce při potížích pro běžné chyby.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/25/2018
ms.author: raynew
ms.openlocfilehash: ca34f27e1d22c6235ec0d6b965d49ec5266f17f6
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126357"
---
# <a name="troubleshoot-azure-migrate"></a>Řešení problémů s Azure Migrate

## <a name="troubleshoot-common-errors"></a>Odstraňování běžných chyb

[Azure Migrate](migrate-overview.md) posuzuje místní úlohy pro migraci do Azure. Pomocí tohoto článku řešení problémů při nasazování a používání Azure Migrate.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Vytvoření projektu Migrace selhala s chybou *požadavky musí obsahovat hlavičky identity uživatele*

Tento problém může být uživatelé, kteří nemají přístup k tenantovi Azure Active Directory (Azure AD) organizace. Když uživatel přidá do tenanta služby Azure AD poprvé, učitelského obdrží e-mailová pozvánka k připojení klienta. Uživatelé musí přejít do e-mailu a přijmout pozvánku k získání úspěšně přidal do tenanta. Pokud jste e-mailu se nezobrazuje, kontaktujte uživatele, který už má přístup k tenantovi a požádejte ho, aby znovu odeslat pozvánku pro vás postupem zadaný [tady](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Po přijetí e-mailové pozvánce, budete muset otevřít e-mailu a klikněte na odkaz v e-mailu k přijetí pozvánky. Až to uděláte, budete muset odhlásit z webu Azure portal a přihlásit znovu, aktualizaci prohlížeče nebude fungovat. Potom můžete zkusit vytvořit projekt migrace.

### <a name="performance-data-for-disks-and-networks-adapters-shows-as-zeros"></a>Ukazuje údaje o výkonu pro adaptéry disků a sítí, jako nuly

Tato situace může nastat, pokud je na méně než tři nastavte úroveň nastavení statistiky na vCenter serveru. Na úrovni 3 nebo vyšší ukládá vCenter historie výkonu virtuálních počítačů pro výpočetní prostředky, úložiště a sítě. Pro nejméně úrovni tři vCenter nejsou uloženy v úložiště a data sítě, ale jenom data využití procesoru a paměti. V tomto scénáři výkonu zobrazí data jako nula ve službě Azure Migrate a Azure Migrate nabízí doporučení velikosti disků a sítí na základě metadat shromážděných z místních počítačů.

Pokud chcete povolit shromažďování dat o výkonu disku a sítě, změňte úroveň nastavení statistiky na tři. Potom počkejte aspoň jeden den do prostředí pro zkoumání a posouzení ho.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Můžu nainstalovat agenty a použít vizualizaci závislostí k vytvoření skupiny. Teď publikovat převzetí služeb při selhání, počítače zobrazit akci "Instalace agenta" místo "Zobrazit závislosti"
* Příspěvek plánované nebo neplánované převzetí služeb při selhání, místní počítače jsou vypnuté a ekvivalentní počítače jsou spuštěné v Azure. Tyto počítače získat jinou adresu MAC. Získají může jinou IP adresu na základě na, jestli se uživatel rozhodl zachovat místní IP adresu, nebo ne. Pokud se systémem MAC a IP adresy liší, Azure Migrate nepřiřadí místních počítačů s daty závislostí Service Map a vyzve uživatele k instalaci agentů místo zobrazení závislostí.
* Odeslat testovací převzetí služeb při selhání na místních počítačích zůstaly zapnuté podle očekávání. Ekvivalentní počítače spuštěné v Azure získat jinou adresu MAC a mohou získat jinou IP adresu. Pokud uživatel blokuje odchozí provoz Log Analytics z těchto počítačů, Azure Migrate nepřiřadí místních počítačů s daty závislostí Service Map a vyzve uživatele k instalaci agentů místo zobrazení závislostí.

## <a name="collector-errors"></a>Chyby kolektoru

### <a name="deployment-of-collector-ova-failed"></a>Nasazení kolektoru vajíčka se nezdařilo

To může dojít, pokud je částečně stáhnout soubor OVA nebo z důvodu prohlížeči Pokud používáte k nasazení soubor OVA webovém klientovi vSphere. Ujistěte se, že po dokončení stahování a vyzkoušejte si nasazení vajíčka se jiný prohlížeč.

### <a name="collector-is-not-able-to-connect-to-the-internet"></a>Kolekce není možné se připojit k Internetu

To může nastat, pokud je počítač, který používáte za proxy serverem. Ujistěte se, že zadáte přihlašovací údaje pro ověření, pokud proxy server potřebuje jeden.
Pokud používáte jakýkoli proxy server brány firewall na základě adresy URL k řízení odchozího připojení, nezapomeňte do seznamu povolených IP adres, že tyto požadované adresy URL:

**Adresa URL** | **Účel**  
--- | ---
*.portal.azure.com | Vyžaduje se ověřilo připojení ke službě Azure a ověřit synchronizaci času problémy.
*.oneget.org | Vyžaduje se stáhnout powershell na základě vCenter PowerCLI modulu.

**Kolektor se nemůže připojit k Internetu kvůli selhání ověření certifikátu**

To může nastat, pokud používáte prověřuje zachycovací proxy server pro připojení k Internetu, a pokud jste neimportovali certifikát proxy serveru na virtuálním počítači kolektoru. Můžete importovat certifikát proxy serveru pomocí podrobně popsané kroky [tady](https://docs.microsoft.com/azure/migrate/concepts-collector#internet-connectivity).

**Kolektor se nemůže připojit k projektu pomocí ID projektu a klíče, které můžu zkopírovali z portálu.**

Ujistěte se, že jste zkopírovat a vložit správné informace. Řešení potíží s, nainstalujte Microsoft Monitoring Agent (MMA) a ověřit, zda může agenta MMA připojit k projektu:

1. Na virtuálním počítači kolektoru, stáhněte si [MMA](https://go.microsoft.com/fwlink/?LinkId=828603).
2. Chcete-li spustit instalaci, poklikejte na stažený soubor.
3. V instalačním programu na **úvodní** klikněte na **Další**. Na stránce **Licenční podmínky** kliknutím na **Souhlasím** přijměte licenci.
4. V **cílovou složku**, udržovat nebo změnit výchozí instalační složku > **Další**.
5. V **možnosti instalace agenta**vyberte **Azure Log Analytics** > **Další**.
6. Klikněte na tlačítko **přidat** přidáte nový pracovní prostor Log Analytics. Vložte ID a klíč, který jste zkopírovali projektu. Pak klikněte na tlačítko **Další**.
7. Ověřte, zda agent může připojit k projektu. Pokud ne, ověřte nastavení. Pokud může agent připojit, ale nemůže kolektoru, obraťte se na podporu.


### <a name="error-802-date-and-time-synchronization-error"></a>Chyba 802: Datum a čas Chyba synchronizace

Hodiny serveru může být mimo synchronizace s aktuálním časem o víc než pět minut. Změníte čas v kolekci virtuálních počítačů tak, aby odpovídaly aktuálního času, následujícím způsobem:

1. Otevřete příkazový řádek správce ve virtuálním počítači.
2. Ke kontrole časové pásmo, spusťte w32tm /tz.
3. Chcete-li synchronizovat čas, spusťte w32tm/resync.

### <a name="vmware-powercli-installation-failed"></a>Instalace VMware PowerCLI se nezdařila.

Azure Migrate collector PowerCLI stáhne a nainstaluje na zařízení. Chyba při instalaci PowerCLI může být způsobeno nedostupný koncových bodů úložiště, které PowerCLI. Řešení potíží, zkuste použít ruční instalaci PowerCLI v následujícím způsobem virtuálního počítače kolektoru:

1. Otevřete prostředí Windows PowerShell v režimu správce
2. Přejděte do adresáře C:\ProgramFiles\ProfilerService\VMWare\Scripts\
3. Spusťte skript InstallPowerCLI.ps1

### <a name="error-unhandledexception-internal-error-occured-systemiofilenotfoundexception"></a>Chyba interní chybě UnhandledException: System.IO.FileNotFoundException

K těmto potížím dochází u Collectoru verze nižší než 1.0.9.5. S těmito potížemi se setkáte, pokud používáte Collector verze 1.0.9.2 nebo verze před GA, jako je 1.0.8.59. [Podrobnou odpověď najdete pomocí uvedeného odkazu na fóra](https://social.msdn.microsoft.com/Forums/azure/en-US/c1f59456-7ba1-45e7-9d96-bae18112fb52/azure-migrate-connect-to-vcenter-server-error?forum=AzureMigrate).

[Pokud chcete tyto potíže vyřešit, upgradujte Collector](https://aka.ms/migrate/col/checkforupdates).

### <a name="error-unabletoconnecttoserver"></a>Chyba UnableToConnectToServer

Nelze se připojit k vCenter serveru "Servername.com:9443" kvůli chybě: existuje neposlouchal žádný koncový bod v https://Servername.com:9443/sdk , který by mohl přijmout zprávu.

Zaškrtněte, pokud budete používat nejnovější verzi zařízení kolektoru a pokud ne, upgradovat zařízení, abyste [nejnovější verzi](https://docs.microsoft.com/azure/migrate/concepts-collector#how-to-upgrade-collector).

Pokud problém pořád probíhá na nejnovější verzi, je možné, protože počítače kolektor nedokáže vyřešit zadaný název serveru vCenter nebo zadaný port je chybný. Ve výchozím nastavení Pokud není port určen, kolekce se pokusí připojit k portu číslo 443.

1. Zkuste příkaz ping Servername.com z počítače kolektor.
2. Pokud krok 1 selže, zkuste se k serveru vCenter připojit přes IP adresu.
3. Zjistěte správné číslo portu pro připojení k serveru vCenter.
4. Nakonec zkontrolujte, jestli server vCenter je spuštěný.

## <a name="troubleshoot-readiness-issues"></a>Řešení potíží s problémy připravenosti

**Problém** | **Oprava**
--- | ---
Nepodporovaný typ spuštění | Azure nepodporuje virtuální počítače s typem spuštění EFI. Doporučujeme převést typ spuštění BIOS, před zahájením migrace. <br/><br/>Můžete použít [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) provedete migraci těchto virtuálních počítačů jako typ spuštění virtuálního počítače budou převedeny do systému BIOS během migrace.
Podmíněně podporovaný operační systém Windows | Prošel konec nepodporuje operační systém a je třeba vlastní podpora smlouvy (CSA) pro [podporují v Azure](https://aka.ms/WSosstatement), zvažte možnost upgradovat operační systém před migrací do Azure.
Nepodporovaný operační systém Windows | Azure podporuje pouze [vybrané verze operačního systému Windows](https://aka.ms/WSosstatement), zvažte možnost upgradovat operační systém počítače před migrací do Azure.
Podmíněně schválený operační systém Linux | Azure schvaluje pouze [vybrané verze operačního systému Linux](../virtual-machines/linux/endorsed-distros.md), zvažte možnost upgradovat operační systém počítače před migrací do Azure.
Neschválený operační systém Linux | Počítač může spustit v Azure, ale Azure nenabízí žádnou podporu operačního systému, zvažte možnost upgradovat operační systém do [schválené pro Linux verze](../virtual-machines/linux/endorsed-distros.md) před migrací do Azure
Neznámý operační systém | Operační systém virtuálního počítače byla zadána jako "Jiné" v systému vCenter Server, které Azure Migrate nemůže určit připravenost pro Azure virtuálního počítače. Ujistěte se, že operační systém, počítače [podporované](https://aka.ms/azureoslist) Azure před migrací počítače.
Nepodporovaná bitová verze operačního systému | Virtuální počítače s operačním systémem 32-bit může spustit v Azure, ale doporučuje se upgradovat operační systém virtuálního počítače z 32bitové na 64-bit před migrací do Azure.
Vyžaduje předplatné sady Visual Studio. | Počítače se Windows klientský operační systém běžící uvnitř ho, který se podporuje jenom v předplatných: Visual Studio.
Virtuální počítač pro požadovaný výkon úložiště se nenašel. | Výkon úložiště (IOPS a propustnosti), vyžaduje se pro tento počítač překračuje podporu virtuálních počítačů Azure. Snížení požadavků na úložiště pro počítač před migrací.
Virtuální počítač nebyl nalezen pro požadovaný výkon sítě. | Výkon sítě (vstup/výstup), vyžaduje se pro tento počítač překračuje podporu virtuálních počítačů Azure. Snížit požadavky na síť pro počítač.
Virtuální počítač nebyl nalezen v zadanou cenovou úroveň. | Cenová úroveň se nastaví na standardní, vezměte v úvahu downsizing virtuální počítač před migrací do Azure. Pokud velikosti úrovně Basic, zvažte možnost změnit cenovou úroveň posouzení na úroveň Standard.
Virtuální počítač nebyl nalezen v zadaném umístění. | Použijte jiné cílové umístění, před migrací.
Minimálně jeden nevhodný disk. | Jeden nebo více disků připojených k virtuálnímu počítači nesplňuje požadavky na Azure. U každého disku připojeného k virtuálnímu počítači Ujistěte se, že velikost disku < 4 TB, v opačném případě zmenšit velikost disku před migrací do Azure. Ujistěte se, že výkon (IOPS a propustnosti) vyžaduje každého disku se nepodporuje v Azure [spravované disky virtuálních počítačů](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits).   
Minimálně jeden nevhodný síťový adaptér. | Odeberte nepoužívané síťové adaptéry z počítače před migrací.
Počet disků překračuje limit. | Odeberte nepoužívané disky z počítače před migrací.
Velikost disku překračuje limit. | Azure podporuje disky s až do velikosti 4 TB. Disky zmenšit na méně než 4 TB před migrací.
V zadaném umístění není k dispozici žádný disk. | Ujistěte se, že je disk v cílovém umístění, před migrací.
Pro zadanou redundanci není k dispozici žádný disk. | Disk by měl použít typ redundance úložiště definované v nastavení posouzení (LRS ve výchozím nastavení).
Z důvodu interní chyby není možné určit vhodnost disku. | Zkuste vytvořit nové posouzení pro skupinu.
Virtuální počítač s požadovaný počtem jader a požadovanou pamětí se nenašel. | Azure nešlo jemné vhodný typ virtuálního počítače. Před migrací snížit velikost paměti a počet jader v místním počítači.
Není možné určit vhodnost virtuálního počítače z důvodu vnitřní chyby. | Zkuste vytvořit nové posouzení pro skupinu.
Možné určit vhodnost u minimálně jeden disk z důvodu vnitřní chyby. | Zkuste vytvořit nové posouzení pro skupinu.
Není možné určit vhodnost pro jeden nebo více síťových adaptérů z důvodu vnitřní chyby. | Zkuste vytvořit nové posouzení pro skupinu.


## <a name="collect-logs"></a>Shromažďování protokolů

**Jak shromažďování protokolů na virtuální počítač kolektoru?**

Ve výchozím nastavení je povoleno protokolování. Protokoly jsou umístěny následovně:

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Shromažďovat události trasování pro Windows, postupujte takto:

1. Na virtuálním počítači kolektoru Otevřete příkazové okno Powershellu.
2. Spustit **Get EventLog - název protokolu aplikací | export-csv eventlog.csv**.

**Jak se shromažďovat protokoly přenosů portálu sítě?**

1. Otevřete prohlížeč a přejděte a přihlaste se [k portálu](https://portal.azure.com).
2. Stisknutím klávesy F12 ke spuštění nástroje pro vývojáře. V případě potřeby zrušte nastavení **Vymazat položky při navigaci**.
3. Klikněte na tlačítko **sítě** kartu a spustit zachytávání síťového provozu:
 - V prohlížeči Chrome, vyberte **zachovat protokolu**. Tento záznam má spustit automaticky. Červené kolečko označuje, že provoz probíhá zachytávání. Pokud se nezobrazí, klikněte na kruh černé spuštění
 - Záznam Edge/IE, má spustit automaticky. Pokud tomu tak není, klikněte na zelené tlačítko Přehrát.
4. Zkuste chybu reprodukovat.
5. Poté, co jste došlo k chybě při nahrávání, zastavit záznam a uložit kopii zaznamenaná aktivita:
 - V prohlížeči Chrome, klikněte pravým tlačítkem myši a klikněte na tlačítko **uložit jako HAR s obsahem**. To zips a exportuje protokoly jako soubor .har.
 - V aplikaci Edge/IE, klikněte na tlačítko **Export zachycené provoz** ikonu. To zips a exportuje do protokolu.
6. Přejděte **konzoly** kartu zkontrolujte všechna upozornění a chyby. Uložení protokolu konzoly:
 - V prohlížeči Chrome klikněte pravým tlačítkem na libovolné místo v protokolu konzoly. Vyberte **uložit jako**, abyste mohli exportovat a zip do protokolu.
 - V aplikaci Edge/IE, klikněte pravým tlačítkem na chyby a vyberte **Kopírovat vše**.
7. Zavřít Developer Tools.

## <a name="collector-error-codes-and-recommended-actions"></a>Kódy chyb kolekcí a doporučené akce

|           |                                |                                                                               |                                                                                                       |                                                                                                                                            |
|-----------|--------------------------------|-------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Kód chyby | Název chyby                      | Zpráva                                                                       | Možné příčiny                                                                                        | Doporučená akce                                                                                                                          |
| 601       | CollectorExpired               | Platnost kolektoru vypršela.                                                        | Platnost kolektoru vypršela.                                                                                    | Stáhněte si prosím novou verzi kolektoru a zkuste to znovu.                                                                                      |
| 751       | UnableToConnectToServer        | Nepovedlo se připojit k vCenter Serveru %Name;, protože došlo k chybě: %ErrorMessage;     | Další podrobnosti najdete v chybové zprávě.                                                             | Vyřešte potíže a zkuste to znovu.                                                                                                           |
| 752       | InvalidvCenterEndpoint         | Server %Name; není vCenter Server.                                  | Zadejte podrobnosti o vCenter Serveru.                                                                       | Opakujte operaci se správnými podrobnostmi vCenter Serveru.                                                                                   |
| 753       | InvalidLoginCredentials        | Nejde se připojit k vCenter Serveru %Name;, protože došlo k chybě: %ErrorMessage; | Připojení k vCenter Serveru nebylo úspěšné z důvodu neplatných přihlašovacích údajů.                             | Zkontrolujte, jestli jsou zadané přihlašovací údaje správné.                                                                                    |
| 754       | NoPerfDataAvaialable           | Nejsou k dispozici žádné údaje o výkonu.                                               | Zkontrolujte úroveň statistiky na vCenter serveru. By měla být nastavená na 3 údaje o výkonu k dispozici. | Změňte úroveň statistiky na 3 (pro intervaly o délkách 5 minut, 30 minut a 2 hodiny), počkejte aspoň jeden den a zkuste to znovu.                   |
| 756       | NullInstanceUUID               | Vyskytl se počítač s InstanceUUID o hodnotě null.                                  | Na vCenter Serveru je asi nesprávný objekt.                                                      | Vyřešte potíže a zkuste to znovu.                                                                                                           |
| 757       | VMNotFound                     | Virtuální počítač se nenašel.                                                  | Virtuální počítač je možná odstraněný: %VMID;                                                                | Zajistěte, aby virtuální počítače vybrané při určování oboru inventáře vCenter existovaly během zjišťování.                                      |
| 758       | GetPerfDataTimeout             | Vypršel časový limit požadavku VCenter. Zpráva % Message;                                  | Přihlašovací údaje k vCenter Serveru nejsou správné.                                                              | Zkontrolujte přihlašovací údaje k vCenter serveru a ujistěte se, že jestli je vCenter Server je dostupný. Zkuste operaci zopakovat. Pokud se problém nevyřeší, obraťte se na podporu. |
| 759       | VmwareDllNotFound              | Nenašla se knihovna DLL VMWare.Vim.                                                     | PowerCLI není správně nainstalovaný.                                                                   | Zkontrolujte prosím, jestli je správně nainstalovaný PowerCLI. Zkuste operaci zopakovat. Pokud se problém nevyřeší, obraťte se na podporu.                               |
| 800       | ServiceError                   | Služba Azure Migrate Collector není spuštěná.                               | Služba Azure Migrate Collector není spuštěná.                                                       | Spusťte službu pomocí modulu services.msc a zkuste operaci zopakovat.                                                                             |
| 801       | PowerCLIError                  | Nepovedlo se nainstalovat VMware PowerCLI.                                          | Nepovedlo se nainstalovat VMware PowerCLI.                                                                  | Zkuste operaci zopakovat. Pokud se problém nevyřeší, nainstalujte ho ručně a zkuste operaci zopakovat.                                                   |
| 802       | TimeSyncError                  | Čas není synchronizovaný s internetovým časovým serverem.                            | Čas není synchronizovaný s internetovým časovým serverem.                                                    | Ujistěte se, jestli je čas tohoto počítače přesně nastavený pro časové pásmo počítače, a zkuste operaci zopakovat.                                 |
| 702       | OMSInvalidProjectKey           | Zadán neplatný klíč projektu                                                | Zadán neplatný klíč projektu                                                                        | Zopakujte operaci se správným klíčem projektu.                                                                                              |
| 703       | OMSHttpRequestException        | Při odesílání požadavku došlo k chybě. Zpráva % Message;                                | Zkontrolujte ID a klíč projektu a ujistěte se, že je koncový bod dostupný.                                       | Zkuste operaci zopakovat. Pokud potíže potrvají, kontaktujte prosím podporu Microsoftu.                                                                     |
| 704       | OMSHttpRequestTimeoutException | Vypršel časový limit požadavku HTTP. Zpráva % Message;                                     | Zkontrolujte ID a klíč projektu a ujistěte se, že je koncový bod dostupný.                                       | Zkuste operaci zopakovat. Pokud potíže potrvají, kontaktujte prosím podporu Microsoftu.                                                                     |
