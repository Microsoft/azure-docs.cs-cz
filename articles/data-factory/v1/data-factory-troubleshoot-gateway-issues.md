---
title: Řešení potíží s Správa dat bránou
description: Poskytuje tipy pro řešení potíží souvisejících s Správa dat bránou.
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
ms.openlocfilehash: 5d83f05c16004edc3ad4842b7e4e9d4b9babe577
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85319063"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Řešení potíží při použití Brány pro správu dat
Tento článek poskytuje informace o řešení potíží s používáním Správa dat brány.

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Integration runtime v místním prostředí v Data Factory](../create-self-hosted-integration-runtime.md).

Podrobné informace o bráně najdete v článku o [bráně Správa dat](data-factory-data-management-gateway.md) . Návod, jak přesouvat data z databáze SQL Server do Microsoft Azure úložiště objektů BLOB pomocí brány, najdete v článku [přesunutí dat mezi místním prostředím a cloudem](data-factory-move-data-between-onprem-and-cloud.md) .

## <a name="failed-to-install-or-register-gateway"></a>Nepovedlo se nainstalovat nebo zaregistrovat bránu.
### <a name="1-problem"></a>1. problém
Tato chybová zpráva se zobrazí při instalaci a registraci brány, konkrétně při stahování instalačního souboru brány.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Příčina
Počítači, na který se pokoušíte nainstalovat bránu, se nepovedlo stáhnout nejnovější instalační soubor brány z webu Download Center kvůli problému v síti.

