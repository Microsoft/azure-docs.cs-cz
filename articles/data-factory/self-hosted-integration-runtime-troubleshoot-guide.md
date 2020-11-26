---
title: Řešení potíží s místním prostředím Integration runtime v Azure Data Factory
description: Přečtěte si, jak řešit problémy s místním hostováním prostředí Integration runtime v Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/17/2020
ms.author: lle
ms.openlocfilehash: 8195c4d072acce5345fa9752f97713aed22d962f
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296950"
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

1. Můžete zvolit protokoly, které chcete odeslat. V případě prostředí *IR* v místním prostředí můžete nahrávat protokoly týkající se neúspěšné aktivity nebo všech protokolů v uzlu IR v místním prostředí. Pro *sdílené infračervené* prostředí můžete nahrávat jenom protokoly týkající se neúspěšné aktivity.

    ![Zvolit protokoly](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Po nahrání protokolů Udržujte záznam ID sestavy, pokud k vyřešení problému potřebujete další pomoc.

    ![Odesílání protokolů](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Žádosti o zobrazení a nahrání protokolu se spustí ve všech online autohostovaných instancích IR. Ujistěte se prosím, že všechny chybějící protokoly jsou online u všech místně hostovaných instancí IR. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Obecné selhání nebo chyba místního prostředí IR

### <a name="out-of-memory-issue"></a>Nedostatek paměti – problém

#### <a name="symptoms"></a>Příznaky

K problému "OutOfMemoryException" dochází při pokusu o spuštění aktivity vyhledávání s propojeným IR nebo místním prostředím IR.

#### <a name="cause"></a>Příčina

Nová aktivita se může naplnit s problémem OOM (OutOfMemory), pokud počítač s infračerveným signálem momentálně využívá vysoké využití paměti. Příčinou může být velký rozsah souběžného spouštění aktivit a chyba je záměrné.

#### <a name="resolution"></a>Řešení

Zkontrolujte využití prostředků a souběžné spouštění aktivit v uzlu IR. Upravte interní a aktivační čas spuštění aktivit, aby nedocházelo k příliš velkému spouštění na stejném uzlu IR.


### <a name="tlsssl-certificate-issue"></a>Problém s certifikátem TLS/SSL

#### <a name="symptoms"></a>Příznaky

Při pokusu o povolení certifikátu TLS/SSL (rozšířené nastavení) v nástroji **Self-hosted IR Configuration Manager** -> **Vzdálený přístup z intranetu** se po výběru certifikátu TLS/SSL zobrazí následující chyba:

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

Ve výše uvedeném případě uživatel používá certifikát s poslední položkou microsoft.com.

#### <a name="cause"></a>Příčina

Jedná se o známý problém s technologií WCF: Při ověřování TLS/SSL technologií WCF se kontroluje pouze poslední název DNS v síti SAN. 

#### <a name="resolution"></a>Řešení

Certifikáty se zástupnými znaky se podporují v místním prostředí IR služby Azure Data Factory v2. K tomuto problému obvykle dochází kvůli nesprávnému certifikátu SSL. Poslední název DNS v síti SAN musí být platný. Při ověřování postupujte následovně. 
1.  Otevřete konzolu pro správu, v podrobnostech certifikátu překontrolujte *alternativní název* *subjektu i* předmět. V takovém případě není například poslední položka v *alternativním názvu předmětu*, která je "DNS Name = Microsoft.com.com", legitimní.
2.  Požádejte společnost o problém s certifikátem, aby odstranila nesprávný název DNS.

### <a name="concurrent-jobs-limit-issue"></a>Problém s limitem souběžných úloh

#### <a name="symptoms"></a>Příznaky

Při pokusu o zvýšení limitu souběžných úloh v uživatelském rozhraní Azure Data Factory dojde k trvalému zablokování ve stavu *Probíhá aktualizace*.
Maximální hodnota počtu souběžných úloh byla nastavená na 24 a chcete tento počet navýšit, aby se úlohy mohly spouštět rychleji. Minimální hodnota, kterou můžete zadat, je 3 a maximální hodnota, kterou můžete zadat, je 32. Zvýšili jste hodnotu z 24 na 32 a kliknete na tlačítko *aktualizovat* v uživatelském rozhraní, které se zablokovalo při *aktualizaci* , jak vidíte níže. Po aktualizaci se zákazníkovi stále zobrazuje hodnota 24 a aktualizace na hodnotu 32 neproběhla.

![Stav aktualizace](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Příčina

Pro toto nastavení platí omezení, protože jeho hodnota závisí na logických jádrech a paměti počítače – stačí ji upravit na nižší hodnotu (například 24) a podívat se na výsledek.

> [!TIP] 
> - Podrobnosti o tom, co je počet logických jader a jak zjistit počet jader v našem počítači, najdete v [tomto článku](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> - Podrobnosti o výpočtu matematického protokolu. log najdete v [tomto článku](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>Problém s certifikátem HA SSL místního prostředí IR

#### <a name="symptoms"></a>Příznaky

Pracovní uzel místního prostředí IR nahlásil následující chybu:

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>Příčina

Při řešení případů souvisejících s ověřováním SSL/TLS metodou handshake můžeme narazit na některé problémy související s ověřováním řetězu certifikátů. 

#### <a name="resolution"></a>Řešení

- Tady je rychlý a intuitivní způsob řešení potíží při sestavení řetězu certifikátů X. 509.
 
    1. Exportujte certifikát, který je potřeba ověřit. Přejděte do správy certifikátů počítače, vyhledejte certifikát, který chcete zkontrolovat, a klikněte pravým tlačítkem na **Všechny úlohy** -> **Exportovat**.
    
        ![Exportovat úlohy](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Zkopírujte exportovaný certifikát do klientského počítače. 
    3. Na straně klienta spusťte na příkazovém řádku následující příkaz. Ujistěte se, že jste nahradili následující *\<certificate path>* a *\<output txt file path>* zástupné symboly souvisejícími cestami.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Příklad:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Ve výstupním souboru TXT zkontrolujte, jestli nedošlo k nějaké chybě. Souhrn chyb najdete na konci souboru TXT.

        Příklad: 

        ![Souhrn chyb](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Pokud se nezobrazí žádná chyba na konci souboru protokolu, jak je znázorněno níže, můžete v klientském počítači zvážit úspěšné sestavení řetězce certifikátů.
        
        ![V souboru protokolu není žádná chyba.](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Pokud je v souboru certifikátu nakonfigurován AIA, CDP a OCSP. Můžeme je vymezit intuitivnějším způsobem.
 
    1. Tyto informace můžete získat tak, že zkontrolujete podrobnosti certifikátu.
    
        ![Podrobnosti certifikátu](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. Spusťte následující příkaz. Ujistěte se, že jste nahradili *\<certificate path>* zástupný symbol se související cestou certifikátu.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. Pak se otevře **nástroj pro načtení adresy URL**. Certifikáty od AIA, CDP a OCSP můžete ověřit kliknutím na tlačítko **Načíst**.

        ![Tlačítko pro načtení](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        Řetěz certifikátů je možné úspěšně sestavit, pokud je certifikát od AIA ověřený a pokud je certifikát od CDP nebo OCSP ověřený.

        Pokud při načítání AIA nebo CDP dojde k selhání, ve spolupráci se síťovým týmem připravte klientský počítač na připojení k cílové adrese URL. Stačí, když je možné ověřit cestu HTTP nebo cestu LDAP.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>Místnímu prostředí IR se nepodařilo načíst soubor nebo sestavení

#### <a name="symptoms"></a>Příznaky

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
Příklad: 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>Příčina

Pokud převezmete monitorování procesů, můžete zobrazit následující výsledek:

[![Monitor procesů](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> Filtr můžete nastavit tak, jak je znázorněno na obrázku obrazovky.
> Oznamujeme, že knihovna DLL **System. ValueTuple** není umístěná ve složce v mezipaměti GAC nebo v adresáři *c:\Program Files\microsoft Integration Runtime\4.0\Gateway* nebo ve složce *c:\Program Files\Microsoft integr Runtime\4.0\Shared* .
> V zásadě platí, že se soubor DLL nejprve načítá ze složky *GAC*, pak ze složky *Shared* a nakonec ze složky *Gateway*. Proto můžete soubor DLL umístit do libovolné složky, což může být užitečné.

![Nastavení filtrů](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Řešení

Zjistíte, že **System.ValueTuple.dll** se nachází ve složce *C:\Program Files\Microsoft integr Runtime\4.0\Gateway\DataScan* Folder. Problém vyřešte tak, že zkopírujete **System.ValueTuple.dll** do složky *C:\Program Files\Microsoft Runtime\4.0\Gateway Integration* .

Stejným způsobem můžete vyřešit i další problémy s chybějícími soubory nebo sestaveními.

#### <a name="more-information"></a>Další informace

Důvodem, proč vidíte System.ValueTuple.dll pod *%windir%\Microsoft.NET\assembly* a *%windir%\assembly* je to, že se jedná o chování rozhraní .NET. 

Z chyby níže můžete zřetelně zobrazit *systém sestavení. ValueTuple* není. Takže k tomuto problému dochází, když se aplikace pokusí ověřit *System.ValueTuple.dll* sestavení.
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
Další informace o globální mezipaměti sestavení (GAC) najdete v [tomto článku](/dotnet/framework/app-domains/gac).


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>Audit chybějícího klíče místního prostředí IR

#### <a name="symptoms"></a>Příznaky

Místní prostředí Integration Runtime bez klíče náhle přejde do režimu offline a v protokolu událostí se zobrazí následující chybová zpráva: `Authentication Key is not assigned yet`

![Chybí ověřovací klíč.](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Příčina

- Na portálu je odstraněný uzel místního prostředí IR nebo logické místní prostředí IR.
- Provedla se čistá odinstalace.

#### <a name="resolution"></a>Řešení

Pokud se nepoužijí žádné z výše uvedených příčin, můžete přejít do složky: *%ProgramData%\Microsoft\Data Transfer\DataManagementGateway* a ověřit, zda je soubor s názvem **Konfigurace** odstraněn. Pokud je odstraněný, podle [těchto](https://www.netwrix.com/how_to_detect_who_deleted_file.html) pokynů proveďte audit a zjistěte, kdo soubor odstranil.

![Ověřit soubor konfigurací](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>Pomocí místního prostředí IR není možné přemostit dvě místní úložiště dat

#### <a name="symptoms"></a>Příznaky

Po vytvoření místních prostředí IR pro zdrojové i cílové úložiště dat chcete tato dvě prostředí IR propojit a dokončit kopírování. Pokud jsou úložiště dat nakonfigurovaná v různých virtuální sítě nebo nemůžou pochopit mechanismus brány, dojde k chybám, jako je: *ovladač zdroje nejde najít v cíli IR*; k *zdroji není přistupující cílovým IR*.
 
#### <a name="cause"></a>Příčina

Místní prostředí IR je navržené jako centrální uzel aktivity kopírování, ne jako klientský agent, kterého je potřeba nainstalovat pro každé úložiště dat.
 
Ve výše uvedeném případě by se propojená služba pro obě úložiště dat měla vytvořit se stejným prostředím IR a toto prostředí IR by mělo mít síťový přístup k oběma úložištím dat. Bez ohledu na to, jestli je prostředí IR nainstalované ve zdrojovém úložišti dat, v cílovém úložišti dat nebo na jiném počítači, pokud se vytvoří dvě propojené služby s různými prostředími IR a použijí se ve stejné aktivitě kopírování, použije se cílové prostředí IR a na počítači s cílovým prostředím IR musí být nainstalované ovladače pro obě úložiště dat.

#### <a name="resolution"></a>Řešení

Nainstalujte v cílovém prostředí IR ovladače pro zdroj i cíl a ujistěte se, že má přístup ke zdrojovému úložišti dat.
 
Pokud provoz nemůže procházet sítí mezi dvěma úložišti dat (například pokud jsou nakonfigurovaná ve dvou virtuálních sítích), není možné dokončit kopírování v rámci jedné aktivity, a to ani s nainstalovaným prostředím IR. V takovém případě můžete ve virtuální síti vytvořit dvě aktivity kopírování se dvěma prostředími IR: Jedno prostředí IR pro kopírování z prvního úložiště dat do služby Azure Blob Storage a druhé prostředí IR pro kopírování ze služby Azure Blob Storage do druhého úložiště dat. To může simulovat požadavek na použití prostředí IR k vytvoření přemostění, které propojí dvě odpojená úložiště dat.


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>Problém se synchronizací přihlašovacích údajů vede ke ztrátě přihlašovacích údajů v režimu vysoké dostupnosti

#### <a name="symptoms"></a>Příznaky

Přihlašovací údaje XXXXXXXXXX pro zdroj dat se odstranily z aktuálního uzlu prostředí Integration Runtime s datovou částí Když odstraníte službu propojení na webu Azure Portal nebo má úkol nesprávnou datovou část, vytvořte novou službu propojení s vašimi přihlašovacími údaji.

#### <a name="cause"></a>Příčina

Vaše místní prostředí IR je vytvořené v režimu vysoké dostupnosti se dvěma uzly, které však nejsou ve stavu synchronizace přihlašovacích údajů, což znamená, že se přihlašovací údaje uložené na uzlu dispečera nesynchronizují s dalšími pracovními uzly. Pokud dojde k převzetí služeb při selhání z uzlu dispečera na pracovní uzel, ale přihlašovací údaje existovaly pouze na předchozím uzlu dispečera, úloha při pokusu o přístup k přihlašovacím údajům selže a zobrazí se výše uvedená chyba.

#### <a name="resolution"></a>Řešení

Jediným způsobem, jak se vyhnout tomuto problému, je zajistit, aby tyto dva uzly byly ve stavu synchronizace přihlašovacích údajů. Jinak musíte pro nového dispečera zadat přihlašovací údaje znovu.


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>Kvůli chybějícímu privátnímu klíči nejde zvolit certifikát

#### <a name="symptoms"></a>Příznaky

1.  Importovali jste do úložiště certifikátů soubor PFX.
2.  Při výběru certifikátu v uživatelském rozhraní nástroje IR Configuration Manager se zobrazí následující chyba:

    ![Chybí privátní klíč.](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Příčina

- Uživatelský účet má nízká oprávnění a nemá přístup k privátnímu klíči.
- Certifikát se vygeneroval jako certifikát podpisu, a ne jako certifikát výměny klíčů.

#### <a name="resolution"></a>Řešení

1.  Použijte k práci s uživatelským rozhraním privilegovaný účet s přístupem k privátnímu klíči.
2.  Importujte certifikát spuštěním následujícího příkazu:
    
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
            1. Přejděte do instalační složky prostředí IR v místním prostředí, přejděte do složky: *Microsoft Integration Runtime\4.0\Shared*.
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

Od vydání *Integration Runtime 3,0* se odebralo tlačítko **registrace** na existujícím uzlu Integration runtime, aby se povolilo čištění a bezpečnější prostředí. Pokud je uzel zaregistrovaný k nějakému modulu runtime integrace (bez ohledu na to, jestli je online) a chcete ho znovu zaregistrovat k jinému modulu runtime integrace, musíte předchozí uzel odinstalovat a pak nainstalovat a zaregistrovat nový uzel.

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


### <a name="ir-service-account-failed-to-fetch-certificate-access"></a>Účtu služby IR se nepodařilo načíst přístup k certifikátu.

#### <a name="symptoms"></a>Příznaky

Při instalaci prostředí IR v místním prostředí pomocí Microsoft Integration Runtime Configuration Manageru se vygeneruje certifikát s důvěryhodnou certifikační autoritou. Certifikát nebylo možné použít k šifrování komunikace mezi dvěma uzly. 

Informace o chybě jsou uvedené níže: 

`Failed to change Intranet communication encryption mode: Failed to grant Integration Runtime service account the access of to the certificate 'XXXXXXXXXX'. Error code 103`

![Nepovedlo se udělit přístup k certifikátu účtu služby IR](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>Příčina

Certifikát používá klíč KSP (Zprostředkovatel úložiště klíčů), což ještě není podporováno. SHIR podporuje teprve zatím certifikát CSP (Cryptographic Service Provider).

#### <a name="resolution"></a>Řešení

Pro tento případ se doporučuje certifikát CSP.

**Řešení 1:** K importu certifikátu použijte následující příkaz:

```
Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx 
```

![Použití příkazu certutil](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**Řešení 2:** Převod certifikátů:

OpenSSL PKCS12-in .\xxxx.pfx. \ xxxx_new. pem-Password Pass:*\<EnterPassword>*

OpenSSL PKCS12-export-in. \ xxxx_new. pem-out xxxx_new. pfx

Před a po převodu:

![Před změnou certifikátu](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![Po změně certifikátu](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### <a name="self-hosted-integration-runtime-version-5x"></a>Samoobslužná Integration Runtime verze 5. x
Pro upgrade na verzi 5. x z Azure Data Factory místního prostředí Integration runtime vyžadujeme **rozhraní .NET Framework runtime 4.7.2** nebo novější. Na stránce stáhnout se stáhnou odkazy na nejnovější verzi 4. x a nejnovější dvě verze 5. x. 


Pro zákazníky ADF v2:
- Pokud je funkce Automatické aktualizace zapnutá a už jste upgradovali modul runtime rozhraní .NET Framework na 4.7.2 nebo vyšší, pak se místní prostředí Integration runtime automaticky upgraduje na nejnovější verzi 5. x.
- Pokud je funkce Automatické aktualizace zapnutá a neupgradovali jste modul runtime rozhraní .NET Framework na 4.7.2 nebo vyšší, pak se místní prostředí Integration runtime automaticky neupgraduje na nejnovější verzi 5. x. Místní prostředí Integration runtime zůstane v aktuální verzi 4. x. Na portálu a v místním prostředí Integration runtime se zobrazí upozornění na upgrade modulu runtime rozhraní .NET Framework.
- Pokud je automatická aktualizace vypnutá a už jste upgradovali modul runtime rozhraní .NET Framework na 4.7.2 nebo novější, můžete si ručně stáhnout nejnovější 5. x a nainstalovat ho do svého počítače.
- Pokud je automatická aktualizace vypnutá a váš modul runtime rozhraní .NET Framework jste neupgradovali na 4.7.2 nebo novější. Když se pokusíte ručně nainstalovat SHIR 5. x a zaregistrovat klíč, budete nejprve muset upgradovat modul runtime rozhraní .NET Framework.


Pro zákazníky ADF V1:
- Místní prostředí Integration runtime 5. X nepodporuje ADF v1.
- Místní prostředí Integration runtime se automaticky upgraduje na nejnovější verzi 4. x. A poslední verze 4. x nebude vypršet. 
- Pokud se pokusíte ručně nainstalovat místní prostředí Integration runtime 5. x a zaregistrovat klíč, budete si dozvěděli, že v místním prostředí Integration runtime 5. x se nepodporuje v1.


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
   > Adresa URL služby se může lišit v závislosti na umístění Data Factory. Adresu URL služby najdete v části připojení k **uživatelským rozhraním ADF**  >  **Connections**  >  **Integration modul runtime** upravit uzly v místním prostředí  >  **IR**  >  **Nodes**  >  **Zobrazit adresy URL služby**.
            
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
> *    Ověřte, zda proxy server musí být vloženy do seznamu bezpečných příjemců. Pokud ano, ujistěte se, že jsou [tyto domény](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) v seznamu bezpečných příjemců.
> *    Ověřte, jestli je certifikát TLS/SSL wu2.frontend.clouddatahub.net/na proxy server důvěryhodný.
> *    Pokud na proxy serveru používáte ověřování pomocí služby Active Directory, změňte účet služby na uživatelský účet, který bude mít přístup k proxy serveru jako služba Integration Runtime.

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Chybová zpráva: uzel Integration runtime (v místním prostředí)/logický SHIR je v neaktivním stavu (s omezením).

#### <a name="cause"></a>Příčina 

Hostující uzel integrovaného modulu runtime může mít **neaktivní** stav, jak je znázorněno na následujícím snímku obrazovky:

![Neaktivní Self-Hosted uzel IR](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

K tomuto chování dochází, když uzly nemůžou vzájemně komunikovat.

#### <a name="resolution"></a>Řešení

1. Přihlaste se k virtuálnímu počítači hostovanému na uzlu. V části **protokoly aplikací a služeb**  >  **Integration runtime** otevřete Prohlížeč událostí a vyfiltrujte všechny protokoly chyb.

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


### <a name="receiving-email-to-update-the-network-configuration-to-allow-communication-with-new-ip-addresses"></a>Příjem e-mailů k aktualizaci konfigurace sítě, aby bylo možné komunikovat s novými IP adresami

#### <a name="email-notification-from-microsoft"></a>E-mailové oznámení od Microsoftu

Můžete obdržet e-mailové oznámení, které doporučuje aktualizovat konfiguraci sítě tak, aby umožňovalo komunikaci s novými IP adresami pro Azure Data Factory 8. listopadu 2020:

   ![E-mailové oznámení](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="how-to-determine-if-you-are-impacted-by-this-notification"></a>Jak zjistit, jestli to ovlivnilo toto oznámení

Toto oznámení má vliv na následující scénáře:
##### <a name="scenario-1-outbound-communication-from-self-hosted-integration-runtime-running-on-premises-behind-the-corporate-firewall"></a>Scénář 1: odchozí komunikace z Integration Runtime v místním prostředí, která běží místně za podnikovou bránou firewall
Jak zjistit, jestli máte vliv na:
- Nebudete mít vliv na definování pravidel brány firewall na základě názvů plně kvalifikovaného názvu domény pomocí přístupu popsaného v tomto dokumentu: [Konfigurace brány firewall a nastavení povolených seznamů pro IP adresu](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway).
- Pokud jste ale výslovně povolili seznam povolených IP adres pro odchozí IP adresy na podnikové bráně firewall, budete ovlivněni.

Akce, která se má provést, pokud máte vliv: upozorněte tým síťové infrastruktury, aby aktualizoval konfiguraci sítě tak, aby používal nejnovější Data Factory IP adresy od 8. listopadu 2020.  Pokud si chcete stáhnout nejnovější IP adresy, klikněte na [odkaz služby značky IP rozsah IP](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)adres ke stažení.

##### <a name="scenario-2-outbound-communication-from-self-hosted-integration-runtime-running-on-an-azure-vm-inside-customer-managed-azure-virtual-network"></a>Scénář 2: odchozí komunikace z Integration Runtime v místním prostředí, která běží na virtuálním počítači Azure v rámci zákaznické spravované virtuální sítě Azure
Jak zjistit, jestli máte vliv na:
- Ověřte, jestli máte v privátní síti žádná odchozí pravidla NSG, která obsahují Integration Runtime v místním prostředí. Pokud neexistují žádná odchozí omezení, nebude to mít žádný vliv.
- Pokud máte omezení odchozího pravidla, ověřte, zda používáte značku služby nebo ne. Pokud používáte značku služby, nemusíte nic měnit ani přidávat, protože nové rozsahy IP adres se nacházejí v rámci existující značky služby. 
 ![Kontrolu cíle](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)
- Pokud jste ale výslovně povolili seznam povolených IP adres v nastavení pravidel NSG ve službě Azure Virtual Network, budete ovlivněni.

Akce, která se má provést, pokud máte vliv: upozorněte tým síťové infrastruktury, aby aktualizoval pravidla NSG v konfiguraci virtuální sítě Azure tak, aby používal nejnovější Data Factory IP adresy od 8. listopadu 2020.  Pokud si chcete stáhnout nejnovější IP adresy, klikněte na [odkaz služby značky IP rozsah IP](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)adres ke stažení.

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-customer-managed-azure-virtual-network"></a>Scénář 3: odchozí komunikace z SSIS Integration Runtime ve službě Azure Virtual Network spravované zákazníkem
- Ověřte, jestli máte v privátní síti žádná odchozí pravidla NSG, která obsahují SSIS Integration Runtime. Pokud neexistují žádná odchozí omezení, nebude to mít žádný vliv.
- Pokud máte omezení odchozího pravidla, ověřte, zda používáte značku služby nebo ne. Pokud používáte značku služby, nemusíte nic měnit ani přidávat, protože nové rozsahy IP adres se nacházejí v rámci existující značky služby.
- Pokud jste ale výslovně povolili seznam povolených adres pro odchozí IP adresy v nastavení pravidel NSG ve virtuální síti Azure, budete ovlivněni.

Akce, která se má provést, pokud máte vliv: upozorněte tým síťové infrastruktury, aby aktualizoval pravidla NSG v konfiguraci virtuální sítě Azure tak, aby používal nejnovější Data Factory IP adresy od 8. listopadu 2020.  Pokud si chcete stáhnout nejnovější IP adresy, klikněte na [odkaz služby značky IP rozsah IP](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)adres ke stažení.

### <a name="could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Nepovedlo se vytvořit vztah důvěryhodnosti pro zabezpečený kanál SSLTLS. 

#### <a name="symptoms"></a>Příznaky

Místní prostředí IR se nemůže připojit ke službě ADF.

Když zkontrolujete protokol událostí SHIR nebo protokoly oznámení klienta v tabulce CustomLogEvent, bude nalezena následující chybová zpráva:

`The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.The remote certificate is invalid according to the validation procedure.`

Jak ověřit certifikát serveru služby ADF:

Nejjednodušší způsob je otevřít adresu URL služby ADF v prohlížeči, třeba otevřít https://eu.frontend.clouddatahub.net/ na počítači, kde je nainstalovaný SHIR, a pak zobrazit informace o certifikátu serveru:

  ![Zkontroluje certifikát serveru služby ADF.](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Ověřit cestu k certifikátu serveru](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>Příčina

Dva možné příčiny tohoto problému:

- Kořenová certifikační autorita certifikátu serveru služby ADF není v počítači, kde je nainstalován SHIR, důvěryhodná. 
- Ve svém prostředí používáte proxy server a certifikát serveru služby ADF je nahrazen proxy serverem, zatímco certifikát nahrazeného serveru není důvěryhodný pro počítač, na kterém je nainstalovaný SHIR.

#### <a name="resolution"></a>Řešení

- Z důvodu 1 se ujistěte, že je certifikát serveru ADF a jeho řetěz certifikátů důvěryhodný pro počítač, na kterém je nainstalovaný SHIR.
- Z důvodu 2 buď důvěřujete nahrazené kořenové certifikační autoritě na SHIR počítači, nebo nakonfigurujte proxy server tak, aby nahradil certifikát serveru ADF.

Podrobnosti o důvěřování certifikátu ve Windows najdete v [tomto článku](https://docs.microsoft.com/skype-sdk/sdn/articles/installing-the-trusted-root-certificate) .

#### <a name="additional-info"></a>Další informace
Zavádíme nový certifikát SSL, který je podepsaný z DigiCert, zkontrolujte prosím, jestli je globální kořen DigiCertu G2 v důvěryhodné kořenové certifikační autoritě.

  ![Globální kořenový adresář G2 DigiCert](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

Pokud ne, [Stáhněte si ho odsud.](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt ) 


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
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stránka s otázkou Microsoft Q&](/answers/topics/azure-data-factory.html)
*  [Fórum přetečení zásobníku pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Průvodce mapováním výkonu datových toků](concepts-data-flow-performance.md)