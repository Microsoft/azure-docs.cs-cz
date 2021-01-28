---
title: Řešení potíží s místním prostředím Integration runtime v Azure Data Factory
description: Přečtěte si, jak řešit problémy s místním hostováním prostředí Integration runtime v Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.author: lle
ms.openlocfilehash: 8a722550d12d019e25ff39de27cc0df2c2762a01
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942045"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Řešení potíží s místním hostováním Integration runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje běžné metody řešení potíží pro místní prostředí Integration runtime (IR) v Azure Data Factory.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Shromážděte místně hostované protokoly IR z Azure Data Factory

V případě neúspěšných aktivit, které jsou spuštěny v místním prostředí IR nebo ve sdíleném INFRAČERVENém prostředí, Azure Data Factory podporuje zobrazování a nahrávání protokolů chyb. Pokud chcete získat ID zprávy o chybách, postupujte podle pokynů uvedených tady a zadejte ID sestavy, ve kterém chcete vyhledat související známé problémy.

1. V Data Factory vyberte **spuštění kanálu**.

1. V části **spuštění aktivit** ve sloupci **Chyba** vyberte zvýrazněné tlačítko, aby se zobrazily protokoly aktivit, jak je znázorněno na následujícím snímku obrazovky:

    ![Snímek obrazovky s oddílem "spuštění aktivit" v podokně "všechna spuštění kanálu".](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

    Protokoly aktivit se zobrazí pro spuštění neúspěšné aktivity.

    ![Snímek obrazovky protokolů aktivit pro neúspěšnou aktivitu.](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png) 
    
1. Chcete-li získat další pomoc, vyberte možnost **Odeslat protokoly**.
 
   Otevře se okno **protokoly prostředí Integration runtime (IR) s oknem Microsoft** .

    ![Snímek obrazovky s oknem "sdílení protokolů místního prostředí Integration runtime (IR) s Microsoftem"](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Vyberte protokoly, které chcete odeslat. 
    * V případě prostředí *IR* v místním prostředí můžete nahrávat protokoly, které souvisejí s chybnou aktivitou nebo všemi protokoly v uzlu IR v místním prostředí. 
    * U *SDÍLENÉHO IR* můžete nahrávat pouze protokoly, které se vztahují k neúspěšné aktivitě.

1. Po nahrání protokolů Udržujte záznam ID sestavy pro pozdější použití, pokud potřebujete další pomoc k vyřešení tohoto problému.

    ![Snímek obrazovky zobrazovaného ID sestavy v okně průběhu nahrávání pro protokoly IR](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Žádosti o zobrazení a nahrání protokolu se spouštějí ve všech online autohostovaných instancích IR. Pokud chybí nějaké protokoly, ujistěte se, že jsou všechny místně hostované instance IR online. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Obecné selhání nebo chyba místního prostředí IR

### <a name="out-of-memory-issue"></a>Nedostatek paměti – problém

#### <a name="symptoms"></a>Příznaky

K chybě OutOfMemoryException (OOM) dojde při pokusu o spuštění aktivity vyhledávání s propojeným IR nebo místním prostředím IR.

#### <a name="cause"></a>Příčina

Nová aktivita může vyvolat chybu OOM v případě, že počítač IR vyvolala vysoké využití paměti. Problém může být způsoben velkým objemem souběžných aktivit a chyba je záměrné.

#### <a name="resolution"></a>Řešení

Ověřte využití prostředků a souběžné spouštění aktivit v uzlu IR. Upravte interní a aktivační čas spuštění aktivit, aby nedocházelo k příliš velkému provádění na jednom uzlu IR současně.

### <a name="concurrent-jobs-limit-issue"></a>Problém s limitem souběžných úloh

#### <a name="symptoms"></a>Příznaky

Při pokusu o zvýšení limitu souběžných úloh z Azure Data Factory rozhraní přestane proces *aktualizovat* stav.

Příklad scénáře: hodnota maximální počet souběžných úloh je aktuálně nastavená na 24 a chcete zvýšit počet tak, aby vaše úlohy mohly běžet rychleji. Minimální hodnota, kterou můžete zadat, je 3 a maximální hodnota je 32. Zvyšte hodnotu z 24 na 32 a potom vyberte tlačítko **aktualizovat** . Proces se zablokuje při *aktualizaci* stavu, jak je znázorněno na následujícím snímku obrazovky. Aktualizujete stránku a tato hodnota se pořád zobrazuje jako 24. Neaktualizovalo se na 32, jak jste očekávali.

![Snímek obrazovky s podoknem uzlů prostředí Integration runtime a zobrazení procesu se zablokuje ve stavu "aktualizace".](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Příčina

Limit počtu souběžných úloh závisí na logickém jádru a paměti počítače. Zkuste hodnotu upravit směrem dolů k hodnotě, například 24, a pak výsledek zobrazení výsledku.

> [!TIP] 
> - Pokud se chcete dozvědět víc o počtu logických jader a určit počet logických hodnot v počítači, podívejte se na [čtyři způsoby, jak najít počet jader v procesoru ve Windows 10](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> - Pokud se chcete dozvědět, jak vypočítat matematický protokol, Projděte si [kalkulačku logaritmus](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-high-availability-ha-ssl-certificate-issue"></a>Problém certifikátu SSL pro místní hostování s vysokou dostupností (HA)

#### <a name="symptoms"></a>Příznaky

Uzel v místním prostředí IR nahlásil následující chybu:

"Nepovedlo se načíst sdílené stavy z primárního uzlu NET. TCP://abc.cloud.corp.Microsoft.com: 8060/ExternalService. svc/. ID aktivity: XXXXX. certifikát X. 509 CN = ABC. Cloud. corp. Microsoft. com, OU = test, O = sestavení řetězce Microsoft se nezdařilo. Použitý certifikát má řetězec důvěryhodnosti, který nelze ověřit. Nahraďte certifikát nebo změňte certificateValidationMode. Funkce odvolání nemohla ověřit odvolání, protože server odvolání byl offline. "

#### <a name="cause"></a>Příčina

Když pracujete s případy, které souvisejí s metodou handshake SSL/TLS, může dojít k problémům souvisejícím s ověřováním řetězu certifikátů. 

#### <a name="resolution"></a>Řešení

- Tady je rychlý a intuitivní způsob řešení potíží při sestavení řetězu certifikátů X. 509:
 
    1. Exportujte certifikát, který je potřeba ověřit. Uděláte to takto:
    
       a. V systému Windows vyberte **Start**, začněte psát **certifikáty** a pak vyberte **spravovat certifikáty počítače**.
       
       b. V Průzkumníkovi souborů v levém podokně vyhledejte certifikát, který chcete ověřit, klikněte na něj pravým tlačítkem myši a vyberte možnost **všechny úkoly**  >  **exportovat**.
    
        ![Snímek obrazovky ovládacího prvku "všechny úkoly" > "Export" pro certifikát v podokně Správa certifikátů počítačů.](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Zkopírujte exportovaný certifikát do klientského počítače. 
    3. Na straně klienta v okně příkazového řádku spusťte následující příkaz. Nezapomeňte nahradit *\<certificate path>* a *\<output txt file path>* skutečnými cestami.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Příklad:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Vyhledejte chyby ve výstupním souboru TXT. Souhrn chyb najdete na konci souboru TXT.

        Příklad: 

        ![Snímek obrazovky s shrnutím chyb na konci souboru TXT](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Pokud se na konci souboru protokolu nezobrazí chyba, jak je znázorněno na následujícím snímku obrazovky, můžete zvážit, že byl řetěz certifikátů na klientském počítači úspěšně sestaven.
        
        ![Snímek obrazovky souboru protokolu zobrazující žádné chyby.](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Pokud je v souboru certifikátu nakonfigurovaná přípona názvu souboru AIA (autorita Information Access), CDP (distribuční bod CRL) nebo protokolu OCSP (Online Certificate Status Protocol), můžete ji vyzkoušet intuitivnější způsobem:
 
    1. Tyto informace získáte zkontrolováním podrobností o certifikátu, jak je znázorněno na následujícím snímku obrazovky:
    
        ![Snímek obrazovky s podrobnostmi o certifikátu](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    
    1. Spusťte následující příkaz. Nezapomeňte nahradit *\<certificate path>* skutečnou cestou certifikátu.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    
        Otevře se nástroj pro načtení adresy URL. 
        
    1. Pokud chcete ověřit certifikáty s příponami AIA, CDP a OCSP, vyberte **načíst**.

        ![Snímek obrazovky s nástrojem pro načítání adresy URL a tlačítkem načíst](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        Řetěz certifikátů jste úspěšně vytvořili, pokud je *ověřený* stav certifikátu z AIA a je *ověřen* stav certifikátu z CDP nebo OCSP.

        Pokud při pokusu o načtení AIA nebo CDP dojde k chybě, můžete pracovat s týmem sítě a získat tak klientský počítač připravený k připojení k cílové adrese URL. V případě, že je možné ověřit cestu HTTP nebo protokol LDAP (Lightweight Directory Access Protocol), bude stačit.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>Místnímu prostředí IR se nepodařilo načíst soubor nebo sestavení

#### <a name="symptoms"></a>Příznaky

Zobrazí se tato chybová zpráva:

"Nelze načíst soubor nebo sestavení ' XXXXXXXXXXXXXXXX, verze = 4.0.2.0, Culture = neutral, PublicKeyToken = XXXXXXXXX ' nebo jeden z jeho závislostí. Systém nemůže najít zadaný soubor. ID aktivity: 92693b45-b4bf-4FC8-89da-2d3dc56f27c3 "
 
Tady je konkrétnější chybová zpráva: 

"Nelze načíst soubor nebo sestavení ' System. ValueTuple, Version = 4.0.2.0, Culture = neutral, PublicKeyToken = XXXXXXXXX ' nebo jednu z jeho závislostí. Systém nemůže najít zadaný soubor. ID aktivity: 92693b45-b4bf-4FC8-89da-2d3dc56f27c3 "

#### <a name="cause"></a>Příčina

V monitorování procesu můžete zobrazit následující výsledek:

[![Snímek obrazovky se seznamem cest v monitoru procesu.](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> V monitorování procesu můžete nastavit filtry, jak je znázorněno na následujícím snímku obrazovky.
>
> Předchozí chybová zpráva říká, že soubor System. ValueTuple knihovny DLL není umístěný ve složce v relaci *globální mezipaměť sestavení* (GAC) ve složce *c:\Program Files\microsoft Integration Runtime\4.0\Gateway* nebo ve složce *c:\Program Files\Microsoft Integration Runtime\4.0\Shared* .
>
> V podstatě proces načte knihovnu DLL nejprve ze složky *GAC* , potom ze *sdílené* složky a nakonec ze složky *Gateway* . Proto můžete načíst knihovnu DLL z libovolné cesty, která je užitečná.

<br>

![Snímek obrazovky se stránkou filtr sledování procesu, který obsahuje seznam filtrů pro knihovnu DLL.](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Řešení

Soubor *System.ValueTuple.dll* najdete ve složce *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* . Chcete-li tento problém vyřešit, zkopírujte soubor *System.ValueTuple.dll* do složky *C:\Program Files\Microsoft integr Runtime\4.0\Gateway* .

Můžete použít stejnou metodu pro řešení dalších chybějících problémů se soubory nebo sestavením.

#### <a name="more-information-about-this-issue"></a>Další informace o tomto problému

Důvod, proč vidíte *System.ValueTuple.dll* pod *%windir%\Microsoft.NET\assembly* a *%windir%\assembly* je to, že se jedná o chování rozhraní .NET. 

V následující chybě můžete jasně zjistit, že chybí sestavení *System. ValueTuple* . K tomuto problému dochází, když se aplikace pokusí ověřit *System.ValueTuple.dll* sestavení.
 
" \<LogProperties> \<ErrorInfo> [{" Code ": 0," Message ":" inicializátor typu pro ' Npgsql. PoolManager ' vyvolal výjimku. "," EventType ": 0," kategorie ": 5," data ": {} ," MsgId ": null," typvýjimky ":" System. TypeInitializationException "," Source ":" Npgsql "," trasování zásobníku ":" "," InnerEventInfos ": [{" Code ": 0," Message ":" nelze načíst soubor nebo sestavení ' System. ValueTuple, Version = 4.0.2.0, Culture = neutral, PublicKeyToken = XXXXXXXXX ' nebo jednu z jeho závislostí. Systém nemůže najít zadaný soubor. "," EventType ": 0," kategorie ": 5," data ": {} ," MsgId":null,"ExceptionType":"System. IO. FileNotFoundException "," Source ":" Npgsql "," trasování zásobníku ":" "," InnerEventInfos ": []}]}] \</ErrorInfo> \</LogProperties> "
 
Další informace o mezipaměti GAC naleznete v tématu [globální mezipaměť sestavení](/dotnet/framework/app-domains/gac)(GAC).


### <a name="self-hosted-integration-runtime-authentication-key-is-missing"></a>Chybí ověřovací klíč prostředí Integration runtime pro místní hostování.

#### <a name="symptoms"></a>Příznaky

Prostředí Integration runtime v místním prostředí se náhle přepne do režimu offline bez ověřovacího klíče a v protokolu událostí se zobrazí následující chybová zpráva: 

"Ověřovací klíč ještě není přiřazen"

![Snímek obrazovky s podoknem prostředí Integration runtime ukazující, že ověřovací klíč ještě není přiřazený](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Příčina

- V Azure Portal byl odstraněn místně hostovaný uzel IR nebo logické prostředí IR v místním prostředí.
- Byla provedena čistá odinstalace.

#### <a name="resolution"></a>Řešení

Pokud se nepoužijí žádné z předchozích příčin, můžete přejít do složky *%ProgramData%\Microsoft\Data Transfer\DataManagementGateway* , kde zjistíte, jestli se *konfigurační* soubor odstranil. Pokud byla odstraněna, postupujte podle pokynů v článku Netwrix, kde [zjistíte, kdo odstranil soubor ze souborových serverů Windows](https://www.netwrix.com/how_to_detect_who_deleted_file.html).

![Snímek obrazovky s podoknem podrobností protokolu událostí pro kontrolu konfiguračních souborů](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cant-use-self-hosted-ir-to-bridge-two-on-premises-datastores"></a>Nejde použít místní prostředí IR k mostu dvou místních úložišť dat.

#### <a name="symptoms"></a>Příznaky

Po vytvoření samoobslužného úřadu pro zdrojové i cílové úložiště dat chcete k dokončení aktivity kopírování připojit tyto dvě finanční autority. Pokud jsou úložiště dat nakonfigurovaná v různých virtuálních sítích nebo úložiště dat nerozumí mechanismu brány, zobrazí se některá z následujících chyb: 

* "Ovladač zdroje nebyl nalezen v cíli IR"
* "Zdroji nelze přistupovat pomocí cílového prostředí IR"
 
#### <a name="cause"></a>Příčina

Prostředí IR v místním prostředí je navrženo jako centrální uzel aktivity kopírování, nikoli klientského agenta, který je třeba nainstalovat pro každé úložiště dat.
 
V takovém případě byste měli vytvořit propojenou službu pro každé úložiště dat se stejným IR a v INFRAČERVENém prostředí by měl mít přístup k úložišti dat přes síť. Nezáleží na tom, jestli je infračervený přenos nainstalovaný ve zdrojovém úložišti dat nebo cílovém úložišti dat, nebo na třetí počítač. Pokud se dvě propojené služby vytvoří s různými finančními autoritami, ale použijí se ve stejné aktivitě kopírování, použije se cílový IR a budete muset nainstalovat ovladače pro úložiště dat v cílovém počítači IR.

#### <a name="resolution"></a>Řešení

Nainstalujte ovladače pro zdrojové i cílové úložiště dat do cílového prostředí IR a ujistěte se, že mají přístup ke zdrojovému úložišti dat.
 
Pokud přenos nemůže probíhat přes síť mezi dvěma úložišti dat (například jsou nakonfigurované ve dvou virtuálních sítích), nebudete moct dokončit kopírování v jedné aktivitě, a to i v případě, že je nainstalované INFRAČERVENé prostředí. Pokud nemůžete dokončit kopírování v rámci jedné aktivity, můžete vytvořit dvě aktivity kopírování se dvěma finančními službami, každé na jednom OTVORu: 
* Kopírování jednoho IR z úložiště dat 1 do Azure Blob Storage
* Zkopírujte další IR z Azure Blob Storage do ddatastore 2. 

Toto řešení může simulovat požadavek na použití IR k vytvoření mostu, který spojuje dvě odpojená úložiště dat.


### <a name="credential-sync-issue-causes-credential-loss-from-ha"></a>Problémy s synchronizací přihlašovacích údajů způsobují ztrátu přihlašovacích údajů z HA.

#### <a name="symptoms"></a>Příznaky

Pokud se přihlašovací údaje zdroje dat "XXXXXXXXXX" z aktuálního uzlu prostředí Integration runtime odstraní s datovou částí, zobrazí se tato chybová zpráva:

"Když odstraníte službu propojení na Azure Portal nebo úloha obsahuje nesprávnou datovou část, vytvořte novou službu propojení znovu s přihlašovacími údaji."

#### <a name="cause"></a>Příčina

Místní prostředí IR je integrováno v režimu HA se dvěma uzly, ale uzly nejsou ve stavu synchronizace přihlašovacích údajů. To znamená, že přihlašovací údaje uložené v uzlu Dispatcher se nesynchronizují s ostatními pracovními uzly. Pokud dojde k převzetí služeb při selhání z uzlu dispečera na pracovní uzel a přihlašovací údaje existují pouze v předchozím uzlu dispečera, úloha selže při pokusu o přístup k přihlašovacím údajům a zobrazí se předchozí chyba.

#### <a name="resolution"></a>Řešení

Jediným způsobem, jak se tomuto problému vyhnout, je ujistit se, že tyto dva uzly jsou ve stavu synchronizace přihlašovacích údajů. Pokud nejsou synchronizované, musíte znovu zadat přihlašovací údaje nového dispečera.


### <a name="cant-choose-the-certificate-because-the-private-key-is-missing"></a>Certifikát nejde zvolit, protože chybí privátní klíč.

#### <a name="symptoms"></a>Příznaky

* Naimportovali jste soubor PFX do úložiště certifikátů.
* Když jste certifikát vybrali prostřednictvím uživatelského rozhraní Configuration Manager pro infračervený čas, zobrazila se tato chybová zpráva:

   "Nepovedlo se změnit režim šifrování intranetové komunikace. Je možné, že certifikát ' \<*certificate name*> ' pravděpodobně nemá privátní klíč, který podporuje výměnu klíčů, nebo proces nemá přístupová práva k privátnímu klíči. Podrobnosti naleznete v informacích o vnitřní výjimce. "

    ![Snímek obrazovky s podoknem nastavení Integration Runtime Configuration Manager, ve kterém se zobrazuje chybová zpráva "chybí privátní klíč".](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Příčina

- Uživatelský účet má nízkou úroveň oprávnění a nemůže získat přístup k privátnímu klíči.
- Certifikát se vygeneroval jako signatura, ale ne jako výměna klíčů.

#### <a name="resolution"></a>Řešení

* K provozování uživatelského rozhraní použijte účet s odpovídajícími oprávněními pro přístup k privátnímu klíči.  
* Importujte certifikát spuštěním následujícího příkazu:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```

## <a name="self-hosted-ir-setup"></a>Instalace prostředí IR pro místní hostování

### <a name="integration-runtime-registration-error"></a>Chyba registrace modulu runtime integrace 

#### <a name="symptoms"></a>Příznaky

Někdy můžete chtít spustit prostředí IR v místním prostředí v jiném účtu, a to z následujících důvodů:
- Zásady společnosti nepovolují účet služby.
- Vyžaduje se nějaké ověřování.

Když změníte účet služby v podokně služby, může se stát, že modul runtime integrace přestane fungovat a zobrazí se následující chybová zpráva:

Uzel Integration Runtime (v místním prostředí) zjistil chybu během registrace. Nelze se připojit k hostitelské službě Integration Runtime (v místním prostředí). "

![Snímek obrazovky okna Integration Runtime Configuration Manager znázorňující chybu registrace IR](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Příčina

Mnoho prostředků je uděleno pouze účtu služby. Když změníte účet služby na jiný účet, zůstanou oprávnění všech závislých prostředků beze změny.

#### <a name="resolution"></a>Řešení

Vyhledejte chybu v protokolu událostí prostředí Integration runtime.

![Snímek obrazovky protokolu událostí IR, který ukazuje, že došlo k chybě za běhu.](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

* Pokud je chyba v protokolu událostí "UnauthorizedAccessException", udělejte toto:

    1. Na panelu služby systému Windows ověřte účet přihlašovací služby *DIAHostService* .

        ![Snímek obrazovky podokna vlastností účtu přihlašovací služby](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Zkontrolujte, zda má účet přihlašovací služby oprávnění ke čtení a zápisu pro složku *%ProgramData%\Microsoft\DataTransfer\DataManagementGateway* .

        - Ve výchozím nastavení se v případě, že účet pro přihlášení ke službě nezměnil, musí mít oprávnění ke čtení a zápisu.

            ![Snímek obrazovky s podoknem oprávnění služby](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

        - Pokud jste změnili přihlašovací účet služby, můžete problém zmírnit tím, že provedete následující akce:
 
            a. Proveďte čistou odinstalaci aktuálního prostředí IR pro místní hostování.   
            b. Nainstalujte prostředí IR pro místní hostování.  
            c. Změňte účet služby následujícím způsobem:  

             i. Přejděte do složky pro instalaci nástroje IR v místním prostředí a pak přepněte do složky *Microsoft Integration Runtime\4.0\Shared* .  
             ii. Otevřete okno příkazového řádku pomocí zvýšených oprávnění. Nahraďte *\<user>* a *\<password>* vlastním uživatelským jménem a heslem a pak spusťte následující příkaz:   
                `dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"`  
             iii. Pokud chcete přejít na účet LocalSystem, ujistěte se, že používáte správný formát pro tento účet: `dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""`  
                Nepoužívat *Tento* formát: `dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""`     
             iv. V případě, že má místní systém vyšší oprávnění než správce, můžete ho také přímo změnit v části "služby".  
             v. Pro přihlašovací účet služby IR můžete použít místního uživatele nebo uživatele domény.            

            d. Zaregistrujte modul runtime integrace.

* Pokud se jedná o chybu "služba Integration Runtime služby" (DIAHostService) se nepodařilo spustit. Ověřte, zda máte dostatečná oprávnění pro spouštění systémových služeb, a postupujte takto:

    1. Na panelu služby systému Windows ověřte účet přihlašovací služby *DIAHostService* .
    
        ![Snímek obrazovky s podoknem přihlášení pro účet služby](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Zkontrolujte, zda se účet přihlašovací služby **přihlásil jako oprávnění služby** pro spuštění služby systému Windows:

        ![Snímek obrazovky s podoknem přihlásit se jako služba](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Další informace

Pokud se ve vašem případě nepoužijí žádné z předchozích dvou vzorů řešení, zkuste shromáždit tyto protokoly událostí Windows: 
- Protokoly aplikací a služeb > Integration Runtime
- Protokoly Windows > aplikaci

### <a name="cant-find-the-register-button-to-register-a-self-hosted-ir"></a>Nepovedlo se najít tlačítko Register pro registraci místního prostředí IR    

#### <a name="symptoms"></a>Příznaky

Při registraci místního prostředí IR se v podokně Configuration Manager nezobrazí tlačítko **Registrovat** .

![Snímek obrazovky s podoknem Configuration Manager, ve kterém se zobrazí zpráva, že uzel Integration runtime není zaregistrován](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Příčina

Od verze Integration Runtime 3,0 se odebralo tlačítko **registrace** na existujících uzlech prostředí Integration runtime, které umožňuje čisticí a bezpečnější prostředí. Pokud byl uzel zaregistrován v prostředí Integration runtime, bez ohledu na to, jestli je online, nebo ne, znovu ho zaregistrujte do jiného prostředí Integration runtime odinstalováním předchozího uzlu a pak ho nainstalujte a zaregistrujte.

#### <a name="resolution"></a>Řešení

1. V Ovládacích panelech odinstalujte existující prostředí Integration runtime.

    > [!IMPORTANT] 
    > V následujícím procesu vyberte **Ano**. Neuchovávat data během procesu odinstalace.

    ![Snímek obrazovky s tlačítkem Ano pro odstranění všech uživatelských dat z prostředí Integration runtime](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Pokud nemáte soubor MSI instalačního programu Integration runtime, Stáhněte si do [webu Download Center](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) nejnovější prostředí Integration runtime.
1. Nainstalujte soubor MSI a zaregistrujte prostředí Integration runtime.


### <a name="unable-to-register-the-self-hosted-ir-because-of-localhost"></a>Nepovedlo se zaregistrovat prostředí IR v místním prostředí, protože je localhost.    

#### <a name="symptoms"></a>Příznaky

Při použití get_LoopbackIpOrName nemůžete zaregistrovat prostředí IR v místním prostředí na novém počítači.

**Ladění:** Došlo k chybě modulu runtime.
Inicializační výraz typu pro Microsoft.DataTransfer.DIAgentHost.DataSourceCache vyvolal výjimku.
Při vyhledávání databáze došlo k neobnovitelná chybě.
 
**Podrobnosti výjimky:** System. TypeInitializationException: inicializátor typu pro: Microsoft. DataTransfer. DIAgentHost. DataSourceCache vyvolal výjimku. ---> System .NET. Sockets. SocketException: došlo k neopravitelné chybě při vyhledávání databáze v System .NET. DNS. GetAddrInfo (název řetězce).

#### <a name="cause"></a>Příčina

K tomuto problému obvykle dochází při řešení místního hostitele.

#### <a name="resolution"></a>Řešení

K hostování souboru použijte IP adresu místního hostitele 127.0.0.1 a problém vyřešte.

### <a name="self-hosted-setup-failed"></a>Nepovedlo se nainstalovat samoobslužnou instalaci.    

#### <a name="symptoms"></a>Příznaky

Nemůžete odinstalovat existující infračervený přenos, nainstalovat nový IR nebo upgradovat existující IR na nový IR.

#### <a name="cause"></a>Příčina

Instalace prostředí Integration Runtime závisí na službě Instalační služba systému Windows. Problémy s instalací může nastat z následujících důvodů:
- Nedostatek volného místa na disku.
- Nedostatečná oprávnění.
- Služba systému Windows NT je uzamčena.
- Využití procesoru je příliš vysoké.
- Soubor MSI je hostovaný v pomalém síťovém umístění.
- Některé systémové soubory nebo registry byly neúmyslně změněny.

### <a name="the-ir-service-account-failed-to-fetch-certificate-access"></a>Účtu služby IR se nepodařilo načíst přístup k certifikátu.

#### <a name="symptoms"></a>Příznaky

Když nainstalujete místní prostředí IR prostřednictvím Microsoft Integration Runtime Configuration Manager, vygeneruje se certifikát s důvěryhodnou certifikační autoritou (CA). Certifikát se nedal použít k šifrování komunikace mezi dvěma uzly a zobrazí se tato chybová zpráva: 

"Nepovedlo se změnit režim šifrování intranetu. nepovedlo se mu udělit účet služby Integration Runtime přístup k certifikátu \<*certificate name*> . Kód chyby 103 "

![Snímek obrazovky zobrazující chybovou zprávu "... Udělení přístupu k certifikátu účtu služby Integration Runtime se nezdařilo.](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>Příčina

Certifikát používá úložiště služby klíčového úložiště (KSP), což ještě není podporováno. V místním prostředí IR podporuje jenom úložiště zprostředkovatele kryptografických služeb (CSP).

#### <a name="resolution"></a>Řešení

V tomto případě doporučujeme používat certifikáty CSP.

**Řešení 1** 

Pokud chcete certifikát importovat, spusťte následující příkaz:

`Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx`

![Snímek obrazovky s příkazem Certutil pro import certifikátu](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**Řešení 2** 

Chcete-li převést certifikát, spusťte následující příkazy:

`openssl pkcs12 -in .\xxxx.pfx -out .\xxxx_new.pem -password pass: <EnterPassword>`
`openssl pkcs12 -export -in .\xxxx_new.pem -out xxxx_new.pfx`

Před a po převodu:

![Snímek obrazovky výsledku před převodem certifikátu](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![Snímek obrazovky výsledku po převodu certifikátu](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### <a name="self-hosted-integration-runtime-version-5x"></a>Místní prostředí Integration runtime verze 5. x
Pro upgrade na verzi 5. x Azure Data Factory Integration runtime v místním prostředí vyžadujeme **.NET Framework runtime 4.7.2** nebo novější. Na stránce pro stažení najdete odkazy ke stažení pro nejnovější verzi 4. x a nejnovější dvě verze 5. x. 

Pro zákazníky Azure Data Factory v2:
- Pokud je zapnutá Automatická aktualizace a už jste upgradovali .NET Framework runtime na 4.7.2 nebo novější, místní prostředí Integration runtime se automaticky upgraduje na nejnovější verzi 5. x.
- Pokud je automatická aktualizace zapnutá a jste neupgradovali .NET Framework runtime na 4.7.2 nebo novější, místní prostředí Integration runtime se automaticky neupgraduje na nejnovější verzi 5. x. Místní prostředí Integration runtime zůstane v aktuální verzi 4. x. Na portálu a v místním prostředí Integration runtime se zobrazí upozornění na upgrade modulu runtime .NET Framework.
- Pokud je automatická aktualizace vypnutá a už jste upgradovali .NET Framework runtime na 4.7.2 nebo novější, můžete si ručně stáhnout nejnovější verzi 5. x a nainstalovat ji na svůj počítač.
- Pokud je automatická aktualizace vypnutá a jste neupgradovali .NET Framework runtime na 4.7.2 nebo novější. Když se pokusíte ručně nainstalovat modul Integration runtime (webhost) 5. x a zaregistrovat klíč, budete muset nejdřív upgradovat .NET Framework verze modulu runtime.


Pro zákazníky Azure Data Factory V1:
- Místní prostředí Integration runtime 5. X nepodporuje Azure Data Factory v1.
- Místní prostředí Integration runtime bude automaticky upgradováno na nejnovější verzi 4. x. A nejnovější verze 4. x nevyprší. 
- Pokud se pokusíte ručně nainstalovat místní prostředí Integration runtime 5. x a zaregistrovat klíč, budete upozorněni na to, že místní prostředí Integration runtime 5. x nepodporuje Azure Data Factory v1.


## <a name="self-hosted-ir-connectivity-issues"></a>Problémy s připojením IR v místním prostředí

### <a name="self-hosted-integration-runtime-cant-connect-to-the-cloud-service"></a>Místní prostředí Integration runtime se nemůže připojit ke cloudové službě.

#### <a name="symptoms"></a>Příznaky

Při pokusu o registraci místního prostředí Integration runtime Configuration Manager zobrazí následující chybová zpráva:

V uzlu Integration Runtime (v místním prostředí) došlo k chybě při registraci. "

![Snímek obrazovky uzlu Integration Runtime (v místním prostředí) zjistil během registrace zprávu Chyba.](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Příčina 

Místní prostředí IR se nemůže připojit k back-endu služby Azure Data Factory. K tomuto problému obvykle dochází v důsledku nastavení sítě v bráně firewall.

#### <a name="resolution"></a>Řešení

1. Zkontrolujte, jestli je spuštěná služba Integration runtime. Pokud je, pokračujte na krok 2.
    
   ![Snímek obrazovky znázorňující, že je spuštěná služba IR v místním prostředí.](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Pokud není žádný proxy server nakonfigurovaný v místním prostředí IR, což je výchozí nastavení, spusťte následující příkaz PowerShellu na počítači, na kterém je nainstalovaný modul runtime integrace v místním prostředí:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > Adresa URL služby se může lišit v závislosti na umístění instance služby Data Factory. Pokud chcete najít adresu URL služby, vyberte možnost připojení **uživatelského rozhraní pro zaadf**(  >    >  **Integration Runtime)**  >    >    >  . umožňuje **Zobrazit adresy URL služby** v místním prostředí.
            
    Očekává se následující odpověď:
            
    ![Snímek obrazovky s odpovědí příkazu PowerShellu](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Pokud neobdržíte odpověď, kterou jste očekávali, použijte jednu z následujících metod, podle potřeby:
            
    * Pokud se zobrazí zpráva "vzdálený název nelze rozpoznat", dojde k problému se službou DNS (Domain Name System). Pokud chcete tento problém vyřešit, obraťte se na svého síťového týmu.
    * Pokud se zobrazí zpráva certifikát SSL/TLS není důvěryhodný, [Zkontrolujte certifikát](https://wu2.frontend.clouddatahub.net/) a ověřte, jestli je na počítači důvěryhodný, a pak pomocí Správce certifikátů nainstalujte veřejný certifikát. Tato akce by měla zmírnit problém.
    * V prohlížeči událostí **systému Windows**  >  **(protokoly)**  >  **protokoly aplikací a služeb**  >  **Integration runtime** a vyhledejte všechny chyby, které způsobila služba DNS, pravidlo brány firewall nebo nastavení sítě společnosti. Pokud tato chyba narazíte, vynuceně ukončete připojení. Vzhledem k tomu, že každá společnost má vlastní vlastní nastavení sítě, požádejte o řešení těchto problémů svého síťového týmu.

1. Pokud je v místním prostředí Integration runtime nakonfigurovaný proxy server, ověřte, že váš proxy server má přístup ke koncovému bodu služby. Vzorový příkaz najdete v tématu [PowerShell, webové požadavky a proxy servery](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

Očekává se následující odpověď:
            
![Snímek obrazovky očekávané odezvy příkazu PowerShellu](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Hlediska proxy serveru:
> * Zkontrolujte, jestli proxy server nutné umístit na seznam bezpečných příjemců. Pokud ano, ujistěte se, že jsou [tyto domény](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) v seznamu bezpečných příjemců.
> * Zkontrolujte, jestli je na proxy server důvěryhodný certifikát SSL/TLS "wu2.frontend.clouddatahub.net/".
> * Pokud na proxy serveru používáte ověřování pomocí služby Active Directory, změňte účet služby na uživatelský účet, který bude mít přístup k proxy serveru jako služba Integration Runtime.

### <a name="error-message-self-hosted-integration-runtime-nodelogical-self-hosted-ir-is-in-inactive-running-limited-state"></a>Chybová zpráva: uzel místního prostředí Integration runtime/logický modul IR v místním prostředí je v neaktivním stavu (s omezením).

#### <a name="cause"></a>Příčina 

Hostující uzel integrovaného modulu runtime může mít stav **neaktivní**, jak je znázorněno na následujícím snímku obrazovky:

![Snímek obrazovky integrovaného modulu runtime v místním prostředí s neaktivním stavem](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

K tomuto chování dochází, když uzly nemůžou vzájemně komunikovat.

#### <a name="resolution"></a>Řešení

1. Přihlaste se k virtuálnímu počítači hostovanému na uzlu (VM). V části **protokoly aplikací a služeb**  >  **Integration runtime** otevřete Prohlížeč událostí a vyfiltrujte protokoly chyb.

1. Zkontrolujte, jestli protokol chyb obsahuje tuto chybu: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Pokud se zobrazí tato chyba, spusťte v okně příkazového řádku následující příkaz: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Pokud se zobrazí chyba příkazového řádku "nelze otevřít připojení k hostiteli", který je zobrazen na následujícím snímku obrazovky, požádejte o pomoc oddělení IT, aby tento problém vyřešil. Po úspěšném dokončení programu Telnet kontaktujte podpora Microsoftu, pokud stále máte problémy se stavem uzlu Integration runtime.
        
   ![Snímek obrazovky "nepovedlo se otevřít připojení k hostiteli" na příkazovém řádku.](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Zkontrolujte, zda protokol chyb obsahuje následující položku:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Chcete-li tento problém vyřešit, zkuste jednu nebo obě následující metody:
    - Vložte všechny uzly do stejné domény.
    - Přidejte IP adresu do mapování hostitele ve všech souborech hostitelů hostovaného virtuálního počítače.

### <a name="connectivity-issue-between-the-self-hosted-ir-and-your-data-factory-instance-or-the-self-hosted-ir-and-the-data-source-or-sink"></a>Problém s připojením mezi místním prostředím IR a instancí služby Data Factory nebo místním prostředím IR a zdrojem dat nebo jímky

Pokud chcete řešit potíže s připojením k síti, měli byste vědět, jak shromažďovat trasování sítě, pochopit, jak ho používat, a [analyzovat trasování Microsoft sledování sítě (Netmon)](#analyze-the-netmon-trace) před použitím nástrojů Netmon v reálných případech z místního prostředí IR.

#### <a name="symptoms"></a>Příznaky

Občas může být nutné vyřešit určité problémy s připojením mezi místním prostředím IR a instancí služby Data Factory, jak je znázorněno na následujícím snímku obrazovky, nebo mezi místním prostředím IR a zdrojem dat nebo jímky. 

![Snímek obrazovky se zprávou "zpracovaný požadavek HTTP se nezdařil"](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

V obou případech se můžete setkat s následujícími chybami:

* Kopírování se nezdařilo s chybou: Type = Microsoft. DataTransfer. Common. Shared. HybridDeliveryException, Message = nelze připojit k SQL Server: ' IP adresa '.

* Došlo k jedné nebo více chybám. Při odesílání žádosti došlo k chybě. Základní připojení bylo ukončeno: došlo k neočekávané chybě při příjmu. Nelze načíst data z přenosového připojení: vzdálený hostitel nuceně zavřel existující připojení. Stávající připojení bylo nuceně ukončeno ID aktivity vzdáleného hostitele.

#### <a name="resolution"></a>Řešení

Když narazíte na předchozí chyby, vyřešte je podle pokynů v této části.

- Shromažďovat trasování Netmon pro analýzu: 

    1. Nastavením filtru můžete zobrazit obnovení ze serveru na straně klienta. V následujícím příkladu obrazovky vidíte, že na straně serveru je Server Data Factory.

        ![Snímek obrazovky se serverem pro vytváření dat.](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

    1. Po získání balíčku pro obnovení můžete najít konverzaci pomocí protokolu TCP (Transmission Control Protocol).

        ![Snímek obrazovky s konverzací TCP](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

    1. Pomocí odebrání filtru dostanete konverzaci mezi klientem a Data Factory serverem.

        ![Snímek obrazovky s podrobnostmi o konverzaci](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)

- Analýza Netmon trasování, které jste shromáždili, ukazuje, že celková hodnota TTL (Time to Live) je 64. Podle hodnot uvedených v článku [základní informace o limitu protokolu IP na hodnotu TTL (Time to Live) a směrování](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) , který se extrahuje v následujícím seznamu, vidíte, že se jedná o systém Linux, který resetuje balíček a způsobuje odpojení.

    Výchozí hodnota TTL a mezní hodnoty směrování se mezi různými operačními systémy liší, jak je uvedeno zde:
    - Linux kernel 2,4 (CIRCA 2001): 255 pro TCP, protokol UDP (User Datagram Protocol) a protokol ICMP (Internet Control Message Protocol)
    - Linux kernel 4,10 (2015): 64 pro TCP, UDP a ICMP
    - Windows XP (2001): 128 pro TCP, UDP a ICMP
    - Windows 10 (2015): 128 pro TCP, UDP a ICMP
    - Windows Server 2008:128 pro TCP, UDP a ICMP
    - Windows Server 2019 (2018): 128 pro TCP, UDP a ICMP
    - macOS (2001): 64 pro TCP, UDP a ICMP

    ![Snímek obrazovky zobrazující hodnotu TTL 61.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    V předchozím příkladu se hodnota TTL zobrazuje jako 61 namísto 64, protože když síťový balíček dosáhne svého cíle, musí jít o různé segmenty směrování, jako jsou směrovače nebo síťová zařízení. Počet směrovačů nebo síťových zařízení se odečte, aby se vytvořila konečná hodnota TTL.
    
    V takovém případě vidíte, že resetování lze odeslat ze systému Linux s hodnotou TTL 64.

-  Pokud chcete potvrdit, ze kterého zařízení resetování může pocházet, zkontrolujte čtvrtý segment směrování z místního prostředí IR.
 
    *Síťový balíček ze systému Linux A s hodnotou TTL 64-> B TTL 64 minus 1 = 63-> C TTL 63 minus 1 = 62-> TTL 62 minus 1 = 61 v místním prostředí IR*

- V ideálním případě by počet směrování TTL byl 128, což znamená, že operační systém Windows spouští vaši instanci služby Data Factory. Jak je znázorněno v následujícím příkladu, *128 mínus 107 = 21 segmentů*, což znamená, že od instance služby Data Factory v místním prostředí IR během metody HANDSHAKE protokolu TCP 3 byly odeslány 21 směrování pro balíček.
 
    ![Snímek obrazovky zobrazující hodnotu TTL 107.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Proto je potřeba zapojit síťový tým, aby zkontroloval, co je čtvrtý segment směrování v místním prostředí IR. Pokud se jedná o bránu firewall, jako u systému Linux, zkontrolujte všechny protokoly a zjistěte, proč zařízení obnoví balíček po ověření TCP 3. 
    
    Pokud si nejste jistí, kde se má prozkoumat, zkuste získat trasování Netmon z místního prostředí IR i brány firewall během problematického času. Tento přístup vám pomůže zjistit, které zařízení mohl resetovat balíček a způsobilo odpojení. V takovém případě je potřeba, abyste svůj síťový tým přesunuli vpřed.

### <a name="analyze-the-netmon-trace"></a>Analýza trasování Netmon

> [!NOTE] 
> Následující pokyny platí pro trasování Netmon. Vzhledem k tomu, že trasování Netmon je aktuálně mimo podporu, můžete k tomuto účelu použít Nástroj Wireshark.

Když se pokusíte **8.8.8.8 888** s shromážděným Netmon trasováním, měli byste vidět trasování na následujících snímcích obrazovky:

![Snímek obrazovky s informacemi o tom, že se nepovedlo otevřít připojení k hostiteli na portu 888, se zobrazí chybová zpráva.](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Snímek obrazovky s popisem trasování Netmon](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Předchozí image ukazují, že se nám nepovedlo vytvořit připojení TCP k serveru **8.8.8.8** na portu **888**, takže se tam zobrazí dva další balíčky **SynReTransmit** . Vzhledem k tomu, že se zdrojový **hostitel2** nemohl připojit k **8.8.8.8** pomocí prvního balíčku, bude se dál pokoušet o připojení.

> [!TIP]
> Chcete-li vytvořit toto připojení, vyzkoušejte následující řešení:
> 1. Vyberte **Filtr zátěžového filtru**  >  **standardní**  >  **vyřeší**  >  **adresy IPv4**.
> 1. Chcete-li použít filtr, zadejte **IPv4. Address = = 8.8.8.8** a pak vyberte **Apply (použít**). Pak byste měli vidět komunikaci z místního počítače do cílové **8.8.8.8**.

![Snímek obrazovky zobrazující adresy filtru](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![Snímek obrazovky zobrazující další adresy filtru](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Úspěšné scénáře jsou uvedené v následujících příkladech: 

- Pokud můžete Telnet **8.8.8.8 53** bez problémů, dojde k úspěšné signalizaci TCP 3 a relace se dokončí metodou HANDSHAKE protokolu TCP 4.

    ![Snímek obrazovky s úspěšným scénářem připojení](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![Snímek obrazovky zobrazující podrobnosti o úspěšném připojení](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- Předchozí Metoda handshake protokolu TCP 3 vytvoří následující pracovní postup:

    ![Diagram pracovního postupu handshake protokolu TCP 3](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- Pomocí metody handshake protokolu TCP 4 k dokončení relace je znázorněno v následujících pracovních postupech:

    ![Snímek obrazovky s podrobnostmi o handshake protokolu TCP 4.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Diagram pracovního postupu handshake protokolu TCP 4.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 

### <a name="microsoft-email-notification-about-updating-your-network-configuration"></a>E-mailová oznámení Microsoftu týkající se aktualizace konfigurace sítě

Může se zobrazit následující e-mailové oznámení, které doporučuje aktualizovat konfiguraci sítě tak, aby umožňovalo komunikaci s novými IP adresami pro Azure Data Factory do 8. listopadu 2020:

   ![Snímek obrazovky s e-mailovými oznámeními Microsoftu požadující aktualizaci konfigurace sítě](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="determine-whether-this-notification-affects-you"></a>Určete, zda toto oznámení ovlivňuje

Toto oznámení platí pro následující scénáře:

##### <a name="scenario-1-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-premises-behind-a-corporate-firewall"></a>Scénář 1: odchozí komunikace z místního prostředí Integration runtime, které běží místně za podnikovou bránou firewall

Jak zjistit, zda jste to ovlivnili:

- Nejste *ovlivněni, pokud* definujete pravidla brány firewall na základě plně kvalifikovaných názvů domén (FQDN), které používají přístup popsaný v tématu [nastavení konfigurace brány firewall a seznamu povolených IP adres](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway).

- Máte *vliv na to, jestli* explicitně povolíte seznam povolených IP adres pro odchozí IP adresy na podnikové bráně firewall.

   Pokud jste to ovlivnili, proveďte následující akci: od 8. listopadu 2020 upozorněte tým síťové infrastruktury, aby aktualizoval konfiguraci sítě tak, aby používal nejnovější IP adresy pro datovou továrnu. Pokud si chcete stáhnout nejnovější IP adresy, vyhledejte [pomocí souborů JSON ke stažení možnosti vyhledat značky služby](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-2-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-an-azure-vm-inside-a-customer-managed-azure-virtual-network"></a>Scénář 2: odchozí komunikace z místního prostředí Integration runtime, které běží na virtuálním počítači Azure ve virtuální síti Azure spravované zákazníkem

Jak zjistit, zda jste to ovlivnili:

- Zkontrolujte, jestli máte v privátní síti nějaká pravidla skupiny zabezpečení sítě (NSG), která obsahuje modul runtime integrace v místním prostředí. Pokud neexistují žádná odchozí omezení, nebudete mít vliv na to.

- Pokud máte omezení odchozího pravidla, zkontrolujte, jestli používáte značky služeb. Pokud používáte značky služeb, nejste ovlivněni. Není potřeba nic měnit ani přidávat, protože nový rozsah IP adres je pod existujícími značkami služby. 

  ![Snímek obrazovky s kontrolou cíle znázorňující DataFactory jako cíl](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)

- To *bude mít vliv, pokud* explicitně povolíte seznam povolených adres pro odchozí IP adresy v nastavení pravidel NSG ve virtuální síti Azure.

   Pokud jste to ovlivnili, proveďte následující akci: od 8. listopadu 2020 upozorněte tým síťové infrastruktury, aby aktualizoval pravidla NSG v konfiguraci vaší virtuální sítě Azure tak, aby používal nejnovější IP adresy datové továrny. Pokud si chcete stáhnout nejnovější IP adresy, vyhledejte [pomocí souborů JSON ke stažení možnosti vyhledat značky služby](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-a-customer-managed-azure-virtual-network"></a>Scénář 3: odchozí komunikace z SSIS Integration Runtime ve službě Azure Virtual Network spravované zákazníkem

Jak zjistit, zda jste to ovlivnili:

- Zkontrolujte, zda máte odchozí pravidla NSG v privátní síti obsahující Integration Runtime služba SSIS (SQL Server Integration Services) (SSIS). Pokud neexistují žádná odchozí omezení, nebudete mít vliv na to.

- Pokud máte omezení odchozího pravidla, zkontrolujte, jestli používáte značky služeb. Pokud používáte značky služeb, nejste ovlivněni. Není potřeba nic měnit ani přidávat, protože nový rozsah IP adres je pod existujícími značkami služby.

- To *bude mít vliv, pokud* explicitně povolíte seznam povolených adres pro odchozí IP adresy v nastavení pravidel NSG ve virtuální síti Azure.

  Pokud jste to ovlivnili, proveďte následující akci: od 8. listopadu 2020 upozorněte tým síťové infrastruktury, aby aktualizoval pravidla NSG v konfiguraci vaší virtuální sítě Azure tak, aby používal nejnovější IP adresy datové továrny. Pokud si chcete stáhnout nejnovější IP adresy, vyhledejte [pomocí souborů JSON ke stažení možnosti vyhledat značky služby](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

### <a name="couldnt-establish-a-trust-relationship-for-the-ssltls-secure-channel"></a>Nepovedlo se vytvořit vztah důvěryhodnosti pro zabezpečený kanál SSL/TLS. 

#### <a name="symptoms"></a>Příznaky

Místní prostředí IR se nemůže připojit ke službě Azure Data Factory.

Po kontrole protokolu událostí místního hostitele v místním prostředí nebo v protokolech oznámení klienta v tabulce CustomLogEvent se zobrazí následující chybová zpráva:

"Základní připojení bylo ukončeno: nelze vytvořit vztah důvěryhodnosti pro zabezpečený kanál SSL/TLS. Vzdálený certifikát je podle ověřovací procedury neplatný.

Nejjednodušší způsob, jak ověřit certifikát serveru Data Factory služby, je otevřít adresu URL služby Data Factory ve vašem prohlížeči. Otevřete například [odkaz ověřit certifikát serveru](https://eu.frontend.clouddatahub.net/) v počítači, na kterém je nainstalováno prostředí IR v místním prostředí, a pak zobrazte informace o certifikátu serveru.

  ![Snímek obrazovky s podoknem kontrolní certifikát serveru služby Azure Data Factory](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Snímek obrazovky okna pro kontrolu cesty k certifikaci serveru.](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>Příčina

Existují dva možné příčiny tohoto problému:

- Důvod 1: kořenová certifikační autorita certifikátu serveru Data Factory služby není důvěryhodná na počítači, na kterém je nainstalovaný modul IR pro místní hostování. 
- Důvod 2: ve svém prostředí používáte proxy server, certifikát serveru služby Data Factory je nahrazen proxy serverem a vyměněný certifikát serveru není důvěryhodný pro počítač, ve kterém je nainstalováno prostředí IR pro místní hostování.

#### <a name="resolution"></a>Řešení

- Z důvodu 1: Ujistěte se, že certifikát serveru Data Factory a jeho řetěz certifikátů jsou důvěryhodné pro počítač, na kterém je nainstalováno prostředí IR pro místní hostování.
- Z důvodu 2: buď považovat nahrazenou kořenovou certifikační autoritu na místně hostovaný počítač IR, nebo nakonfigurujte proxy server tak, aby nahradil certifikát serveru Data Factory.

Další informace o důvěřování certifikátů ve Windows najdete v tématu [instalace důvěryhodného kořenového certifikátu](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate).

#### <a name="additional-information"></a>Další informace
Zavedli jsme nový certifikát SSL, který je podepsaný z DigiCert. Zkontrolujte, zda je globální kořenový adresář G2 DigiCert v důvěryhodné kořenové certifikační autoritě.

  ![Snímek obrazovky zobrazující globální kořenovou složku G2 DigiCert v adresáři důvěryhodných kořenových certifikačních autorit](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

Pokud není v důvěryhodné kořenové certifikační autoritě, [Stáhněte si ho sem](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt ). 


## <a name="self-hosted-ir-sharing"></a>Sdílení místního prostředí IR

### <a name="sharing-a-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Sdílení prostředí IR v místním prostředí v jiném tenantovi se nepodporuje. 

#### <a name="symptoms"></a>Příznaky

V případě, že se pokoušíte sdílet technologii IR v místním prostředí pomocí uživatelského rozhraní Azure Data Factory, si můžete všimnout jiných datových továren (v různých klientech), ale nemůžete je sdílet mezi datovými továrnami, které jsou v různých klientech.

#### <a name="cause"></a>Příčina

Prostředí IR v místním prostředí není možné sdílet mezi klienty.

## <a name="next-steps"></a>Další kroky

Další pomoc při řešení potíží najdete v následujících zdrojích informací:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stránka Microsoft Q&](/answers/topics/azure-data-factory.html)
*  [Fórum přetečení zásobníku pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Průvodce mapováním výkonu datových toků](concepts-data-flow-performance.md)