#### <a name="resolution"></a>Řešení
Zkontrolujte nastavení proxy server brány firewall, abyste viděli, jestli nastavení zablokovalo síťové připojení z počítače ke [službě Stažení softwaru](https://download.microsoft.com/), a aktualizujte nastavení odpovídajícím způsobem.

Případně můžete stáhnout instalační soubor pro nejnovější bránu z [webu Download Center](https://www.microsoft.com/download/details.aspx?id=39717) na dalších počítačích, které mají přístup ke službě Stažení softwaru. Potom můžete zkopírovat instalační soubor do hostitelského počítače brány a spustit ho ručně a nainstalovat a aktualizovat bránu.

### <a name="2-problem"></a>2. problém
Tato chyba se zobrazí při pokusu o instalaci brány kliknutím na **instalovat přímo na tomto počítači** v Azure Portal.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Příčina
Brána je už na tomto počítači nainstalovaná.

#### <a name="resolution"></a>Řešení
Odinstalujte existující bránu na počítači a znovu klikněte na odkaz **instalovat přímo na tento počítač** .

### <a name="3-problem"></a>3. problém
Tato chyba se může zobrazit při registraci nové brány.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Příčina
Tato zpráva se může zobrazit z jednoho z následujících důvodů:

* Formát klíče brány je neplatný.
* Platnost klíče brány se zrušila.
* Klíč brány se znovu vygeneroval z portálu.  

#### <a name="resolution"></a>Řešení
Ověřte, jestli používáte správný klíč brány z portálu. V případě potřeby znovu vygenerujte klíč a pomocí klíče Zaregistrujte bránu.

### <a name="4-problem"></a>4. problém
Při registraci brány se může zobrazit následující chybová zpráva.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Obsah nebo formát klíče je neplatný.](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Příčina
Obsah nebo Formát vstupního klíče brány nejsou správné. Jedním z důvodů může být, že jste zkopírovali jenom část klíče z portálu nebo nepoužíváte Neplatný klíč.

#### <a name="resolution"></a>Řešení
Na portálu vygenerujte klíč brány a pomocí tlačítka Kopírovat zkopírujte celý klíč. Pak ho vložte do tohoto okna a zaregistrujte bránu.

### <a name="5-problem"></a>5. problém
Při registraci brány se může zobrazit následující chybová zpráva.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Klíč brány je neplatný nebo prázdný.](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Příčina
Klíč brány se znovu vygeneroval nebo se brána v Azure Portal odstranila. K tomu může dojít také v případě, že instalace brány Správa dat není nejnovější.

#### <a name="resolution"></a>Řešení
Ověřte, jestli je instalační program brány Správa dat nejnovější verzi. nejnovější verzi najdete na webu Microsoft [Download Center](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Pokud je instalační program aktuální nebo nejnovější a na portálu stále existuje brána, znovu vygenerujte klíč brány v Azure Portal a pomocí tlačítka Kopírovat zkopírujte celý klíč a pak ho vložte do tohoto okna, abyste mohli bránu zaregistrovat. V opačném případě znovu vytvořte bránu a začněte znovu.

### <a name="6-problem"></a>6. problém
Při registraci brány se může zobrazit následující chybová zpráva.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Klíč brány je neplatný nebo prázdný.](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Příčina
K této chybě může dojít, protože brána byla odstraněna nebo byl znovu vygenerován příslušný klíč brány.

#### <a name="resolution"></a>Řešení
Pokud byla brána odstraněna, znovu ji vytvořte z portálu, klikněte na **zaregistrovat**, zkopírujte klíč z portálu, vložte ho a zkuste zaregistrovat bránu.

Pokud brána stále existuje, ale její klíč se znovu vygeneroval, zaregistrujte bránu pomocí nového klíče. Pokud klíč nemáte, znovu vygenerujte klíč z portálu.

### <a name="7-problem"></a>7. problém
Když zaregistrujete bránu, možná budete muset zadat cestu a heslo pro certifikát.

![Zadat certifikát](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Příčina
Brána byla zaregistrována na jiných počítačích před. Během prvotní registrace brány byl k bráně přidružen šifrovací certifikát. Certifikát může být buď vygenerovaný automaticky bránou, nebo poskytnutý uživatelem.  Tento certifikát slouží k šifrování přihlašovacích údajů úložiště dat (propojených služeb).  

![Export certifikátu](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Při obnovení brány na jiném hostitelském počítači Průvodce registrací vyžádá tento certifikát k dešifrování přihlašovacích údajů dříve šifrovaných pomocí tohoto certifikátu.  Bez tohoto certifikátu se přihlašovací údaje nedají dešifrovat novou bránou a následné provádění aktivit kopírování přidružené k této nové bráně selže.  

#### <a name="resolution"></a>Řešení
Pokud jste vyexportovali certifikát přihlašovacích údajů z původního počítače brány pomocí tlačítka **exportovat** na kartě **nastavení** v Správa dat Configuration Manager brány, použijte zde certifikát.

Tuto fázi nelze přeskočit při obnovování brány. Pokud certifikát chybí, musíte bránu odstranit z portálu a znovu vytvořit novou bránu.  Kromě toho aktualizujte všechny propojené služby, které souvisí s bránou, tak, že znovu zadáte své přihlašovací údaje.

### <a name="8-problem"></a>8. problém
Může se zobrazit následující chybová zpráva.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Příčina
K této chybě dochází, pokud je brána v prostředí, které vyžaduje proxy server HTTP pro přístup k prostředkům Internetu, nebo se změní heslo pro ověřování proxy serveru, ale ve vaší bráně není odpovídajícím způsobem aktualizován.

#### <a name="resolution"></a>Řešení
Postupujte podle pokynů v části věnované hledisku proxy serveru v tomto článku a nakonfigurujte nastavení proxy pomocí Správa dat brány Configuration Manager.

## <a name="gateway-is-online-with-limited-functionality"></a>Brána je online s omezenými funkcemi
### <a name="1-problem"></a>1. problém
Zobrazuje se stav brány jako online s omezenými funkcemi.

#### <a name="cause"></a>Příčina
Stav brány se zobrazí jako online s omezenými funkcemi z jednoho z následujících důvodů:

* Brána se nemůže ke cloudové službě připojit prostřednictvím Azure Service Bus.
* Cloudová služba se nemůže připojit k bráně prostřednictvím Service Bus.

Pokud je brána online s omezenými funkcemi, možná nebudete moci použít Průvodce kopírováním Data Factory k vytváření datových kanálů pro kopírování dat do nebo z místních úložišť dat. Jako alternativní řešení můžete použít Data Factory Editor na portálu, v aplikaci Visual Studio nebo v Azure PowerShell.

#### <a name="resolution"></a>Řešení
Řešení tohoto problému (online s omezenými funkcemi) je založené na tom, jestli se brána nemůže připojit ke cloudové službě, nebo jiným způsobem. Následující části obsahují tato řešení.

### <a name="2-problem"></a>2. problém
Zobrazí se následující chyba.

`Error: Gateway cannot connect to cloud service through service bus`

![Brána se nemůže připojit ke cloudové službě.](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Příčina
Brána se nemůže ke cloudové službě připojit prostřednictvím Service Bus.

#### <a name="resolution"></a>Řešení
Použijte následující postup, chcete-li bránu opět přejít do režimu online:

1. Povolí odchozí pravidla IP adres na počítači brány a v podnikové bráně firewall. IP adresy můžete najít z protokolu událostí systému Windows (ID = = 401): byl proveden pokus o přístup k soketu způsobem zakázanému jeho přístupovými oprávněními XX. CZK. CZK. XX: 9350.
1. Nakonfigurujte nastavení proxy serveru v bráně. Podrobnosti najdete v části věnované hledisku proxy serveru.
1. Povolte Odchozí porty 5671 a 9350-9354 na bráně Windows Firewall na počítači brány a v podnikové bráně firewall. Podrobnosti najdete v části porty a brána firewall. Tento krok je nepovinný, ale doporučujeme zvážit jeho výkon.

### <a name="3-problem"></a>3. problém
Zobrazí se následující chyba.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Příčina
Přechodná Chyba připojení k síti

#### <a name="resolution"></a>Řešení
Použijte následující postup, chcete-li bránu opět přejít do režimu online:

1. Počkejte pár minut, připojení se automaticky obnoví, až bude chyba pryč.
1. Pokud chyba přetrvává, restartujte službu brány.

## <a name="failed-to-author-linked-service"></a>Nepovedlo se vytvořit propojenou službu.
### <a name="problem"></a>Problém
Tato chyba se může zobrazit při pokusu o použití Správce přihlašovacích údajů na portálu k zadání přihlašovacích údajů pro novou propojenou službu nebo k aktualizaci přihlašovacích údajů pro existující propojenou službu.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Když se zobrazí tato chyba, stránka nastavení Configuration Manager Správa dat brány by mohla vypadat jako na následujícím snímku obrazovky.

![Databáze není dostupná.](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Příčina
Může dojít ke ztrátě certifikátu TLS/SSL na počítači brány. Počítač brány nemůže načíst certifikát, který je aktuálně používán pro šifrování TLS. Může se zobrazit také chybová zpráva v protokolu událostí, která je podobná následující zprávě.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Řešení
Problém můžete vyřešit pomocí těchto kroků:

1. Spusťte Správa dat Configuration Manager brány.
2. Přepněte na kartu **Nastavení**.  
3. Chcete-li změnit certifikát TLS/SSL, klikněte na tlačítko **změnit** .

   ![Tlačítko změnit certifikát](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Vyberte nový certifikát jako certifikát TLS/SSL. Můžete použít jakýkoli certifikát TLS/SSL, který vygenerujete vy nebo libovolnou organizaci.

   ![Zadat certifikát](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Aktivita kopírování se nezdařila
### <a name="problem"></a>Problém
Po nastavení kanálu na portálu si můžete všimnout následujícího selhání UserErrorFailedToConnectToSqlserver.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Příčina
K tomu může dojít z různých důvodů a zmírnění omezení se podle potřeby mění.

#### <a name="resolution"></a>Řešení
Před připojením k databázi SQL povolte odchozí připojení TCP přes port TCP/1433 na straně klienta Správa dat brány.

Pokud je cílová databáze v Azure SQL Database, ověřte také SQL Server nastavení brány firewall pro Azure.

Pokud chcete otestovat připojení k místnímu úložišti dat, přečtěte si následující část.

## <a name="data-store-connection-or-driver-related-errors"></a>Připojení úložiště dat nebo chyby související s ovladačem
Pokud se zobrazí chyby související s připojením nebo ovladačem úložiště dat, proveďte následující kroky:

1. Spusťte Správa dat Configuration Manager brány na počítači brány.
2. Přepněte na kartu **Diagnostika** .
3. V části **Test připojení**přidejte hodnoty skupiny brány.
4. Kliknutím na **test** zjistíte, jestli se můžete připojit k místnímu zdroji dat z počítače brány pomocí informací o připojení a přihlašovacích údajů. Pokud se testovací připojení nepodaří ani po instalaci ovladače, restartujte bránu, aby se získaly nejnovější změny.

![Test připojení na kartě Diagnostika](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Protokoly brány
### <a name="send-gateway-logs-to-microsoft"></a>Posílání protokolů brány Microsoftu
Když kontaktujete podpora Microsoftu a získáte pomoc s řešením problémů s bránou, může se zobrazit výzva, abyste nasdíleli protokoly brány. S vydáním brány můžete sdílet požadované protokoly brány pomocí dvou kliknutí na tlačítko v Správa dat brány Configuration Manager.    

1. V Configuration Manager Správa dat brány přepněte na kartu **Diagnostika** .

    ![Karta diagnostiky Správa dat brány](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Kliknutím na **Odeslat protokoly** zobrazíte následující dialogové okno.

    ![Protokoly odeslání protokolů Správa dat brány](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. Volitelné Kliknutím na **Zobrazit protokoly** Zkontrolujte protokoly v prohlížeči událostí.
4. Volitelné Kliknutím na **Ochrana osobních údajů** zkontrolujte prohlášení o zásadách ochrany osobních údajů v Microsoft Web Services.
5. Až budete spokojeni s tím, co se chystáte nahrát, klikněte na **Odeslat protokoly** a ve skutečnosti odešlete protokoly za posledních 7 dní do Microsoftu pro řešení potíží. Měl by se zobrazit stav operace Odeslat protokoly, jak je znázorněno na následujícím snímku obrazovky.

    ![Stav odesílání protokolů služby Správa dat Gateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Po dokončení operace se zobrazí dialogové okno, jak je znázorněno na následujícím snímku obrazovky.

    ![Stav odesílání protokolů služby Správa dat Gateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Uložte **ID sestavy** a nasdílejte ji pomocí podpora Microsoftu. ID sestavy slouží k vyhledání protokolů brány, které jste odeslali pro řešení potíží.  ID sestavy je také Uloženo v prohlížeči událostí.  Najdete ho tak, že začnete s ID události "25" a zkontrolujete datum a čas.

    ![ID sestavy pro odeslání protokolů služby Správa dat Gateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Archivovat protokoly brány na hostitelském počítači brány
Existují některé scénáře, kdy máte problémy s bránou a nemůžete přímo sdílet protokoly brány:

* Bránu nainstalujete ručně a zaregistrujete bránu.
* Zkusíte zaregistrovat bránu pomocí znovu vygenerovaného klíče v Správa dat brány Configuration Manager.
* Pokusíte se odeslat protokoly a službu hostitel brány nejde připojit.

V těchto scénářích můžete protokoly brány ukládat jako soubor zip a sdílet je, když se obrátíte na podporu Microsoftu. Například pokud se při registraci brány zobrazí chyba, jak je znázorněno na následujícím snímku obrazovky.   

![Chyba registrace Správa dat brány](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Kliknutím na odkaz **Archivovat protokoly brány** můžete archivovat a ukládat protokoly a potom tento soubor zip sdílet s podporou Microsoftu.

![Protokoly archivu Správa dat brány](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Vyhledání protokolů brány
Podrobné informace protokolu brány najdete v protokolech událostí systému Windows.

1. Spusťte Windows **Prohlížeč událostí**.
2. Vyhledejte protokoly ve složce **Application and Services log**  >  **Správa dat Gateway** .

   Když řešíte problémy související s bránou, hledejte události na úrovni chyb v prohlížeči událostí.

![Protokoly Správa dat brány v prohlížeči událostí](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
