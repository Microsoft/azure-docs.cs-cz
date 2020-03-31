---
title: Brána pro správu dat pro data factory
description: Nastavte bránu dat pro přesun dat mezi místním a cloudem. K přesunu dat použijte bránu pro správu dat v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 1340c205477b256e3d96ff7ccacb64e575725c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065409"
---
# <a name="data-management-gateway"></a>Brána správy dat
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [runtime integrace s vlastním hostitelem v .](../create-self-hosted-integration-runtime.md)

> [!NOTE]
> Brána pro správu dat byla nyní přejmenována na runtime integrace s vlastním hostitelem.

Brána pro správu dat je klientský agent, který je nutné nainstalovat v místním prostředí ke kopírování dat mezi cloudovými a místními úložišti dat. Místní úložiště dat podporovaná aplikací Data Factory jsou uvedena v části [Podporované zdroje dat.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)

Tento článek doplňuje návod v přesunutí [dat mezi místní a cloudová data úložiště](data-factory-move-data-between-onprem-and-cloud.md) článku. V návodu vytvoříte kanál, který používá bránu k přesunutí dat z místní databáze SQL Serveru do objektu blob Azure. Tento článek obsahuje podrobné podrobné informace o bráně pro správu dat.

Bránu pro správu dat můžete škálovat tak, že k ní přisuzujete více místních počítačů. Můžete vertikálně navýšit kapacitu zvýšením počtu úloh přesunu dat, které lze spustit souběžně na uzlu. Tato funkce je také k dispozici pro logickou bránu s jedním uzlem. Podrobnosti najdete [v článku Škálování brány pro správu dat v](data-factory-data-management-gateway-high-availability-scalability.md) Azure Data Factory.

> [!NOTE]
> V současné době brána podporuje pouze aktivitu kopírování a aktivitu uložené procedury v datové továrně. Není možné použít bránu z vlastní aktivity pro přístup k místním zdrojům dat.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled
### <a name="capabilities-of-data-management-gateway"></a>Možnosti brány pro správu dat
Brána pro správu dat poskytuje následující funkce:

* Modelujte místní zdroje dat a cloudové zdroje dat v rámci stejné továrny dat a přesouvat data.
* Mějte jedno podokno skla pro monitorování a správu s přehledem o stavu brány ze stránky Data Factory.
* Spravujte bezpečně přístup k místním zdrojům dat.
  * V podnikové bráně firewall nejsou nutné žádné změny. Brána provádí pouze odchozí připojení založená na protokolu HTTP na otevření internetu.
  * Šifrujte přihlašovací údaje pro místní úložiště dat pomocí certifikátu.
* Přesun dat efektivně – data se přenášejí paralelně, odolná vůči občasným problémům se sítí s logikou automatického opakování.

### <a name="command-flow-and-data-flow"></a>Tok příkazů a tok dat
Když použijete aktivitu kopírování ke kopírování dat mezi místním a cloudem, aktivita používá bránu k přenosu dat z místního zdroje dat do cloudu a naopak.

