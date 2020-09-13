---
title: Řešení potíží s místním prostředím Integration runtime v Azure Data Factory
description: Přečtěte si, jak řešit problémy s místním hostováním prostředí Integration runtime v Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/10/2020
ms.author: abnarain
ms.openlocfilehash: a6a0a62bd857dff575e17f47f1e2394375b08c45
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033655"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Řešení potíží s místním hostováním Integration runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje běžné metody řešení potíží pro prostředí Integration runtime v místním prostředí v Azure Data Factory.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Shromážděte místně hostované protokoly IR z Azure Data Factory

Pro neúspěšné aktivity běžící v místním prostředí IR/Shared IR Azure Data Factory podporuje zobrazování a nahrávání protokolů chyb. Pomocí následujících kroků můžete získat ID zprávy o chybách a pak zadat ID sestavy, kde najdete související známé problémy.

1. Přejít na stránku **spuštění aktivit**

1. Ve sloupci **Chyba** klikněte níže na tlačítko níže.

    ![Stránka spuštění aktivit](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. Zobrazí se související protokoly pro spuštění neúspěšné aktivity. Další pomoc získáte kliknutím na tlačítko **Odeslat protokoly** .

    ![Odeslat protokoly](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. Můžete zvolit protokoly, které chcete odeslat. V případě prostředí *IR*v místním prostředí můžete nahrávat protokoly týkající se neúspěšné aktivity nebo všech protokolů v uzlu IR v místním prostředí. Pro *sdílené infračervené*prostředí můžete nahrávat jenom protokoly týkající se neúspěšné aktivity.

    ![Zvolit protokoly](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Po nahrání protokolů Udržujte záznam ID sestavy, pokud k vyřešení problému potřebujete další pomoc.

    ![Nahrát protokoly](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Žádosti o zobrazení a nahrání protokolu se spustí ve všech online autohostovaných instancích IR. Ujistěte se prosím, že všechny chybějící protokoly jsou online u všech místně hostovaných instancí IR. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Obecná vnitřní chyba nebo chyba IR v místním prostředí

### <a name="tlsssl-certificate-issue"></a>Problém certifikátu TLS/SSL

#### <a name="symptoms"></a>Příznaky

Při pokusu o povolení certifikátu TLS/SSL (rozšířené) z místního prostředí **IR Configuration Manager**  ->  **vzdálený přístup z intranetu**po výběru certifikátu TLS/SSL se zobrazí tato chyba:

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

Ve výše uvedeném případě uživatel jako poslední položku používá certifikát s názvem "microsoft.com".

#### <a name="cause"></a>Příčina

Jedná se o známý problém WCF: ověřování WCF TLS/SSL jenom kontroluje poslední DNSName v síti SAN. 

#### <a name="resolution"></a>Řešení

Certifikát se zástupným znakem se podporuje v prostředí IR pro místní hostování Azure Data Factory v2. K tomuto problému obvykle dochází, protože certifikát SSL není správný. Poslední DNSName v síti SAN by měl být platný. Použijte následující postup k ověření. 
1.  Otevřete konzolu pro správu, v podrobnostech certifikátu překontrolujte *alternativní název* *subjektu i* předmět. V takovém případě není například poslední položka v *alternativním názvu předmětu*, která je "DNS Name = Microsoft.com.com", legitimní.
2.  Požádejte společnost o problém s certifikátem, aby odstranila nesprávný název DNS.

### <a name="concurrent-jobs-limit-issue"></a>Problém s limitem souběžných úloh

#### <a name="symptoms"></a>Příznaky

Při pokusu o zvýšení limitu souběžných úloh z Azure Data Factoryho uživatelského rozhraní se přestane reagovat, protože se *aktualizuje* trvale.
Maximální hodnota souběžných úloh byla nastavena na 24 a chcete zvýšit počet, aby úlohy mohly běžet rychleji. Minimální hodnota, kterou můžete zadat, je 3 a maximální hodnota, kterou můžete zadat, je 32. Zvýšili jste hodnotu z 24 na 32 a kliknete na tlačítko *aktualizovat* v uživatelském rozhraní, které se zablokovalo při *aktualizaci* , jak vidíte níže. Po obnovení si zákazník tuto hodnotu ještě nezískal jako 24 a nikdy se neaktualizoval na 32.

![Stav aktualizace](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Příčina

Omezení nastavení závisí na tom, zda je tato hodnota závislá na logicCore počítače a paměti, můžete ji upravovat pouze na menší hodnotu, jako je například 24, a výsledek zobrazit.

> [!TIP] 
> - Podrobnosti o tom, co je počet logických jader a jak zjistit počet jader v našem počítači, najdete v [tomto článku](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> - Podrobnosti o výpočtu matematického protokolu. log najdete v [tomto článku](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>Problémy s certifikátem pro místní hostování IR HA SSL

#### <a name="symptoms"></a>Příznaky

Uzel v místním prostředí IR ohlásil chybu níže:

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>Příčina

Když budeme zpracovávat případy související s metodou handshake SSL/TLS, můžeme se setkat s problémy souvisejícími s ověřováním řetězu certifikátů. 

#### <a name="resolution"></a>Řešení

- Tady je rychlý a intuitivní způsob řešení potíží při sestavení řetězu certifikátů X. 509.
 
    1. Exportujte certifikát, který je třeba ověřit. Přejděte na Správa certifikátu počítače a Najděte certifikát, který chcete ověřit, a klikněte pravým tlačítkem na **všechny úkoly**  ->  **exportovat**.
    
        ![Exportovat úlohy](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Zkopírujte exportovaný certifikát do klientského počítače. 
    3. Na straně klienta spusťte pod příkazem CMD příkaz níže. Ujistěte se, že jste nahradili následující *\<certificate path>* a *\<output txt file path>* zástupné symboly souvisejícími cestami.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Například:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Ověřte, zda výstupní soubor txt neobsahuje chybu. Souhrn chyb najdete na konci souboru txt.

        Například: 

        ![Souhrn chyb](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Pokud se nezobrazí žádná chyba na konci souboru protokolu, jak je znázorněno níže, můžete v klientském počítači zvážit úspěšné sestavení řetězce certifikátů.
        
        ![V souboru protokolu není žádná chyba.](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Pokud je v souboru certifikátu nakonfigurován AIA, CDP a OCSP. Můžeme je vymezit intuitivnějším způsobem.
 
    1. Tyto informace můžete získat tak, že zkontrolujete podrobnosti certifikátu.
    
        ![Podrobnosti certifikátu](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. Spusťte příkaz níže. Ujistěte se, že jste nahradili *\<certificate path>* zástupný symbol se související cestou certifikátu.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. Pak se otevře **Nástroj pro načtení adresy URL** . Kliknutím na tlačítko **načíst** můžete ověřit certifikáty z AIA, CDP a protokolu OCSP.

        ![Tlačítko pro načtení](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        Řetěz certifikátů se dá úspěšně sestavit, pokud je certifikát z AIA ověřený a certifikát z CDP nebo OCSP je ověřený.

        Pokud se při načítání AIA, CDP, práce se síťovým týmem zobrazí chyba, aby byl klientský počítač připraven k připojení k cílové adrese URL. Bude stačit, pokud je možné ověřit cestu http nebo cestu protokolu LDAP.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>Prostředí IR v místním prostředí nemůže načíst soubor nebo sestavení.

#### <a name="symptoms"></a>Příznaky

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
Například: 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>Příčina

Pokud převezmete monitorování procesů, můžete zobrazit následující výsledek:

[![Monitor procesů](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> Filtr můžete nastavit tak, jak je znázorněno na obrázku obrazovky.
> Oznamujeme, že knihovna DLL **System. ValueTuple** není umístěná ve složce v mezipaměti GAC nebo v adresáři *c:\Program Files\microsoft Integration Runtime\4.0\Gateway*nebo ve složce *c:\Program Files\Microsoft integr Runtime\4.0\Shared* .
> V podstatě načte knihovnu DLL ze složky *GAC* a pak ze složky *Gateway* *Shared* a finally. Proto můžete umístit knihovnu DLL na libovolnou cestu, která může být užitečná.

![Nastavení filtrů](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Řešení

Zjistíte, že **System.ValueTuple.dll** se nachází ve složce *C:\Program Files\Microsoft integr Runtime\4.0\Gateway\DataScan* Folder. Problém vyřešte tak, že zkopírujete **System.ValueTuple.dll** do složky *C:\Program Files\Microsoft Runtime\4.0\Gateway Integration* .

Můžete použít stejnou metodu pro řešení chybějících potíží s jiným souborem nebo sestavením.

#### <a name="more-information"></a>Další informace

Důvodem, proč vidíte System.ValueTuple.dll pod *%windir%\Microsoft.NET\assembly* a *%windir%\assembly* je to, že se jedná o chování rozhraní .NET. 

Z chyby níže můžete zřetelně zobrazit *systém sestavení. ValueTuple* není. Takže k tomuto problému dochází, když se aplikace pokusí ověřit *System.ValueTuple.dll*sestavení.
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
Další informace o globální mezipaměti sestavení (GAC) najdete v [tomto článku](https://docs.microsoft.com/dotnet/framework/app-domains/gac).


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>Jak auditovat chybějící místně hostovaný klíč IR

#### <a name="symptoms"></a>Příznaky

Prostředí Integration runtime v místním prostředí se náhle přepne do režimu offline bez klíče, v protokolu událostí se zobrazí chybová zpráva: `Authentication Key is not assigned yet`

![Chybí ověřovací klíč.](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Příčina

- Odstraní se místně hostovaný uzel IR nebo logické prostředí IR v místním prostředí (webhost) na portálu.
- Je provedena čistá odinstalace.

#### <a name="resolution"></a>Řešení

Pokud se nepoužijí žádné z výše uvedených příčin, můžete přejít do složky: *%ProgramData%\Microsoft\Data Transfer\DataManagementGateway*a ověřit, zda je soubor s názvem **Konfigurace** odstraněn. Pokud se odstraní, postupujte podle pokynů uvedených [tady](https://www.netwrix.com/how_to_detect_who_deleted_file.html) a proveďte audit, který soubor odstraní.

![Ověřit soubor konfigurací](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>Nejde použít místní prostředí IR k mostu dvou místních úložišť dat.

#### <a name="symptoms"></a>Příznaky

Po vytvoření samoobslužného úřadu pro ukládání zdrojového i cílového úložiště dat chcete, aby se tyto dvě finanční údaje připojovaly společně, aby se dokončila kopie. Pokud jsou úložiště dat nakonfigurovaná v různých virtuální sítě nebo nemůžou pochopit mechanismus brány, dojde k chybám, jako je: *ovladač zdroje nejde najít v cíli IR*; k *zdroji není přistupující cílovým IR*.
 
#### <a name="cause"></a>Příčina

Prostředí IR v místním prostředí je navrženo jako centrální uzel aktivity kopírování, nikoli klientského agenta, který je třeba nainstalovat pro každé úložiště dat.
 
V takovém případě by měla být propojená služba pro každé úložiště dat vytvořena se stejným IR a má by mít přístup k úložišti dat prostřednictvím sítě. Bez ohledu na to, jestli je IR nainstalovaný se zdrojovým úložištěm dat, cílovým úložištěm dat nebo na třetím počítači, pokud se vytvoří dvě propojené služby s jiným finančním úřadem, ale používá se ve stejné aktivitě kopírování, použije se cílový IR a v cílovém počítači IR se musí nainstalovat ovladače pro úložiště dat.

#### <a name="resolution"></a>Řešení

Nainstalujte ovladače pro zdroj i cíl do cílového prostředí IR a ujistěte se, že mají přístup ke zdrojovému úložišti dat.
 
Pokud přenos nemůže proběhnout přes síť mezi dvěma datovými úložišti (například jsou nakonfigurované ve dvou virtuální sítě), nesmíte kopii dokončit v jedné aktivitě, i když je nainstalovaná možnost IR. V takovém případě můžete vytvořit dvě aktivity kopírování se dvěma finančními autoritami, každý z nich: 1 IR ke zkopírování z úložiště dat 1 do Azure Blob Storage, druhý pro kopírování z Azure Blob Storage do úložiště dat 2. To může simulovat požadavek na použití infračerveného signálu k vytvoření mostu, který spojuje dvě odpojená úložiště dat.


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>Problémy s synchronizací přihlašovacích údajů způsobují ztráty přihlašovacích údajů z HA.

#### <a name="symptoms"></a>Příznaky

Přihlašovací údaj ke zdroji dat "XXXXXXXXXX" je odstraněn z aktuálního Integration Runtime uzlu s datovou částí "při odstranění služby propojení v Azure Portal nebo úloha má nesprávnou datovou část, vytvořte novou službu propojení znovu s přihlašovacími údaji".

#### <a name="cause"></a>Příčina

Místní prostředí IR je integrováno v režimu HA se dvěma uzly, ale nejsou ve stavu synchronizace přihlašovacích údajů, což znamená, že přihlašovací údaje uložené v uzlu Dispatcher nejsou synchronizované s ostatními pracovními uzly. Pokud dojde k převzetí služeb při selhání z uzlu dispečer na pracovní uzel, ale přihlašovací údaje existovaly pouze v předchozím uzlu Dispatcher, úloha selže při pokusu o přístup k přihlašovacím údajům a dojde k chybě.

#### <a name="resolution"></a>Řešení

Jediným způsobem, jak se tomuto problému vyhnout, je zajistit, aby byly dva uzly ve stavu synchronizace přihlašovacích údajů. V opačném případě musíte znovu zadat přihlašovací údaje pro nového dispečera.


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>Nejde zvolit certifikát z důvodu chybějícího privátního klíče.

#### <a name="symptoms"></a>Příznaky

1.  Importujte soubor PFX do úložiště certifikátů.
2.  Při výběru certifikátu prostřednictvím uživatelského rozhraní Configuration Manager IR se zobrazí chyba:

    ![Chybí privátní klíč.](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Příčina

- Uživatelský účet je v nízkém oprávnění a nemůže získat přístup k privátnímu klíči.
- Certifikát se vygeneroval jako signatura, ale ne jako výměna klíčů.

#### <a name="resolution"></a>Řešení

1.  Použijte privilegovaný účet, který má přístup k privátnímu klíči pro práci s uživatelským rozhraním.
2.  Pro import certifikátu spusťte následující příkaz:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```


## <a name="self-hosted-ir-setup"></a>Instalace prostředí IR pro místní hostování

### <a name="the-integration-runtime-registration-error"></a>Chyba registrace Integration Runtime 

#### <a name="symptoms"></a>Příznaky

Někdy chceme v jiném účtu spustit prostředí IR v místním prostředí, a to z následujících důvodů:
- Zásady společnosti nepovolují účet služby.
- Vyžaduje se nějaké ověřování.

Po změně účtu služby na panelu služby se může stát, že Integration Runtime přestane fungovat.

![Chyba registrace IR](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Příčina

K dispozici je celá řada prostředků, které jsou přiděleny pouze účtu služby. Při změně účtu služby na jiný účet zůstane oprávnění všech závislých prostředků stejné.

#### <a name="resolution"></a>Řešení

Chcete-li zjistit chybu, vyhledejte v protokolu událostí Integration Runtime.

![Protokol událostí IR](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

Pokud se chyba zobrazí jako výše *UnauthorizedAccessException*, postupujte podle následujících pokynů:


1. Ověřte účet přihlašovací služby *DIAHostService* na panelu služby systému Windows.

    ![Účet přihlašovací služby](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Ověřte, zda má účet přihlašovací služby oprávnění R/W pro složku: *%ProgramData%\Microsoft\DataTransfer\DataManagementGateway*.

    - Ve výchozím nastavení platí, že pokud se účet přihlášení služby nezměnil, měl by mít oprávnění k R/W.

        ![Oprávnění služby](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

    - Pokud jste změnili přihlašovací účet služby, postupujte podle následujících kroků a problém vyřešíte takto:
        1. Vyčistit odinstalujte aktuálně místně hostovaný IR.
        1. Nainstalujte prostředí IR pro místní hostování.
        1. Chcete-li změnit účet služby, postupujte podle pokynů níže: 
            1. Přejděte do složky selfhosted IR pro instalaci, přejděte do složky: *Microsoft Integration Runtime\4.0\Shared*.
            1. Spusťte příkazový řádek s oprávněním vyšší úrovně. Nahraďte *\<user>* a *\<password>* vlastním uživatelským jménem a heslem a pak spusťte následující příkaz:
                       
                ```
                dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"
                ```
            1. Pokud chcete změnit účet LocalSystem, ujistěte se, že pro tento účet používáte správný formát. Níže je uveden příklad správného formátu:

                ```
                dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""
                ```         
                Nepoužívejte **Formát** , jak je znázorněno níže:

                ```
                dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""
                ```              
            1. V případě alternativy, protože místní systém má vyšší oprávnění než správce, můžete ho také přímo změnit v části "služby".
            1. Pro přihlašovací účet služby IR můžete použít místní/doménový uživatel.            
        1. Zaregistrujte Integration Runtime.

Pokud se chyba zobrazí jako: *služba Integration runtime služby (DIAHostService) se nepodařilo spustit. Ověřte, zda máte dostatečná oprávnění pro spouštění systémových služeb*, postupujte podle následujících pokynů:

1. Ověřte účet přihlašovací služby *DIAHostService* na panelu služby systému Windows.
   
    ![Účet přihlašovací služby](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Ověřte, zda má účet přihlašovací služby oprávnění **Přihlásit se jako služba** pro spuštění služby systému Windows:

    ![Přihlášení jako služba](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Další informace

Pokud se ve vašem případě nepoužijí žádné z výše uvedených dvou vzorů, zkuste shromáždit níže uvedené protokoly událostí Windows: 
- Protokoly aplikací a služeb – > Integration Runtime
- Protokoly Windows – > aplikace

### <a name="cannot-find-register-button-to-register-a-self-hosted-ir"></a>Nejde najít tlačítko registrovat pro registraci místního prostředí IR.    

#### <a name="symptoms"></a>Příznaky

Tlačítko **zaregistrovat** nebylo v uživatelském rozhraní Configuration Manager při registraci místního prostředí IR nalezeno.

![Tlačítko pro registraci](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Příčina

Od vydání *Integration Runtime 3,0*se odebralo tlačítko **registrace** na existujícím uzlu Integration runtime, aby se povolilo čištění a bezpečnější prostředí. Pokud je uzel zaregistrovaný k nějakému modulu runtime integrace (bez ohledu na to, jestli je online) a chcete ho znovu zaregistrovat k jinému modulu runtime integrace, musíte předchozí uzel odinstalovat a pak nainstalovat a zaregistrovat nový uzel.

#### <a name="resolution"></a>Řešení

1. Chcete-li odinstalovat existující Integration Runtime, použijte ovládací panel.

    > [!IMPORTANT] 
    > V níže uvedeném procesu vyberte Ano. Neuchovávat data během procesu odinstalace.

    ![Odstranění dat](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Pokud nemáte instalační službu Integration runtime MSI, Stáhněte si nejnovější Integration Runtime na webu [Download Center](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) .
1. Nainstalujte soubor MSI a zaregistrujte Integration Runtime.


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>Místní prostředí IR nejde zaregistrovat kvůli místnímu hostiteli    

#### <a name="symptoms"></a>Příznaky

Při get_LoopbackIpOrName nejde zaregistrovat prostředí IR v místním prostředí na novém počítači.

**Ladění:** Došlo k chybě modulu runtime.
Inicializační procedura typu Microsoft. DataTransfer. DIAgentHost. DataSourceCache způsobila výjimku.
Při vyhledávání databáze došlo k neobnovitelná chybě.
 
**Podrobnosti výjimky:** System. TypeInitializationException: inicializátor typu pro: Microsoft. DataTransfer. DIAgentHost. DataSourceCache vyvolal výjimku. ---> System .NET. Sockets. SocketException: došlo k neopravitelné chybě při vyhledávání databáze v System .NET. DNS. GetAddrInfo (název řetězce).

#### <a name="cause"></a>Příčina

K tomuto problému obvykle dochází při rozpoznávání localhost.

#### <a name="resolution"></a>Řešení

K hostování souboru použijte localhost 127.0.0.1 a tento problém vyřešte.


### <a name="self-hosted-setup-failed"></a>Nepovedlo se nainstalovat samoobslužnou instalaci.    

#### <a name="symptoms"></a>Příznaky

Stávající infračervený přenos nelze odinstalovat, nebo můžete nainstalovat nový IR nebo upgradovat existující IR na nový IR.

#### <a name="cause"></a>Příčina

Instalace závisí na službě Instalační služba systému Windows. K dispozici jsou varianty, které můžou způsobit problém s instalací:
- Nedostatek místa na disku
- Nedostatečná oprávnění
- Služba NT je z nějakého důvodu uzamčena.
- Využití procesoru je příliš vysoké.
- Soubor MSI je hostovaný v pomalém síťovém umístění.
- Některé systémové soubory nebo registry byly neúmyslně změněny.


## <a name="self-hosted-ir-connectivity-issues"></a>Problémy s připojením IR v místním prostředí

### <a name="self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Místní prostředí Integration runtime se nemůže připojit ke cloudové službě.

#### <a name="symptoms"></a>Příznaky

![Problém s připojením IR v místním prostředí](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Příčina 

Místní prostředí Integration runtime se nemůže připojit ke službě Data Factory (back-end). K tomuto problému obvykle dochází v důsledku nastavení sítě v bráně firewall.

#### <a name="resolution"></a>Řešení

1. Ověřte, jestli je spuštěná služba Integration runtime.
    
   ![Stav spuštění služby IR v místním prostředí](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Pokud je služba spuštěná, pokračujte krokem 3.

1. Pokud není v místním prostředí Integration runtime nakonfigurovaný žádný proxy server (což je výchozí nastavení), spusťte následující příkaz PowerShellu na počítači, na kterém je nainstalovaný modul runtime integrace v místním prostředí:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > Adresa URL služby se může lišit v závislosti na umístění Data Factory. Adresu URL služby najdete v části připojení k **uživatelským rozhraním ADF**  >  **Connections**  >  **Integration modul runtime**upravit uzly v místním prostředí  >  **IR**  >  **Nodes**  >  **Zobrazit adresy URL služby**.
            
    Očekává se následující odpověď:
            
    ![Odezva příkazu PowerShellu](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Pokud neobdržíte očekávanou odpověď, použijte jednu z následujících metod, která je vhodná pro vaši situaci:
            
    * Pokud se zobrazí zpráva "vzdálený název nelze rozpoznat", dojde k problému se službou DNS (Domain Name System). Pokud chcete tento problém vyřešit, obraťte se na svého síťového týmu.
    * Pokud se zobrazí zpráva "certifikát SSL/TLS není důvěryhodný", zkontrolujte, zda je certifikát pro https://wu2.frontend.clouddatahub.net/ důvěryhodný na počítači a pak nainstalujte veřejný certifikát pomocí Správce certifikátů. Tato akce by měla zmírnit problém.
    * V prohlížeči událostí **systému Windows**  >  **(protokoly)**  >  **protokoly aplikací a služeb**  >  **Integration runtime** a vyhledejte všechny chyby, které způsobila služba DNS, pravidlo brány firewall nebo nastavení sítě společnosti. (Pokud narazíte na chybu, nuceně uzavřete připojení.) Vzhledem k tomu, že každá společnost má vlastní nastavení sítě, požádejte o řešení těchto problémů svého síťového týmu.

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
            
![Odezva příkazu PowerShellu 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Hlediska proxy serveru:
> *    Ověřte, zda proxy server musí být vloženy do seznamu bezpečných příjemců. Pokud ano, ujistěte se, že jsou [tyto domény](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) v seznamu bezpečných příjemců.
> *    Ověřte, jestli je certifikát TLS/SSL wu2.frontend.clouddatahub.net/na proxy server důvěryhodný.
> *    Pokud na proxy serveru používáte ověřování pomocí služby Active Directory, změňte účet služby na uživatelský účet, který bude mít přístup k proxy serveru jako služba Integration Runtime.

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Chybová zpráva: uzel Integration runtime (v místním prostředí)/logický SHIR je v neaktivním stavu (s omezením).

#### <a name="cause"></a>Příčina 

Hostující uzel integrovaného modulu runtime může mít **neaktivní** stav, jak je znázorněno na následujícím snímku obrazovky:

![Neaktivní místní hostitelský uzel IR](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

K tomuto chování dochází, když uzly nemůžou vzájemně komunikovat.

#### <a name="resolution"></a>Řešení

1. Přihlaste se k virtuálnímu počítači hostovanému na uzlu. V části **protokoly aplikací a služeb**  >  **Integration runtime**otevřete Prohlížeč událostí a vyfiltrujte všechny protokoly chyb.

1. Zkontroluje, jestli protokol chyb obsahuje tuto chybu: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Pokud se zobrazí tato chyba, spusťte na příkazovém řádku následující příkaz: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Pokud se zobrazí následující chyba, obraťte se na oddělení IT, kde najdete nápovědu k vyřešení tohoto problému. Po úspěšném dokončení programu Telnet kontaktujte podpora Microsoftu, pokud stále máte problémy se stavem uzlu modulu runtime integrační.
        
   ![Chyba příkazového řádku](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Ověřte, zda protokol chyb obsahuje následující:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Chcete-li tento problém vyřešit, zkuste jednu nebo obě následující metody:
    - Vložte všechny uzly do stejné domény.
    - Přidejte IP adresu do mapování hostitele ve všech souborech hostitelů hostovaného virtuálního počítače.

### <a name="connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Potíže s připojením mezi místním prostředím IR a Data Factorym nebo místním prostředím IR a zdrojem dat/jímky v místním prostředí

Pokud chcete řešit potíže s připojením k síti, měli byste vědět, jak shromažďovat trasování sítě, pochopit, jak ho používat, a [analyzovat trasování Netmon](#how-to-analyze-netmon-trace) před použitím nástrojů Netmon v reálných případech z místního prostředí IR.

#### <a name="symptoms"></a>Příznaky

Při řešení potíží s připojením, jako je například níže mezi místním prostředím IR a Data Factory v místním prostředí, může dojít k potížím: 

![Požadavek HTTP se nezdařil.](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Nebo jeden mezi místním a datovým prostředím v místním prostředí – dojde k následujícím chybám:

`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

#### <a name="resolution"></a>Řešení:

Pokud se setkáte s výše uvedenými problémy, přečtěte si následující pokyny k řešení potíží:

Netmon trasování a proveďte další analýzu.
- Za prvé můžete nastavit filtr tak, aby se na straně klienta zobrazily všechny resetované informace ze serveru. V níže uvedeném příkladu uvidíte stranu serveru Data Factory serveru.

    ![Server datové továrny](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- Po získání balíčku pro obnovení můžete najít konverzaci pomocí protokolu TCP.

    ![Najít konverzaci](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- Pak můžete pomocí odebrání filtru získat převod mezi klientem a Data Factory serverem.

    ![Získat konverzaci](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- Na základě shromážděného trasování Netmon můžeme říct, že celková hodnota TTL (TimeToLive) je 64. V závislosti na **výchozích hodnotách TTL a segmentů směrování** uvedených v [tomto článku](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) (jak jsou extrahovány níže) vidíte, že se jedná o systém Linux, který resetuje balíček a způsobuje odpojení.

    Výchozí hodnoty TTL a mezních hodnot směrování se mezi různými operačními systémy liší, Toto jsou výchozí hodnoty pro několik:
    - Linux kernel 2,4 (CIRCA 2001): 255 pro TCP, UDP a ICMP
    - Linux kernel 4,10 (2015): 64 pro TCP, UDP a ICMP
    - Windows XP (2001): 128 pro TCP, UDP a ICMP
    - Windows 10 (2015): 128 pro TCP, UDP a ICMP
    - Windows Server 2008:128 pro TCP, UDP a ICMP
    - Windows Server 2019 (2018): 128 pro TCP, UDP a ICMP
    - macOS (2001): 64 pro TCP, UDP a ICMP

    ![HODNOTA TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Je ale zobrazená jako 61 namísto 64 výše uvedeného příkladu, protože když síťový balíček dosáhne cíle, musí jít o jiný segment směrování, jako jsou směrovače nebo síťová zařízení. Počet směrovačů/síťových zařízení bude v konečné hodnotě TTL snížen.
    V tomto případě můžeme vidět, že se resetování může odeslat ze systému Linux s hodnotou TTL 64.

- Musíme zkontrolovat čtvrtý segment směrování z místního prostředí IR a potvrdit, odkud zařízení pro resetování může pocházet.
 
    *Síťový balíček ze systému Linux A s hodnotou TTL 64-> B TTL 64 minus 1 = 63-> C TTL 63 minus 1 = 62-> TTL 62 minus 1 = 61 v místním prostředí IR*

- V ideálním případě bude hodnota TTL 128, což znamená, že systém Windows spouští naši Data Factory. Jak je znázorněno v následujícím příkladu, *128 – 107 = 21 směrování*, což znamená, že 21 směrování pro balíček bylo odesláno z Data Factory do místního prostředí IR během ověřování TCP 3.
 
    ![HODNOTA TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Proto je potřeba zapojit síťový tým a ověřit, co je čtvrtý segment směrování z místního prostředí IR. Pokud se jedná o bránu firewall jako systém Linux, zkontrolujte všechny protokoly, proč zařízení obnoví balíček po TCP 3 handshake. Pokud si ale nejste jistí, kde se má prozkoumat, zkuste získat trasování Netmon z místního prostředí IR a brány firewall během problematické doby, abyste zjistili, které zařízení může resetovat tento balíček a způsobit odpojení. V takovém případě je potřeba, abyste svůj síťový tým přesunuli vpřed.

### <a name="how-to-collect-netmon-trace"></a>Jak shromažďovat Netmon trasování

1.  Stáhněte si nástroje Netmon z [tohoto webu](https://cnet-downloads.com/network-monitor)a nainstalujte je na serverovém počítači (jakýkoli server s problémem) a klienta (jako je například prostředí IR pro místní hostování).

2.  Vytvořte složku, například v následující cestě: *D:\netmon*. Ujistěte se, že má dostatek místa pro uložení protokolu.

3.  Zaznamenejte informace o IP adrese a portu. 
    1. Spuštění příkazového řádku
    2. Vyberte Spustit jako správce a spusťte následující příkaz:
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  Zachyťte trasování Netmon (síťový balíček).
    1. Spuštění příkazového řádku
    2. Vyberte Spustit jako správce a spusťte následující příkaz:
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. Můžete použít tři různé příkazy pro zachycení síťové stránky:
        - Možnost A: příkaz souboru RoundRobin (zachytí se jenom jeden soubor a přepíše staré protokoly).

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - Možnost B: příkaz zřetězeného souboru (vytvoří se nový soubor, pokud se dosáhne 200 MB).
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - Možnost C: příkaz plánovaného souboru.

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  Stisknutím **kombinace kláves CTRL + C** zastavíte zachytávání trasování Netmon.
 
> [!NOTE]
> Pokud je na klientském počítači možné shromažďovat pouze trasování Netmon, Získejte prosím adresu IP serveru, která vám usnadní analýzu tohoto trasování.

### <a name="how-to-analyze-netmon-trace"></a>Jak analyzovat trasování Netmon

> [!NOTE] 
> Níže uvedená instrukce se vztahuje na Netmon trasování. Vzhledem k tomu, že trasování Netmon je aktuálně mimo podporu, můžete Nástroj Wireshark využít jako stejný.

Když se pokusíte o **8.8.8.8 888** s shromažďovaným trasováním Netmon, zobrazí se pod trasováním následující:

![Netmon trasování 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Netmon trasování 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

To znamená, že nemůžete na straně serveru **8.8.8.8** vytvořit připojení TCP na základě portu **888**, takže uvidíte dva další balíčky **SynReTransmit** . Vzhledem k tomu, že source **hostitel2** se nepovedlo vytvořit připojení k **8.8.8.8** v prvním balíčku, zůstane při vytváření připojení.

> [!TIP]
> - Můžete kliknout na možnost **Load Filter**  ->  **Standard Filter**  ->  **adresy**  ->  **IPv4**.
> - Zadejte **IPv4. Address = = 8.8.8.8** as Filter a klikněte na **použít**. Pak se zobrazí jenom komunikace z místního počítače do cílového **8.8.8.8**.

![filtrovat adresy 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![filtrovat adresy 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Níže uvedený příklad ukazuje, jak dobrý scénář vypadá. 

- Pokud služba Telnet **8.8.8.8 53** funguje bez jakýchkoli potíží, uvidíte, že se provede metoda HANDSHAKE protokolu TCP 3 a pak se relace dokončí s metodou HANDSHAKE protokolu TCP 4.

    ![dobrý příklad scénáře 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![dobrý příklad scénáře 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- V závislosti na výše uvedené signalizaci TCP 3 se můžete podívat pod pracovní postup:

    ![Pracovní postup handshake protokolu TCP 3](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- Metoda handshake protokolu TCP 4 pro dokončení relace a její pracovní postup se zobrazí následujícím způsobem:

    ![Protokol handshake protokolu TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Pracovní postup handshake protokolu TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


## <a name="self-hosted-ir-sharing"></a>Sdílení místního prostředí IR

### <a name="share-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Sdílení prostředí IR v místním prostředí v jiném tenantovi se nepodporuje. 

#### <a name="symptoms"></a>Příznaky

Můžete si všimnout jiných datových továren (v různých klientech) při pokusu o sdílení modulu IR v místním prostředí z Azure Data Factory uživatelského rozhraní, ale nemůžou sdílet místně hostované prostředí IR napříč datovými továrnami v různých klientech.

#### <a name="cause"></a>Příčina

Prostředí IR v místním prostředí nelze sdílet mezi klienty.


## <a name="next-steps"></a>Další kroky

Další pomoc při řešení potíží najdete v následujících zdrojích informací:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa k Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stránka s otázkou Microsoft Q&](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Fórum přetečení zásobníku pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Průvodce mapováním výkonu datových toků](concepts-data-flow-performance.md)
