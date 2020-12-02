---
title: Správa dat bránu pro Data Factory
description: K přesunu dat použijte Správa dat bránu v Azure Data Factory.
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
ms.openlocfilehash: 94c1bed8d94f73bc7794037b307618f4c36c4518
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450599"
---
# <a name="data-management-gateway"></a>Brána správy dat
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Integration runtime v místním prostředí v](../create-self-hosted-integration-runtime.md).

> [!NOTE]
> Správa dat brána se teď změnila jako Integration Runtimeá v místním prostředí.

Brána pro správu dat je klientský Agent, který musíte nainstalovat do místního prostředí a kopírovat data mezi cloudovým a místním úložištěm dat. Místní úložiště dat podporovaná Data Factory jsou uvedená v části [podporované zdroje dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) .

Tento článek doplňuje návod v článku [přesun dat mezi místními a cloudových úložišť dat](data-factory-move-data-between-onprem-and-cloud.md) . V tomto návodu vytvoříte kanál, který používá bránu k přesunu dat z databáze SQL Server do objektu blob Azure. Tento článek poskytuje podrobné informace o bráně pro správu dat.

Bránu pro správu dat můžete škálovat tak, že přidružíte několik místních počítačů k bráně. Horizontální navýšení kapacity můžete zvýšit tak, že zvýšíte počet úloh přesunu dat, které můžou běžet souběžně na uzlu. Tato funkce je k dispozici také pro logickou bránu s jedním uzlem. Podrobnosti najdete [v tématu škálování brány pro správu dat v Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) článku.

> [!NOTE]
> Brána v současné době podporuje pouze aktivitu kopírování a uloženou proceduru v Data Factory. Pro přístup k místním zdrojům dat není možné bránu použít z vlastní aktivity.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled
### <a name="capabilities-of-data-management-gateway"></a>Možnosti brány pro správu dat
Brána pro správu dat nabízí následující možnosti:

* Model místních zdrojů dat a cloudových zdrojů dat v rámci stejné datové továrny a přesun dat.
* Pro monitorování a správu, které mají přehled o stavu brány na stránce Data Factory, je nutné mít jedno podokno skla.
* Spravujte přístup k místním zdrojům dat zabezpečeným způsobem.
  * Pro podnikovou bránu firewall se nevyžadují žádné změny. Brána zpřístupňuje odchozí připojení založená na protokolu HTTP pouze k otevření Internetu.
  * Zašifrujte přihlašovací údaje pro vaše místní úložiště dat pomocí certifikátu.
* Efektivní přesouvání dat – data se přenášejí paralelně, odolné proti přerušované síťové problémy s logikou automatického opakování.

### <a name="command-flow-and-data-flow"></a>Tok příkazů a tok dat
Když použijete aktivitu kopírování ke kopírování dat mezi místním prostředím a cloudem, aktivita používá bránu k přenosu dat z místního zdroje dat do cloudu a naopak.