Tady je tok dat na vysoké úrovni a souhrn ![kroků pro kopírování s bránou dat: Tok dat pomocí brány](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Vývojář dat vytvoří bránu pro Azure Data Factory pomocí [portálu Azure](https://portal.azure.com) nebo [rutiny PowerShellu](https://docs.microsoft.com/powershell/module/az.datafactory/).
2. Vývojář dat vytvoří propojenou službu pro místní úložiště dat zadáním brány. V rámci nastavení propojené služby vývojář dat používá aplikaci Nastavení pověření k určení typů ověřování a pověření. Dialogové okno aplikace Nastavení pověření komunikuje s úložištěm dat pro testování připojení a bránou pro uložení pověření.
3. Brána zašifruje přihlašovací údaje pomocí certifikátu přidruženého k bráně (dodávaném vývojářem dat) před uložením přihlašovacích údajů do cloudu.
4. Služba Data Factory komunikuje s bránou pro plánování & správu úloh prostřednictvím řídicího kanálu, který používá sdílenou frontu služby Azure service bus. Když je třeba úlohu aktivity kopírování zakopnout, data factory fronty požadavek spolu s informacemi o pověření. Brána spustí úlohu po dotazování fronty.
5. Brána dešifruje pověření se stejným certifikátem a pak se připojí k místnímu úložišti dat s řádným typem ověřování a pověřeními.
6. Brána zkopíruje data z místního úložiště do cloudového úložiště nebo naopak v závislosti na tom, jak je aktivita kopírování nakonfigurována v datovém kanálu. V tomto kroku brána přímo komunikuje se službami cloudového úložiště, jako je azure blob storage přes zabezpečený kanál (HTTPS).

### <a name="considerations-for-using-gateway"></a>Důležité informace o použití brány
* Jednu instanci brány pro správu dat lze použít pro více místních zdrojů dat. Instance jedné brány je však **svázaná pouze s jednou továrně dat Azure** a nelze ji sdílet s jinou datovou továrnou.
* V jednom počítači můžete mít nainstalovanou **pouze jednu instanci brány pro správu dat.** Předpokládejme, že máte dvě továrny na data, které potřebují přístup k místním zdrojům dat, je třeba nainstalovat brány do dvou místních počítačů. Jinými slovy, brána je vázána na konkrétní datovou továrnu
* **Brána nemusí být ve stejném počítači jako zdroj dat**. Však s brána blíže ke zdroji dat snižuje čas pro bránu pro připojení ke zdroji dat. Doporučujeme nainstalovat bránu do počítače, který se liší od počítače, který hostuje místní zdroj dat. Pokud brána a zdroj dat jsou na různých počítačích, brána nesoutěží o prostředky se zdrojem dat.
* Můžete mít **více bran na různých počítačích připojení ke stejnému místnímu zdroji dat**. Můžete mít například dvě brány obsluhující dvě továrny na data, ale stejný místní zdroj dat je registrován u obou datových továren.
* Pokud už máte v počítači nainstalovanou bránu, která obsluhuje scénář **Power BI,** nainstalujte **samostatnou bránu pro Azure Data Factory** na jiný počítač.
* Brána musí být použita i v případě, že používáte **ExpressRoute**.
* Zacházejte se zdrojem dat jako s místním zdrojem dat (který je za bránou firewall), i když používáte **ExpressRoute**. Pomocí brány můžete vytvořit připojení mezi službou a zdrojem dat.
* Bránu musíte **použít i** v případě, že úložiště dat je v cloudu na **virtuálním počítači Azure IaaS**.

## <a name="installation"></a>Instalace
### <a name="prerequisites"></a>Požadavky
* Podporované verze **operačního systému** jsou Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Instalace brány pro správu dat na řadič idopisu není v současné době podporována.
* Je vyžadována rozhraní .NET Framework 4.5.1 nebo vyšší. Pokud instalujete bránu do počítače se systémem Windows 7, nainstalujte rozhraní .NET Framework 4.5 nebo novější. Podrobnosti naleznete [v části Požadavky na systém rozhraní .NET Framework.](https://msdn.microsoft.com/library/8z6watww.aspx)
* Doporučená **konfigurace** pro počítač brány je alespoň 2 GHz, 4 jádra, 8 GB RAM a 80 GB disk.
* Pokud hostitelský počítač přejde do režimu spánku, brána nereaguje na požadavky na data. Proto před instalací brány nakonfigurujte příslušné **plánnapájení** v počítači. Pokud je počítač nakonfigurován pro hibernaci, instalace brány zobrazí výzvu.
* Chcete-li úspěšně nainstalovat a nakonfigurovat bránu pro správu dat, musíte být správcem počítače. Do **brány pro správu dat místní** skupiny Windows mohou přidat další uživatele. Členové této skupiny mohou ke konfiguraci brány použít nástroj Správce **konfigurace brány brány pro správu dat.**

Jako spuštění aktivity kopírování dochází na určité frekvenci, využití prostředků (CPU, paměť) v počítači také sleduje stejný vzor s špičkou a nečinnosti časy. Využití prostředků také závisí do značné míry na množství dat, které jsou přesunuty. Pokud probíhá více úloh kopírování, zobrazí se zvýšení využití prostředků v době špičky.

### <a name="installation-options"></a>Možnosti instalace
Bránu pro správu dat lze nainstalovat následujícími způsoby:

* Stažením instalačního balíčku MSI ze služby [Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=39717). MSI lze také použít k upgradu stávající brány pro správu dat na nejnovější verzi, se všemi nastaveními zachována.
* Kliknutím na **stáhnout a nainstalovat** odkaz brány dat v části RUČNÍ NASTAVENÍ nebo Nainstalovat přímo do tohoto **počítače** v části EXPRESS SETUP. Podrobný návod k použití expresního nastavení najdete v článku [Přesun dat mezi místními a cloudovými](data-factory-move-data-between-onprem-and-cloud.md) články. Ruční krok vás přenese do centra pro stahování. Pokyny ke stažení a instalaci brány ze služby Download Center jsou v další části.

### <a name="installation-best-practices"></a>Doporučené postupy instalace:
1. Nakonfigurujte plán napájení na hostitelském počítači pro bránu tak, aby počítač nebyl přepzen do režimu spánku. Pokud hostitelský počítač přejde do režimu spánku, brána nereaguje na požadavky na data.
2. Záloha certifikát přidružený k bráně.

### <a name="install-the-gateway-from-download-center"></a>Instalace brány ze služby Stažení softwaru
1. Přejděte na [stránku pro stažení brány Microsoft Data Management Gateway](https://www.microsoft.com/download/details.aspx?id=39717).
2. Klepněte na tlačítko **Stáhnout**, vyberte **64bitovou** verzi (32bitová verze již není podporována) a klepněte na tlačítko **Další**.
3. Spusťte **MSI** přímo nebo jej uložte na pevný disk a spusťte.
4. Na **úvodní** stránce vyberte **jazyk,** na který klepněte na **tlačítko Další**.
5. **Přijměte** licenční smlouvu s koncovým uživatelem a klepněte na tlačítko **Další**.
6. Vyberte **složku,** chcete-li bránu nainstalovat, a klepněte na tlačítko **Další**.
7. Na stránce **Připraveno k instalaci** klepněte na tlačítko **Instalovat**.
8. Chcete-li dokončit instalaci, klepněte na tlačítko **Dokončit.**
9. Získejte klíč z portálu Azure. Podrobné pokyny naleznete v další části.
10. Na stránce **Register gateway** nástroje Správce konfigurace brány **správy dat** spuštěné na vašem počítači postupujte takto:
    1. Vložte klíč do textu.
    2. Volitelně můžete text klíče zobrazit kliknutím na **zobrazit klíč brány.**
    3. Klepněte na tlačítko **Registrovat**.

### <a name="register-gateway-using-key"></a>Registrace brány pomocí klíče
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Pokud jste ještě nevytvořili logickou bránu na portálu
Chcete-li vytvořit bránu na portálu a získat klíč ze stránky **Konfigurace,** postupujte podle pokynů z návodu v článku [Přesunout data mezi místním a cloudovým](data-factory-move-data-between-onprem-and-cloud.md) článkem.

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Pokud jste již vytvořili logickou bránu na portálu
1. Na webu Azure Portal přejděte na stránku **Data Factory** a klikněte na dlaždici **Propojené služby.**

    ![Stránka Data Factory](media/data-factory-data-management-gateway/data-factory-blade.png)
2. Na stránce **Propojené služby** vyberte logickou **bránu,** kterou jste vytvořili na portálu.

    ![logická brána](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. Na stránce **Brána dat** klikněte na **Stáhnout a nainstalovat bránu dat**.

    ![Odkaz ke stažení na portálu](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. Na stránce **Konfigurovat** klepněte na tlačítko **Znovu vytvořit klíč**. Po pečlivém přečtení klepněte na tlačítko Ano na varovné zprávě.

    ![Znovu vytvořit klíč](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Klikněte na tlačítko Kopírovat vedle klávesy. Klíč je zkopírován do schránky.

    ![Kopírování klíče](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Ikony na hlavním panelu / oznámení
Na následujícím obrázku jsou zobrazeny některé ikony zásobníku.

![ikony systémové liště](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Pokud přesunete kurzor nad ikonu nebo oznámení na hlavním panelu, zobrazí se podrobnosti o stavu operace brány/aktualizace v vyskakovacím okně.

### <a name="ports-and-firewall"></a>Porty a brána firewall
Existují dvě brány firewall, které je třeba zvážit: **podniková brána firewall** spuštěná na centrálním směrovači organizace a **brána firewall systému Windows** nakonfigurovaná jako daemon v místním počítači, ve kterém je brána nainstalována.

![Brány firewall](./media/data-factory-data-management-gateway/firewalls2.png)

Na podnikové úrovni brány firewall je třeba nakonfigurovat následující domény a odchozí porty:

| Názvy domén | Porty | Popis |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Používá se pro komunikaci s back-endem služby Data Movement Service. |
| *.core.windows.net |443 |Používá se pro fázované kopírování pomocí objektu Blob Azure (pokud je nakonfigurovaný)|
| *.frontend.clouddatahub.net |443 |Používá se pro komunikaci s back-endem služby Data Movement Service. |
| *.servicebus.windows.net |9350-9354, 5671 |Volitelné předávání sběrnice přes protokol TCP používané Průvodcem kopírováním |

Na úrovni brány firewall systému Windows jsou tyto odchozí porty obvykle povoleny. Pokud ne, můžete nakonfigurovat domény a porty odpovídajícím způsobem v počítači brány.

> [!NOTE]
> 1. Na základě zdroje / propadů může být pravděpodobně možné whitelist další domény a odchozí porty v podnikové bráně firewall /Windows.
> 2. U některých cloudových databází (například [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings), Azure Data [Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access)atd.) budete možná muset na konfiguraci brány v jejich bráně.
>
>

#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Kopírování dat ze zdrojového úložiště dat do úložiště dat jímky
Ujistěte se, že jsou pravidla brány firewall správně povolena v podnikové bráně firewall, bráně firewall systému Windows v počítači brány a samotném úložišti dat. Povolení těchto pravidel umožňuje brány úspěšně připojit ke zdroji i jímky. Povolte pravidla pro každé úložiště dat, které je součástí operace kopírování.

Chcete-li například zkopírovat z **místního úložiště dat do jímky databáze Azure SQL nebo jímky datového skladu Azure SQL**, proveďte následující kroky:

* Povolte odchozí komunikaci **TCP** na portu **1433** pro bránu firewall systému Windows i podnikovou bránu firewall.
* Nakonfigurujte nastavení brány firewall serveru Azure SQL server tak, aby do seznamu povolených ADRES IP přidávala IP adresu počítače brány.

> [!NOTE]
> Pokud brána firewall neumožňuje odchozí port 1433, gateway nemůže přistupovat přímo k Azure SQL. V takovém případě můžete použít [fázované kopírování](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) do databáze SQL Azure/ SQL Azure DW. V tomto scénáři bude vyžadovat pouze HTTPS (port 443) pro přesun dat.
>
>

### <a name="proxy-server-considerations"></a>Důležité informace o serveru proxy
Pokud prostředí podnikové sítě používá pro přístup k Internetu proxy server, nakonfigurujte bránu pro správu dat tak, aby používala příslušná nastavení serveru proxy. Proxy server můžete nastavit během počáteční fáze registrace.

![Nastavit proxy server během registrace](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Gateway používá proxy server pro připojení ke cloudové službě. Během **počátečního** nastavení klikněte na Změnit odkaz. Zobrazí se dialogové okno **nastavení proxy** serveru.

![Nastavení proxy serveru pomocí správce konfigurace](media/data-factory-data-management-gateway/SetProxySettings.png)

Existují tři možnosti konfigurace:

* **Nepoužívejte proxy server**: Gateway explicitně nepoužívá žádný proxy server pro připojení ke cloudovým službám.
* **Použít systémový proxy server**: Brána používá nastavení proxy serveru, které je nakonfigurováno v souborech diahost.exe.config a diawp.exe.config. Pokud není v diahost.exe.config a diawp.exe.config nakonfigurován žádný proxy server, brána se připojí ke cloudové službě přímo, aniž by procházela proxy serverem.
* **Použití vlastního proxy serveru**: Nakonfigurujte nastavení proxy serveru HTTP pro bránu namísto použití konfigurací v diahost.exe.config a diawp.exe.config. Adresa a port jsou povinné. Uživatelské jméno a heslo jsou volitelné v závislosti na nastavení ověřování serveru proxy. Všechna nastavení jsou šifrována pomocí certifikátu pověření brány a uložena místně v hostitelském počítači brány.

Hostitelská služba brány pro správu dat se po uložení aktualizovaného nastavení proxy automaticky restartuje.

Po úspěšné registraci brány použijte nástroj Správce konfigurace brány pro správu dat.

1. Spusťte **Správce konfigurace brány pro správu dat**.
2. Přepněte na kartu **Nastavení**.
3. Kliknutím na **Změnit** odkaz v části **Proxy HTTP** spusťte dialogové okno Nastavit **http proxy.**
4. Po klepnutí na tlačítko **Další** se zobrazí dialogové okno s upozorněním s žádostí o povolení uložit nastavení proxy serveru a restartovat hostitelskou službu brány.

Proxy server HTTP můžete zobrazit a aktualizovat pomocí nástroje Configuration Manager.

![Nastavení proxy serveru pomocí správce konfigurace](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Pokud nastavíte proxy server s ověřováním NTLM, hostitelská služba brány bude spuštěna pod účtem domény. Pokud později změníte heslo pro účet domény, nezapomeňte aktualizovat nastavení konfigurace služby a odpovídajícím způsobem jej restartovat. Z důvodu tohoto požadavku doporučujeme použít účet vyhrazené domény pro přístup k proxy serveru, který nevyžaduje časté aktualizace hesla.
>
>

### <a name="configure-proxy-server-settings"></a>Konfigurace nastavení serveru proxy
Pokud vyberete použít nastavení **systémového proxy serveru** pro proxy server HTTP, brána použije nastavení proxy serveru v diahost.exe.config a diawp.exe.config. Pokud není v diahost.exe.config a diawp.exe.config zadán žádný proxy server, brána se připojí ke cloudové službě přímo, aniž by procházela proxy serverem. Následující postup obsahuje pokyny pro aktualizaci souboru diahost.exe.config.

1. V Průzkumníkovi souborů vytvořte bezpečnou kopii *souboru\\\\C: Program Files\\Microsoft Data Management\\Gateway 2.0\\Shared\\diahost.exe.config* pro zálohování původního souboru.
2. Spusťte program Notepad.exe spuštěný jako správce a otevřete textový soubor *C:\\\\Program Files\\Microsoft Data Management Gateway\\2.0\\Shared\\diahost.exe.config*. Výchozí značku pro system.net najdete tak, jak je znázorněno v následujícím kódu:

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Potom můžete přidat podrobnosti o serveru proxy, jak je znázorněno v následujícím příkladu:

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Další vlastnosti jsou povoleny uvnitř proxy značky k určení požadovaných nastavení, jako je scriptLocation. Podívejte se na [proxy Element (Nastavení sítě)](https://msdn.microsoft.com/library/sa91de1e.aspx) na syntaxi.

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Uložte konfigurační soubor do původního umístění a restartujte hostitelskou službu Brány pro správu dat, která změny zachytí. Restartování služby: Použijte aplet služeb z ovládacího panelu nebo z **nástroje Správce konfigurace brány pro správu dat** > klepněte na tlačítko Zastavit **službu** a potom klepněte na **tlačítko Spustit službu**. Pokud se služba nespustí, je pravděpodobné, že do upravovaného konfiguračního souboru aplikace byla přidána nesprávná syntaxe značky XML.

> [!IMPORTANT]
> Nezapomeňte aktualizovat jak diahost.exe.config, **tak** diawp.exe.config.

Kromě těchto bodů se také musíte ujistit, že Microsoft Azure je na seznamu povolených položek vaší společnosti. Seznam platných IP adres Microsoft Azure lze stáhnout ze služby [Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=41653)společnosti Microsoft .

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Možné příznaky problémů s bránou firewall a serverem proxy
Pokud narazíte na chyby podobné následujícím, je to pravděpodobně způsobeno nesprávnou konfigurací brány firewall nebo proxy serveru, což blokuje připojení brány k datové továrně, aby se ověřila. V části se ujistěte, že brána firewall a proxy server jsou správně nakonfigurovány.

1. Při pokusu o registraci brány se zobrazí následující chyba: "Registrace klíče brány se nezdařila. Než se pokusíte znovu zaregistrovat klíč brány, zkontrolujte, zda je brána pro správu dat v připojeném stavu a že je spuštěna hostitelská služba brány pro správu dat."
2. Při otevření nástroje Configuration Manager se zobrazí stav "Odpojeno" nebo "Připojení". Při prohlížení protokolů událostí systému Windows se v části "Prohlížeč událostí" > "Protokoly aplikací a služeb" > "Brána pro správu dat" zobrazují chybové zprávy, například následující chyba:`Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Otevřený port 8050 pro šifrování pověření
Aplikace **Nastavení přihlašovacích údajů** používá příchozí port **8050** k přenosu přihlašovacích údajů do brány při nastavování místní propojené služby na webu Azure Portal. Během instalace brány se ve výchozím nastavení instalace brány otevře v počítači brány.

Pokud používáte bránu firewall jiného výrobce, můžete port 8050 otevřít ručně. Pokud při instalaci brány narazíte na problém s bránou firewall, můžete zkusit nainstalovat bránu pomocí následujícího příkazu bez konfigurace brány.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Pokud se rozhodnete port 8050 v počítači brány neotevírat, použijte ke konfiguraci přihlašovacích údajů úložiště dat jiné mechanismy než použití aplikace **Nastavení pověření.** Můžete například použít rutinu [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) PowerShell. V části Nastavení přihlašovacích údajů a zabezpečení o nastavení přihlašovacích údajů pro ukládání dat.

## <a name="update"></a>Aktualizace
Ve výchozím nastavení je brána pro správu dat automaticky aktualizována, když je k dispozici novější verze brány. Brána není aktualizována, dokud nebudou dokončeny všechny naplánované úlohy. Brána nezpracovává žádné další úkoly, dokud nebude dokončena operace aktualizace. Pokud se aktualizace nezdaří, brána je vrácena zpět do staré verze.

Naplánovaný čas aktualizace se zobrazí na následujících místech:

* Stránka vlastností brány na webu Azure Portal.
* Domovská stránka správce konfigurace brány pro správu dat
* Zpráva s oznámením systémové liště.

Karta Domů nástroje Správce konfigurace brány pro správu dat zobrazuje plán aktualizací a poslední instalaci/aktualizaci brány.

![Plán aktualizací](media/data-factory-data-management-gateway/UpdateSection.png)

Aktualizaci můžete nainstalovat ihned nebo počkat, až bude brána automaticky aktualizována v naplánovaný čas. Na následujícím obrázku je například zobrazena zpráva s oznámením zobrazeným ve Správci konfigurace brány spolu s tlačítkem Aktualizovat, na které můžete kliknout a okamžitě ji nainstalovat.

![Aktualizace ve Správci konfigurace DMG](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Zpráva s oznámením na hlavním panelu systému bude vypadat tak, jak je znázorněno na následujícím obrázku:

![Zpráva na hlavním panelu systému](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Stav operace aktualizace (ruční nebo automatický) se zobrazí v systémové liště. Při příštím spuštění nástroje Gateway Configuration Manager se na oznamovacím panelu zobrazí zpráva, že brána byla aktualizována spolu s odkazem na [nové téma](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Zakázání/povolení funkce automatické aktualizace
Funkci automatické aktualizace můžete zakázat nebo povolit následujícím postupem:

[Pro bránu s jedním uzly]
1. Spusťte prostředí Windows PowerShell na počítači brány.
2. Přepněte do složky *\\\\C: Program\\Files\\Microsoft\\ Integration Runtime\\3.0 PowerShellScript.*
3. Spuštěním následujícího příkazu vypněte funkci automatické aktualizace (zakázat).

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. Pokud chcete ji zase zapnout:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
   [Pro vysoce dostupnou a škálovatelnou bránu s více uzlů](data-factory-data-management-gateway-high-availability-scalability.md)
1. Spusťte prostředí Windows PowerShell na počítači brány.
2. Přepněte do složky *\\\\C: Program\\Files\\Microsoft\\ Integration Runtime\\3.0 PowerShellScript.*
3. Spuštěním následujícího příkazu vypněte funkci automatické aktualizace (zakázat).

    Pro bránu s vysokou dostupností funkce, další AuthKey param je vyžadováno.
    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. Pokud chcete ji zase zapnout:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>Configuration Manager
Po instalaci brány můžete spustit Správce konfigurace brány pro správu dat jedním z následujících způsobů:

1. V okně **Hledat** zadejte **Bránu pro správu dat,** abyste měli přístup k tomuto nástroji.
2. Spusťte spustitelný soubor *ConfigManager.exe* ve složce: *C:\\\\Program Files\\Microsoft Data Management Gateway\\2.0\\Shared*.

### <a name="home-page"></a>Domovská stránka
Domovská stránka umožňuje provést následující akce:

* Zobrazení stavu brány (připojené ke cloudové službě apod.).
* **Zaregistrujte** se pomocí klíče z portálu.
* **Zastavte** a spusťte **hostitelskou službu brány pro správu dat** v počítači brány.
* **Naplánujte aktualizace** v určitou dobu dnů.
* Zobrazení data poslední **aktualizace**brány .

### <a name="settings-page"></a>Stránka Nastavení
Stránka Nastavení umožňuje provést následující akce:

* Zobrazení, změna a export **certifikát** používaný bránou. Tento certifikát se používá k šifrování přihlašovacích údajů zdroje dat.
* Změňte **port HTTPS** pro koncový bod. Brána otevře port pro nastavení přihlašovacích údajů zdroje dat.
* **Stav** koncového bodu
* View **SSL certifikát** se používá pro komunikaci TLS/SSL mezi portálem a bránou pro nastavení přihlašovacích údajů pro zdroje dat.

### <a name="remote-access-from-intranet"></a>Vzdálený přístup z intranetu
Tato funkce bude povolena v budoucnu. V nadcházejících aktualizacích (v3.4 nebo novější) vám umožníme povolit / zakázat jakékoli vzdálené připojení, které se dnes stane pomocí portu 8050 (viz část výše) při použití aplikace PowerShell nebo Credential Manager pro šifrování pověření.

### <a name="diagnostics-page"></a>Stránka Diagnostika
Stránka Diagnostika umožňuje provést následující akce:

* Povolte podrobné **protokolování**, zobrazte protokoly v prohlížeči událostí a odesílejte protokoly společnosti Microsoft, pokud došlo k chybě.
* **Otestujte připojení** ke zdroji dat.

### <a name="help-page"></a>Stránka nápovědy
Na stránce Nápověda se zobrazí následující informace:

* Stručný popis brány
* Číslo verze
* Odkazy na online nápovědu, prohlášení o zásadách ochrany osobních údajů a licenční smlouvu.

## <a name="monitor-gateway-in-the-portal"></a>Monitorovat bránu na portálu
Na webu Azure Portal můžete zobrazit snímek využití prostředků téměř v reálném čase (procesor, paměť, síť (dovnitř/ven) atd.) na počítači brány.

1. Na webu Azure Portal přejděte na domovskou stránku vaší datové továrny a klikněte na dlaždici **Propojené služby.**

    ![Domovská stránka objektu pro vytváření dat](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. Vyberte **bránu** na stránce **Propojené služby.**

    ![Stránka Propojených služeb](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. Na stránce **Brána** uvidíte využití paměti a procesoru brány.

    ![Využití procesoru a paměti brány](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. Povolte **upřesňující nastavení,** abyste viděli další podrobnosti, například využití sítě.
    
    ![Pokročilé monitorování brány](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

V následující tabulce jsou uvedeny popisy sloupců v seznamu **Uzly brány:**

Vlastnost sledování | Popis
:------------------ | :----------
Name (Název) | Název logické brány a uzlů přidružených k bráně. Node je místní počítač se systémem Windows, na který je nainstalována brána. Informace o tom, že máte více než jeden uzel (až čtyři uzly) v jedné logické bráně, naleznete v [tématu Brána pro správu dat – vysoká dostupnost a škálovatelnost](data-factory-data-management-gateway-high-availability-scalability.md).
Status | Stav logické brány a uzlů brány. Příklad: Online/Offline/Limited/etc. Informace o těchto stavech naleznete v části [Stav brány.](#gateway-status)
Version | Zobrazuje verzi logické brány a každého uzlu brány. Verze logické brány je určena na základě verze většiny uzlů ve skupině. Pokud jsou v nastavení logické brány uzly s různými verzemi, budou správně fungovat pouze uzly se stejným číslem verze jako logická brána. Ostatní jsou v omezeném režimu a je třeba je ručně aktualizovat (pouze v případě, že se automatická aktualizace nezdaří).
Dostupná paměť | Dostupná paměť v uzlu brány. Tato hodnota je snímek téměř v reálném čase.
Využití procesoru | Využití procesoru uzlu brány. Tato hodnota je snímek téměř v reálném čase.
Síťové (příchozí/odchozí) | Využití uzlu brány v síti. Tato hodnota je snímek téměř v reálném čase.
Souběžné úlohy (spuštěné/limitové) | Počet úloh nebo úloh spuštěných v každém uzlu. Tato hodnota je snímek téměř v reálném čase. Limit označuje maximální souběžné úlohy pro každý uzel. Tato hodnota je definována na základě velikosti stroje. Můžete zvýšit limit vertikálně navýšit souběžné spuštění úlohv pokročilých scénářích, kde je procesor/paměť/síť nedostatečně využívána, ale aktivity jsou vypršení časového limitu. Tato funkce je také k dispozici s bránou s jedním uzlem (i když není povolena funkce škálovatelnosti a dostupnosti).
Role | Existují dva typy rolí v bráně s více uzly - Dispečer a pracovník. Všechny uzly jsou pracovníci, což znamená, že všechny mohou být použity k provádění úloh. Existuje pouze jeden uzel dispečera, který se používá k vytahování úloh/úloh z cloudových služeb a jejich odeslání do různých uzlů pracovního procesu (včetně sebe sama).

Na této stránce se zobrazí některá nastavení, která dávají větší smysl, když jsou v bráně dva nebo více uzlů (scénář horizontálního navýšení kapacity). Viz [Brána pro správu dat – vysoká dostupnost a škálovatelnost,](data-factory-data-management-gateway-high-availability-scalability.md) kde najdete podrobnosti o nastavení brány s více uzly.

### <a name="gateway-status"></a>Stav brány
Následující tabulka obsahuje možné stavy **uzlu brány**:

Status  | Komentáře/scénáře
:------- | :------------------
Online | Uzel připojený ke službě Data Factory.
Offline | Uzel je offline.
Inovace | Uzel je automaticky aktualizován.
Omezeně | Kvůli problému s připojením. Může být způsobeno problémem s portem HTTP 8050, problémem s připojením sběrnice nebo problémem se synchronizací pověření.
Inactive | Uzel je v konfiguraci odlišné od konfigurace jiných většinových uzlů.<br/><br/> Uzel může být neaktivní, pokud se nemůže připojit k jiným uzlům.

Následující tabulka obsahuje možné stavy **logické brány**. Stav brány závisí na stavech uzlů brány.

Status | Komentáře
:----- | :-------
Potřebuje registraci | Na tuto logickou bránu ještě není registrován žádný uzel.
Online | Uzly brány jsou online
Offline | Žádný uzel ve stavu online.
Omezeně | Ne všechny uzly v této bráně jsou v pořádku stavu. Tento stav je upozornění, že některé uzlmůže být dole! <br/><br/>Může být způsobeno problémem synchronizace pověření v uzlu dispečera/pracovníka.

## <a name="scale-up-gateway"></a>Škálování brány navýšit kapacitu
Můžete nakonfigurovat počet **souběžných úloh přesunu dat,** které lze spustit v uzlu, abyste zvýšili kapacitu možností přesouvání dat mezi místními a cloudovými úložišti dat.

Pokud dostupné paměti a procesoru nejsou využívány dobře, ale kapacita nečinnosti je 0, měli byste vertikálně navýšit kapacitu zvýšením počtu souběžných úloh, které lze spustit na uzlu. Můžete také vertikálně navýšit kapacitu, když jsou časování aktivit, protože brána je přetížena. V upřesňujícím nastavení uzlu brány můžete zvýšit maximální kapacitu uzlu.

## <a name="troubleshooting-gateway-issues"></a>Poradce při potížích s bránou
Informace a tipy pro řešení problémů s používáním brány pro správu dat najdete v článku [Poradce při potížích](data-factory-troubleshoot-gateway-issues.md) s bránou pro řešení potíží s bránou.

## <a name="move-gateway-from-one-machine-to-another"></a>Přesunutí brány z jednoho počítače do druhého
Tato část obsahuje postup pro přesunutí klienta brány z jednoho počítače do jiného počítače.

1. Na portálu přejděte na **domovskou stránku Data Factory**a klikněte na dlaždici **Propojené služby.**

    ![Propojení bran](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Vyberte bránu v části **DATOVÉ BRÁNY** na stránce **Propojené služby.**

    ![Stránka Propojené služby s vybranou bránou](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. Na stránce **Brána dat** klikněte na **Stáhnout a nainstalovat bránu dat**.

    ![Odkaz na bránu ke stažení](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. Na stránce **Konfigurace** klikněte na **Stáhnout a nainstalovat bránu dat**a podle pokynů nainstalujte bránu dat do počítače.

    ![Konfigurace stránky](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Ponechte **nástroj Microsoft Data Management Gateway Configuration Manager** otevřený.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. Na stránce **Konfigurovat** na portálu klikněte na panelu příkazů na **tlačítko Znovu vytvořit klávesu** a varovnou zprávu klepněte na **tlačítko Ano.** Klikněte na **tlačítko Kopírovat** vedle textu klíče, který zkopíruje klíč do schránky. Brána na starém počítači přestane fungovat, jakmile znovu vytvoříte klíč.

    ![Znovu vytvořit klíč](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Vložte **klíč** do textového pole na stránce **Brána registrace** nástroje Správce konfigurace brány pro **správu dat** v počítači. (nepovinné) Kliknutím na zaškrtávací políčko **Zobrazit klíč brány** zobrazíte text klíče.

    ![Kopírovat klíč a registrovat](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Kliknutím na **Registrovat** zaregistrujte bránu u cloudové služby.
9. Na kartě **Nastavení** klikněte na **Změnit** a vyberte stejný certifikát, který byl použit se starou bránou, zadejte **heslo**a klepněte na **tlačítko Dokončit**.

   ![Zadat certifikát](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Certifikát ze staré brány můžete exportovat následujícím postupem: spuštěnínástroje Správce brány pro správu dat na starém počítači, přepnutí na kartu **Certifikát,** kliknutí na tlačítko **Exportovat** a postupujte podle pokynů.
10. Po úspěšné registraci brány byste měli vidět **nastavení registrace** na **Registrované** a **Stav** nastavené na **Spuštěno** na domovské stránce Správce konfigurace brány.

## <a name="encrypting-credentials"></a>Šifrování přihlašovacích údajů
Chcete-li zašifrovat přihlašovací údaje v Editoru datových toků, postupujte takto:

1. Spuštění webového prohlížeče na **počítači brány**, přejděte na [portál Azure](https://portal.azure.com). V případě potřeby vyhledejte svou datovou továrnu, otevřete továrnu dat na stránce **DATA FACTORY** a pak klikněte na Author **& Deploy** a spusťte Editor datových tocích.
2. Kliknutím na existující **propojenou službu** ve stromovém zobrazení zobrazíte její definici JSON nebo vytvořte propojenou službu, která vyžaduje bránu pro správu dat (například SQL Server nebo Oracle).
3. V editoru JSON zadejte pro vlastnost **gatewayName** název brány.
4. Zadejte název serveru pro vlastnost **Zdroj dat** v **řetězci connectionString**.
5. Zadejte název databáze vlastnosti **Počáteční katalog** v **modulu connectionString**.
6. Na panelu příkazů, který spouští aplikaci **Správce přihlašovacích údajů** pro klepnutí jednou, klepněte na tlačítko **Šifrovat.** Mělo by se zobrazit dialogové okno **Nastavení přihlašovacích údajů.**

    ![Dialogové okno Nastavení pověření](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. V dialogovém okně **Nastavení přihlašovacích údajů** proveďte následující kroky:
   1. Vyberte **ověřování,** které má služba Data Factory použít pro připojení k databázi.
   2. Zadejte jméno uživatele, který má přístup k databázi pro nastavení **UŽIVATELSKÉ JMÉNO.**
   3. Zadejte heslo pro uživatele pro nastavení **HESLO.**
   4. Klepnutím na **tlačítko OK** zašifrujete přihlašovací údaje a zavřete dialogové okno.
8. Měli byste vidět **encryptedCredential** vlastnost v **connectionString** nyní.

    ```JSON
    {
        "name": "SqlServerLinkedService",
        "properties": {
            "type": "OnPremisesSqlServer",
            "description": "",
            "typeProperties": {
                "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
                "gatewayName": "adftutorialgateway"
            }
        }
    }
    ```
   Pokud k portálu přistupujete z počítače, který se liší od počítače brány, musíte se ujistit, že se aplikace Správce přihlašovacích údajů může připojit k počítači brány. Pokud se aplikace nemůže dostat k počítači brány, neumožňuje nastavit přihlašovací údaje pro zdroj dat a otestovat připojení ke zdroji dat.

Při použití aplikace **Nastavení pověření** portál zašifruje pověření pomocí certifikátu určeného na kartě **Certifikát** správce **konfigurace brány** v počítači brány.

Pokud hledáte přístup založený na rozhraní API pro šifrování přihlašovacích údajů, můžete použít rutinu [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) PowerShell k šifrování pověření. Rutina používá certifikát, který je brána nakonfigurována k šifrování pověření. Šifrovaná pověření přidáte do prvku **EncryptedCredential** **řetězce connectionString** v JSON. JSON používáte s rutinou [New-AzDataFactoryLinkedService](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactorylinkedservice) nebo v editoru datových toků.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Existuje ještě jeden přístup pro nastavení přihlašovacích údajů pomocí Editoru datových toků. Pokud vytvoříte službu propojenou serverem SQL Server pomocí editoru a zadáte pověření ve formátu prostého textu, pověření se zašifrují pomocí certifikátu, který vlastní služba Data Factory. Nepoužívá certifikát, který je brána nakonfigurována pro použití. Zatímco tento přístup může být v některých případech o něco rychlejší, je méně bezpečný. Proto doporučujeme postupovat podle tohoto přístupu pouze pro účely vývoje nebo testování.

## <a name="powershell-cmdlets"></a>Rutiny prostředí PowerShell
Tato část popisuje, jak vytvořit a zaregistrovat bránu pomocí rutin Azure PowerShell.

1. Spusťte **Azure PowerShell** v režimu správce.
2. Přihlaste se ke svému účtu Azure spuštěním následujícího příkazu a zadáním přihlašovacích údajů Azure.

    ```powershell
    Connect-AzAccount
    ```
3. Pomocí rutiny **New-AzDataFactoryGateway** vytvořte logickou bránu následujícím způsobem:

    ```powershell
    $MyDMG = New-AzDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Příklad příkazu a výstupu**:

    ```
    PS C:\> $MyDMG = New-AzDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description "gateway for walkthrough"

    Name              : MyGateway
    Description       : gateway for walkthrough
    Version           :
    Status            : NeedRegistration
    VersionStatus     : None
    CreateTime        : 9/28/2014 10:58:22
    RegisterTime      :
    LastConnectTime   :
    ExpiryTime        :
    ProvisioningState : Succeeded
    Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI
    ```

1. V Azure PowerShellu přepněte do složky: *C:\\\\Program Files\\Microsoft Integration Runtime\\3.0\\PowerShellScript\\*. Spusťte *soubor RegisterGateway.ps1* přidružený k místní proměnné **$Key** jak je znázorněno v následujícím příkazu. Tento skript zaregistruje klientského agenta nainstalovaného v počítači s logickou bránou, kterou vytvoříte dříve.

    ```powershell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Bránu můžete zaregistrovat ve vzdáleném počítači pomocí parametru IsRegisterOnRemoteMachine. Příklad:

    ```powershell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Rutina **Get-AzDataFactoryGateway** můžete použít k získání seznamu bran ve vaší továrně dat. Když se **stav** zobrazí **online**, znamená to, že brána je připravena k použití.

    ```powershell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
   Bránu můžete odebrat pomocí rutiny **Remove-AzDataFactoryGateway** a aktualizovat popis brány pomocí rutin **Set-AzDataFactoryGateway.** Syntaxe a další podrobnosti o těchto rutinách naleznete v tématu Odkaz na rutinu datové továrny.  

### <a name="list-gateways-using-powershell"></a>Seznam bran pomocí PowerShellu

```powershell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Odebrání brány pomocí PowerShellu

```powershell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>Další kroky
* Viz [Přesunutí dat mezi místními a cloudovými úložišti dat.](data-factory-move-data-between-onprem-and-cloud.md) V návodu vytvoříte kanál, který používá bránu k přesunutí dat z místní databáze SQL Serveru do objektu blob Azure.
