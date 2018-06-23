---
title: Brána pro správu dat pro objekt pro vytváření dat | Microsoft Docs
description: Nastaví bránu dat pro přesun dat mezi místními a cloudem. Přesunutí dat pomocí brány pro správu dat v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: d28d25b74ce46c0323b01670bfdb639c02b92d8a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335801"
---
# <a name="data-management-gateway"></a>Brána správy dat
> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [hostovanou na vlastním integrace runtime v verze 2](../create-self-hosted-integration-runtime.md). 

> [!NOTE]
> Brána pro správu dat má nyní byl přejmenované jako Self-hosted integrace Runtime.  

Brána pro správu dat je klientský agent, který je nutné nainstalovat v prostředí místní ke kopírování dat mezi úložišti dat cloudu a místně. Místní data podporovaných službou Data Factory úložiště jsou uvedeny v [podporované zdroje dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) části.

Doplňuje podle návodu v tomto článku [přesun dat mezi místní a cloudové úložiště dat](data-factory-move-data-between-onprem-and-cloud.md) článku. V tomto návodu vytvoříte kanál, který používá bránu pro přesun dat z databáze SQL serveru místně do objektu blob Azure. Tento článek obsahuje podrobné podrobné informace o Brána pro správu dat. 

Brána pro správu dat můžete škálovat tím, že přidružíte více místní počítače s bránou. Je možné škálovat až zvýšením počtu úloh přesun dat, které můžou běžet souběžně na uzlu. Tato funkce je také k dispozici pro logické brány se jeden uzel. V tématu [škálování Brána pro správu dat v Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) článku.

> [!NOTE]
> Brána v současné době podporuje pouze aktivity kopírování a aktivity uložené procedury v datové továrně. Není možné používat pro přístup k místním zdrojům dat brány z vlastních aktivit.      

## <a name="overview"></a>Přehled
### <a name="capabilities-of-data-management-gateway"></a>Možnosti brány pro správu dat
Brána pro správu dat poskytuje následující možnosti:

* Model místní zdroje dat a cloudové zdroje dat v rámci stejné služby data factory a přesouvat data.
* Máte jedno podokno skla pro monitorování a správu s přehledem stav brány. na stránce služby Data Factory.
* Spravovat přístup k místním zdrojům dat bezpečně.
  * Žádné změny do podnikové brány firewall. Brána je pouze odchozí připojení k Internetu otevřete založené na protokolu HTTP.
  * Šifrování přihlašovacích údajů pro vaše místní data úložiště pomocí certifikátu.
* Přesun dat efektivně – data se přenáší paralelně, odolné vůči přerušované sítě problémy s automatickým logika opakovaných pokusů.

### <a name="command-flow-and-data-flow"></a>Příkaz a tok dat
Pokud použijete aktivitu kopírování ke kopírování dat mezi místními a cloudovými, aktivita používá bránu k přenosu dat z místního zdroje dat do cloudu a naopak.