Zde je tok dat vysoké úrovně pro a shrnutí kroků pro kopírování pomocí služby data Gateway: ![ tok dat pomocí brány](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Vývojář dat vytvoří bránu pro Azure Data Factory pomocí rutiny [Azure Portal](https://portal.azure.com) nebo [PowerShellu](/powershell/module/az.datafactory/).
2. Vývojář dat vytvoří propojenou službu pro místní úložiště dat zadáním brány. V rámci nastavení propojené služby používá vývojář dat nastavení aplikace s přihlašovacími údaji k zadání typů ověřování a přihlašovacích údajů. Dialogové okno Nastavení přihlašovacích údajů komunikuje s úložištěm dat a otestuje připojení a bránu pro uložení přihlašovacích údajů.
3. Brána šifruje přihlašovací údaje pomocí certifikátu přidruženého k bráně (poskytované vývojářem dat) před uložením přihlašovacích údajů v cloudu.
4. Služba Data Factory komunikuje s bránou pro plánování & správu úloh prostřednictvím řídicího kanálu, který používá sdílenou frontu služby Azure Service Bus. Když je potřeba spustit úlohu aktivity kopírování, Data Factory zařadí do fronty požadavek spolu s přihlašovacími údaji. Po cyklickém dotazování fronty se brána vypíná mimo tuto úlohu.
5. Brána dešifruje přihlašovací údaje se stejným certifikátem a pak se připojí k místnímu úložišti dat pomocí správného typu ověřování a přihlašovacích údajů.
6. Brána kopíruje data z místního úložiště do cloudového úložiště nebo naopak, a to v závislosti na tom, jak je aktivita kopírování nakonfigurovaná v datovém kanálu. V tomto kroku brána přímo komunikuje s cloudovou službou úložiště, jako je Azure Blob Storage přes zabezpečený kanál (HTTPS).

### <a name="considerations-for-using-gateway"></a>Předpoklady pro používání brány
* Jednu instanci brány pro správu dat lze použít pro více místních zdrojů dat. **Jedna instance brány je ale vázaná jenom na jednu objekt pro vytváření dat Azure** a nedá se sdílet s jinou datovou továrnou.
* V jednom počítači může být nainstalovaná **jenom jedna instance brány pro správu dat** . Předpokládejme, že máte dva datové továrny, které potřebují přístup k místním zdrojům dat, musíte nainstalovat brány do dvou místních počítačů. Jinými slovy, brána je vázaná na konkrétní objekt pro vytváření dat.
* **Brána nemusí být ve stejném počítači jako zdroj dat**. Nicméně když se brána přiblíží ke zdroji dat, zkracuje se čas, kdy se brána připojí ke zdroji dat. Doporučujeme nainstalovat bránu na počítač, který je jiný než ten, který je hostitelem místního zdroje dat. Když je brána a zdroj dat na různých počítačích, brána nesoutěží o prostředky se zdrojem dat.
* Můžete mít **několik bran na různých počítačích, které se připojují ke stejnému místnímu zdroji dat**. Například můžete mít dvě brány, které obsluhují dva datové továrny, ale stejný místní zdroj dat je zaregistrován u obou datových továrn.
* Pokud již máte v počítači nainstalovánu bránu, která obsluhuje **Power BI** scénář, nainstalujte **samostatnou bránu pro Azure Data Factory** na jiném počítači.
* Brána se musí používat i v případě, že používáte **ExpressRoute**.
* Považovat zdroj dat za místní zdroj dat (který je za bránou firewall) i v případě, že používáte **ExpressRoute**. Bránu použijte k navázání připojení mezi službou a zdrojem dat.
* **Bránu musíte použít** i v případě, že je úložiště dat v cloudu na **virtuálním počítači Azure s IaaS**.

## <a name="installation"></a>Instalace
### <a name="prerequisites"></a>Předpoklady
* Podporované verze **operačního systému** jsou Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, windows Server 2012, windows Server 2012 R2. Instalace brány pro správu dat v řadiči domény se v tuto chvíli nepodporuje.
* Vyžaduje se .NET Framework 4.5.1 nebo vyšší. Pokud instalujete bránu na počítač se systémem Windows 7, nainstalujte .NET Framework 4,5 nebo novější. Podrobnosti najdete v tématu [.NET Framework systémových požadavků](/dotnet/framework/get-started/system-requirements) .
* Doporučená **Konfigurace** pro počítač brány je minimálně 2 GHz, 4 jádra, 8 GB RAM a 80-GB disku.
* Pokud se hostitelský počítač přepne do režimu hibernace, brána nereaguje na požadavky na data. Proto před instalací brány nakonfigurujte příslušné **schéma napájení** v počítači. Pokud je počítač nakonfigurovaný do režimu hibernace, zobrazí se při instalaci brány zpráva.
* Abyste mohli úspěšně nainstalovat a nakonfigurovat bránu pro správu dat, musíte být správcem počítače. Můžete přidat další uživatele do místní skupiny Windows **Uživatelé brány pro správu dat** . Členové této skupiny můžou ke konfiguraci brány použít nástroj **Správa dat brány Configuration Manager** .

Vzhledem k tomu, že se spustí aktivita kopírování na konkrétní frekvenci, využívá využití prostředků (CPU, paměť) na počítači stejný vzor s špičkou a nečinnými časy. Využití prostředků také závisí na množství dat, která se přesunují. Když probíhají více úloh kopírování, vidíte využití prostředků v době špičky.

### <a name="installation-options"></a>Možnosti instalace
Bránu pro správu dat je možné nainstalovat následujícími způsoby:

* Stažením instalačního balíčku MSI z [webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Pomocí MSI můžete také upgradovat existující bránu pro správu dat na nejnovější verzi, kde jsou zachována všechna nastavení.
* Kliknutím na **Stáhnout a nainstalovat bránu data Gateway** v části Ruční instalace nebo **instalovat přímo do tohoto počítače** v části Expresní instalace. Podrobné pokyny k používání Expresní instalace najdete v článku [přesunutí dat mezi místním a cloudovým](data-factory-move-data-between-onprem-and-cloud.md) článkem. Ruční krok přejde do centra Stažení softwaru. Pokyny ke stažení a instalaci brány z webu Download Center najdete v další části.

### <a name="installation-best-practices"></a>Osvědčené postupy pro instalaci:
1. Nakonfigurujte schéma napájení na hostitelském počítači pro bránu tak, aby se počítač nev režimu hibernace. Pokud se hostitelský počítač přepne do režimu hibernace, brána nereaguje na požadavky na data.
2. Zálohujte certifikát přidružený k bráně.

### <a name="install-the-gateway-from-download-center"></a>Instalace brány z webu Download Center
1. Přejděte na [stránku pro stažení brány Microsoft Správa dat](https://www.microsoft.com/download/details.aspx?id=39717).
2. Klikněte na tlačítko **Stáhnout**, vyberte **64** verze (32-bit není podporován) a klikněte na tlačítko **Další**.
3. Spusťte soubor **MSI** přímo nebo ho uložte na pevný disk a spusťte příkaz.
4. Na **úvodní** stránce vyberte **jazyk** a klikněte na tlačítko **Další**.
5. **Přijměte** licenční smlouvu End-User a klikněte na **Další**.
6. Vyberte **složku** pro instalaci brány a klikněte na **Další**.
7. Na stránce **připraveno k instalaci** klikněte na **nainstalovat**.
8. Instalaci dokončíte kliknutím na **Dokončit** .
9. Získat klíč z Azure Portal. Podrobné pokyny najdete v další části.
10. Na stránce **zaregistrovat bránu** **Správa dat brány Configuration Manager** na vašem počítači spusťte následující kroky:
    1. Vložte klíč do textu.
    2. Volitelně můžete kliknutím na **Zobrazit klíč brány** zobrazit text klíče.
    3. Klikněte na **Zaregistrovat**.

### <a name="register-gateway-using-key"></a>Registrovat bránu pomocí klíče
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Pokud jste na portálu ještě nevytvořili logickou bránu
Pokud chcete vytvořit bránu na portálu a získat klíč ze stránky **Konfigurace** , postupujte podle pokynů v návodu v článku [přesun dat mezi místním a cloudovým](data-factory-move-data-between-onprem-and-cloud.md) článkem.

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Pokud jste již vytvořili logickou bránu na portálu
1. V Azure Portal přejděte na stránku **Data Factory** a klikněte na dlaždici **propojené služby** .

    ![Stránka Data Factory](media/data-factory-data-management-gateway/data-factory-blade.png)
2. Na stránce **propojené služby** vyberte logickou **bránu** , kterou jste vytvořili na portálu.

    ![logická brána](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. Na stránce **Brána dat** klikněte na **Stáhnout a nainstalujte bránu dat**.

    ![Odkaz ke stažení na portálu](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. Na stránce **Konfigurace** klikněte na možnost **znovu vytvořit klíč**. Po pečlivém čtení klikněte na tlačítko Ano u zprávy s upozorněním.

    ![Tlačítko pro opětovné vytvoření klíče](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Klikněte na tlačítko Kopírovat vedle klíče. Klíč se zkopíruje do schránky.

    ![Kopírování klíče](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Ikony a oznámení na hlavním panelu systému
Na následujícím obrázku vidíte některé ikony zásobníku, které vidíte.

![ikony na hlavním panelu systému](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Pokud přesunete kurzor přes ikonu na hlavním panelu systému/zprávu oznámení, zobrazí se podrobnosti o stavu operace brány/aktualizace v místním okně.

### <a name="ports-and-firewall"></a>Porty a brána firewall
Je potřeba vzít v úvahu dvě brány firewall: **podniková brána firewall** spuštěná v centrálním směrovači organizace a **Brána Windows Firewall** nakonfigurovaná jako démon v místním počítači, kde je brána nainstalovaná.

![brány firewall](./media/data-factory-data-management-gateway/firewalls2.png)

Na úrovni podnikové brány firewall je potřeba nakonfigurovat následující domény a odchozí porty:

| Názvy domén | Porty | Popis |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Používá se pro komunikaci s back-end služby pro přesun dat. |
| *.core.windows.net |443 |Používá se pro připravené kopírování pomocí objektu blob Azure (Pokud je nakonfigurované).|
| *.frontend.clouddatahub.net |443 |Používá se pro komunikaci s back-end služby pro přesun dat. |
| *.servicebus.windows.net |9350-9354, 5671 |Volitelná služba Service Bus Relay přes TCP, kterou používá Průvodce kopírováním |

Na úrovni brány Windows Firewall jsou tyto Odchozí porty obvykle povoleny. Pokud ne, můžete podle potřeby nakonfigurovat domény a porty na počítači brány.

> [!NOTE]
> 1. V závislosti na vašich zdrojích a jímka možná budete muset v podniku nebo v bráně Windows Firewall zapnout další domény a odchozí porty.
> 2. U některých cloudových databází (například: [Azure SQL Database](../../azure-sql/database/firewall-configure.md), [Azure Data Lake](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)atd.) možná budete muset v konfiguraci brány firewall zapnout IP adresu počítače brány.
>
>

#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Kopírování dat ze zdrojového úložiště dat do úložiště dat jímky
Zajistěte, aby byla pravidla brány firewall správně povolená na podnikovém firewallu, bráně Windows Firewall na počítači brány a samotném úložišti dat. Povolení těchto pravidel umožní, aby se brána úspěšně připojovala ke zdroji a jímky. Povolte pravidla pro každé úložiště dat, které je součástí operace kopírování.

Pokud například chcete kopírovat z **místního úložiště dat do jímky Azure SQL Database nebo do jímky Azure synapse Analytics**, proveďte následující kroky:

* Povolí odchozí komunikaci **TCP** na portu **1433** pro bránu firewall systému Windows i pro podnikovou bránu firewall.
* Nakonfigurujte nastavení brány firewall logického SQL serveru tak, aby se do seznamu povolených IP adres přidala IP adresa počítače brány.

> [!NOTE]
> Pokud brána firewall nepovoluje odchozí port 1433, nemůže brána získat přímý přístup k Azure SQL. V takovém případě můžete použít [připravené kopírování](./data-factory-copy-activity-performance.md#staged-copy) na SQL Database/SQL Managed Instance/SQL Azure DW. V tomto scénáři budete pro přesun dat vyžadovat jenom HTTPS (port 443).
>
>

### <a name="proxy-server-considerations"></a>Požadavky na proxy server
Pokud vaše firemní síťové prostředí používá proxy server pro přístup k Internetu, nakonfigurujte bránu pro správu dat tak, aby používala příslušné nastavení proxy serveru. Proxy server můžete nastavit během fáze prvotní registrace.

![Nastavit proxy během registrace](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Brána používá proxy server k připojení ke cloudové službě. Při počátečním nastavení klikněte na **změnit** propojení. Zobrazí se dialogové okno **nastavení proxy serveru** .

![Nastavení proxy pomocí Správce konfigurace 1](media/data-factory-data-management-gateway/SetProxySettings.png)

Existují tři možnosti konfigurace:

* **Nepoužívat proxy**: Brána explicitně nepoužívá žádný proxy server pro připojení ke cloudovým službám.
* **Použít systémový proxy server**: Brána používá nastavení proxy serveru, které je nakonfigurované v diahost.exe.config a diawp.exe.config. Pokud v diahost.exe.config a diawp.exe.config není nakonfigurovaný žádný proxy server, brána se připojí ke cloudové službě přímo bez přechodu přes proxy server.
* **Použití vlastního proxy serveru**: Nakonfigurujte nastavení proxy serveru http tak, aby se používalo pro bránu, místo použití konfigurací v diahost.exe.config a diawp.exe.config. Adresa a port jsou požadovány. Uživatelské jméno a heslo jsou nepovinné v závislosti na nastavení ověřování proxy serveru. Všechna nastavení se šifrují pomocí certifikátu přihlašovacích údajů brány a ukládají se místně na hostitelském počítači brány.

Po uložení aktualizovaných nastavení proxy serveru se služba hostitel brány pro správu dat automaticky restartuje.

Pokud chcete zobrazit nebo aktualizovat nastavení proxy serveru po úspěšné registraci brány, použijte Správa dat Configuration Manager brány.

1. Spusťte **Configuration Manager Správa dat brány**.
2. Přepněte na kartu **Nastavení**.
3. Kliknutím na **změnit** odkaz v části **proxy HTTP** otevřete dialogové okno **nastavit proxy server http** .
4. Po kliknutí na tlačítko **Další** se zobrazí dialogové okno s upozorněním, které žádá o vaše oprávnění k uložení nastavení proxy serveru a k restartování služby Hostitel brány.

Proxy server HTTP můžete zobrazit a aktualizovat pomocí nástroje Configuration Manager.

![Nastavení proxy pomocí Configuration Manageru 2](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Pokud nastavíte proxy server s ověřováním NTLM, služba hostitel brány se spustí pod účtem domény. Pokud později změníte heslo pro účet domény, nezapomeňte aktualizovat nastavení konfigurace služby a odpovídajícím způsobem ho znovu spustit. Z tohoto důvodu doporučujeme použít vyhrazený doménový účet pro přístup k proxy server, která nevyžaduje, aby se heslo často aktualizovalo.
>
>

### <a name="configure-proxy-server-settings"></a>Konfigurace nastavení proxy server
Pokud vyberete možnost **použít systémové proxy** serveru pro proxy server http, brána použije nastavení proxy serveru v diahost.exe.config a diawp.exe.config. Pokud v diahost.exe.config a diawp.exe.config není zadaný žádný proxy server, brána se připojí ke cloudové službě přímo bez přechodu přes proxy server. Následující postup poskytuje pokyny k aktualizaci diahost.exe.config souboru.

1. V Průzkumníku souborů proveďte bezpečnou kopii *C: \\ \\ Program Files \\ Microsoft Správa dat Gateway \\ 2,0 \\ Shared \\diahost.exe.config* k zálohování původního souboru.
2. Spusťte Notepad.exe spuštěno jako správce a otevřete textový soubor *C: \\ \\ Program Files \\ Microsoft Správa dat Gateway \\ 2,0 \\ Shared \\diahost.exe.config*. Najdete výchozí značku pro system.net, jak je znázorněno v následujícím kódu:

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Pak můžete přidat proxy server podrobnosti, jak je znázorněno v následujícím příkladu:

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    V rámci značky proxy jsou povoleny další vlastnosti, které určují požadovaná nastavení, jako je scriptLocation. Odkaz na [element proxy (nastavení sítě)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings) v syntaxi.

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Uložte konfigurační soubor do původního umístění a pak znovu spusťte službu hostitel Správa dat brány, která změny vezme. Restartování služby: pomocí apletu služby v Ovládacích panelech nebo z **Správa dat brány Configuration Manager** > klikněte na tlačítko **Zastavit službu** a pak klikněte na **Spustit službu**. Pokud se služba nespustí, je pravděpodobně přidána nesprávná syntaxe značky XML do konfiguračního souboru aplikace, který byl upraven.

> [!IMPORTANT]
> Nezapomeňte **aktualizovat diahost.exe.config i diawp.exe.config** .

Kromě těchto bodů musíte také zajistit, aby Microsoft Azure v seznamu povolených společností. Seznam platných IP adres Microsoft Azure lze stáhnout z webu [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Možné příznaky pro bránu firewall a problémy s proxy server
Pokud dojde k chybám, které jsou podobné těm, je pravděpodobně způsobena nesprávnou konfigurací brány firewall nebo proxy server, která blokuje připojení brány k Data Factory a jejich ověření. Pokud chcete zajistit správnou konfiguraci brány firewall a proxy server, přečtěte si předchozí část.

1. Při pokusu o registraci brány se zobrazí následující chyba: "nepovedlo se zaregistrovat klíč brány. Než se pokusíte znovu zaregistrovat klíč brány, zkontrolujte, že je brána pro správu dat v připojeném stavu a že je spuštěná služba Správa dat brána hostitele. "
2. Když otevřete Configuration Manager, zobrazí se stav "Odpojeno" nebo "připojování". Při prohlížení protokolů událostí systému Windows v části Prohlížeč událostí > protokoly aplikací a služeb > Správa dat brána se zobrazí chybové zprávy, jako například následující chyba: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Otevřete port 8050 pro šifrování přihlašovacích údajů.
**Nastavení přihlašovacích údajů** používá k přenosu přihlašovacích údajů k bráně při nastavování místní propojené služby v Azure Portal příchozí port **8050** . Při instalaci brány se ve výchozím nastavení otevře instalace brány na počítači brány.

Pokud používáte bránu firewall jiného výrobce, můžete ručně otevřít port 8050. Pokud během instalace brány narazíte na problém brány firewall, můžete zkusit nainstalovat bránu bez konfigurace brány firewall pomocí následujícího příkazu.

```cmd
msiexec /q /i DataManagementGateway.msi NOFIREWALL=1
```

Pokud se rozhodnete neotevírat port 8050 na počítači brány, použijte jiné mechanismy než použití aplikace **Nastavení přihlašovacích údajů** ke konfiguraci přihlašovacích údajů úložiště dat. Můžete například použít rutinu [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) prostředí PowerShell. V části Nastavení přihlašovacích údajů a zabezpečení najdete informace o tom, jak se dají nastavit přihlašovací údaje úložiště dat.

## <a name="update"></a>Aktualizace
Ve výchozím nastavení je brána pro správu dat automaticky aktualizována, pokud je k dispozici novější verze brány. Brána není aktualizována, dokud nebudou dokončeny všechny naplánované úlohy. Brána nezpracovává žádné další úlohy, dokud se nedokončí operace aktualizace. Pokud se aktualizace nezdařila, brána se vrátí zpět na starou verzi.

Čas plánované aktualizace se zobrazí v následujících umístěních:

* Stránka vlastností brány v Azure Portal.
* Domovská stránka Configuration Manager Správa dat brány
* Zpráva oznámení na hlavním panelu systému.

Karta domů v Configuration Manager brány Správa dat zobrazuje plán aktualizace a čas, kdy byla brána naposledy nainstalována/aktualizována.

![Plán aktualizací](media/data-factory-data-management-gateway/UpdateSection.png)

Aktualizaci můžete nainstalovat hned nebo počkat, až se brána automaticky aktualizuje v naplánovaném čase. Například na následujícím obrázku vidíte zprávu s oznámením, která se zobrazuje v Configuration Manager brány, spolu s tlačítkem aktualizovat, na kterou můžete kliknout a hned ji nainstalovat.

![Aktualizace v DMG Configuration Manager](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Zpráva oznámení na hlavním panelu by vypadala tak, jak je znázorněno na následujícím obrázku:

![Zpráva na hlavním panelu systému](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Na hlavním panelu systému se zobrazí stav operace aktualizace (ruční nebo automatické). Když Configuration Manager bránu příště spustíte, zobrazí se zpráva na oznamovacím panelu, že se brána aktualizovala spolu s odkazem na novinky v [tématu Co je nového](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Zakázání nebo povolení funkce Automatické aktualizace
Funkci Automatické aktualizace můžete zakázat nebo povolit provedením následujících kroků:

[Pro bránu s jedním uzlem]
1. Na počítači brány spusťte prostředí Windows PowerShell.
2. Přepněte do složky *C: \\ \\ Program Files \\ Microsoft Integration runtime \\ 3,0 \\ PowerShellScript \\* .
3. Spuštěním následujícího příkazu vypněte funkci Automatické aktualizace vypnuto (zakázat).

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. Postup, jak ho znovu zapnout:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
   [Vysoce dostupná a škálovatelná brána s více uzly](data-factory-data-management-gateway-high-availability-scalability.md)
1. Na počítači brány spusťte prostředí Windows PowerShell.
2. Přepněte do složky *C: \\ \\ Program Files \\ Microsoft Integration runtime \\ 3,0 \\ PowerShellScript \\* .
3. Spuštěním následujícího příkazu vypněte funkci Automatické aktualizace vypnuto (zakázat).

    Pro bránu s funkcí vysoké dostupnosti se vyžaduje extra parametr AuthKey.
    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. Postup, jak ho znovu zapnout:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>Configuration Manager
Jakmile bránu nainstalujete, můžete spustit Správa dat Configuration Manager brány jedním z následujících způsobů:

1. V okně **hledání** zadejte pro přístup k tomuto nástroji **Správa dat bránu** .
2. Spusťte spustitelný *ConfigManager.exe* ve složce: *C: \\ \\ Program Files \\ Microsoft Správa dat brána \\ 2,0 \\ Shared*.

### <a name="home-page"></a>Domovská stránka
Domovská stránka vám umožní provést následující akce:

* Zobrazit stav brány (připojeno ke cloudové službě atd.)
* **Zaregistrujte** se pomocí klíče z portálu.
* **Zastavte** a spusťte **službu Správa dat hostitele brány** na počítači brány.
* **Naplánujte aktualizace** v určitou dobu dnů.
* Zobrazit datum **Poslední aktualizace** brány

### <a name="settings-page"></a>Stránka Nastavení
Stránka nastavení vám umožňuje provést následující akce:

* Zobrazení, změna a export **certifikátu** používaného bránou. Tento certifikát se používá k šifrování přihlašovacích údajů ke zdroji dat.
* Změňte **port HTTPS** pro koncový bod. Brána otevře port pro nastavení přihlašovacích údajů ke zdroji dat.
* **Stav** koncového bodu
* Zobrazení **certifikátu SSL** se používá pro komunikaci TLS/SSL mezi portálem a bránou pro nastavení přihlašovacích údajů pro zdroje dat.

### <a name="remote-access-from-intranet"></a>Vzdálený přístup z intranetu
Tato funkce bude v budoucnu povolena. V nadcházejících aktualizacích (v 3.4 nebo novějších) vám umožníme povolit nebo zakázat jakékoli vzdálené připojení, k němuž dnes dojde pomocí portu 8050 (viz část výše) při použití prostředí PowerShell nebo aplikace Správce přihlašovacích údajů pro šifrování přihlašovacích údajů.

### <a name="diagnostics-page"></a>Stránka diagnostiky
Stránka Diagnostika umožňuje provést následující akce:

* Povolit podrobné **protokolování**, zobrazit protokoly v prohlížeči událostí a odeslat protokoly společnosti Microsoft v případě selhání.
* **Otestujte připojení** ke zdroji dat.

### <a name="help-page"></a>Stránka nápovědy
Na stránce s informací o nápovědě se zobrazí následující informace:

* Stručný popis brány
* Číslo verze
* Odkazy na online nápovědě, prohlášení o zásadách ochrany osobních údajů a licenční smlouvu.

## <a name="monitor-gateway-in-the-portal"></a>Monitorování brány na portálu
V Azure Portal můžete na počítači brány Zobrazit snímek využití prostředků téměř v reálném čase (CPU, paměť, síť (na vstupu/výstup) atd.).

1. V Azure Portal přejděte na domovskou stránku objektu pro vytváření dat a klikněte na dlaždici **propojené služby** .

    ![Domovská stránka objektu pro vytváření dat](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. Vyberte **bránu** na stránce **propojené služby** .

    ![Stránka propojených služeb](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. Na stránce **Brána** můžete zobrazit paměť a využití CPU bránou.

    ![Využití procesoru a paměti bránou](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. Pokud chcete zobrazit další podrobnosti, jako je například využití sítě, povolte **upřesňující nastavení** .
    
    ![Rozšířené monitorování brány](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

Následující tabulka uvádí popisy sloupců v seznamu **uzly brány** :

Vlastnost monitorování | Popis
:------------------ | :----------
Název | Název logické brány a uzlů přidružených k bráně Uzel je místní počítač s Windows, na kterém je brána nainstalovaná. Informace o tom, jak mít více než jeden uzel (až čtyři uzly) v jedné logické bráně, najdete v tématu [Správa dat brány – vysoká dostupnost a škálovatelnost](data-factory-data-management-gateway-high-availability-scalability.md).
Status | Stav logické brány a uzlů brány. Příklad: online/offline/omezený/atd. Informace o těchto stavech najdete v části [stav brány](#gateway-status) .
Verze | Zobrazuje verzi logické brány a všech uzlů brány. Verze logické brány je určena na základě verze většiny uzlů ve skupině. Pokud v instalaci logické brány existují uzly s různými verzemi, budou správně fungovat pouze uzly se stejným číslem verze jako logická brána. Ostatní jsou v omezeném režimu a je potřeba je ručně aktualizovat (jenom v případě, že se automatická aktualizace nezdařila).
Dostupná paměť | Dostupná paměť v uzlu brány Tato hodnota je snímkem téměř v reálném čase.
Využití procesoru | Využití procesoru uzlu brány Tato hodnota je snímkem téměř v reálném čase.
Sítě (za sekundu) | Využití sítě uzlu brány. Tato hodnota je snímkem téměř v reálném čase.
Souběžné úlohy (spuštěné/omezení) | Počet úloh nebo úloh, které jsou spuštěny na jednotlivých uzlech. Tato hodnota je snímkem téměř v reálném čase. Limit označuje maximální počet souběžných úloh pro každý uzel. Tato hodnota je definovaná na základě velikosti počítače. Limit můžete zvýšit tak, aby se v pokročilých scénářích zvýšilo zatížení souběžných úloh, kde je procesor/paměť/síť využívána, ale aktivity čekají na vypršení časového limitu. Tato funkce je dostupná taky u brány s jedním uzlem (i když není povolená funkce škálovatelnosti a dostupnosti).
Role | Existují dva typy rolí v bráně pro více uzlů – dispečer a pracovní proces. Všechny uzly jsou pracovní procesy, což znamená, že je můžete použít ke spouštění úloh. K dispozici je jen jeden uzel dispečera, který se používá k vyžádání úkolů nebo úloh z cloudových služeb a jejich odeslání do různých pracovních uzlů (včetně sebe samé).

Na této stránce se zobrazí některá nastavení, která jsou smysluplnější, když v bráně existují dva nebo více uzlů (scénář horizontálního navýšení kapacity). Podrobnosti o nastavení brány pro více uzlů najdete v tématu [Správa dat brány – vysoká dostupnost a škálovatelnost](data-factory-data-management-gateway-high-availability-scalability.md) .

### <a name="gateway-status"></a>Stav brány
Následující tabulka uvádí možné stavy **uzlu brány**:

Status  | Komentáře a scénáře
:------- | :------------------
Online | Uzel je připojený ke službě Data Factory.
Offline | Uzel je offline.
Inovován | Uzel se automaticky aktualizuje.
Omezeně | Kvůli problému s připojením. Důvodem může být problém s portem HTTP 8050, potížím s připojením k Service Bus nebo problémy s synchronizací přihlašovacích údajů.
Inactive | Uzel je v konfiguraci odlišnou od konfigurace jiných majoritní uzlů.<br/><br/> Uzel může být neaktivní, pokud se nemůže připojit k jiným uzlům.

Následující tabulka uvádí možné stavy **logické brány**. Stav brány závisí na stavech uzlů brány.

Status | Komentáře
:----- | :-------
Vyžaduje registraci | Pro tuto logickou bránu ještě není zaregistrovaný žádný uzel.
Online | Uzly brány jsou online.
Offline | Žádný uzel ve stavu online.
Omezeně | Ne všechny uzly v této bráně jsou v dobrém stavu. Tento stav je upozorněním, že některý uzel může být mimo provoz. <br/><br/>Důvodem může být problém synchronizace přihlašovacích údajů u dispečera nebo pracovního uzlu.

## <a name="scale-up-gateway"></a>Horizontální navýšení kapacity brány
Můžete nakonfigurovat počet **souběžných úloh přesunu dat** , které se můžou spouštět na uzlu, abyste mohli škálovat možnosti přesouvání dat mezi místními a cloudovým úložištěm dat.

Pokud není dostupná paměť a procesor dobře využité, ale kapacita nečinnosti je 0, měli byste škálovat kapacitu zvýšením počtu souběžných úloh, které se můžou spouštět na uzlu. Možná budete chtít škálovat i v případě, že aktivity čekají na vypršení časového limitu, protože brána je přetížená. V rozšířených nastaveních uzlu brány můžete zvýšit maximální kapacitu uzlu.

## <a name="troubleshooting-gateway-issues"></a>Řešení potíží s bránou
Informace a tipy pro řešení potíží s používáním brány pro správu dat najdete v článku [řešení potíží s bránou](data-factory-troubleshoot-gateway-issues.md) .

## <a name="move-gateway-from-one-machine-to-another"></a>Přesun brány z jednoho počítače do druhého
V této části najdete postup přesunutí klienta brány z jednoho počítače na jiný počítač.

1. Na portálu přejděte na **domovskou stránku Data Factory** a klikněte na dlaždici **propojené služby** .

    ![Propojení bran dat](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. V části **brány dat** stránky **propojených služeb** vyberte svou bránu.

    ![Stránka propojených služeb s vybranou branou](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. Na stránce **Brána dat** klikněte na **Stáhnout a nainstalujte bránu dat**.

    ![Stáhnout odkaz na bránu](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. Na stránce **Konfigurace** klikněte na možnost **Stáhnout a nainstalovat bránu data Gateway** a postupujte podle pokynů k instalaci brány dat na počítač.

    ![Konfigurace stránky](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Ponechte **Configuration Manager otevřené brány Microsoft Správa dat** .

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. Na portálu na stránce **Konfigurace** klikněte na panelu příkazů na **znovu vytvořit klíč** a pro zprávu upozornění klikněte na **Ano** . Klikněte na tlačítko **Kopírovat** vedle textu klíče, který kopíruje klíč do schránky. Brána na původním počítači přestane fungovat, jakmile znovu vytvoříte klíč.

    ![Znovu vytvořit klíč 2](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Vložte **klíč** do textového pole na stránce **registrovat bránu** **Správa dat brány Configuration Manager** na vašem počítači. volitelné Pokud chcete zobrazit text klíče, klikněte na **Zobrazit klíč brány** zaškrtávací políčko.

    ![Kopírovat klíč a zaregistrovat](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Kliknutím na **Registrovat** zaregistrujete bránu v cloudové službě.
9. Na kartě **Nastavení** klikněte na **změnit** a vyberte stejný certifikát, který se použil pro starou bránu, zadejte **heslo** a klikněte na **Dokončit**.

   ![Zadat certifikát](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Certifikát můžete z původní brány exportovat pomocí následujících kroků: spustit Správa dat Configuration Manager brány na původním počítači, přepnout na kartu **certifikát** , kliknout na tlačítko **exportovat** a postupovat podle pokynů.
10. Po úspěšné registraci brány by se na domovské stránce Configuration Manager brány měla zobrazit položka **registrace** nastavená na hodnotu **zaregistrováno** a **stav** nastavená na **spuštěno** .

## <a name="encrypting-credentials"></a>Šifrování přihlašovacích údajů
Chcete-li zašifrovat přihlašovací údaje v editoru Data Factory, proveďte následující kroky:

1. Na **počítači brány** spusťte webový prohlížeč a přejděte na [Azure Portal](https://portal.azure.com). V případě potřeby vyhledejte datovou továrnu, otevřete datovou továrnu na stránce **Data Factory** a potom kliknutím na **vytvořit & nasazení** spusťte Data Factory Editor.
2. Kliknutím na existující **propojenou službu** ve stromovém zobrazení zobrazte její definici JSON nebo vytvořte propojenou službu, která vyžaduje bránu pro správu dat (například: SQL Server nebo Oracle).
3. V editoru JSON zadejte název brány do pole vlastnost **brány** .
4. Zadejte název serveru pro vlastnost **zdroj dat** v **připojovacím řetězci**.
5. Zadejte název databáze pro vlastnost **počáteční katalog** v **připojovacím řetězci**.
6. Na panelu příkazů klikněte na tlačítko **Šifrovat** , které spouští aplikaci **Správce přihlašovacích údajů** po kliknutí. Mělo by se zobrazit dialogové okno **nastavení pověření** .

    ![Dialogové okno Nastavení přihlašovacích údajů](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. V dialogovém okně **nastavení pověření** proveďte následující kroky:
   1. Vyberte **ověřování** , které má služba Data Factory použít pro připojení k databázi.
   2. Zadejte jméno uživatele, který má přístup k databázi pro nastavení **uživatelského jména** .
   3. Jako nastavení **hesla** zadejte heslo pro uživatele.
   4. Kliknutím na tlačítko **OK** Zašifrujte přihlašovací údaje a zavřete dialogové okno.
8. V **ConnectionString** by se teď měla zobrazit vlastnost **encryptedCredential** .

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
   Pokud přistupujete k portálu z počítače, který se liší od počítače brány, je nutné zajistit, aby se aplikace Správce přihlašovacích údajů mohla připojit k počítači brány. Pokud se aplikace nemůže připojit k počítači brány, nepovoluje vám nastavení přihlašovacích údajů pro zdroj dat a testování připojení ke zdroji dat.

Když použijete aplikaci **nastavení pověření** , portál zašifruje pověření s certifikátem zadaným na kartě **certifikát** **Configuration Manager brány** na počítači brány.

Pokud hledáte přístup založeného na rozhraní API pro šifrování přihlašovacích údajů, můžete k šifrování přihlašovacích údajů použít rutinu [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) prostředí PowerShell. Rutina používá certifikát, který je nakonfigurován pro použití pro šifrování přihlašovacích údajů. Můžete přidat šifrované přihlašovací údaje k elementu **EncryptedCredential** **připojovacího řetězce** ve formátu JSON. JSON použijete pomocí rutiny [New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) nebo v editoru Data Factory.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Při nastavování přihlašovacích údajů pomocí editoru Data Factory existuje jeden další postup. Pokud vytvoříte propojenou službu SQL Server pomocí editoru a zadáte přihlašovací údaje do prostého textu, přihlašovací údaje se šifrují pomocí certifikátu, který vlastní služba Data Factory. Nepoužívá certifikát, který je nakonfigurován pro použití bránou. I když tento přístup může být v některých případech trochu rychlejší, je méně bezpečný. Proto doporučujeme postupovat podle tohoto přístupu pouze pro účely vývoje a testování.

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

1. V Azure PowerShell přepněte do složky: *C: \\ \\ Program Files \\ Microsoft Integration runtime \\ 3,0 \\ PowerShellScript \\*. Spusťte *RegisterGateway.ps1* přidružené k místní proměnné **$Key** , jak je znázorněno v následujícím příkazu. Tento skript zaregistruje klientského agenta nainstalovaného na vašem počítači pomocí logické brány, kterou jste vytvořili dříve.

    ```powershell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Bránu můžete zaregistrovat na vzdáleném počítači pomocí parametru IsRegisterOnRemoteMachine. Příklad:

    ```powershell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Pomocí rutiny **Get-AzDataFactoryGateway** můžete získat seznam bran ve vaší datové továrně. Pokud se **stav** zobrazuje **online**, znamená to, že je vaše brána připravená k použití.

    ```powershell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
   Bránu můžete odebrat pomocí rutiny **Remove-AzDataFactoryGateway** a popisu aktualizace pro bránu pomocí rutin **set-AzDataFactoryGateway** . Syntaxi a další podrobnosti o těchto rutinách najdete v tématu Data Factory Reference k rutinám.  

### <a name="list-gateways-using-powershell"></a>Vypsání bran pomocí PowerShellu

```powershell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Odebrání brány pomocí PowerShellu

```powershell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>Další kroky
* Viz článek [přesun dat mezi místními a cloudových úložišť dat](data-factory-move-data-between-onprem-and-cloud.md) . V tomto návodu vytvoříte kanál, který používá bránu k přesunu dat z databáze SQL Server do objektu blob Azure.