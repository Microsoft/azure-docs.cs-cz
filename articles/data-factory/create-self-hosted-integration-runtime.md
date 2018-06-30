---
title: Vytvoření vlastním hostováním integrace runtime v Azure Data Factory | Microsoft Docs
description: Naučte se vytvářet vlastním hostováním integrace runtime v Azure Data Factory, která umožňuje datové továrny pro přístup k úložišti dat v privátní síti.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: be2ac5427516bc29f87052667e4b44be52757f1e
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113149"
---
# <a name="how-to-create-and-configure-self-hosted-integration-runtime"></a>Jak vytvořit a nakonfigurovat Self-hosted integrace Runtime
Integrační modul Runtime (IR) je na výpočetní infrastruktuře používá k zajištění funkce integrace dat různých prostředích sítě Azure Data Factory. Podrobnosti o IR najdete v tématu [Přehled integrace modulu Runtime](concepts-integration-runtime.md).

Runtime vlastním hostováním integrace je schopen spuštění aktivity kopírování mezi daty cloudové úložiště a úložiště dat v privátní síti a odeslání aktivit transformace výpočetní prostředky v místní nebo Azure Virtual Network. Potřeb runtime vlastním hostováním integrace nainstalujte na virtuální počítač v privátní síti nebo na místním počítači.  

Tento dokument uvádí, jak můžete vytvořit a nakonfigurovat Self-hosted infračerveného signálu.

## <a name="high-level-steps-to-install-self-hosted-ir"></a>Podrobný postup instalace vlastním hostováním reakcí na Incidenty
1.  Vytvořte vlastním hostováním integrace modulu runtime. Tady je příklad PowerShell:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
2.  Stáhněte a nainstalujte modul runtime vlastním hostováním integrace (v místním počítači).
3.  Načtení ověřovací klíč a zaregistrujte runtime vlastním hostováním integrace s klíčem. Tady je příklad PowerShell:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="command-flow-and-data-flow"></a>Příkaz a tok dat
Když přesouváte data mezi místními a cloudovými, aktivita vlastním hostováním integrace runtime používá k přenosu dat z místního zdroje dat do cloudu a naopak.

Zde je podrobný datový tok pro souhrn kroky pro kopírování s vlastním hostováním IR:

![Podrobný přehled](media\create-self-hosted-integration-runtime\high-level-overview.png)

1. Data developer vytvoří runtime vlastním hostováním integrace v rámci služby Azure data factory pomocí rutiny prostředí PowerShell. Na portálu Azure v současné době nepodporuje tuto funkci.
2. Data developer vytvoří propojené služby pro místnímu úložišti dat zadáním instanci vlastním hostováním integrace modulu runtime, která měla použít pro připojení k úložištím dat. Jako součást nastavení propojené služby, developer dat používá aplikace, správce přihlašovacích údajů. (v současné době není podporováno) pro nastavení typy ověřování a přihlašovací údaje. Dialogové okno přihlašovací údaje správce aplikace komunikuje s úložištěm dat k testování připojení a vlastním hostováním integrace modulu runtime uložit přihlašovací údaje.
4.  Integrace s vlastním hostováním runtime uzel šifruje pověření pomocí Windows Data Protection aplikace programovací rozhraní (DPAPI) a uloží ho místně. Pokud pro zajištění vysoké dostupnosti jsou nastavené více uzlů, přihlašovací údaje jsou synchronizovány další mezi ostatní uzly. Každý uzel ji zašifruje pomocí rozhraní DPAPI a uložený místně. Synchronizace přihlašovacích údajů je transparentní pro vývojáře data a jsou zpracována vlastním hostováním infračerveného signálu.    
5.  Služba data Factory komunikuje s vlastním hostováním integrace modulu runtime pro plánování a správu úloh prostřednictvím **řídicí kanál** používající frontou sdílené Azure service bus. Když úlohu aktivity musí být spuštěn, Data Factory zařadí do fronty požadavek spolu s žádné přihlašovací údaje (v případě, že přihlašovací údaje se neukládají již na vlastním hostováním integrace runtime). Modul runtime vlastním hostováním integrace se spustí úlohu po dotazování fronty.
6.  Integrace s vlastním hostováním runtime zkopíruje data z místního úložiště do cloudového úložiště, nebo naopak v závislosti na konfiguraci aktivitě kopírování v datovém kanálu. Pro tento krok runtime vlastním hostováním integrace přímo komunikuje s služby cloudového úložiště, jako je například úložiště objektů Blob Azure přes zabezpečený kanál (HTTPS).

