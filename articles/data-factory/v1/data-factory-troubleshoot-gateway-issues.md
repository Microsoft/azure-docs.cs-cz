---
title: Řešení problémů Brána pro správu dat | Microsoft Docs
description: Tipy pro řešení potíží s Brána pro správu dat poskytuje.
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 9453099af8d9be21a97808733ffa8f8bfe5a364a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37047560"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Řešení potíží při použití Brány pro správu dat
Tento článek obsahuje informace o řešení potíží s použitím brány pro správu dat.

> [!NOTE]
> Tento článek se týká verze 1 Azure Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [hostovanou na vlastním integrace runtime v datové továrně](../create-self-hosted-integration-runtime.md).

Najdete v článku [Brána pro správu dat](data-factory-data-management-gateway.md) článku podrobné informace o bráně. Najdete v článku [přesun dat mezi místními a cloudovými](data-factory-move-data-between-onprem-and-cloud.md) článku návod přesun dat z databáze SQL serveru místně do Microsoft Azure Blob storage pomocí brány.

## <a name="failed-to-install-or-register-gateway"></a>Nepodařilo se nainstalovat nebo registraci brány
### <a name="1-problem"></a>1. Problém
Zobrazí tato chybová zpráva při instalaci a registraci bránu, konkrétně při stahování souboru instalace brány.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Příčina
Počítač, na který se pokoušíte nainstalovat brány se nepodařilo stáhnout nejnovější instalační soubor brány z webu Stažení softwaru kvůli problému v síti.

