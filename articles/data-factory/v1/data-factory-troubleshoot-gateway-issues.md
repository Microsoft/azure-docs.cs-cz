---
title: Poradce při potížích s bránou správy dat
description: Obsahuje tipy pro řešení problémů souvisejících s bránou pro správu dat.
services: data-factory
author: nabhishek
manager: anandsub
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 09d51de3ae0bd4baca585d2abdd936b1a29567d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065030"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Řešení potíží při použití Brány pro správu dat
Tento článek obsahuje informace o řešení problémů s používáním brány pro správu dat.

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, podívejte se na [prostředí runtime integrace s vlastním hostitelem v datové továrně](../create-self-hosted-integration-runtime.md).

Podrobné informace o bráně najdete v článku [Brána pro správu dat.](data-factory-data-management-gateway.md) V článku [Přesunutí dat mezi místním i cloudovým](data-factory-move-data-between-onprem-and-cloud.md) článkem najdete návod k přesunu dat z místní databáze SQL Serveru do úložiště objektů blob Microsoft Azure pomocí brány.

## <a name="failed-to-install-or-register-gateway"></a>Instalace nebo registrace brány se nezdařila.
### <a name="1-problem"></a>1. Problém
Tato chybová zpráva se zobrazí při instalaci a registraci brány, konkrétně při stahování instalačního souboru brány.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Příčina
Počítači, do kterého se pokoušíte nainstalovat bránu, se nepodařilo stáhnout nejnovější instalační soubor brány ze služby Stažení softwaru z důvodu problému se sítí.