## <a name="considerations-for-using-self-hosted-ir"></a>Důležité informace týkající se použití vlastním hostováním reakcí na Incidenty

- Modul runtime jednom vlastním hostováním integrace lze použít pro více zdrojů dat na místě. Ale **runtime jednom vlastním hostováním integrace je vázaný na pouze jeden objekt pro vytváření dat Azure** a nemohou být sdíleny s jinou služby data factory.
- Můžete mít **jenom jednu instanci vlastním hostováním integrace runtime** nainstalované na jednom počítači. Předpokládejme, že, máte dvě datové továrny, které potřebují přístup k místním zdrojům dat, musíte nainstalovat modul runtime vlastním hostováním integrace na dva místní počítače. Jinými slovy je integrace s vlastním hostováním runtime vázaný na konkrétní data factory
- **Vlastním hostováním integrace runtime nemusí být ve stejném počítači jako zdroj dat**. Však s hostovanou na vlastním integrace runtime blíže ke zdroji dat zkracuje čas pro připojení ke zdroji dat vlastním hostováním integrace modulu runtime. Doporučujeme nainstalovat modul runtime vlastním hostováním integrace na počítači, který se liší od verze zdroje dat místního hostitele. Když vlastním hostováním integrace modulu runtime a zdroj dat na různých počítačích, modul runtime vlastním hostováním integrace není pokouší o prostředky se zdrojem dat.
- Můžete mít **více vlastním hostováním integrační moduly runtime na různé počítače připojení ke zdroji dat stejnou místní**. Například můžete mít dvě vlastním hostováním integrace runtime obsluhující dvě datové továrny ale stejný místní zdroj dat není zaregistrována datové továrny.
- Pokud je již nainstalovaná na vašem počítači slouží **Power BI** scénář, nainstalovat **runtime samostatné vlastním hostováním integrace pro Azure Data Factory** na jiném počítači.
- Integrace s vlastním hostováním runtime musí použít pro podporu integrace dat v rámci virtuální sítě Azure.
- Váš zdroj dat považovat za zdroj dat na místě, (který je za bránou firewall) i při použití **ExpressRoute**. Navázat připojení mezi službou a zdroji dat pomocí modulu runtime vlastním hostováním integrace.
- I v případě, že je úložiště dat v cloudu, je nutné použít modul runtime vlastním hostováním integrace **virtuálního počítače Azure IaaS**.
- Úlohy mohou selhat v modulu Runtime integrace Self-hosted nainstalovaná v systému Windows Server, na které se standardem FIPS je povolené šifrování. Chcete-li tento problém obejít, zakažte kompatibilní se standardem FIPS šifrování na serveru. Chcete-li zakázat kompatibilní se standardem FIPS šifrování, změňte následující hodnotu registru od 1 (povoleno) na hodnotu 0 (zakázáno): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Požadavky

- U podporovaných **operačního systému** jsou verze Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. Instalace modulu runtime vlastním hostováním integrace na **řadič domény nepodporuje**.
- **Rozhraní .NET framework 4.6.1 nebo vyšší** je vyžadován. Pokud k instalaci modulu runtime vlastním hostováním integrace na počítače s Windows 7, nainstalujte rozhraní .NET Framework 4.6.1 nebo novější. V tématu [požadavky na systém rozhraní .NET Framework](/dotnet/framework/get-started/system-requirements) podrobnosti.
- Doporučené **konfigurace** pro modul runtime s vlastním hostováním integrace počítač je alespoň 2 GHz, 4 jádra, 8 GB paměti RAM a 80-GB místa na disku.
- Pokud hostitelský počítač přejde do režimu spánku, modul runtime vlastním hostováním integrace neodpovídá na požadavky na data. Před instalací modulu runtime vlastním hostováním integrace proto nakonfigurujte příslušné napájení v počítači. Pokud je počítač nakonfigurovaný do režimu spánku, vyzve k instalaci modulu runtime vlastním hostováním integrace zprávu.
- Musíte být správce na počítači k instalaci a konfiguraci modulu runtime vlastním hostováním integrace úspěšně.
- Jako běh aktivit kopie dojít na konkrétní frekvenci, využití prostředků (procesor, paměť) na počítači také používá se stejný vzor s ve špičce a doby nečinnosti. Využití prostředků závisí také výraznou na množství dat přesouvá. Více úloh kopie jsou v průběhu, uvidíte zvedla během špiček využití prostředků.

