---
title: Brána správy dat pro službu Data Factory | Dokumentace Microsoftu
description: Nastavte bránu data gateway pro přesun dat mezi místním prostředím a cloudem. Přesunutí dat pomocí brány správy dat ve službě Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 728adae62677eb2edb1e203df9b0d9f11f6acecf
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022304"
---
# <a name="data-management-gateway"></a>Brána správy dat
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [v místním prostředí integration runtime ve službě](../create-self-hosted-integration-runtime.md). 

> [!NOTE]
> Brána správy dat má teď byl přejmenované jako modul Integration Runtime.  

Brána správy dat je klientský agent, který je nutné nainstalovat prostředí v místním ke kopírování dat mezi úložišti dat cloudové a místní. Místní datové úložiště podporovaných službou Data Factory jsou uvedeny v [podporované zdroje dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) oddílu.

Doplňuje názorný postup v tomto článku [přesun dat mezi místním prostředím a cloudem úložišť dat](data-factory-move-data-between-onprem-and-cloud.md) článku. V tomto návodu vytvoříte kanál, který používá bránu pro přesun dat z databáze v místním SQL serveru do objektu blob Azure. Tento článek obsahuje podrobné informace o brána správy dat. 

Brána správy dat můžete horizontálně tím, že přidružíte více místních počítačů s bránou. Můžete škálovat nahoru zvýšením počtu data přesun úloh, které můžou běžet souběžně na uzlu. Tato funkce je také k dispozici pro logické brány se jeden uzel. Zobrazit [brána správy dat škálování ve službě Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) , kde najdete podrobnosti.

> [!NOTE]
> Brána v současné době podporuje pouze aktivitu kopírování a aktivitu uložené procedury ve službě Data Factory. Není možné používat pro přístup k místním zdrojům dat z vlastní aktivity.      

## <a name="overview"></a>Přehled
### <a name="capabilities-of-data-management-gateway"></a>Možnosti brány správy dat
Brána správy dat poskytuje následující možnosti:

* Model místní zdroje dat a cloudových zdrojů dat v rámci stejné služby data factory a přesouvat data.
* Máte podokně ze skla pro monitorování a správu přehled o stavu brány ze stránky s Data Factory.
* Správa přístupu k místním zdrojům dat bezpečně.
  * Žádné změny nutné podnikovou bránu firewall. Brána je jenom odchozí připojení HTTP do otevřeného Internetu.
  * Šifrovat přihlašovací údaje pro vaše místní úložiště dat vaším certifikátem.
* Přesun dat efektivně – data se přenáší paralelně, problémy se sítí odolné vůči přerušované pomocí automatického Logika opakování.

### <a name="command-flow-and-data-flow"></a>Příkaz toku a toku dat
Pokud použijete aktivitu kopírování ke kopírování dat mezi místním prostředím a cloudem, aktivita používá bránu k přenosu dat z místního zdroje dat do cloudu a naopak.