Zde je souhrn kroků pro kopírování pomocí brána dat a podrobný datový tok pro: ![tok dat pomocí brány.](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Data developer vytvoří bránu pro Azure Data Factory pomocí [portál Azure](https://portal.azure.com) nebo [rutiny prostředí PowerShell](https://msdn.microsoft.com/library/dn820234.aspx).
2. Data developer vytvoří propojené služby pro místnímu úložišti dat tak, že zadáte brány. Jako součást nastavení propojené služby developer dat používá aplikace nastavení pověření k určení typů ověřování a přihlašovací údaje.  Dialogové okno nastavení pověření aplikace komunikuje s úložištěm dat k testování připojení a bránu a uložte přihlašovací údaje.
3. Brána šifruje přihlašovací údaje s certifikát přidružený k bráně (poskytl vývojáře data), před uložením přihlašovací údaje v cloudu.
4. Služba data Factory komunikuje s bránou pro plánování a správu úloh přes řídicí kanál, který používá sdílené služby Azure service bus fronty. Když úlohu kopírování aktivity musí být spuštěna., Data Factory zařadí do fronty požadavek spolu s přihlašovací údaje. Brána se spustí úlohu po dotazování fronty.
5. Brána dešifruje přihlašovací údaje s stejný certifikát a pak připojí k místnímu úložišti dat typu správné ověření a přihlašovací údaje se.
6. Brána zkopíruje data z místního úložiště do cloudového úložiště, nebo naopak v závislosti na konfiguraci aktivitě kopírování v datovém kanálu. Pro tento krok brány komunikuje přímo se služby cloudového úložiště, jako je například úložiště objektů Blob Azure přes zabezpečený kanál (HTTPS).

### <a name="considerations-for-using-gateway"></a>Předpoklady pro použití brány
* Jedna instance brány pro správu dat můžete použít pro více zdrojů dat na místě. Ale **jedna instance brány je vázaný na pouze jeden objekt pro vytváření dat Azure** a nemohou být sdíleny s jinou služby data factory.
* Můžete mít **pouze jedna instance brány pro správu dat** nainstalované na jednom počítači. Předpokládejme, že, máte dvě datové továrny, které potřebují přístup k místním zdrojům dat, budete muset nainstalovat brány ve dvou počítačích na místě. Jinými slovy brány je vázaný na konkrétní data factory
* **Brány nemusí být ve stejném počítači jako zdroj dat**. S brány blíž ke zdroji dat však zkracuje čas pro bránu pro připojení ke zdroji dat. Doporučujeme nainstalovat bránu na počítači, který se liší od ten, který je hostitelem zdroje dat na místě. Pokud zdroj brány a data jsou na různé počítače, brána není pokouší o prostředky se zdrojem dat.
* Můžete mít **více bran na různé počítače připojení ke zdroji dat stejnou místní**. Například můžete mít dvě brány obsluhující dvě datové továrny ale stejný místní zdroj dat není zaregistrována datové továrny.
* Pokud je již nainstalovaná na vašem počítači slouží **Power BI** scénář, nainstalovat **samostatnou bránu pro Azure Data Factory** na jiném počítači.
* Brány musí použít i v případě, že používáte **ExpressRoute**.
* Váš zdroj dat považovat za zdroj dat na místě, (který je za bránou firewall) i při použití **ExpressRoute**. Použijte bránu k navázání připojení mezi službou a zdroj dat.
* Je nutné **použití brány** i v případě, že je úložiště dat v cloudu **virtuálního počítače Azure IaaS**.

## <a name="installation"></a>Instalace
### <a name="prerequisites"></a>Požadavky
* U podporovaných **operačního systému** jsou verze Windows 7, Windows 8 nebo 8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Instalace brány pro správu dat na řadiči domény není aktuálně podporována.
* Rozhraní .NET framework 4.5.1 nebo novější je vyžadován. Pokud instalujete brány na počítače s Windows 7, nainstalujte rozhraní .NET Framework 4.5 nebo novější. V tématu [požadavky na systém rozhraní .NET Framework](https://msdn.microsoft.com/library/8z6watww.aspx) podrobnosti.
* Doporučené **konfigurace** pro bránu počítač je alespoň 2 GHz, 4 jádra, 8 GB paměti RAM a 80-GB místa na disku.
* Pokud hostitelský počítač přejde do režimu spánku, brána neodpovídá na požadavky na data. Proto konfigurovat odpovídající **schéma napájení** v počítači před instalací brány. Pokud je počítač nakonfigurovaný do režimu spánku, vyzve k instalaci brány zprávu.
* Musíte být správce na počítači k instalaci a konfiguraci brány pro správu dat úspěšně. Můžete přidat další uživatelům **Brána pro správu dat uživatele** místní skupiny Windows. Členové této skupiny jsou schopné používat **Správce konfigurace brány pro správu dat** nástroj pro konfiguraci brány.

Jako běh aktivit kopie dojít na konkrétní frekvenci, využití prostředků (procesor, paměť) na počítači také používá se stejný vzor s ve špičce a doby nečinnosti. Využití prostředků závisí také výraznou na množství dat přesouvá. Více úloh kopie jsou v průběhu, uvidíte zvedla během špiček využití prostředků.

### <a name="installation-options"></a>Možnosti instalace
Brána pro správu dat lze nainstalovat následujícím způsobem:

* Stažením balíčku MSI Instalační program z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717).  Soubor MSI můžete také použít k upgradu existující brány pro správu dat na nejnovější verzi, se všemi možnými nastavení zachovaná.
* Kliknutím na **stáhnout a nainstalovat bránu dat** odkazu na RUČNÍ instalace nebo **nainstalovat přímo na tomto počítači** pod Expresní instalace. V tématu [přesun dat mezi místními a cloudovými](data-factory-move-data-between-onprem-and-cloud.md) podrobné pokyny k používání Expresní instalace najdete v článku. Manuální krok přejdete na webu Stažení softwaru.  Pokyny ke stažení a instalaci brány ze služby Stažení softwaru jsou v další části.

### <a name="installation-best-practices"></a>Osvědčené postupy instalace:
1. Schéma napájení nakonfigurujte na hostitelském počítači pro bránu, aby počítač nepřejde do režimu spánku. Pokud hostitelský počítač přejde do režimu spánku, brána neodpovídá na požadavky na data.
2. Zálohujte certifikát přidružený k bráně.

### <a name="install-the-gateway-from-download-center"></a>Bránu nainstalovat ze služby Stažení softwaru
1. Přejděte na [stránky pro stažení Brána pro správu dat](https://www.microsoft.com/download/details.aspx?id=39717).
2. Klikněte na tlačítko **Stáhnout**, vyberte příslušnou verzi (**32-bit** vs. **64-bit**) a klikněte na tlačítko **Další**.
3. Spustit **MSI** přímo nebo uložte ho na pevný disk a spustit.
4. Na **úvodní** vyberte **jazyk** klikněte na tlačítko **Další**.
5. **Přijměte** licenční smlouvu a klikněte na tlačítko **Další**.
6. Vyberte **složky** instalace brány a klikněte na tlačítko **Další**.
7. Na **připravené k instalaci** klikněte na tlačítko **nainstalovat**.
8. Klikněte na tlačítko **Dokončit** k dokončení instalace.
9. Získáte klíč z portálu Azure. Najdete v části Další podrobné pokyny.
10. Na **registrace brány** stránka **Správce konfigurace brány pro správu dat** spuštěná na počítači, proveďte následující kroky:
    1. Vložte klíč v textu.
    2. Volitelně klikněte na **Show klíč brány** zobrazíte text klíče.
    3. Klikněte na tlačítko **zaregistrovat**.

### <a name="register-gateway-using-key"></a>Zaregistrujte bránu pomocí klíče
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Pokud jste ještě nevytvořili logické brány na portálu
Vytvoření brány na portálu a získání klíče z **konfigurace** stránky, postupujte podle kroků z návod v [přesun dat mezi místními a cloudovými](data-factory-move-data-between-onprem-and-cloud.md) článku.    

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Pokud jste již vytvořili logické brány na portálu
1. Na portálu Azure, přejděte na **Data Factory** a klikněte na tlačítko **propojené služby** dlaždici.

    ![Stránka objektu pro vytváření dat](media/data-factory-data-management-gateway/data-factory-blade.png)
2. V **propojené služby** vyberte logické **brány** vytvořený na portálu.

    ![logické brány](media/data-factory-data-management-gateway/data-factory-select-gateway.png)  
3. V **bránu Data Gateway** klikněte na tlačítko **stáhnout a nainstalovat bránu dat**.

    ![Stáhněte si odkaz na portálu](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)   
4. V **konfigurace** klikněte na tlačítko **znovu vytvořte klíč**. Klepněte na tlačítko Ano upozornění po přečtení ji pečlivě.

    ![Znovu vytvořte klíč](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Klikněte na tlačítko Kopírovat vedle klíč. Klíč je zkopírován do schránky.

    ![Kopírování klíče](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Systémové ikony oznamovací oblasti nebo oznámení
Následující obrázek ukazuje některé ikony oznamovací oblasti, které vidíte.

![systémové ikony oznamovací oblasti](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Pokud přesuňte ukazatel na systému panelu ikonu nebo oznámení, můžete zobrazit podrobnosti o stavu operace aktualizace pro bránu v automaticky otevíraném okně.

### <a name="ports-and-firewall"></a>Porty a brány firewall
Existují dvě brány firewall, je potřeba zvážit: **podniková brána firewall** systémem směrovači centrální organizace, a **brány Windows firewall** nakonfigurovaný jako démon na místním počítači, kde je brána nainstalovat.  

![brány firewall](./media/data-factory-data-management-gateway/firewalls2.png)

Na úrovni podniková brána firewall je nutné nakonfigurovat následující domény a odchozí porty:

| Názvy domén | Porty | Popis |
| --- | --- | --- |
| *.servicebus.windows.net |443, 80 |Používá ke komunikaci s back-end služba pro přesun dat |
| *.core.windows.net |443 |Použít pro kopírování připravený pomocí objektů Blob v Azure (Pokud je nakonfigurováno)|
| *.frontend.clouddatahub.net |443 |Používá ke komunikaci s back-end služba pro přesun dat |
| *.servicebus.windows.net |9350-9354, 5671 |Předávání přes volitelné service bus přes TCP používá Průvodce kopírováním |


Na úrovni brány firewall systému Windows jsou obvykle povolené tyto Odchozí porty. Pokud není, můžete nakonfigurovat domén a porty odpovídajícím způsobem v počítači brány.

> [!NOTE]
> 1. Na základě vaší zdroje / jímky, možná budete muset povolených dalších domén a odchozí porty v bráně firewall podnikové a Windows.
> 2. Pro některé databáze cloudu (například: [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings), [Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access)atd), budete muset seznam povolených adres IP adresu počítače brány na jejich konfiguraci brány firewall.
>
>


#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Kopírování dat ze zdrojového úložiště dat do úložiště dat jímka
Zkontrolujte, zda jsou pravidla brány firewall na podnikové brány firewall, brána Windows firewall v počítači s bránou povolené správně a data ukládat sám sebe. Tato pravidla povolíte bránu pro připojení k obou zdroj a jímka úspěšně. Povolení pravidel pro každou úložiště dat, který je zahrnut v operaci kopírování.

Například pro kopírování z **úložišti místní data jímky Azure SQL Database nebo jímky Azure SQL Data Warehouse**, proveďte následující kroky:

* Povolit odchozí **TCP** komunikaci na portu **1433** pro bránu Windows firewall a podniková brána firewall.
* Konfigurace nastavení brány firewall serveru Azure SQL na IP adresu počítače brány přidat do seznamu povolených IP adres.

> [!NOTE]
> Pokud brána firewall nedovoluje odchozí port 1433, brána nemá přístup k Azure SQL přímo. V takovém případě můžete použít [připravený kopie](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) do SQL Azure Database nebo datového skladu SQL Azure. V tomto scénáři by pro přesun dat vyžadují jenom protokolu HTTPS (port 443).
>
>


### <a name="proxy-server-considerations"></a>Důležité informace o proxy serveru
Pokud vaše podnikové síťové prostředí používá proxy server pro přístup k Internetu, nakonfigurujte Brána pro správu dat použít příslušná nastavení proxy serveru. Během fáze počáteční registrace můžete nastavit proxy server.

![Sada proxy během registrace](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Brána používá proxy server pro připojení ke cloudové službě. Klikněte na tlačítko **změnu** propojení během počáteční instalace. Zobrazí **nastavení proxy serveru** dialogové okno.

![Sada proxy serveru pomocí Správce konfigurace](media/data-factory-data-management-gateway/SetProxySettings.png)

Existují tři možnosti konfigurace:

* **Nepoužívejte proxy**: Brána pro připojení ke cloudovým službám explicitně nepoužívá jakýkoli proxy server.
* **Použít proxy server systému**: Brána používá proxy server, který je nastavení nakonfigurované v diahost.exe.config a diawp.exe.config.  Pokud žádný proxy server je nakonfigurován v diahost.exe.config a diawp.exe.config, brána připojí ke cloudové službě přímo bez proxy serveru.
* **Používat vlastní proxy server**: Konfigurace nastavení pro bránu, místo použití konfigurace v diahost.exe.config a diawp.exe.config proxy protokolu HTTP.  Adresa a Port jsou povinné.  Uživatelské jméno a heslo jsou volitelné v závislosti na nastavení vašeho proxy ověřování.  Všechna nastavení jsou šifrován certifikát přihlašovacích údajů brány a jsou uložené místně na hostitelském počítači brány.

Brána pro správu dat hostitelská služba restartuje automaticky po uložení aktualizované nastavení proxy serveru.

Po Brány byl úspěšně registrován, pokud chcete zobrazit nebo aktualizovat nastavení proxy serveru, použijte Správce konfigurace brány pro správu dat.

1. Spusťte **Správce konfigurace brány pro správu dat**.
2. Přepněte na kartu **Nastavení**.
3. Klikněte na tlačítko **změnu** na odkaz v **server Proxy protokolu HTTP** části spustíte **nastavení proxy serveru HTTP** dialogové okno.  
4. Po kliknutí **Další** tlačítko se zobrazí dialog s upozorněním žádostí o oprávnění k uložení nastavení proxy serveru a restartujte hostitelskou službu brány.

Můžete zobrazit a aktualizovat server proxy protokolu HTTP pomocí nástroje Configuration Manager.

![Sada proxy serveru pomocí Správce konfigurace](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Pokud jste nastavili proxy server pomocí ověřování NTLM, kompatibilní se hostitelská služba brány pro účet domény. Pokud změníte heslo pro účet domény později, musíte aktualizovat nastavení konfigurace pro službu a restartujte ji odpovídajícím způsobem. Kvůli tomuto požadavku, doporučujeme používat vyhrazený doménový účet pro přístup k proxy serveru, který nevyžaduje často aktualizovat heslo.
>
>

### <a name="configure-proxy-server-settings"></a>Nakonfigurujte nastavení serveru proxy
Pokud vyberete **používat proxy server systému** nastavení pro proxy server HTTP, brána používá nastavení proxy v diahost.exe.config a diawp.exe.config.  Pokud žádný proxy server je zadané v diahost.exe.config a diawp.exe.config, brána připojí ke cloudové službě přímo bez proxy serveru. Následující postup obsahuje pokyny pro aktualizaci souboru diahost.exe.config.  

1. V Průzkumníku souborů vytvořte bezpečné kopii C:\Program Files\Microsoft Data správy Gateway\2.0\Shared\diahost.exe.config zálohování původní soubor.
2. Spusťte Notepad.exe spuštění jako správce a otevřete textový soubor "C:\Program Files\Microsoft Data správy Gateway\2.0\Shared\diahost.exe.config. Najít výchozí značka pro system.net, jak je znázorněno v následujícím kódu:

         <system.net>
             <defaultProxy useDefaultCredentials="true" />
         </system.net>    

   Poté můžete přidat podrobnosti o proxy serveru, jak je znázorněno v následujícím příkladu:

         <system.net>
               <defaultProxy enabled="true">
                     <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
               </defaultProxy>
         </system.net>

   Další vlastnosti jsou uvnitř značky proxy povoleno zadat požadované nastavení, jako je scriptLocation. Odkazovat na [proxy – Element (nastavení sítě)](https://msdn.microsoft.com/library/sa91de1e.aspx) na syntaxi.

         <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
3. Konfigurační soubor uložte do původního umístění a potom restartujte službu hostitel brány správy dat, která vybere změny. Restartování služby: pomocí apletu služby v Ovládacích panelech nebo z **Správce konfigurace brány pro správu dat** > klikněte na tlačítko **zastavit službu** tlačítko a pak klikněte na **Start Služba**. Pokud se služba nespustí, je pravděpodobné, že byl přidán nesprávnou syntaxi – značka XML do konfiguračního souboru aplikace, která byla upravena.

> [!IMPORTANT]
> Nezapomeňte aktualizovat **i** diahost.exe.config a diawp.exe.config.  


Kromě těchto bodů musíte také zkontrolujte, zda že je v seznamu povolených IP adres vaší společnosti Microsoft Azure. Seznam platných Microsoft Azure IP adres si můžete stáhnout z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Možné příznaky brány firewall a proxy server s problémy
Pokud narazíte na chyby, které jsou podobné těm, které jsou následující, je pravděpodobně z důvodu nesprávné konfigurace brány firewall nebo proxy serveru, která blokuje brány v připojení k objektu pro vytváření dat ke svému ověření. Podívejte se na předchozí část, aby brána firewall a proxy serveru jsou správně nakonfigurovány.

1. Při pokusu o registraci brány se zobrazí následující chyba: "se nepodařilo zaregistrovat klíč brány. Než se pokusíte znovu zaregistrujte klíč brány, potvrďte, že je brána pro správu dat v připojeném stavu a je spuštěna hostitelská služba brány správy dat."
2. Když otevřete nástroje Configuration Manager, uvidíte stav jako "Odpojeno" nebo "Připojení". Při zobrazení protokoly událostí systému Windows, v části "Prohlížeč událostí" > "Aplikace a služby Logs" > "Brána pro správu dat", se zobrazí chybové zprávy, jako je například k následující chybě: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Otevřete port 8050 pro šifrování přihlašovacích údajů.
**Nastavení pověření** aplikace používá portu pro příchozí spojení **8050** k předávání přes přihlašovací údaje k bráně při nastavování služby místní propojené na portálu Azure. Během instalace brány ve výchozím nastavení, instalaci brány nástroje ho otevře na počítači s bránou.

Pokud používáte bránu firewall jiného výrobce, můžete ručně otevřete port 8050. Pokud narazíte na problém brány firewall během instalace brány, můžete zkusit pomocí následujícího příkazu se nainstalovat bránu bez konfiguraci brány firewall.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Pokud se rozhodnete, že není otevřete port 8050 na počítači s bránou, použijte mechanismy pro než pomocí **nastavení pověření** aplikace nakonfigurovat přihlašovací údaje k úložišti dat. Například můžete použít [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) rutiny prostředí PowerShell. V tématu [nastavení přihlašovacích údajů a zabezpečení](#set-credentials-and-securityy) oddíl na tom, jak data ukládat přihlašovací údaje lze nastavit.

## <a name="update"></a>Aktualizace
Ve výchozím nastavení se Brána pro správu dat automaticky aktualizuje až bude dostupná novější verze brány. Brána není aktualizován, dokud všechny naplánované úlohy se provádějí. Žádné další úlohy jsou zpracovávány bránou, dokud se nedokončí operaci aktualizace. Pokud se aktualizace nezdaří, brány je vrácena zpět na původní verzi.

Zobrazí čas naplánované aktualizace na těchto místech:

* Stránka Vlastnosti brány na portálu Azure.
* Domovská stránka nástroje správu Správce konfigurace brány dat
* Zpráva oznámení panelu systému.

Kartě Domů nástroje správu Správce konfigurace brány dat zobrazí plán aktualizací a čas posledního bráně byl nainstalován aktualizovány.

![Plán aktualizací](media/data-factory-data-management-gateway/UpdateSection.png)

Můžete instalovat aktualizace hned, nebo počkat pro bránu, aby se automaticky aktualizovaly v naplánovaném čase. Například následující obrázek ukazuje oznámení zobrazí v aplikaci Správce konfigurace brány společně s tlačítko aktualizace, které lze klepnout a nainstalujte ji okamžitě.

![Aktualizace v DMG Configuration Manager](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Oznámení na hlavním panelu systému vypadat, jak je znázorněno na následujícím obrázku:

![Zpráva panelu systému](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Zobrazí stav operace aktualizace (ručně nebo automaticky) na hlavním panelu. Při příštím spuštění Správce konfigurace brány, zobrazí se zpráva na panelu oznámení, že brány se aktualizovala spolu s odkazem na [co je nového tématu](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Zapnout/vypnout automatickou aktualizaci funkce
Je možné zapnout/vypnout funkci Automatické aktualizace provedením následujících kroků:

[Pro jeden uzel brány]
1. Spusťte prostředí Windows PowerShell v počítači s bránou.
2. Přejděte do složky C:\Program Files\Microsoft integrace Runtime\3.0\PowerShellScript\.
3. Spusťte následující příkaz, který zapnout automatickou aktualizaci funkci vypnout (zakázat).   

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1  -off
    ```
4. Chcete-li zpátky na:

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on  
    ```
[Pro více uzly vysoce dostupnou a škálovatelnou brány (preview)](data-factory-data-management-gateway-high-availability-scalability.md)
1. Spusťte prostředí Windows PowerShell v počítači s bránou.
2. Přejděte do složky C:\Program Files\Microsoft integrace Runtime\3.0\PowerShellScript\.
3. Spusťte následující příkaz, který zapnout automatickou aktualizaci funkci vypnout (zakázat).   

    Pro bránu s funkci vysoké dostupnosti (preview) se vyžaduje navíc AuthKey param.
    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1  -off -AuthKey <your auth key>
    ```
4. Chcete-li zpátky na:

    ```PowerShell
    .\IntegrationRuntimeAutoUpdateToggle.ps1  -on -AuthKey <your auth key> 
    ```

## <a name="configuration-manager"></a>Configuration Manager
Po instalaci brány, můžete spustit Správce konfigurace brány pro správu dat v jednom z následujících způsobů:

1. V **vyhledávání** zadejte **Brána pro správu dat** pro přístup k tento nástroj.
2. Spuštění spustitelného souboru **ConfigManager.exe** ve složce: **C:\Program Files\Microsoft Data správy Gateway\2.0\Shared**

### <a name="home-page"></a>Domovská stránka
Domovská stránka umožňuje provést následující akce:

* Zobrazení stavu brány (připojené ke cloudové službě atd.).
* **Zaregistrovat** pomocí klíče z portálu.
* **Zastavit** a spustit **hostitele brány správy dat služby** na počítači s bránou.
* **Naplánovat aktualizace** v určitém čase dny.
* Zobrazit datum, kdy byl brány **poslední aktualizace**.

### <a name="settings-page"></a>Stránka Nastavení
Stránka nastavení umožňuje provádět následující akce:

* Zobrazit, změnit a exportujte **certifikát** používá bránu. Tento certifikát se používá k šifrování přihlašovacích údajů zdroje dat.
* Změna **HTTPS port** pro koncový bod. Brána otevře port pro nastavení přihlašovacích údajů zdroje dat.
* **Stav** koncového bodu
* Zobrazení **certifikát SSL** se používá pro komunikaci prostřednictvím protokolu SSL mezi portálem a bránu a nastavte přihlašovací údaje pro zdroje dat.  

### <a name="remote-access-from-intranet"></a>Vzdálený přístup z intranetu  
Tato funkce bude v budoucnu povolena. V budoucích aktualizacích (v3.4 nebo novější) jsme vám umožní povolit / zakázat žádné vzdálené připojení, které dnes dochází při používání aplikace prostředí PowerShell nebo správce přihlašovacích údajů pro šifrování přihlašovacích údajů pomocí portu 8050 (viz část výše). 

### <a name="diagnostics-page"></a>Stránku diagnostiky
Stránka Diagnostika umožňuje provádět následující akce:

* Povolit podrobné **protokolování**, zkontrolujte protokoly v prohlížeči událostí a protokoly odeslat společnosti Microsoft, pokud došlo k chybě.
* **Testovací připojení** ke zdroji dat.  

### <a name="help-page"></a>Stránka nápovědy
Na stránce nápovědy zobrazí následující informace:  

* Stručný popis brány
* Číslo verze
* Odkazy na online nápovědy, prohlášení o ochraně osobních údajů a licenční smlouvy.  

## <a name="monitor-gateway-in-the-portal"></a>Monitorování brány na portálu
Na portálu Azure můžete zobrazit skoro v reálném čase snímek využití prostředků (procesoru, paměti, network(in/out) atd.) v počítači brány.  

1. Na portálu Azure, přejděte na domovské stránce objektu pro vytváření dat a klikněte na tlačítko **propojené služby** dlaždici. 

    ![Domovská stránka objektu pro vytváření dat](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png) 
2. Vyberte **brány** v **propojené služby** stránky.

    ![Stránka propojené služby](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. V **brány** stránky, uvidíte paměť a využití procesoru brány.

    ![Využití procesoru a paměti brány](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png) 
4. Povolit **upřesňující nastavení** zobrazíte další podrobnosti, jako je například využití sítě.
    
    ![Rozšířené monitorování brány](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

Následující tabulka obsahuje popisy sloupců **uzly brány** seznamu:  

Vlastnost monitorování | Popis
:------------------ | :---------- 
Název | Název logické brány a uzly asociovaným s bránou. Uzel je místní počítač Windows, který je brána nainstalovaná na něm. Informace na s více než jeden uzel (až čtyř uzlů) v jedné logické brány najdete v tématu [Brána pro správu dat – vysokou dostupnost a škálovatelnost](data-factory-data-management-gateway-high-availability-scalability.md).    
Status | Stav logické brány a uzly brány. Příklad: Online nebo Offline nebo Limited/atd. Informace o těchto stavů najdete v tématu [stav brány](#gateway-status) části. 
Verze | Zobrazuje verzi logické brány a každý uzel brány. Verze logické brány je určen na základě verze Většina uzlů ve skupině. Pokud nejsou správně uzly s různými verzemi v nastavení logické brány, pouze uzly se číslo verze jako funkce logické brány. Ostatní jsou v režimu omezené a je nutné ručně aktualizovat (pouze v případě automatické aktualizace nezdaří). 
Dostupná paměť | Dostupná paměť na uzel brány. Tato hodnota je snímku near v reálném čase. 
Využití procesoru | Využití procesoru uzlu brány. Tato hodnota je snímku near v reálném čase. 
Sítě (vstup/výstup) | Využití uzlu brány sítě. Tato hodnota je snímku near v reálném čase. 
Souběžné úlohy (spuštění / omezit) | Počet úlohy nebo úlohy na jednotlivých uzlech spuštěné. Tato hodnota je snímku near v reálném čase. Limit označuje, že maximální souběžných úloh pro každý uzel. Tato hodnota je definována v závislosti na velikosti počítače. Můžete zvýšit limit škálování provádění souběžné úlohy v pokročilých scénářích, kde procesoru a paměti nebo síťových je v části využívat, ale aktivity jsou vypršení časového limitu. Tato funkce je také dostupná s jedním uzlem bránou (i když není povolená funkce škálovatelnost a dostupnost).  
Role | Existují dva typy rolí v několika uzly brány - dispečera a pracovního procesu. Všechny uzly jsou pracovníci, což znamená, že se všechny slouží k provedení úlohy. Je pouze jeden uzel dispečera, který se používá k přijetí změn úlohy nebo úlohy z cloudové služby a jejich odesílání na jiný pracovní uzly (včetně samotného).

Na této stránce se zobrazí některá nastavení, která dávat větší smysl, pokud jsou dostupné dva nebo víc uzlů (škálování scénář) v bráně. V tématu [Brána pro správu dat – vysokou dostupnost a škálovatelnost](data-factory-data-management-gateway-high-availability-scalability.md) podrobnosti o nastavení brány více uzly.

### <a name="gateway-status"></a>Stav brány.
Následující tabulka obsahuje možné stavy z **uzel brány**: 

Status  | Komentáře nebo scénáře
:------- | :------------------
Online | Uzel připojen ke službě Data Factory.
Offline | Uzel je offline.
Upgrade | Uzel, která má být automaticky aktualizován.
Omezená | Problém s připojením. Může být kvůli problému 8050 portu HTTP, problém s připojením služby sběrnice nebo problémům synchronizace přihlašovacích údajů. 
Neaktivní | Uzel je v konfiguraci se liší od konfigurace jiných Většina uzlů.<br/><br/> Uzlem může být neaktivní, když se nemůže připojit k jiné uzly. 


Následující tabulka obsahuje možné stavy z **logické brány**. Stav brány. závisí na stavy, které jsou uzly brány. 

Status | Komentáře
:----- | :-------
Nutná registrace | K této logické brány je ještě zaregistrován žádný uzel.
Online | Brána uzly jsou online
Offline | Žádný uzel ve stavu online.
Omezená | Ne všechny uzly v této brány jsou v dobrém stavu. Tento stav se upozornění, že některé uzel může být mimo provoz! <br/><br/>Může být kvůli problémům synchronizace přihlašovacích údajů na dispečera nebo pracovního uzlu. 

## <a name="scale-up-gateway"></a>Škálování brány
Můžete konfigurovat počet **úlohy Přesun souběžných datového** , můžete spustit na uzlu škálování schopnosti produktu přesouvání dat mezi místními a cloudovými datová úložiště. 

Pokud nejsou dobře využité dostatek paměti a procesoru, ale nečinnosti kapacita je 0, by měl škálovat zvýšením počtu souběžných úloh, které můžou běžet na uzlu. Můžete také vertikálně navýšit kapacitu aktivity jsou vypršení časového limitu, protože je přetížena brány. V rozšířených nastaveních uzel brány můžete zvýšit maximální kapacita pro uzel. 
  

## <a name="troubleshooting-gateway-issues"></a>Řešení potíží s brány
V tématu [řešení potíží s brány](data-factory-troubleshoot-gateway-issues.md) informace nebo tipy pro řešení potíží s použitím brány pro správu dat najdete v článku.  

## <a name="move-gateway-from-one-machine-to-another"></a>Přesunutí brány z jednoho počítače na jiný
Tato část obsahuje kroky pro přesunutí klient brány z jednoho počítače do jiného počítače.

1. Na portálu, přejděte na **Domovská stránka objektu pro vytváření dat**a klikněte na tlačítko **propojené služby** dlaždici.

    ![Odkaz brány dat](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Vyberte bránu v **brány DATA GATEWAYS** části **propojené služby** stránky.

    ![Stránka propojené služby s vybranou bránu](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. V **bránu Data gateway** klikněte na tlačítko **stáhnout a nainstalovat bránu dat**.

    ![Stáhněte si odkaz brány](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. V **konfigurace** klikněte na tlačítko **stáhnout a nainstalovat bránu dat**a podle pokynů nainstalujte bránu dat na počítač.

    ![Konfigurace stránky](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Zachovat **Správce konfigurace brány pro správu dat společnosti Microsoft** otevřete.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)    
6. V **konfigurace** na portálu, klikněte na tlačítko **znovu vytvořte klíč** na panelu příkazů a klikněte na **Ano** pro upozornění. Klikněte na tlačítko **tlačítko Kopírovat** vedle text klíče, který kopíruje klíč do schránky. Brány na původní počítač přestane fungovat jako brzy znovu vytvořte klíč.  

    ![Znovu vytvořte klíč](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Vložení **klíč** do textového pole v **registrace brány** stránky **Správce konfigurace brány pro správu dat** na váš počítač. (volitelné) Klikněte na tlačítko **Show klíč brány** zaškrtávací políčko, chcete-li zobrazit text klíče.

    ![Kopírování klíče a registrace](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Klikněte na tlačítko **zaregistrovat** k registraci brány s cloudovou službou.
9. Na **nastavení** , klikněte na **změnu** vyberte stejný certifikát, který byl použit s původní brány, zadejte **heslo**a klikněte na tlačítko **Dokončit**.

   ![Zadejte certifikát](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Provedením následujících kroků můžete exportovat certifikát z původního brány: Spusťte Správce konfigurace brány pro správu dat na původní počítač, přepněte do **certifikát** , klikněte na **exportovat** tlačítko a postupujte podle pokynů.
10. Po úspěšné registraci brány, měli byste vidět **registrace** nastavena na **registrovaná** a **stav** nastavena na **Začínáme** na domovské stránce Správce konfigurace brány.

## <a name="encrypting-credentials"></a>Šifrování přihlašovacích údajů
K šifrování přihlašovacích údajů v editoru služby Data Factory, proveďte následující kroky:

1. Spustit webový prohlížeč na **počítač brány**, přejděte na [portál Azure](http://portal.azure.com). Hledání objektu pro vytváření dat v případě potřeby, otevřete objekt pro vytváření dat v **DATA FACTORY** a pak klikněte na tlačítko **vytvořit a nasadit** ke spuštění editoru služby Data Factory.   
2. Klikněte na existující **propojená služba** ve stromovém zobrazení naleznete ve své definici JSON nebo vytvoření propojené služby, který vyžaduje Brána pro správu dat (například: SQL Server nebo Oracle).
3. V editoru JSON pro **gatewayName** vlastnost, zadejte název brány.
4. Zadejte název serveru pro **zdroj dat** vlastnost **connectionString**.
5. Zadejte název databáze **počáteční katalog** vlastnost **connectionString**.    
6. Klikněte na tlačítko **šifrovat** na panelu příkazů, který spustí a klikněte na tlačítko-po **správce přihlašovacích údajů** aplikace. Měli byste vidět **nastavení pověření** dialogové okno.

    ![Dialogové okno Nastavení přihlašovacích údajů](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. V **nastavení pověření** dialogové okno pole, proveďte následující kroky:
   1. Vyberte **ověřování** má služba Data Factory sloužící k připojení k databázi.
   2. Zadejte jméno uživatele, který má přístup k databázi pro **uživatelské jméno** nastavení.
   3. Zadejte heslo pro uživatele **heslo** nastavení.  
   4. Klikněte na tlačítko **OK** k šifrování přihlašovacích údajů a zavřete dialogové okno.
8. Měli byste vidět **encryptedCredential** vlastnost **connectionString** nyní.

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
Pokud máte přístup k portálu z počítače, který se liší od počítače brány, musí se ujistěte, že aplikace Správce přihlašovacích údajů můžete připojit k počítači s bránou. Pokud aplikace nebudou moci připojit počítače brány, neumožňuje vám umožní nastavit přihlašovací údaje pro zdroj dat a otestovat připojení ke zdroji dat.  

Při použití **nastavení pověření** aplikace portálu šifruje přihlašovací údaje s certifikát uvedený v **certifikát** kartě **Správce konfigurace brány**  na počítači s bránou.

Pokud hledáte způsob založené na rozhraní API pro šifrování přihlašovacích údajů, můžete použít [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) rutiny prostředí PowerShell k šifrování přihlašovacích údajů. Rutina používá certifikát tuto bránu Pokud chcete použít k šifrování přihlašovacích údajů. Přidat zašifrované přihlašovací údaje k **EncryptedCredential** element **connectionString** v kódu JSON. Použití formátu JSON s [New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) rutiny nebo v editoru služby Data Factory.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Neexistuje jeden další postup pro nastavení přihlašovacích údajů pomocí editoru služby Data Factory. Pokud vytvoříte službu SQL Server propojené pomocí editoru a zadejte přihlašovací údaje ve formátu prostého textu, přihlašovací údaje jsou šifrované pomocí certifikátu, který vlastní službu Data Factory. Nepoužívá certifikát této brány je nakonfigurovaný na použití. Tento přístup může být v některých případech trochu rychlejší, je to méně bezpečné. Proto doporučujeme, postupujte podle tohoto přístupu pouze pro účely vývoje/testování.

## <a name="powershell-cmdlets"></a>Rutiny prostředí PowerShell
Tato část popisuje, jak vytvořit a registrovat bránu pomocí rutin prostředí Azure PowerShell.

1. Spusťte **prostředí Azure PowerShell** v režimu správce.
2. Přihlaste se k účtu Azure tak, že spustíte následující příkaz a zadání přihlašovacích údajů Azure.

    ```PowerShell
    Connect-AzureRmAccount
    ```
3. Použití **New-AzureRmDataFactoryGateway** rutiny pro vytvoření logické brány následujícím způsobem:

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

1. V prostředí Azure PowerShell přejděte do složky: **C:\Program Files\Microsoft Data správy Gateway\2.0\PowerShellScript\**. Spustit **RegisterGateway.ps1** přidružený k místní proměnné **$Key** jak je znázorněno v následujícím příkazu. Tento skript zaregistruje Klientský agent nainstalovaný na počítači s logické brány, kterou vytvoříte dříve.

    ```PowerShell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Pomocí parametru IsRegisterOnRemoteMachine můžete registrovat bránu ve vzdáleném počítači. Příklad:

    ```PowerShell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Můžete použít **Get-AzureRmDataFactoryGateway** rutiny získat seznam bran v datové továrně. Když **stav** ukazuje **online**, znamená to vaše brána je připravený k použití.

    ```PowerShell        
    Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
Můžete odebrat brány pomocí **odebrat AzureRmDataFactoryGateway** brány pomocí rutiny a aktualizace popis **Set-AzureRmDataFactoryGateway** rutiny. Syntaxe a další podrobnosti o těchto rutinách najdete v tématu referenční Data Factory.  

### <a name="list-gateways-using-powershell"></a>Seznam brány pomocí prostředí PowerShell

```PowerShell
Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Odebrat bránu pomocí prostředí PowerShell

```PowerShell
Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```


## <a name="next-steps"></a>Další postup
* V tématu [přesun dat mezi místní a cloudové úložiště dat](data-factory-move-data-between-onprem-and-cloud.md) článku. V tomto návodu vytvoříte kanál, který používá bránu pro přesun dat z databáze SQL serveru místně do objektu blob Azure.  