## <a name="installation-best-practices"></a>Osvědčené postupy instalace
Modul runtime vlastním hostováním integrace může být instalován stahování balíčku MSI Instalační program z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). V tématu [přesun dat mezi místními a cloudovými článku](tutorial-hybrid-copy-powershell.md) podrobné pokyny.

- Konfigurovat schéma napájení na hostitelském počítači pro modul runtime s vlastním hostováním integrace tak, aby tento počítač není režimu hibernace. Pokud hostitelský počítač přejde do režimu spánku, se změní offline vlastním hostováním integrace modulu runtime.
- Přihlašovací údaje spojené s vlastním hostováním integrace runtime pravidelně zálohujte.

## <a name="install-and-register-self-hosted-ir-from-download-center"></a>Instalace a registrace Self-hosted IR ze služby Stažení softwaru

1. Přejděte na [stránky pro stažení Microsoft integrace Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
2. Klikněte na tlačítko **Stáhnout**, vyberte příslušnou verzi (**32-bit** vs. **64-bit**) a klikněte na tlačítko **Další**.
3. Spustit **MSI** přímo nebo uložte ho na pevný disk a spustit.
4. Na **úvodní** vyberte **jazyk** klikněte na tlačítko **Další**.
5. **Přijměte** licenční smlouvu a klikněte na tlačítko **Další**.
6. Vyberte **složky** instalaci modulu runtime vlastním hostováním integrace a klikněte na tlačítko **Další**.
7. Na **připravené k instalaci** klikněte na tlačítko **nainstalovat**.
8. Klikněte na tlačítko **Dokončit** k dokončení instalace.
9. Získání klíče ověřování pomocí Azure PowerShell. Příklad prostředí PowerShell pro načítání ověřovací klíč.

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Na **zaregistrovat integrace Runtime (samoobslužně hostované)** stránku nástroje Microsoft integrace modulu Runtime Configuration Manager spuštěn na počítači, proveďte následující kroky:
    1. Vložení **ověřovací klíč** v části textu.
    2. Volitelně klikněte na **zobrazit ověřovací klíč** zobrazíte text klíče.
    3. Klikněte na tlačítko **zaregistrovat**.


## <a name="high-availability-and-scalability"></a>Vysoké dostupnosti a škálovatelnosti
Modul Runtime Self-hosted integrace může být přidružena k několika místním počítačům. Tyto počítače se označují jako uzly. Může mít až čtyři uzly, které jsou přidružené k modulu Runtime Self-hosted integrace. Výhody s více uzly (místní počítače s nainstalovanou bránu) pro logické brány jsou:
1. Vyšší dostupnost Self-hosted integrace Runtime, aby byla již jediný bod selhání v svoji velkých objemů dat řešení nebo cloudových dat integraci s Azure Data Factory, zajištění kontinuity s maximálně 4 uzly.
2. Vylepšený výkon a propustnost při přesouvání dat mezi místními a cloudovými datová úložiště. Další informace získáte [porovnání výkonu](copy-activity-performance.md).

Jednoduše instalací softwaru Self-hosted integrace Runtime z můžete přidružit více uzlů [centra stahování softwaru společnosti](https://www.microsoft.com/download/details.aspx?id=39717) a její registrací a to buď ověřovací klíče získané z Nové AzureRmDataFactoryV2IntegrationRuntimeKey rutiny, jak je popsáno v [kurzu](tutorial-hybrid-copy-powershell.md)

> [!NOTE]
> Není nutné k vytvoření nových Self-hosted integrace Runtime pro přidružení každý uzel. Můžete nainstalovat modul runtime vlastním hostováním integrace na jiném počítači a zaregistrovat ji pomocí stejného klíče ověřování. 

> [!NOTE]
> Před přidáním jiný uzel pro **vysokou dostupnost a škálovatelnost**, zkontrolujte, zda **'vzdálený přístup k intranetu,** možnost je **povoleno** na uzlu 1 (Microsoft Integrační modul Runtime Configuration Manager -> Nastavení -> vzdáleného přístupu k intranetu). 

### <a name="tlsssl-certificate-requirements"></a>Požadavky na certifikát protokolu TLS/SSL
Tady jsou požadavky na certifikát TLS/SSL, který se používá k zabezpečení komunikace mezi integrace modulu runtime uzly:

- Certifikát musí být veřejně důvěryhodné X509 v3 certifikátu. Doporučujeme vám, že používáte certifikáty vydané veřejnou (třetích stran) certifikační autoritou (CA).
- Tento certifikát musí důvěřovat každý uzel integrace modulu runtime.
- Zástupný znak certifikáty jsou podporovány. Pokud je název vaší plně kvalifikovaný název domény **node1.domain.contoso.com**, můžete použít ***. domain.contoso.com** jako název subjektu certifikátu.
- Vzhledem k tomu, že se použije pouze poslední položky alternativní názvy subjektu a všechny ostatní bude ignorován kvůli aktuálním omezením, nedoporučuje se používat certifikátů SAN. Například máte certifikát SAN, jejichž SAN jsou **node1.domain.contoso.com** a **node2.domain.contoso.com**, můžete použít pouze tohoto certifikátu na počítače, jejichž plně kvalifikovaný název domény **node2.domain.contoso.com**.
- Podporuje všechny klíče velikost podporovaná technologií Windows Server 2012 R2 pro certifikáty SSL.
- Certifikát pomocí CNG klíče nejsou podporovány. Doesrted DoesDoes nepodporuje certifikáty, které používají klíči CNG.

## <a name="system-tray-icons-notifications"></a>Systémové ikony oznamovací oblasti nebo oznámení
Pokud přesuňte ukazatel na systému panelu ikonu nebo oznámení, můžete najít podrobnosti o stavu vlastním hostováním integrace modulu runtime.

![Upozornění na hlavním panelu systému](media\create-self-hosted-integration-runtime\system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Porty a brány firewall
Existují dvě brány firewall, je potřeba zvážit: **podniková brána firewall** systémem směrovači centrální organizace, a **brány Windows firewall** nakonfigurovaný jako démon procesu v místním počítači, kde Integrace s vlastním hostováním runtime je nainstalována.

![Brána firewall](media\create-self-hosted-integration-runtime\firewall.png)

V **podniková brána firewall** úrovně, je nutné nakonfigurovat následující domény a odchozí porty:

Názvy domén | Porty | Popis
------------ | ----- | ------------
*.servicebus.windows.net | 443, 80 | Používá ke komunikaci s back-end služba pro přesun dat
*.core.windows.net | 443 | Použít pro kopírování připravený pomocí objektů Blob v Azure (Pokud je nakonfigurováno)
*.frontend.clouddatahub.net | 443 | Používá ke komunikaci s back-end služba pro přesun dat

V **brány Windows firewall** úrovni (úroveň počítače), jsou obvykle povolené tyto Odchozí porty. Pokud není, můžete nakonfigurovat domén a porty odpovídajícím způsobem na hostovanou na vlastním integrace modulu runtime počítače.

> [!NOTE]
> Na základě vaší zdroje / jímky, možná budete muset povolených dalších domén a odchozí porty v bráně firewall podnikové a Windows.
>
> Pro některé databáze cloudu (například: databáze SQL Azure, Azure Data Lake, atd.), budete muset seznam povolených adres IP adresu počítače runtime vlastním hostováním integrace na jejich konfiguraci brány firewall.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopírování dat z zdroj jímka
Zkontrolujte, zda pravidla brány firewall jsou povolené správně na podnikové brány firewall, brána Windows firewall na počítače s vlastním hostováním integrace modulu runtime a ukládat data sám sebe. Tato pravidla povolíte vlastním hostováním integrace modulu runtime pro připojení k obou zdroj a jímka úspěšně. Povolení pravidel pro každou úložiště dat, který je zahrnut v operaci kopírování.

Například pro kopírování ze **místní datové úložiště jímky Azure SQL Database nebo jímky Azure SQL Data Warehouse**, proveďte následující kroky:

- Povolit odchozí **TCP** komunikaci na portu **1433** pro bránu Windows firewall a podniková brána firewall.
- Konfigurace nastavení brány firewall serveru Azure SQL přidat IP adresu počítače runtime vlastním hostováním integrace do seznamu povolených IP adres.

> [!NOTE]
> Pokud brána firewall nedovoluje odchozí port 1433, integrace s vlastním hostováním runtime nemají přímý přístup Azure SQL. V takovém případě můžete použít [připravený kopie](copy-activity-performance.md) do SQL Azure Database nebo datového skladu SQL Azure. V tomto scénáři by pro přesun dat vyžadují jenom protokolu HTTPS (port 443).


## <a name="proxy-server-considerations"></a>Důležité informace o proxy serveru
Pokud vaše podnikové síťové prostředí používá proxy server pro přístup k Internetu, nakonfigurujte příslušná nastavení proxy serveru použijte vlastním hostováním integrace modulu runtime. Během fáze počáteční registrace můžete nastavit proxy server.

![Zadejte proxy server](media\create-self-hosted-integration-runtime\specify-proxy.png)

Integrace s vlastním hostováním runtime používá proxy server pro připojení ke cloudové službě. Klikněte na odkaz změnit během počáteční instalace. Zobrazí dialogové okno nastavení proxy serveru.

![Sada proxy](media\create-self-hosted-integration-runtime\set-http-proxy.png)

Existují tři možnosti konfigurace:

- **Nepoužívejte proxy**: integrace s vlastním hostováním runtime pro připojení ke cloudovým službám explicitně nepoužívá jakýkoli proxy server.
- **Použít proxy server systému**: hostovanou na vlastním integrace modulu runtime používá proxy nastavení, který je nakonfigurován diahost.exe.config a diawp.exe.config. Pokud žádný proxy server je nakonfigurován v diahost.exe.config a diawp.exe.config, vlastním hostováním integrace runtime připojí ke cloudové službě přímo bez proxy.
- **Používat vlastní proxy server**: Konfigurace nastavení pro modul runtime vlastním hostováním integrace, místo použití konfigurace v diahost.exe.config a diawp.exe.config proxy protokolu HTTP. Adresa a Port jsou povinné. Uživatelské jméno a heslo jsou volitelné v závislosti na nastavení vašeho proxy ověřování. Všechna nastavení jsou zašifrované pomocí rozhraní Windows DPAPI na vlastním hostováním integrace runtime a jsou uložené místně na počítači.

Modul runtime integrace hostitelská služba se automaticky restartuje, po uložení aktualizované nastavení proxy serveru.

Po integraci s vlastním hostováním runtime byl úspěšně zaregistrován, pokud chcete zobrazit nebo aktualizovat nastavení proxy serveru, použijte integrace modulu Runtime Configuration Manager.

1.  Spusťte **nástroje Configuration Manager Microsoft integrace modulu Runtime**.
2.  Přepněte na kartu **Nastavení**.
3.  Klikněte na tlačítko **změnu** na odkaz v **server Proxy protokolu HTTP** části spustíte **nastavení proxy serveru HTTP** dialogové okno.
4.  Po kliknutí **Další** tlačítko se zobrazí dialog s upozorněním žádostí o oprávnění k uložení nastavení proxy serveru a restartujte službu hostitel běhu integrace.

Můžete zobrazit a aktualizovat server proxy protokolu HTTP pomocí nástroje Configuration Manager.

![Zobrazení proxy](media\create-self-hosted-integration-runtime\view-proxy.png)

> [!NOTE]
> Pokud jste nastavili proxy server pomocí ověřování NTLM, integrace runtime hostitelská služba běží pod účtem domény. Pokud změníte heslo pro účet domény později, musíte aktualizovat nastavení konfigurace pro službu a restartujte ji odpovídajícím způsobem. Kvůli tomuto požadavku, doporučujeme používat vyhrazený doménový účet pro přístup k proxy serveru, který nevyžaduje často aktualizovat heslo.

### <a name="configure-proxy-server-settings"></a>Nakonfigurujte nastavení serveru proxy

Pokud vyberete **používat proxy server systému** nastavení pro proxy server HTTP, integrace s vlastním hostováním runtime používá nastavení proxy v diahost.exe.config a diawp.exe.config. Pokud žádný proxy server je zadané v diahost.exe.config a diawp.exe.config, vlastním hostováním integrace runtime připojí ke cloudové službě přímo bez proxy. Následující postup obsahuje pokyny pro aktualizaci souboru diahost.exe.config.

1. V Průzkumníku souborů vytvořte bezpečné kopii C:\Program Files\Microsoft integrace Runtime\3.0\Shared\diahost.exe.config zálohování původní soubor.
2. Spusťte Notepad.exe spuštění jako správce a otevřete textový soubor "C:\Program Files\Microsoft integrace Runtime\3.0\Shared\diahost.exe.config. Najít výchozí značka pro system.net, jak je znázorněno v následujícím kódu:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Poté můžete přidat podrobnosti o proxy serveru, jak je znázorněno v následujícím příkladu:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Další vlastnosti jsou uvnitř značky proxy povoleno zadat požadované nastavení, jako je scriptLocation. V tématu [proxy – Element (nastavení sítě)](https://msdn.microsoft.com/library/sa91de1e.aspx) syntaxe.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Konfigurační soubor uložte do původního umístění a potom restartujte službu hostitel běhu integrace Self-hosted, která vybere změny. Restartování služby: pomocí apletu služby v Ovládacích panelech nebo z **integrace modulu Runtime Configuration Manager** > klikněte na tlačítko **zastavit službu** tlačítko a pak klikněte na **Start Služba**. Pokud se služba nespustí, je pravděpodobné, že byl přidán nesprávnou syntaxi – značka XML do konfiguračního souboru aplikace, která byla upravena.

> [!IMPORTANT]
> Nezapomeňte aktualizovat diahost.exe.config a diawp.exe.config.

Kromě těchto bodů musíte také zkontrolujte, zda že je v seznamu povolených IP adres vaší společnosti Microsoft Azure. Seznam platných Microsoft Azure IP adres si můžete stáhnout z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Možné příznaky brány firewall a proxy server s problémy
Pokud narazíte na chyby, které jsou podobné těm, které jsou následující, je pravděpodobně z důvodu nesprávné konfigurace brány firewall nebo proxy serveru, která blokuje vlastním hostováním integrace runtime v připojení k objektu pro vytváření dat ke svému ověření. Podívejte se na předchozí část, aby brána firewall a proxy serveru jsou správně nakonfigurovány.

1.  Při pokusu o registraci modulu runtime vlastním hostováním integrace se zobrazí následující chyba: "se nepodařilo zaregistrovat tento uzel integrace Runtime! Potvrďte, že ověřovací klíč je platný, a na tomto počítači je spuštěna služba integrace služby hostitele. "
2.  Když otevřete Správce konfigurace integrace modulu Runtime, uvidíte stav jako "**odpojeno**"nebo"**připojení**". Při zobrazení protokoly událostí systému Windows, v části "Prohlížeč událostí" > "Aplikace a služby Logs" > "Microsoft integrace Runtime", se zobrazí chybové zprávy, jako je například k následující chybě:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-intranet"></a>Povolte vzdálený přístup z intranetu  
V případě, pokud používáte **prostředí PowerShell** nebo **aplikace Správce přihlašovacích údajů** k šifrování přihlašovacích údajů z jiného počítače (v síti) než runtime vlastním hostováním integrace nainstalovanou, pak by vyžadovaly **'vzdálený přístup z intranetu,** možnost povolit. Pokud spustíte **prostředí PowerShell** nebo **aplikace Správce přihlašovacích údajů** k šifrování přihlašovacích údajů ve stejném počítači, kde runtime vlastním hostováním integrace je nainstalován, pak **' vzdáleného přístupu z intranetu,** možná není zapnuté.

Vzdálený přístup z intranetu by měla být **povoleno** před přidáním jiný uzel pro **vysokou dostupnost a škálovatelnost**.  

Během integrace s vlastním hostováním runtime instalace (v 3.3.xxxx.x a vyšší), ve výchozím nastavení, instalaci modulu runtime vlastním hostováním integrace zakáže **'vzdálený přístup z intranetu,** na počítači vlastním hostováním integrace modulu runtime.

Pokud používáte bránu firewall jiného výrobce, můžete ručně otevřete port 8060 (nebo uživateli nakonfigurovanému port). Pokud narazíte na problém brány firewall během instalace modulu runtime vlastním hostováním integrace, můžete zkusit pomocí následujícího příkazu k instalaci modulu runtime vlastním hostováním integraci bez konfiguraci brány firewall.

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```
> [!NOTE]
> **Aplikace Správce přihlašovacích údajů** dosud nejsou k dispozici pro šifrování přihlašovacích údajů v ADFv2. Tato podpora přidáme později.  

Pokud se rozhodnete, že není otevřete port 8060 na počítači vlastním hostováním integrace modulu runtime, použijte mechanismy pro než pomocí ** nastavení pověření ** aplikace nakonfigurovat přihlašovací údaje k úložišti dat. Například můžete použít rutinu New-AzureRmDataFactoryV2LinkedServiceEncryptCredential prostředí PowerShell. Najdete v části Nastavení přihlašovacích údajů a zabezpečení na tom, jak přihlašovací údaje úložiště dat může být nastavena.


## <a name="next-steps"></a>Další postup
Projděte si následující kurz pro podrobné pokyny: [kurz: kopírování místně data do cloudu](tutorial-hybrid-copy-powershell.md).