Tady je podrobný datový tok pro a seznam kroků pro kopírování pomocí brány dat: ![Tok dat pomocí brány](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Data pro vývojáře pro službu Azure Data Factory pomocí vytvoří bránu [webu Azure portal](https://portal.azure.com) nebo [rutinu Powershellu](https://docs.microsoft.com/powershell/module/azurerm.datafactories/).
2. Vývojáři dat vytvoří propojené služby pro do místního úložiště dat tak, že zadáte brány. Jako součást nastavení propojené služby vývojáři dat používá aplikace nastavení přihlašovacích údajů k určení typů ověřování a přihlašovací údaje.  Dialogové okno Nastavení přihlašovacích údajů aplikace komunikuje s úložišti dat k testování připojení a přihlašovací údaje uložte do brány.
3. Brána šifruje přihlašovací údaje se platnost certifikátu spojeného s bránou (poskytnutých dat pro vývojáře), před uložením přihlašovacích údajů v cloudu.
4. Služba data Factory komunikuje s bránou pro plánování a Správa úloh prostřednictvím řídicí kanál, která používá frontu Service bus sdílených služeb Azure. Když úlohu aktivity kopírování musí být spuštěna, Data Factory zařadí do fronty požadavek spolu s přihlašovací údaje. Brána zahajuje úlohy po dotazování fronty.
5. Brána dešifruje přihlašovací údaje s jedním certifikátem a potom připojí k úložišti dat v místním s typem řádné ověření a přihlašovací údaje.
6. Brána kopíruje data z místního úložiště do cloudového úložiště, nebo naopak v závislosti na konfiguraci aktivitu kopírování v datovém kanálu. Pro tento krok brána komunikuje přímo se službami cloudového úložiště, například Azure Blob Storage přes zabezpečený kanál (HTTPS).

### <a name="considerations-for-using-gateway"></a>Důležité informace týkající se použití brány
* Jedna instance brány správy dat lze použít pro více zdrojů dat v místním. Ale **jedna instance brány je vázán na jenom jeden Azure data factory** a nemůže je sdílet s jinou datovou továrnu.
* Můžete mít **pouze jedna instance brány správy dat** nainstalována na jednom počítači. Předpokládejme, máte dva datové továrny, které potřebují přístup k místním zdrojům dat, je potřeba nainstalovat na dva počítače se místní brány. Jinými slovy brána se váže na konkrétní datové továrny
* **Brána nemusí být ve stejném počítači jako zdroj dat**. Však brány blíž ke zdroji dat s snižuje čas potřebný pro bránu pro připojení ke zdroji dat. Doporučujeme, abyste bránu nainstalovat na počítač, který se liší od jednoho, který je hostitelem zdroje dat místní. Když bránu a zdroj dat na různých počítačích, brána není soutěží o prostředky se zdrojem dat.
* Můžete mít **více bran na odlišných počítačích s připojením ke stejnému zdroji dat v místním**. Například může mít dvě brány obsluhuje dva datové továrny, ale stejného zdroje dat v místním zaregistruje datové továrny.
* Pokud už máte nainstalovanou na váš počítač obsluhující bránu **Power BI** scénář, nainstalujte **samostatnou bránu pro službu Azure Data Factory** na jiném počítači.
* Brány musí používat, i v případě, že používáte **ExpressRoute**.
* Považovat za zdroj dat místní, (který je za bránou firewall) zdroj dat i při použití **ExpressRoute**. K navázání připojení mezi službou a zdroji dat pomocí brány.
* Je nutné **použití brány** i v případě, že je úložiště dat v cloudu **virtuálních počítačů Azure IaaS**.

## <a name="installation"></a>Instalace
### <a name="prerequisites"></a>Požadavky
* Podporované **operačního systému** jsou verze Windows 7, Windows 8 a 8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Instalace brány pro správu dat na řadiči domény není aktuálně podporována.
* Rozhraní .NET framework 4.5.1 nebo novější. Při instalaci brány na počítači s Windows 7, nainstalujte rozhraní .NET Framework 4.5 nebo novější. Zobrazit [rozhraní .NET Framework System Requirements](https://msdn.microsoft.com/library/8z6watww.aspx) podrobnosti.
* Doporučené **konfigurace** brány je počítač minimálně 2 GHz, 4 jádra, 8 GB paměti RAM a 80 GB disk.
* Pokud hostitelský počítač přejde do režimu spánku, brána neodpovídá na požadavky na data. Proto nakonfigurovat odpovídající **schéma napájení** počítače před instalací brány. Pokud je počítač nakonfigurovaný do režimu hibernace, vyzve instalace brány zprávu.
* Musíte být správcem na počítači pro instalaci a konfiguraci brány správy dat úspěšně. Můžete přidat další uživatele **Brána pro správu dat uživatele** místní skupiny Windows. Členové této skupiny budou moct používat **Data Management Gateway Configuration Manager** nástroj pro konfiguraci brány.

Při spuštění aktivity kopírování se provádělo na konkrétní frekvence, využití prostředků (procesor, paměť) na počítači také používá stejný vzor s ve špičce a doby nečinnosti. Využití prostředků také závisí do značné míry na množství dat, který se přesouvá. Právě probíhají úlohy s více kopií, uvidíte využití prostředků zvýší během špiček.

### <a name="installation-options"></a>Možnosti instalace
Brána správy dat lze nainstalovat následujícím způsobem:

* Stáhněte si balíček MSI Instalační program z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717).  Soubor MSI můžete také použít k upgradu existující Brána pro správu dat na nejnovější verzi, se všemi možnými nastavení zachovány.
* Kliknutím na **stáhněte a nainstalujte bránu data gateway** odkaz v části RUČNÍ instalace nebo **přímo na tento počítač nainstalovat** pod Expresní instalace. Zobrazit [přesun dat mezi místním prostředím a cloudem](data-factory-move-data-between-onprem-and-cloud.md) najdete podrobné pokyny k používání Expresní instalace. Ruční krok přejdete na webu download center.  V další části jsou pokyny pro stažení a instalace brány ze služby Stažení softwaru.

### <a name="installation-best-practices"></a>Osvědčené postupy instalace:
1. Konfigurovat schéma napájení na hostitelském počítači brány tak, aby počítač nepřejde do režimu spánku. Pokud hostitelský počítač přejde do režimu spánku, brána neodpovídá na požadavky na data.
2. Zazálohujte si platnost certifikátu spojeného s bránou.

### <a name="install-the-gateway-from-download-center"></a>Instalace brány ze služby Stažení softwaru
1. Přejděte do [stránku pro stažení brány správy dat Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
2. Klikněte na tlačítko **Stáhnout**, vyberte odpovídající verzi (**32-bit** vs. **64-bit**) a klikněte na tlačítko **Další**.
3. Spustit **MSI** přímo nebo uložit na pevný disk a spusťte.
4. Na **úvodní** stránce **jazyk** klikněte na tlačítko **Další**.
5. **Přijměte** licenční smlouvu a klikněte na tlačítko **Další**.
6. Vyberte **složky** instalace brány a klikněte na tlačítko **Další**.
7. Na **připraveno k instalaci** klikněte na **nainstalovat**.
8. Klikněte na tlačítko **Dokončit** pro dokončení instalace.
9. Získání klíče z portálu Azure portal. V části Další podrobné pokyny.
10. Na **registrace brány** stránce **Data Management Gateway Configuration Manager** spuštěn v počítači, proveďte následující kroky:
    1. Vložte klíč, který v textu.
    2. Klikněte na možnost **zobrazit brány klíč** zobrazíte text klíče.
    3. Klikněte na tlačítko **zaregistrovat**.

### <a name="register-gateway-using-key"></a>Registrace brány pomocí klíče
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Pokud jste ještě nevytvořili logické brány v portálu
Vytvoření brány na portálu a získejte klíč ze **konfigurovat** stránce, postupujte podle kroků z návodu v [přesun dat mezi místním prostředím a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku.    

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Pokud jste již vytvořili logické brány v portálu
1. Na webu Azure portal, přejděte na **služby Data Factory** stránce a klikněte na tlačítko **propojené služby** dlaždici.

    ![Stránka datová továrna](media/data-factory-data-management-gateway/data-factory-blade.png)
2. V **propojené služby** vyberte logický **brány** vytvořené na portálu.

    ![logické brány](media/data-factory-data-management-gateway/data-factory-select-gateway.png)  
3. V **bránu Data Gateway** klikněte na **stáhněte a nainstalujte bránu data gateway**.

    ![Odkaz na portálu ke stažení](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)   
4. V **konfigurovat** klikněte na **znovu vytvořte klíč**. Klepněte na tlačítko Ano upozornění po přečtení pečlivě.

    ![Znovu vytvořte klíč](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Klikněte na tlačítko kopírování vedle klíče. Klíč se zkopíruje do schránky.

    ![Kopírování klíče](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Systémové ikony oznamovací oblasti nebo oznámení
Následující obrázek ukazuje některé z ikony na hlavním panelu, které se zobrazí.

![systémové ikony oznamovací oblasti](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Přesuňte ukazatel na zprávu ikonu a oznámení na hlavním panelu systému zobrazí podrobnosti o stav brány nebo aktualizovat operace v místním okně.

### <a name="ports-and-firewall"></a>Porty a brány firewall
Existují dvě brány firewall, je potřeba zvážit: **podnikovou bránu firewall** běžící na střed směrovače organizace, a **brány Windows firewall** nakonfigurovaný jako démon na místním počítači, ve kterém je brána nainstalovat.  

![brány firewall](./media/data-factory-data-management-gateway/firewalls2.png)

Na úrovni podnikovou bránu firewall musí konfigurace následujících domén a odchozích portech:

| Názvy domén | Porty | Popis |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Slouží ke komunikaci s back-end služba pro přesun dat |
| *.core.windows.net |443 |Použít pro kopírování připravený pomocí objektů Blob v Azure (Pokud je nakonfigurovaná)|
| *.frontend.clouddatahub.net |443 |Slouží ke komunikaci s back-end služba pro přesun dat |
| *.servicebus.windows.net |9350-9354, 5671 |Volitelné služby Service bus relay přes TCP, které používají Průvodce kopírováním |


Brány firewall na úrovni Windows jsou obvykle povolené tyto porty pro odchozí spojení. Pokud ne, můžete nakonfigurovat domény a porty odpovídajícím způsobem na počítači brány.

> [!NOTE]
> 1. Podle zdroje / jímky, bude pravděpodobně nutné další domény seznamu povolených IP adres a odchozí porty v bráně firewall podnikové/Windows.
> 2. U některých cloudových databází (například: [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings), [Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access)atd), budete muset seznamu povolených IP adres IP adresu počítače brány na jejich konfiguraci brány firewall.
>
>


#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Kopírování dat ze zdrojového úložiště dat do úložiště dat jímky
Ujistěte se, že pravidla brány firewall jsou povolené vhodným způsobem v podnikové brány firewall, brána Windows firewall na počítači brány a samotné úložiště dat. Když se tato pravidla povolí bránu pro připojení k oba zdroje a jímky úspěšně. Povolte pravidla pro každé úložiště dat, která je zahrnuta v operaci kopírování.

Například pro kopírování z **nebo místní úložiště dat jímky Azure SQL Database nebo Azure SQL Data Warehouse jímky**, proveďte následující kroky:

* Povolit odchozí **TCP** navázat komunikaci s portem **1433** pro bránu Windows firewall a podnikovou bránu firewall.
* Konfigurace nastavení brány firewall pro server Azure SQL přidat IP adresu počítači brány do seznamu povolených IP adres.

> [!NOTE]
> Pokud vaše brána firewall neumožňuje odchozí port 1433, brána nemá přístup k Azure SQL přímo. V takovém případě můžete použít [připravené kopírování](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) k databázi SQL Azure a datový Sklad SQL Azure. V tomto scénáři by v případě přesunu dat vyžadují jenom protokolu HTTPS (port 443).
>
>


### <a name="proxy-server-considerations"></a>Důležité informace o proxy serveru
Pokud vaše podnikové síťové prostředí používá proxy server pro přístup k Internetu, nakonfigurujte bránu správy dat použít příslušná nastavení proxy serveru. Během fáze počáteční registraci můžete nastavit proxy server.

![Nastavení proxy serveru během registrace](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Brána používá proxy server pro připojení ke cloudové službě. Klikněte na tlačítko **změnu** propojení při počátečním nastavení. Zobrazí **nastavení proxy serveru** dialogového okna.

![Nastavení proxy serveru pomocí Správce konfigurace](media/data-factory-data-management-gateway/SetProxySettings.png)

Existují tři možnosti konfigurace:

* **Nepoužívat proxy server**: Brána explicitně nepoužívá všechny proxy pro připojení ke cloudovým službám.
* **Použít systémový proxy server**: Brána používá nastavení serveru proxy, který je nakonfigurovaný v diahost.exe.config a diawp.exe.config.  Pokud žádný proxy server je nakonfigurován diahost.exe.config a diawp.exe.config, brána se připojí ke cloudové službě přímo bez proxy serveru.
* **Použít vlastní proxy server**: Konfigurace nastavení pro bránu, místo použití konfigurací v diahost.exe.config a diawp.exe.config proxy HTTP.  Vyžaduje se adresa a Port.  Uživatelské jméno a heslo jsou volitelné na základě nastavení ověřování váš proxy server.  Všechna nastavení jsou šifrované pomocí certifikát přihlašovacích údajů brány a ukládají místně na hostitelském počítači brány.

Brána správy dat hostitelská služba modulu restartuje automaticky po uložení aktualizované nastavení proxy serveru.

Po brány se úspěšně zaregistroval, pokud chcete zobrazit nebo aktualizovat nastavení proxy serveru, použijte Data Management Gateway Configuration Manager.

1. Spuštění **Data Management Gateway Configuration Manager**.
2. Přepněte na kartu **Nastavení**.
3. Klikněte na tlačítko **změnu** odkaz v **proxy server HTTP** části ke spuštění **nastavit proxy server HTTP** dialogového okna.  
4. Po klepnutí **Další** tlačítko, se zobrazí dialogové okno upozornění s žádostí o vaše svolení k uložení nastavení proxy serveru a hostitelskou službu brány.

Můžete zobrazit a aktualizovat server proxy protokolu HTTP pomocí nástroje Configuration Manager.

![Nastavení proxy serveru pomocí Správce konfigurace](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Pokud nastavení proxy serveru pomocí ověřování NTLM hostitelská služba brány běží pod účtem domény. Pokud změníte heslo pro účet domény, později, nezapomeňte aktualizovat nastavení konfigurace pro službu a restartujte ji odpovídajícím způsobem. Z důvodu tohoto požadavku doporučujeme že použít vyhrazený doménový účet pro přístup k proxy serveru, který nevyžaduje často aktualizujte heslo.
>
>

### <a name="configure-proxy-server-settings"></a>Konfigurace nastavení proxy serveru
Pokud vyberete **použít systémový proxy server** nastavení pro proxy server HTTP, brána používá nastavení proxy v diahost.exe.config a diawp.exe.config.  Pokud v diahost.exe.config a diawp.exe.config není zadán žádný proxy server, brána se připojí ke cloudové službě přímo bez proxy serveru. Následující postup obsahuje pokyny pro aktualizaci diahost.exe.config souboru.  

1. V Průzkumníku souborů vytvořte kopii bezpečné C:\Program Files\Microsoft Data správy Gateway\2.0\Shared\diahost.exe.config zálohovat původní soubor.
2. Spusťte Notepad.exe spuštěný v režimu správce a otevřete textový soubor "C:\Program Files\Microsoft Data správy Gateway\2.0\Shared\diahost.exe.config. Najít výchozí značka pro system.net, jak je znázorněno v následujícím kódu:

         <system.net>
             <defaultProxy useDefaultCredentials="true" />
         </system.net>    

   Poté můžete přidat podrobnosti o proxy serveru, jak je znázorněno v následujícím příkladu:

         <system.net>
               <defaultProxy enabled="true">
                     <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
               </defaultProxy>
         </system.net>

   Další vlastnosti jsou uvnitř značky proxy povoleno zadat požadované nastavení, jako je scriptLocation. Odkazovat na [proxy – Element (nastavení sítě)](https://msdn.microsoft.com/library/sa91de1e.aspx) o syntaxi.

         <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
3. Konfigurační soubor uložte do původního umístění a pak restartujte službu hostitel brány správy dat, která převezme tyto změny. Restartujte službu: pomocí apletu služby v Ovládacích panelech nebo z **Data Management Gateway Configuration Manager** > klikněte na tlačítko **zastavit službu** tlačítko a pak klikněte na tlačítko **Start Služba**. Pokud služba nespustí, je pravděpodobné, že nesprávná syntaxe značky XML byl přidán do konfiguračního souboru aplikace, které proběhly.

> [!IMPORTANT]
> Nezapomeňte aktualizovat **obě** diahost.exe.config a diawp.exe.config.  


Kromě těchto bodů musíte také ověřte, že Microsoft Azure je v seznamu povolených IP adres vaší společnosti. Seznam platných adres IP adres Microsoft Azure si můžete stáhnout z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Je to možné příznaky u problémů souvisejících se server brány firewall a proxy
Pokud narazíte na chyby podobné následující dotazy, je pravděpodobně v důsledku nesprávné konfigurace brány firewall nebo proxy server, který blokuje brána bránily v připojení ke službě Data Factory ke svému ověření. Přečtěte si předchozí část, aby vaše brány firewall a proxy serveru jsou správně nakonfigurované.

1. Při pokusu o registraci gateway zobrazí následující chybová zpráva: "Nepovedlo se zaregistrovat klíč brány. Než se pokusíte znovu zaregistrujte klíč bránu, zkontrolujte, jestli je brána pro správu dat v připojeném stavu a je spuštěna služba hostitele brány správy dat."
2. Při otevření nástroje Configuration Manager můžete zobrazit stav jako "Odpojeno" nebo "Připojení". Při prohlížení protokolů událostí Windows, v části "Prohlížeč událostí" > "Aplikace a služby protokoly" > "Brána správy dat", se zobrazí chybové zprávy, jako je například chybová zpráva: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Otevřete port 8050 pro šifrování přihlašovacích údajů.
**Nastavení přihlašovacích údajů** aplikace používá příchozí port **8050** přenosu přihlašovacích údajů k bráně při nastavování propojený na místní službu na portálu Azure portal. Během instalace brány ve výchozím nastavení, instalaci brány otevře na počítači brány.

Pokud používáte bránu firewall jiného dodavatele, můžete ručně otevřete port 8050. Pokud narazíte na problém brány firewall během instalace brány, můžete zkusit nainstalovat bránu bez konfigurace brány firewall pomocí následujícího příkazu.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Pokud zvolíte ne pro otevření portu 8050 na počítači brány, použijte mechanismy pro zaslání než pomocí **nastavení přihlašovacích údajů** nakonfigurovat přihlašovací údaje úložiště dat aplikace. Například můžete použít [New-AzureRmDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/azurerm.datafactories/new-azurermdatafactoryencryptvalue) rutiny Powershellu. Zobrazit [nastavení přihlašovacích údajů a zabezpečení](#set-credentials-and-securityy) věnované jak přihlašovací údaje úložiště dat je možné nastavit.

## <a name="update"></a>Aktualizace
Ve výchozím nastavení se Brána pro správu dat automaticky aktualizuje při je dostupná novější verze brány. Brána není aktualizován, dokud všechny naplánované úlohy se provádějí. Žádné další úlohy jsou zpracovány brány, dokud se nedokončí operaci aktualizace. Pokud se aktualizace nezdaří, brána se vrátí zpět na předchozí verzi aplikace.

Čas plánované aktualizace se zobrazí na následujících místech:

* Stránka vlastností brány na webu Azure Portal.
* Domovská stránka nástroje Data Management Gateway Configuration Manager
* Upozornění na hlavním panelu systému.

Na kartě Domů na Data Management Gateway Configuration Manageru zobrazí plán aktualizace a čas poslední brány byla nainstalovaná aktualizace.

![Plán aktualizací](media/data-factory-data-management-gateway/UpdateSection.png)

Můžete instalovat aktualizace hned nebo počkejte, brána se automaticky aktualizují v naplánovaném čase. Například následující obrázek ukazuje oznámení zobrazí v aplikaci Správce konfigurace brány spolu s tlačítko Aktualizovat, pomocí kterého Pokud chcete instalovat hned.

![Aktualizace v DMG Configuration Manageru](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Upozornění na hlavním panelu systému by vypadat, jak je znázorněno na následujícím obrázku:

![Zpráva na hlavním panelu systému](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Zobrazí stav operace aktualizace (ruční nebo automatické) na hlavním panelu systému. Při příštím spuštění Správce konfigurace brány se zobrazí zpráva, že brána byla aktualizována spolu s odkazem na oznamovací pruh [co je nové téma](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Zakázat nebo povolit automatické aktualizace funkcí
Můžete zakázat nebo povolit funkci Automatické aktualizace provedením následujících kroků:

[Pro jeden uzel brány]
1. Spusťte Windows PowerShell na počítači brány.
2. Přejděte do složky C:\Program Files\Microsoft integrace Runtime\3.0\PowerShellScript\.
3. Spusťte následující příkaz, který zapnout automatické aktualizace funkci vypnout (zakázat).   

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1  -off
    ```
4. Chcete-li ji zpět na:

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on  
    ```
[Pro vysoce dostupnou a škálovatelnou bránu několika uzly](data-factory-data-management-gateway-high-availability-scalability.md)
1. Spusťte Windows PowerShell na počítači brány.
2. Přejděte do složky C:\Program Files\Microsoft integrace Runtime\3.0\PowerShellScript\.
3. Spusťte následující příkaz, který zapnout automatické aktualizace funkci vypnout (zakázat).   

    Pro bránu s vysokou dostupností funkcí je navíc param AuthKey vyžaduje.
    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1  -off -AuthKey <your auth key>
    ```
4. Chcete-li ji zpět na:

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1  -on -AuthKey <your auth key> 
    ```

## <a name="configuration-manager"></a>Configuration Manager
Po instalaci brány, můžete spustit Správce konfigurace brány správy dat v jednom z následujících způsobů:

1. V **hledání** okno, zadejte **brána správy dat** pro přístup k tohoto nástroje.
2. Spuštění spustitelného souboru **ConfigManager.exe** ve složce: **C:\Program Files\Microsoft Data správy Gateway\2.0\Shared**

### <a name="home-page"></a>Domovská stránka
Na domovské stránce umožňuje provádět následující akce:

* Zobrazit stav brány (připojený ke cloudové službě atd.).
* **Zaregistrujte** pomocí klíče z portálu.
* **Zastavit** a spustit **Data Management Gateway hostitelská služba modulu** na počítači brány.
* **Naplánovat aktualizace** v určitém čase dnů.
* Zobrazit datum, kdy byl brány **poslední aktualizace**.

### <a name="settings-page"></a>Stránka Nastavení
Na stránce nastavení můžete provést následující akce:

* Zobrazit, změnit a exportovat **certifikát** ho používat. Tento certifikát se používá k šifrování přihlašovacích údajů zdroje dat.
* Změna **HTTPS port** pro koncový bod. Brána otevře port pro nastavení přihlašovacích údajů zdroje dat.
* **Stav** koncového bodu
* Zobrazení **certifikát SSL** se používá pro komunikaci prostřednictvím protokolu SSL mezi portálem a brána se nastavit přihlašovací údaje pro zdroje dat.  

### <a name="remote-access-from-intranet"></a>Vzdálený přístup z intranetu  
Tato funkce bude povolena v budoucnu. V budoucích aktualizacích (verzi 3.4 nebo novější), dáme vám povolit / zakázat vzdálené připojení, který ještě dnes se stane při používání Powershellu nebo správce přihlašovacích údajů pro šifrování přihlašovacích údajů pomocí portu 8050 (viz výše). 

### <a name="diagnostics-page"></a>Stránku diagnostiky
Na stránce diagnostiky můžete provést následující akce:

* Povolení podrobného **protokolování**, zobrazovat protokoly v prohlížeči událostí a odeslat protokoly do Microsoftu, pokud došlo k chybě.
* **Test připojení** ke zdroji dat.  

### <a name="help-page"></a>Stránka nápovědy
Na stránce nápovědy zobrazí následující informace:  

* Stručný popis brány
* Číslo verze
* Obsahuje odkazy na online nápovědy, zásady ochrany osobních údajů a licenční smlouvu.  

## <a name="monitor-gateway-in-the-portal"></a>Monitorování brány na portálu
Na webu Azure Portal uvidíte v reálném čase snímek využití prostředků (procesoru, paměti, network(in/out) atd.) na počítači brány.  

1. Na webu Azure portal, přejděte na domovskou stránku datové továrny a klikněte na tlačítko **propojené služby** dlaždici. 

    ![Domovská stránka datové továrny](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png) 
2. Vyberte **brány** v **propojené služby** stránky.

    ![Stránka propojené služby](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. V **brány** stránky, můžete zobrazit paměť a využití procesoru brány.

    ![Využití procesoru a paměti brány](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png) 
4. Povolit **upřesňující nastavení** zobrazíte další podrobnosti, jako je využití sítě.
    
    ![Rozšířené monitorování brány](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

Následující tabulka obsahuje popis sloupců **uzly brány** seznamu:  

Vlastnosti monitorování | Popis
:------------------ | :---------- 
Název | Název logické brány a uzly, které jsou přidružená k bráně. Uzel je místní počítač Windows, která je brána nainstalovaná na něm. Informace o tom, že máte více než jeden uzel (až čtyři uzly) v jedné logické brány, naleznete v tématu [brána správy dat – vysoká dostupnost a škálovatelnost](data-factory-data-management-gateway-high-availability-scalability.md).    
Status | Stav logické brány a uzly brány. Příklad: Online/Offline/Limited/atd. Informace o těchto stavů najdete v tématu [stav brány](#gateway-status) oddílu. 
Verze | Zobrazuje verzi logické brány a každý uzel brány. Verze logické brány je určena na základě verze Většina uzlů ve skupině. Pokud nejsou správně uzly s různými verzemi v nastavení logické brány pouze uzly se stejným číslem verze jako funkci logické brány. Ostatní jsou v režimu omezen a potřeba ručně aktualizovat (pouze v případě automatických aktualizací selže). 
Dostupná paměť | Dostupná paměť na uzel brány. Tato hodnota je snímek téměř v reálném čase. 
Využití procesoru | Využití procesoru uzlu brány. Tato hodnota je snímek téměř v reálném čase. 
Síť (vstup/výstup) | Využití brány uzlu sítě. Tato hodnota je snímek téměř v reálném čase. 
Souběžné úlohy (spuštění / Limit) | Počet úloh nebo úlohy spuštěné na každém uzlu. Tato hodnota je snímek téměř v reálném čase. Limit označuje maximální počet souběžných úloh pro každý uzel. Tato hodnota je definována v závislosti na velikosti počítačů. Můžete zvýšit limit vertikální navýšení kapacity provádění souběžné úlohy v pokročilých scénářích, kdy je využití procesoru/paměti/sítě nevyužitých, ale aktivity se nestíhají dokončit. Tato možnost je také dostupné s jedním uzlem bránou (i když není povolená funkce, škálovatelnost a dostupnost).  
Role | Existují dva typy rolí v několika uzly brány - dispečer a pracovního procesu. Všechny uzly jsou pracovních procesů, což znamená, že jsou všechny slouží ke spuštění úlohy. Existuje pouze jeden uzel dispečer, který slouží k vyžádání úlohy nebo úlohy ze služby cloud services a jejich vypravování do různých pracovních uzlů (včetně samotného).

Na této stránce se zobrazí některá nastavení, která dávat větší smysl, pokud existují dva nebo více uzlů (horizontální navýšení kapacity scénář) v bráně. Zobrazit [brána správy dat – vysoká dostupnost a škálovatelnost](data-factory-data-management-gateway-high-availability-scalability.md) podrobné informace o nastavení brány více uzly.

### <a name="gateway-status"></a>Stav brány
Následující tabulka uvádí možné stavy **uzel brány**: 

Status  | Komentáře a scénáře
:------- | :------------------
Online | Uzel je připojený ke službě Data Factory.
Offline | Uzel je offline.
Upgrade | Uzel je automaticky aktualizuje.
Omezená | Z důvodu problému s připojením. Může být způsobeno problém 8050 portu HTTP, problém s připojením service bus nebo problémům synchronizace přihlašovacích údajů. 
Neaktivní | Uzel je v konfiguraci se liší od konfigurace dalších většinou uzlů.<br/><br/> Uzlem může být neaktivní, pokud se nemůže připojit k ostatním uzlům. 


Následující tabulka uvádí možné stavy **logické brány**. Stav brány, závisí na stavy uzlů brány. 

Status | Komentáře
:----- | :-------
Je nutné registrovat | Žádný uzel ještě není zaregistrované u této logické brány
Online | Uzly brány jsou online
Offline | Žádný uzel ve stavu online.
Omezená | Ne všechny uzly v této brány jsou v dobrém stavu. Tento stav se upozornění, že některý uzel může být mimo provoz. <br/><br/>Může být kvůli problémům synchronizace přihlašovacích údajů na dispečerský/pracovní uzel. 

## <a name="scale-up-gateway"></a>Vertikálně navýšit kapacitu brány
Můžete nakonfigurovat počet **souběžných datových přesun úloh** , který lze spustit na uzlu vertikálně navýšit kapacitu funkci pro přesun dat mezi místním prostředím a cloudem datových úložišť. 

Pokud nejsou dostupné paměti a procesoru využívá dobře, ale nečinná kapacita je 0, by měla vertikálně navýšit kapacitu zvýšením počtu souběžných úloh, které můžou běžet na uzlu. Můžete také vertikálně navýšit kapacitu, když aktivity jsou vypršení časového limitu, protože brána je přetížena. V rozšířených nastaveních uzel brány můžete zvýšit maximální kapacita pro uzel. 
  

## <a name="troubleshooting-gateway-issues"></a>Potíží s bránou
Zobrazit [potíží s bránou](data-factory-troubleshoot-gateway-issues.md) článku informace a tipy pro odstraňování potíží s pomocí brány správy dat pro.  

## <a name="move-gateway-from-one-machine-to-another"></a>Přesunutí brány z jednoho počítače na jiný
Tato část obsahuje kroky pro přesun klientovi brány z jednoho počítače do jiného počítače.

1. Na portálu přejděte **Domovská stránka datové továrny**a klikněte na tlačítko **propojené služby** dlaždici.

    ![Propojení brány dat](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Vyberte bránu **brány DATA GATEWAYS** část **propojené služby** stránky.

    ![Stránka propojené služby s vybranou bránu](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. V **bránu Data gateway** klikněte na **stáhněte a nainstalujte bránu data gateway**.

    ![Brána odkaz ke stažení](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. V **konfigurovat** klikněte na **stáhněte a nainstalujte bránu data gateway**a postupujte podle pokynů k instalaci brány dat na počítači.

    ![Konfigurace stránky](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Zachovat **Microsoft Data Management Gateway Configuration Manager** otevřete.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)    
6. V **konfigurovat** stránky na portálu, klikněte na tlačítko **znovu vytvořte klíč** na panelu příkazů a klikněte na **Ano** pro upozornění. Klikněte na tlačítko **tlačítka pro kopírování** vedle klíče text, který klíč se zkopíruje do schránky. Brána na počítači staré přestane fungovat, jak brzy znovu vytvořit klíč.  

    ![Znovu vytvořte klíč](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Vložit **klíč** do textového pole v **registraci brány** stránku **Data Management Gateway Configuration Manager** na svém počítači. (volitelné) Klikněte na tlačítko **zobrazit brány klíč** zaškrtávací políčko, chcete-li zobrazit text klíče.

    ![Zkopírovat klíč a registrace](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Klikněte na tlačítko **zaregistrovat** k registraci brány s cloudovou službou.
9. Na **nastavení** klikněte na tlačítko **změnu** vyberte stejný certifikát, který byl použit s původní brány, zadejte **heslo**a klikněte na tlačítko **Dokončit**.

   ![Zadejte certifikát](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Provedením následujících kroků můžete exportovat certifikát ze staré brány: spustit Správce konfigurace brány pro správu dat na původní počítač, přejděte **certifikát** klikněte na tlačítko **exportovat** a postupujte podle pokynů.
10. Po úspěšném dokončení registrace brány, měli byste vidět **registrace** nastavena na **registrované** a **stav** nastavena na **spuštěno** na domovské stránce Správce konfigurace brány.

## <a name="encrypting-credentials"></a>Šifrování přihlašovacích údajů
K šifrování přihlašovacích údajů v editoru služby Data Factory, proveďte následující kroky:

1. Spustit webový prohlížeč na **počítači brány**, přejděte na [webu Azure portal](http://portal.azure.com). Vyhledejte svou datovou továrnu v případě potřeby, otevřete datovou továrnu v **služby DATA FACTORY** stránce a potom klikněte na tlačítko **vytvořit a nasadit** ke spuštění editoru služby Data Factory.   
2. Klikněte na existující **propojená služba** ve stromovém zobrazení zobrazit své definici JSON nebo vytvořte propojenou službu, která vyžaduje bránu pro správu dat (například: SQL Server nebo Oracle).
3. V editoru JSON pro **název brány** vlastnost, zadejte název brány.
4. Zadejte název serveru, **zdroj dat** vlastnost **connectionString**.
5. Zadejte název databáze **Initial Catalog** vlastnost **connectionString**.    
6. Klikněte na tlačítko **šifrovat** tlačítko na panelu příkazů, které spouští kliknutím na-po **správce přihlašovacích údajů** aplikace. Měli byste vidět **nastavení přihlašovacích údajů** dialogové okno.

    ![Dialogové okno Nastavení přihlašovacích údajů](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. V **nastavení přihlašovacích údajů** dialogové okno pole, proveďte následující kroky:
   1. Vyberte **ověřování** , který má služba Data Factory používat pro připojení k databázi.
   2. Zadejte jméno uživatele, který má přístup k databázi pro **uživatelské jméno** nastavení.
   3. Zadejte heslo pro tohoto uživatele **heslo** nastavení.  
   4. Klikněte na tlačítko **OK** pro šifrování přihlašovacích údajů a zavřete dialogové okno.
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
Pokud přístup k portálu z počítače, který se liší od počítače brány, musí se ujistěte, zda aplikace Správce přihlašovacích údajů může připojit k počítači brány. Pokud aplikace nemá přístup počítači brány, nepovoluje se nastavit přihlašovací údaje pro zdroj dat a chcete otestovat připojení ke zdroji dat.  

Při použití **nastavení přihlašovacích údajů** aplikace, na portálu šifruje přihlašovací údaje s certifikát uvedený v **certifikát** karty **Správce konfigurace brány**  na počítači brány.

Pokud chcete pro přístup na rozhraní API pro šifrování přihlašovacích údajů, můžete použít [New-AzureRmDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/azurerm.datafactories/new-azurermdatafactoryencryptvalue) rutiny Powershellu pro šifrování přihlašovacích údajů. Rutina se používá certifikát této brány je nakonfigurován na použití pro šifrování přihlašovacích údajů. Přidat zašifrované přihlašovací údaje k **EncryptedCredential** elementu **connectionString** v kódu JSON. Používáte ve formátu JSON pomocí [New-AzureRmDataFactoryLinkedService](https://docs.microsoft.com/powershell/module/azurerm.datafactories/new-azurermdatafactorylinkedservice) rutiny nebo v editoru služby Data Factory.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Existuje více přístupů pro nastavení přihlašovacích údajů pomocí editoru služby Data Factory. Pokud vytvoříte pomocí editoru propojené služby SQL serveru a zadejte přihlašovací údaje ve formátu prostého textu, přihlašovací údaje jsou šifrované pomocí certifikátu, který je vlastníkem služby Data Factory. Certifikát nepoužívá tato brána je nakonfigurován pro použití. Přestože tento přístup může být v některých případech mírně rychlejší, je to méně bezpečné. Proto doporučujeme, abyste postupovali podle tohoto přístupu pouze pro účely vývoje a testování.

## <a name="powershell-cmdlets"></a>Rutiny prostředí PowerShell
Tato část popisuje, jak vytvořit a zaregistrovat bránu pomocí rutin prostředí Azure PowerShell.

1. Spuštění **prostředí Azure PowerShell** v režimu správce.
2. Přihlaste se ke svému účtu Azure tak, že spustíte následující příkaz a zadávání přihlašovacích údajů Azure.

    ```PowerShell
    Connect-AzureRmAccount
    ```
3. Použití **New-AzureRmDataFactoryGateway** rutina pro vytvoření logické brány následujícím způsobem:

    ```PowerShell
    $MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Ukázkový příkaz a výstupní**:

    ```
    PS C:\> $MyDMG = New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

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

1. V prostředí Azure PowerShell přejděte do složky: **C:\Program Files\Microsoft Data správy Gateway\2.0\PowerShellScript\**. Spustit **RegisterGateway.ps1** přidružený k místní proměnné **$Key** jak je znázorněno v následujícím příkazu. Tento skript zaregistruje Klientský agent nainstalovaný na vašem počítači využít logické brány, kterou vytvořili dříve.

    ```PowerShell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Brána ve vzdáleném počítači můžete registrovat pomocí parametru IsRegisterOnRemoteMachine. Příklad:

    ```PowerShell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Můžete použít **Get-AzureRmDataFactoryGateway** rutiny zobrazíte seznam bran ve službě data factory. Když **stav** ukazuje **online**, to znamená, že vaše brána je připravená k použití.

    ```PowerShell        
    Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
Můžete odebrat bránu pomocí **odebrat AzureRmDataFactoryGateway** brány pomocí rutiny a aktualizace popis **Set-AzureRmDataFactoryGateway** rutiny. Syntaxe a další podrobnosti o těchto rutinách najdete v tématu Reference k rutinám Data Factory.  

### <a name="list-gateways-using-powershell"></a>Seznam Gateway pomocí Powershellu

```PowerShell
Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Odstranění brány pomocí Powershellu

```PowerShell
Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```


## <a name="next-steps"></a>Další postup
* Zobrazit [přesun dat mezi místním prostředím a cloudem úložišť dat](data-factory-move-data-between-onprem-and-cloud.md) článku. V tomto návodu vytvoříte kanál, který používá bránu pro přesun dat z databáze v místním SQL serveru do objektu blob Azure.  