#### <a name="resolution"></a>Řešení
Zkontrolujte nastavení proxy serveru brány firewall zobrazíte, zda nastavení blokování síťové připojení z počítače na [centra stahování softwaru společnosti](https://download.microsoft.com/)a aktualizovat nastavení odpovídajícím způsobem.

Alternativně můžete stáhnout instalační soubor pro nejnovější bránu z [centra stahování softwaru společnosti](https://www.microsoft.com/download/details.aspx?id=39717) na jiných počítačích, kteří mohou přistupovat k stažení softwaru. Pak můžete zkopírovat soubor Instalační služby systému k hostitelskému počítači brány a spouštět ručně, aby instalace a aktualizace brány.

### <a name="2-problem"></a>2. Problém
Se zobrazí tato chyba, když se pokoušíte nainstalovat bránu kliknutím **nainstalovat přímo na tomto počítači** na portálu Azure.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Příčina
Brána je už nainstalovaná na počítači.

#### <a name="resolution"></a>Řešení
Odinstalujte existující bránu na počítači a klikněte na tlačítko **nainstalovat přímo na tomto počítači** propojení znovu.

### <a name="3-problem"></a>3. Problém
Tato chyba může zobrazit při registraci novou bránu.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Příčina
Tato zpráva se může zobrazit pro jednu z následujících důvodů:

* Formát klíč brány je neplatný.
* Klíč brány byla zrušena platnost.
* Klíč brány byl znovu vygenerovat, z portálu.  

#### <a name="resolution"></a>Řešení
Ověřte, zda používáte správné brány klíč z portálu. V případě potřeby znovu vygenerovat klíč a pomocí klíče k registraci brány.

### <a name="4-problem"></a>4. Problém
Pokud se registrace brány, může se zobrazit tato chybová zpráva.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Obsah nebo formát klíče je neplatný](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Příčina
Obsah nebo formátu vstupní brána klíče je nesprávné. Jedním z důvodů může být, že jste zkopírovali pouze část klíč z portálu nebo používáte neplatný klíč.

#### <a name="resolution"></a>Řešení
Generovat klíč brány na portálu a použijte tlačítko Kopírovat můžete zkopírovat celý klíč. Vložte jej v tomto okně k registraci brány.

### <a name="5-problem"></a>5. Problém
Pokud se registrace brány, může se zobrazit tato chybová zpráva.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Klíč brány je neplatný nebo prázdný](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Příčina
Byl znovu vygenerovat klíč brány nebo byl odstraněn brány na portálu Azure. Může také dojít, pokud instalační program brány pro správu dat není nejnovější.

#### <a name="resolution"></a>Řešení
Zkontrolujte, pokud instalační program brány pro správu dat je na nejnovější verzi, můžete nějakého najít na nejnovější verzi na Microsoft [centra stahování softwaru společnosti](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Pokud je nastavení aktuální / nejnovější a brány stále existuje na portálu, znovu vygenerovat klíč brány na portálu Azure a použijte tlačítko Kopírovat můžete zkopírovat celý klíč a vložte jej v tomto okně k registraci brány. Jinak bránu znovu vytvořte a začít znovu.

### <a name="6-problem"></a>6. Problém
Pokud se registrace brány, může se zobrazit tato chybová zpráva.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Klíč brány je neplatný nebo prázdný](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Příčina
K této chybě může dojít, protože brána byla odstraněna, nebo byl znovu vygenerovat klíč přidružené brány.

#### <a name="resolution"></a>Řešení
Pokud brána byla odstraněna, znovu vytvořit bránu z portálu, klikněte na **zaregistrovat**, zkopírujte klíč z portálu, vložte ho a zkuste registraci brány.

Pokud brána stále existuje, ale byla znovu vygeneroval svůj klíč, použijte nový klíč k registraci brány. Pokud nemáte klíč, znovu vygenerujte klíč znovu z portálu.

### <a name="7-problem"></a>7. Problém
Pokud se registrace brány, vám může být potřeba zadejte cestu a heslo pro certifikát.

![Zadejte certifikát](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Příčina
Brána je zaregistrován na jiných počítačích než. Při počáteční registraci brány byla přidružena brány šifrovací certifikát. Certifikát můžete být samoobslužné generované bránu nebo zadané uživatelem.  Tento certifikát se používá k šifrování přihlašovacích údajů úložiště dat (propojené služby).  

![Export certifikátu](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Při obnovování brány na jiný hostitelský počítač, Průvodce registrací požádá o tohoto certifikátu dešifrovat přihlašovací údaje dříve zašifrované pomocí tohoto certifikátu.  Bez tohoto certifikátu přihlašovací údaje nelze dešifrovat pomocí nové brány a další kopie aktivity spuštěních přidružené k této nové brány se nezdaří.  

#### <a name="resolution"></a>Řešení
Pokud jste exportovali certifikát přihlašovacích údajů z původní počítač brány pomocí **exportovat** na tlačítko **nastavení** kartě v správy Správce konfigurace brány dat, použití certifikátu Tady.

Při obnovení bránu nedá Přeskočit tuto fázi. Pokud chybí certifikát, budete muset odstranit bránu z portálu a znovu vytvořit novou bránu.  Kromě toho aktualizujte všechny propojené služby, které se vztahují k bráně nutnosti opětovného zadávání svých přihlašovacích údajů.

### <a name="8-problem"></a>8. Problém
Zobrazí se následující chybová zpráva.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Příčina
Tato chyba se stane, když vaše brána je v prostředí, které vyžaduje proxy server HTTP přístup k internetovým prostředkům nebo heslo pro ověřování vašeho proxy serveru změní, ale není příslušným způsobem aktualizuje v bránu.

#### <a name="resolution"></a>Řešení
Postupujte podle pokynů v [důležité informace o Proxy serveru](#proxy-server-considerations) části tohoto článku a konfigurace nastavení proxy serveru s Data Management Gateway Configuration Manager.

## <a name="gateway-is-online-with-limited-functionality"></a>Je brána online s omezenou funkčností
### <a name="1-problem"></a>1. Problém
Stav brány, zobrazí jako online s omezenou funkčností.

#### <a name="cause"></a>Příčina
Zobrazí stav brány jako online s omezenou funkčností pro jednu z následujících důvodů:

* Brána se nemůže připojit ke cloudové službě přes Azure Service Bus.
* Cloudové služby se nemůže připojit k bráně přes službu Service Bus.

Když je brána online s omezenou funkčností, není možné pomocí Průvodce kopírování objektu pro vytváření dat k vytvoření datových kanálů pro kopírování dat do nebo z místní datová úložiště. Jako alternativní řešení můžete použít Editor služby Data Factory v portálu, Visual Studio nebo Azure PowerShell.

#### <a name="resolution"></a>Řešení
Řešení tohoto problému (online s omezenou funkčností) je založená na tom, jestli brána se nemůže připojit cloudové služby nebo jiným způsobem. Následující části obsahují tato řešení.

### <a name="2-problem"></a>2. Problém
Zobrazí následující chyba.

`Error: Gateway cannot connect to cloud service through service bus`

![Brána se nemůže připojit ke cloudové službě](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Příčina
Brána se nemůže připojit ke cloudové službě přes službu Service Bus.

#### <a name="resolution"></a>Řešení
Postupujte podle těchto kroků bránu zpátky do online režimu:

1. Povolit IP adresy odchozí pravidla na počítači s bránou a podnikové brány firewall. Můžete najít IP adresy z protokolu událostí systému Windows (ID == 401): došlo pokusu o přístup k soketu způsobem, jeho přístupovými oprávněními XX. XX. XX. XX:9350.
* Konfigurace nastavení proxy serveru na bráně. Najdete v článku [důležité informace o Proxy serveru](#proxy-server-considerations) podrobnosti.
* Povolte Odchozí porty 5671 a 9350-9354 na obou bránu Windows Firewall na počítači s bránou a podnikové brány firewall. Najdete v článku [porty a brány firewall](#ports-and-firewall) podrobnosti. Tento krok je volitelný, ale doporučujeme ho důvodů výkonu.

### <a name="3-problem"></a>3. Problém
Zobrazí následující chyba.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Příčina
Přechodná chyba v připojení k síti.

#### <a name="resolution"></a>Řešení
Postupujte podle těchto kroků bránu zpátky do online režimu:

1. Počkejte několik minut, připojení se automaticky obnoví při chyba byla odstraněna.
* Pokud chyba přetrvává, restartujte službu brány.

## <a name="failed-to-author-linked-service"></a>Nepodařilo se vytvořit propojenou službu
### <a name="problem"></a>Problém
Tato chyba se můžete setkat, když se pokusíte použít Správce přihlašovacích údajů na portálu a zadejte přihlašovací údaje pro nová propojená služba nebo aktualizujte pověření pro existující propojenou službu.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Když se tato chyba, stránka nastavení nástroje Data Management Gateway Configuration Manager může vypadat jako na následujícím snímku obrazovky.

![Databázi nelze získat přístup.](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Příčina
Certifikát SSL může byla v počítači s bránou ztraceny. Počítač brány nelze načíst aktuálně certifikát, který se používá pro šifrování SSL. Můžete si také prohlédnout chybovou zprávu do protokolu událostí, která se podobá následující zprávě.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Řešení
Postupujte podle těchto kroků problém vyřešit:

1. Spusťte Správce konfigurace brány pro správu dat.
2. Přepněte na kartu **Nastavení**.  
3. Klikněte **změnit** tlačítko změňte certifikát SSL.

   ![Tlačítko změnit certifikát](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Vyberte nový certifikát jako certifikát SSL. Můžete použít libovolný certifikát SSL, který je generován můžete nebo některé z organizací.

   ![Zadejte certifikát](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Aktivita kopírování se nezdaří
### <a name="problem"></a>Problém
Možná jste si všimli následující selhání "UserErrorFailedToConnectToSqlserver" po nastavení kanál v portálu.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Příčina
K tomu může dojít z různých důvodů a zmírnění se liší podle toho.

#### <a name="resolution"></a>Řešení
Povolte odchozí připojení TCP přes port TCP 1433 nebo na straně klienta Brána pro správu dat před připojením k databázi SQL.

Pokud je cílová databáze je databáze Azure SQL, zkontrolujte nastavení systému SQL Server brány firewall pro Azure také.

Najdete v následující části, abyste otestovali připojení k úložišti dat na místě.

## <a name="data-store-connection-or-driver-related-errors"></a>Připojení nebo chyby související s ovladačů úložiště dat
Pokud se zobrazí data uložit připojení nebo chyby související s ovladačem, proveďte následující kroky:

1. Spusťte Správce konfigurace brány pro správu dat na počítači s bránou.
2. Přepnout **diagnostiky** kartě.
3. V **Test připojení**, přidejte hodnoty skupiny brány.
4. Klikněte na tlačítko **Test** chcete zobrazit, pokud je můžete připojit k místnímu zdroji dat z počítače brány pomocí informací o připojení a přihlašovací údaje. Pokud se testovací připojení nepodaří ani po instalaci ovladače, restartujte bránu, aby se získaly nejnovější změny.

![Test připojení na kartě diagnostiky](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Protokoly Gateway
### <a name="send-gateway-logs-to-microsoft"></a>Odeslání protokolů s brány Microsoft
Při kontaktování Microsoft Support získat nápovědu k řešení potíží s brány možná sdílet svoje protokoly brány. S vydáním brány můžete sdílet protokoly požadované gateway s dvěma kliknutí na tlačítko v Data Management Gateway Configuration Manager.    

1. Přepnout **diagnostiky** kartě v Data Management Gateway Configuration Manager.

    ![Karta diagnostiku brány správy dat](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Klikněte na tlačítko **odeslat protokoly** zobrazíte následující dialogové okno.

    ![Data Management Gateway odeslat protokoly](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Volitelné) Klikněte na tlačítko **zobrazit protokoly** ke kontrole protokoluje události prohlížeč.
4. (Volitelné) Klikněte na tlačítko **o ochraně osobních údajů** ke kontrole prohlášení o ochraně osobních údajů služeb Microsoft web services.
5. Až budete spokojeni se chystáte se nahrát, klikněte na tlačítko **odeslat protokoly** ve skutečnosti odeslat protokoly z posledních sedmi dnů společnosti Microsoft pro řešení potíží. Stav operace odesílání protokolů byste měli vidět, jak je znázorněno na následujícím snímku obrazovky.

    ![Data Management Gateway odeslat protokoly stavu](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Po dokončení operace se zobrazí dialogové okno, jak je znázorněno na následujícím snímku obrazovky.

    ![Data Management Gateway odeslat protokoly stavu](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Uložit **ID sestavy** a sdílet je s Microsoft Support. ID sestavy slouží k vyhledání brány protokoly, které jste nahráli k řešení potíží.  ID sestavy se také uloženy události prohlížeč.  Můžete najít prohlížením událost s ID "25" a zkontrolujte datum a čas.

    ![Data Management Gateway odeslat protokoly ID sestavy](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Archiv brány protokoly na hostitelském počítači brány
Je několik scénářů, kdy máte problémy s brány a protokoly gateway nemohou sdílet přímo:

* Ručně nainstalujte brány a zaregistrovat bránu.
* Pokusíte registrovat bránu pomocí obnoveného klíče v Data Management Gateway Configuration Manager.
* Zkuste odeslat protokoly a nemůže být připojen hostitelskou službu brány.

Pro tyto scénáře můžete uložit protokoly gateway jako soubor zip a sdílet ho při kontaktujte podporu společnosti Microsoft. Například pokud obdržíte chybu při registraci brány jako ukazuje následující snímek obrazovky.   

![Chyba registrace brány správy dat](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Klikněte na tlačítko **archivu protokoly gateway** propojit archivaci a uložte protokoly a potom sdílet soubor zip s podporu společnosti Microsoft.

![Protokoly archivu brány správy dat](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Najít protokoly gateway
Podrobné brány protokolu informace naleznete v protokolech událostí systému Windows.

1. Spustit systém Windows **Prohlížeč událostí**.
2. Vyhledejte v protokolech **protokoly aplikací a služeb** > **Brána pro správu dat** složky.

 Pokud se řešení potíží s problémy související s brány, vyhledejte chyby úrovně události události prohlížeč.

![Brána pro správu dat protokoly v prohlížeči událostí](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