#### <a name="resolution"></a>Řešení
Zkontrolujte nastavení serveru proxy brány firewall, zda nastavení blokuje síťové připojení z počítače do [služby stažení](https://download.microsoft.com/)a odpovídajícím způsobem aktualizujte nastavení.

Případně můžete stáhnout instalační soubor pro nejnovější bránu ze [služby Download Center](https://www.microsoft.com/download/details.aspx?id=39717) na jiných počítačích, které mají přístup ke službě download center. Potom můžete zkopírovat instalační soubor do hostitelského počítače brány a spustit jej ručně k instalaci a aktualizaci brány.

### <a name="2-problem"></a>2. Problém
Tato chyba se zobrazí, když se pokoušíte nainstalovat bránu kliknutím **na nainstalovat přímo v tomto počítači** na webu Azure Portal.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Příčina
V počítači je již nainstalována brána.

#### <a name="resolution"></a>Řešení
Odinstalujte existující bránu v počítači a znovu klepněte **na odkaz přímo na tento počítač.**

### <a name="3-problem"></a>3. Problém
Tato chyba se může zobrazit při registraci nové brány.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Příčina
Tato zpráva se může zobrazit z jednoho z následujících důvodů:

* Formát klíče brány je neplatný.
* Klíč brány byl zrušen.
* Klíč brány byl regenerován z portálu.  

#### <a name="resolution"></a>Řešení
Ověřte, zda používáte správný klíč brány z portálu. V případě potřeby znovu vygenerujte klíč a použijte klíč k registraci brány.

### <a name="4-problem"></a>4. Problém
Při registraci brány se může zobrazit následující chybová zpráva.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Obsah nebo formát klíče je neplatný.](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Příčina
Obsah nebo formát klíče vstupní brány je nesprávný. Jedním z důvodů může být, že jste zkopírovali pouze část klíče z portálu nebo používáte neplatný klíč.

#### <a name="resolution"></a>Řešení
Vygenerujte klíč brány na portálu a pomocí tlačítka kopírování zkopírujte celý klíč. Pak jej vložte do tohoto okna a zaregistrujte bránu.

### <a name="5-problem"></a>5. Problém
Při registraci brány se může zobrazit následující chybová zpráva.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Klíč brány je neplatný nebo prázdný.](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Příčina
Klíč brány byl obnoven nebo brána byla odstraněna na webu Azure Portal. Může se také stát, pokud nastavení brány pro správu dat není nejnovější.

#### <a name="resolution"></a>Řešení
Zkontrolujte, zda je nastavení brány pro správu dat nejnovější verzí, nejnovější verzi najdete v [centru pro stahování](https://go.microsoft.com/fwlink/p/?LinkId=271260)Microsoft .

Pokud je nastavení aktuální/ nejnovější a brána stále existuje na portálu, regenerovat klíč brány na webu Azure portal a pomocí tlačítka kopírovat zkopírovat celý klíč a vložte jej do tohoto okna zaregistrovat bránu. V opačném případě znovu vytvořte bránu a začněte znovu.

### <a name="6-problem"></a>6. Problém
Při registraci brány se může zobrazit následující chybová zpráva.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Klíč brány je neplatný nebo prázdný.](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Příčina
K této chybě může dojít, protože brána byla odstraněna nebo byl obnoven přidružený klíč brány.

#### <a name="resolution"></a>Řešení
Pokud byla brána odstraněna, znovu vytvořte bránu z portálu, klikněte na **Registrovat**, zkopírujte klíč z portálu, vložte ho a pokuste se bránu zaregistrovat.

Pokud brána stále existuje, ale její klíč byl znovu vygenerován, použijte nový klíč k registraci brány. Pokud nemáte klíč, znovu vygenerujte klíč z portálu.

### <a name="7-problem"></a>7. Problém
Při registraci brány může být nutné zadat cestu a heslo pro certifikát.

![Zadat certifikát](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Příčina
Brána byla registrována na jiných počítačích dříve. Při počáteční registraci brány byl k bráně přidružen šifrovací certifikát. Certifikát může být buď vlastní generované bránou nebo poskytované uživatelem.  Tento certifikát se používá k šifrování přihlašovacích údajů úložiště dat (propojené služby).  

![Export certifikátu](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Při obnovení brány v jiném hostitelském počítači požádá průvodce registrací o dešifrování pověření dříve zašifrovaných tímto certifikátem.  Bez tohoto certifikátu nelze pověření dešifrovat novou bránou a následné spuštění aktivity kopírování přidružené k této nové bráně se nezdaří.  

#### <a name="resolution"></a>Řešení
Pokud jste exportovali certifikát pověření z původního počítače brány pomocí tlačítka **Export** ovat na kartě **Nastavení** ve Správci konfigurace brány pro správu dat, použijte certifikát zde.

Tuto fázi nelze přeskočit při obnovení brány. Pokud certifikát chybí, je třeba odstranit bránu z portálu a znovu vytvořit novou bránu.  Kromě toho aktualizujte všechny propojené služby, které souvisejí s bránou, opětovným zadáním jejich pověření.

### <a name="8-problem"></a>8. Problém
Může se zobrazit následující chybová zpráva.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Příčina
K této chybě dochází, když je brána v prostředí, které vyžaduje proxy server HTTP pro přístup k internetovým prostředkům, nebo se změní ověřovací heslo proxy serveru, ale není odpovídajícím způsobem aktualizováno ve vaší bráně.

#### <a name="resolution"></a>Řešení
Postupujte podle pokynů v části Důležité informace o serveru proxy v tomto článku a nakonfigurujte nastavení serveru proxy pomocí nástroje Data Management Gateway Configuration Manager.

## <a name="gateway-is-online-with-limited-functionality"></a>Brána je online s omezenou funkčností
### <a name="1-problem"></a>1. Problém
Stav brány se zobrazí jako online s omezenou funkčností.

#### <a name="cause"></a>Příčina
Stav brány se zobrazí jako online s omezenou funkčností z jednoho z následujících důvodů:

* Gateway se nemůže připojit ke cloudové službě prostřednictvím Služby Azure Service Bus.
* Cloudová služba se nemůže připojit k bráně prostřednictvím služby Service Bus.

Pokud je brána online s omezenými funkcemi, je možné, že nebudete moci pomocí Průvodce kopírováním datové továrny vytvořit datové kanály pro kopírování dat do nebo z místních úložišť dat. Jako řešení můžete použít Editor datové továrny na portálu, Visual Studiu nebo Azure PowerShellu.

#### <a name="resolution"></a>Řešení
Řešení tohoto problému (online s omezenou funkčností) je založeno na tom, zda se brána nemůže připojit ke cloudové službě nebo jiným způsobem. Následující části obsahují tato řešení.

### <a name="2-problem"></a>2. Problém
Zobrazí se následující chyba.

`Error: Gateway cannot connect to cloud service through service bus`

![Brána se nemůže připojit ke cloudové službě.](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Příčina
Brána se nemůže připojit ke cloudové službě prostřednictvím služby Service Bus.

#### <a name="resolution"></a>Řešení
Chcete-li bránu zprovoznit, postupujte takto:

1. Povolte odchozí pravidla IP adresy na počítači brány a podnikové bráně firewall. Ip adresy můžete najít z protokolu událostí systému Windows (ID == 401): Došlo k pokusu o přístup k soketu způsobem zakázaným jeho přístupovými oprávněními XX. Xx. Xx. XX:9350.
1. Konfigurace nastavení proxy serveru v bráně. Podrobnosti naleznete v části Důležité informace o serveru proxy.
1. Povolte odchozí porty 5671 a 9350-9354 v bráně Windows Firewall v počítači brány i v podnikové bráně firewall. Podrobnosti najdete v části Porty a brána firewall. Tento krok je volitelný, ale doporučujeme jej pro zvážení výkonu.

### <a name="3-problem"></a>3. Problém
Zobrazí se následující chyba.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Příčina
Přechodná chyba v připojení k síti.

#### <a name="resolution"></a>Řešení
Chcete-li bránu zprovoznit, postupujte takto:

1. Počkejte několik minut, připojení se automaticky obnoví, když je chyba pryč.
1. Pokud chyba přetrvává, restartujte službu brány.

## <a name="failed-to-author-linked-service"></a>Autorpropojené služby se nezdařilo.
### <a name="problem"></a>Problém
Tato chyba se může zobrazit při pokusu o použití Správce přihlašovacích údajů na portálu k zadání pověření pro novou propojenou službu nebo aktualizaci pověření pro existující propojenou službu.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Když se zobrazí tato chyba, stránka nastavení Správce konfigurace brány pro správu dat může vypadat jako následující snímek obrazovky.

![Databáze není k dispozici.](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Příčina
Certifikát TLS/SSL mohl být v počítači brány ztracen. Počítač brány nemůže načíst certifikát, který se aktuálně používá pro šifrování TLS. V protokolu událostí se také může zobrazit chybová zpráva, která je podobná následující zprávě.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Řešení
Chcete-li problém vyřešit, postupujte takto:

1. Spusťte správce konfigurace brány pro správu dat.
2. Přepněte na kartu **Nastavení**.  
3. Klepnutím na tlačítko **Změnit** změňte certifikát TLS/SSL.

   ![Tlačítko Změnit certifikát](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Vyberte nový certifikát jako certifikát TLS/SSL. Můžete použít libovolný certifikát TLS/SSL, který jste vy generovali vy nebo jakákoli organizace.

   ![Zadat certifikát](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Aktivita kopírování se nezdaří.
### <a name="problem"></a>Problém
Můžete si všimnout následující "UserErrorFailedToConnectToSqlserver" selhání po nastavení kanálu na portálu.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Příčina
K tomu může dojít z různých důvodů a zmírnění se odpovídajícím způsobem liší.

#### <a name="resolution"></a>Řešení
Před připojením k databázi SQL povolte odchozí připojení TCP přes port TCP/1433 na straně klienta brány pro správu dat.

Pokud je cílová databáze databáze Azure SQL, zkontrolujte také nastavení brány firewall SERVERU SQL Server pro Azure.

V následující části otestujte připojení k místnímu úložišti dat.

## <a name="data-store-connection-or-driver-related-errors"></a>Připojení úložiště dat nebo chyby související s ovladačem
Pokud se zobrazí připojení k úložišti dat nebo chyby související s ovladačem, proveďte následující kroky:

1. Spusťte správce konfigurace brány pro správu dat v počítači brány.
2. Přepněte na kartu **Diagnostika.**
3. V **části Test Connection**přidejte hodnoty skupiny bran.
4. Kliknutím na **Test** zjistěte, jestli se můžete připojit k místnímu zdroji dat z počítače brány pomocí informací o připojení a přihlašovacích údajů. Pokud se testovací připojení nepodaří ani po instalaci ovladače, restartujte bránu, aby se získaly nejnovější změny.

![Testovat připojení na kartě Diagnostika](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Protokoly brány
### <a name="send-gateway-logs-to-microsoft"></a>Odeslání protokolů bran společnosti Microsoft
Když se obrátíte na podporu microsoftu a získáte pomoc s řešením problémů s bránou, můžete být požádáni o sdílení protokolů brány. S vydáním brány můžete sdílet požadované protokoly brány se dvěma kliknutími na tlačítka ve Správci konfigurace brány pro správu dat.    

1. Přepněte na kartu **Diagnostika** ve Správci konfigurace brány pro správu dat.

    ![Karta Diagnostika brány správy dat](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Kliknutím na **Odeslat protokoly** zobrazíte následující dialogové okno.

    ![Protokoly odesílání brány pro správu dat](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Nepovinné) Kliknutím na **protokoly zobrazení** zkontrolujte protokoly v prohlížeči událostí.
4. (Nepovinné) Kliknutím na **ochrana osobních údajů** zkontrolujte prohlášení o zásadách ochrany osobních údajů webových služeb společnosti Microsoft.
5. Pokud jste spokojeni s tím, co se chystáte nahrát, klepněte na tlačítko **Odeslat protokoly** skutečně odeslat protokoly z posledních sedmi dnů společnosti Microsoft pro řešení potíží. Měli byste vidět stav operace odesílání protokolů, jak je znázorněno na následujícím snímku obrazovky.

    ![Stav protokolů odesílání protokolů brány pro správu dat](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Po dokončení operace se zobrazí dialogové okno, jak je znázorněno na následujícím snímku obrazovky.

    ![Stav protokolů odesílání protokolů brány pro správu dat](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Uložte **ID sestavy** a sdílejte ho s podporou Microsoftu. ID sestavy se používá k vyhledání protokolů brány, které jste nahráli pro řešení potíží.  ID sestavy se také uloží do prohlížeče událostí.  Můžete ji najít při pohledu na ID události "25", a zkontrolujte datum a čas.

    ![ID sestavy odesílání protokolů protokolů brány pro správu dat](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Protokoly archivační brány na hostitelském počítači brány
Existují některé scénáře, kde máte problémy s bránou a nemůžete sdílet protokoly brány přímo:

* Ručně nainstalovat bránu a zaregistrovat bránu.
* Pokusíte se zaregistrovat bránu s regenerateovaný klíč v nástroji Configuration Manager brány pro správu dat.
* Pokusíte se odeslat protokoly a hostitelská služba brány nelze připojit.

V těchto scénářích můžete uložit protokoly brány jako soubor ZIP a sdílet je, když kontaktujete podporu společnosti Microsoft. Pokud se například při registraci brány zobrazí chyba, jak je znázorněno na následujícím snímku obrazovky.   

![Chyba registrace brány pro správu dat](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Klepněte na odkaz **Protokoly brány archivace** na archivaci a uložení protokolů a potom sdílejte soubor ZIP s podporou společnosti Microsoft.

![Protokoly archivu brány pro správu dat](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Vyhledání protokolů brány
Podrobné informace o protokolu brány naleznete v protokolech událostí systému Windows.

1. Spusťte **Prohlížeč událostí systému**Windows .
2. Vyhledejte protokoly ve složce **Application and Services Logs** > **Data Management Gateway** .

   Při řešení problémů souvisejících s bránou vyhledejte události na úrovni chyb v prohlížeči událostí.

![Brány pro správu dat se hlásí v prohlížeči událostí](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
