---
title: Řešení potíží s brána správy dat | Dokumentace Microsoftu
description: Poskytuje tipy, jak řešit problémy spojené s brána správy dat.
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: b39fb3ed6dbcf8fde2dadf0de59d154285eb8587
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022534"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Řešení potíží při použití Brány pro správu dat
Tento článek obsahuje informace o odstraňování potíží s pomocí brány správy dat.

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [v místním prostředí integration runtime ve službě Data Factory](../create-self-hosted-integration-runtime.md).

Zobrazit [brána správy dat](data-factory-data-management-gateway.md) podrobné informace o bráně. Zobrazit [přesun dat mezi místním prostředím a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku návod pro přesun dat z databáze v místním SQL serveru do Microsoft Azure Blob storage s použitím brány.

## <a name="failed-to-install-or-register-gateway"></a>Nepovedlo se nainstalovat nebo registrace brány
### <a name="1-problem"></a>1. Problém
Se zobrazí tato chybová zpráva při instalaci a registraci brány, konkrétně při stahování instalační soubor brány.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Příčina
Na počítači, na které se pokoušíte nainstalovat bránu se nepodařilo stažení nejnovějšího instalačního souboru brány ze služby Stažení softwaru kvůli problému v síti.

#### <a name="resolution"></a>Řešení
Zkontrolujte nastavení proxy serveru brány firewall chcete zobrazit, zda nastavení blokování síťové připojení mezi počítačem [centra stahování](https://download.microsoft.com/)a aktualizujte nastavení odpovídajícím způsobem.

Alternativně můžete stáhnout instalační soubor pro nejnovější brány z [centra stahování](https://www.microsoft.com/download/details.aspx?id=39717) na jiných počítačích, které můžou přistupovat k webu download center. Pak můžete zkopírovat soubor Instalační služby systému k hostitelskému počítači brány a spustit ji ručně nainstalovat a aktualizovat bránu.

### <a name="2-problem"></a>2. Problém
Pokud se pokoušíte bránu nainstalovat po kliknutí se zobrazí tato chyba **přímo na tento počítač nainstalovat** na webu Azure Portal.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Příčina
Brána je již nainstalován na počítači.

#### <a name="resolution"></a>Řešení
Odinstalovat stávající brány na počítači a klikněte na tlačítko **přímo na tento počítač nainstalovat** znovu propojit.

### <a name="3-problem"></a>3. Problém
Tato chyba může zobrazit při registraci nové brány.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Příčina
Může se zobrazit tato zpráva pro jednu z následujících důvodů:

* Formát klíče brány je neplatný.
* Platnost klíče brány byla zrušena.
* Brána klíč byl obnoven z portálu.  

#### <a name="resolution"></a>Řešení
Ověřte, zda používáte správný brány klíč z portálu. V případě potřeby znovu vygenerujte klíč a pomocí klíče ji zaregistrovat ve.

### <a name="4-problem"></a>4. Problém
Zobrazí se pravděpodobně následující chybová zpráva při registraci brány.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Obsah nebo formát klíče je neplatný](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Příčina
Obsah nebo formát vstupní brána klíče je nesprávné. Jedním z důvodů může být, že jste si zkopírovali pouze část klíče z portálu nebo používáte neplatný klíč.

#### <a name="resolution"></a>Řešení
Vygenerujte klíč brány na portálu a pomocí tlačítka pro kopírování zkopírujte celý klíč. Následně jej vložte do tohoto okna k registraci brány.

### <a name="5-problem"></a>5. Problém
Zobrazí se pravděpodobně následující chybová zpráva při registraci brány.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Brána klíč je neplatný nebo prázdný](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Příčina
Byly znovu vygenerovány klíč brány nebo byl odstraněn brány na webu Azure Portal. Je také může dojít, pokud instalační program brány správy dat není nejnovější.

#### <a name="resolution"></a>Řešení
Zaškrtněte, pokud instalační program brány správy dat je na nejnovější verzi, můžete nejnovější verzi naleznete na webu společnosti Microsoft [centra stahování](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Pokud je aktuální / nejnovější nastavení a brány stále existuje na portálu, znovu vygenerovat klíč brány na webu Azure Portal a pomocí tlačítka pro kopírování zkopírujte celý klíč a vložte ho v tomto okně registrace brány. V opačném případě znovu vytvořte bránu a začít od začátku.

### <a name="6-problem"></a>6. Problém
Zobrazí se pravděpodobně následující chybová zpráva při registraci brány.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![Brána klíč je neplatný nebo prázdný](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Příčina
K této chybě může dojít, protože brána byla odstraněna nebo přidružené bráně klíč byl obnoven.

#### <a name="resolution"></a>Řešení
Pokud brána byla odstraněna, znovu vytvořte bránu z portálu, klikněte na tlačítko **zaregistrovat**, zkopírujte klíč z portálu, vložte ho a zkuste registraci brány.

Pokud brána stále existuje, ale jeho klíč byl obnoven, použijte nový klíč k registraci brány. Pokud nemáte klíč, znovu vygenerujte klíč znovu z portálu.

### <a name="7-problem"></a>7. Problém
Při registraci brány, možná muset zadat cestu a heslo pro certifikát.

![Zadejte certifikát](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Příčina
Brána je zaregistrován na jiných počítačích než. Při počáteční registraci brány byla přidružena brány šifrovací certifikát. Certifikát můžete se svým generovaných brány nebo uživatel zadal.  Tento certifikát se používá k šifrování přihlašovacích údajů úložiště dat (propojené služby).  

![Export certifikátu](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Při obnovení brány na jiný hostitelský počítač, Průvodce registrací vyzve k zadání tohoto certifikátu dešifrovat přihlašovací údaje dříve zašifrována s tímto certifikátem.  Bez tohoto certifikátu není možné dešifrovat přihlašovací údaje, pomocí nové brány a následné kopie aktivity spuštění přidružené k této nové brány se nezdaří.  

#### <a name="resolution"></a>Řešení
Pokud jste exportovali certifikát přihlašovacích údajů z původní počítač brány s použitím **exportovat** tlačítko **nastavení** kartu v Data Management Gateway Configuration Manager, použijte certifikát Tady.

Při obnovování brány nelze přeskočit tuto fázi. Pokud chybí certifikát, musíte bránu odstranit z portálu a znovu vytvořit novou bránu.  Kromě toho aktualizujte všechny propojené služby, které se vztahují k bráně nutnosti opětovného zadávání přihlašovacích údajů.

### <a name="8-problem"></a>8. Problém
Zobrazí se pravděpodobně následující chybová zpráva.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Příčina
K této chybě dochází, když vaše brána je v prostředí, které vyžaduje proxy server HTTP pro přístup k internetovým prostředkům nebo váš proxy server ověřování heslo se změnilo, ale není příslušným způsobem aktualizuje v bráně.

#### <a name="resolution"></a>Řešení
Postupujte podle pokynů [důležité informace o Proxy serveru](#proxy-server-considerations) části tohoto článku a konfigurace nastavení proxy serveru pomocí Data Management Gateway Configuration Manager.

## <a name="gateway-is-online-with-limited-functionality"></a>Brána je online s omezenou funkčností
### <a name="1-problem"></a>1. Problém
Zobrazí stav brány jako online s omezenou funkčností.

#### <a name="cause"></a>Příčina
Zobrazí stav brány jako online s omezenou funkčností pro jednu z následujících důvodů:

* Brána se nemůže připojit ke cloudové službě Azure Service Bus.
* Cloudová služba nemůže připojit k bráně prostřednictvím služby Service Bus.

Když brána je online s omezenou funkčností, nemusí být možné použít Průvodce kopírováním služby Data Factory k vytvoření kanálů dat pro kopírování dat do nebo z místních úložišť dat. Jako alternativní řešení můžete použít Editor služby Data Factory na portálu, Visual Studio nebo Azure Powershellu.

#### <a name="resolution"></a>Řešení
Řešení tohoto problému (online s omezenou funkčností) je založena na, jestli se brána nemůže připojit k cloudové službě nebo jiným způsobem. Následující části obsahují tato řešení.

### <a name="2-problem"></a>2. Problém
Zobrazí následující chyba.

`Error: Gateway cannot connect to cloud service through service bus`

![Brána se nemůže připojit k cloudové službě](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Příčina
Brána se nemůže připojit ke cloudové službě prostřednictvím služby Service Bus.

#### <a name="resolution"></a>Řešení
Následujícím postupem získejte bránu zpět do režimu online:

1. Odchozí pravidla na počítači brány a podnikové brány firewall povolit IP adresy. IP adresy můžete najít v protokolu událostí Windows (ID == 401): Byl proveden pokus o přístup k soketu tak její přístupová oprávnění XX připojení zakázáno. XX. XX. XX:9350.
* Konfigurace nastavení proxy serveru na bráně. Zobrazit [důležité informace o Proxy serveru](#proxy-server-considerations) podrobné informace.
* Povolte Odchozí porty firewallu Windows na počítači brány a podnikové brány firewall 9350-9354 a 5671. Zobrazit [porty a brány firewall](#ports-and-firewall) podrobné informace. Tento krok je volitelný, ale doporučujeme ho pro posouzení výkonu.

### <a name="3-problem"></a>3. Problém
Zobrazí následující chyba.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Příčina
Přechodné chybě připojení k síti.

#### <a name="resolution"></a>Řešení
Následujícím postupem získejte bránu zpět do režimu online:

1. Počkejte několik minut, připojení se automaticky obnoví při chyba je pryč.
* Pokud chyba přetrvává, restartujte službu brány.

## <a name="failed-to-author-linked-service"></a>Nepovedlo se vytvořit propojené služby
### <a name="problem"></a>Problém
Při pokusu o použijte Správce přihlašovacích údajů portálu a zadejte přihlašovací údaje pro nové propojené služby, nebo aktualizujte přihlašovací údaje pro existující propojenou službu, může se zobrazit tato chyba.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Když se zobrazí tato chyba, na stránce nastavení Data Management Gateway Configuration Manageru může vypadat jako na následujícím snímku obrazovky.

![Databáze je nedostupné.](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Příčina
Certifikát SSL je může ztratit na počítači brány. Počítači brány nelze načíst aktuálně certifikát, který se používá pro šifrování SSL. Také může zobrazit chybovou zprávu do protokolu událostí, která se podobá následující zprávě.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Řešení
Postupujte podle těchto kroků problém vyřešit:

1. Spusťte Správce konfigurace služby Data Management Gateway.
2. Přepněte na kartu **Nastavení**.  
3. Klikněte na tlačítko **změnit** tlačítko a změňte certifikát SSL.

   ![Tlačítko změnit certifikát](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Vyberte nový certifikát jako certifikát SSL. Můžete použít libovolný certifikát SSL, který je generován jste nebo jakékoli organizaci.

   ![Zadejte certifikát](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Aktivita kopírování selže
### <a name="problem"></a>Problém
Můžete si všimnout následujícího selhání "UserErrorFailedToConnectToSqlserver" po nastavení kanálu na portálu.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Příčina
K tomu může dojít z různých důvodů a omezení rizik se liší odpovídajícím způsobem.

#### <a name="resolution"></a>Řešení
Povolte odchozí připojení TCP přes port TCP/1433 na Brána pro správu dat na straně klienta před připojováním k databázi SQL.

Pokud cílová databáze Azure SQL database, zkontrolujte nastavení systému SQL Server brány firewall pro Azure i.

Následující část k otestování připojení k úložišti dat místní.

## <a name="data-store-connection-or-driver-related-errors"></a>Připojení úložiště dat nebo chyby související s ovladačem
Pokud se zobrazí data uložit připojení nebo chyby související s ovladačem, proveďte následující kroky:

1. Spusťte Správce konfigurace brány pro správu dat na počítači brány.
2. Přepněte **diagnostiky** kartu.
3. V **Test připojení**, přidejte skupiny hodnoty brány.
4. Klikněte na tlačítko **Test** zobrazíte, pokud se můžete připojit k místnímu zdroji dat z počítače brány s použitím informací o připojení a přihlašovací údaje. Pokud se testovací připojení nepodaří ani po instalaci ovladače, restartujte bránu, aby se získaly nejnovější změny.

![Test připojení v karta Diagnostika](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Protokoly brány
### <a name="send-gateway-logs-to-microsoft"></a>Poslat Microsoftu protokoly brány
Až budete kontaktovat Microsoft Support pomoc při řešení potíží bránu, může se zobrazit výzva k sdílet protokoly brány. Verze brány můžete sdílet protokoly požadované brány s dvě kliknutí na tlačítko v Data Management Gateway Configuration Manager.    

1. Přepněte **diagnostiky** kartu v Data Management Gateway Configuration Manager.

    ![Karta Diagnostika brány správy dat](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Klikněte na tlačítko **odeslat protokoly** zobrazíte následující dialogové okno.

    ![Data Management Gateway odesílání protokolů](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (Volitelné) Klikněte na tlačítko **zobrazit protokoly** Zkontrolujte protokoly v události prohlížeč.
4. (Volitelné) Klikněte na tlačítko **ochrany osobních údajů** ke kontrole prohlášení společnosti Microsoft webové služby.
5. Až budete spokojení s co se chystáte nahrát, klikněte na tlačítko **odeslat protokoly** skutečně odeslat protokoly z posledních sedmi dnů společnosti Microsoft pro řešení potíží. Stav operace Odeslat protokoly byste měli vidět, jak je znázorněno na následujícím snímku obrazovky.

    ![Stav data Management Gateway odesílání protokolů.](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Po dokončení operace se zobrazí dialogové okno, jak je znázorněno na následujícím snímku obrazovky.

    ![Stav data Management Gateway odesílání protokolů.](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Uložit **ID sestavy** a sdílet ho s Microsoft Support. ID sestavy slouží k vyhledání protokoly brány, které jste nahráli k řešení potíží.  ID sestavy se také uloží v události prohlížeč.  Můžete najít zobrazením ID události "25" a zkontrolovat datum a čas.

    ![Data Management Gateway odesílání protokolů ID sestavy](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Archiv brány protokolů na hostitelském počítači brány
Existují některé scénáře, ve kterém máte problémy brány a protokoly brány nemohou sdílet přímo:

* Ručně nainstalovat bránu a registraci brány.
* Došlo k pokusu o registraci brány se znovu vygenerovalo klíčem v Data Management Gateway Configuration Manager.
* Pokusu o odeslání protokolů a nemůže být připojený hostitel služby brány.

Pro tyto případy můžete uložit protokoly brány jako soubor zip a ji sdílet, když požádáte podporu Microsoftu. Například pokud se zobrazí chybová zpráva při registraci brány, jako je znázorněno na následujícím snímku obrazovky.   

![Chyba registrace brány správy dat](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Klikněte na tlačítko **archivovat protokoly brány** propojit archivace a uložení protokolů a potom sdílet soubor zip s podporou Microsoftu.

![Protokoly archivu brány správy dat](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Vyhledejte protokoly brány
Informace protokolu podrobné brány najdete v protokolu událostí Windows.

1. Spusťte Windows **Prohlížeč událostí**.
2. Vyhledejte v protokolech **protokoly aplikací a služeb** > **brána správy dat** složky.

 Pokud řešíte problémy související s brány, hledejte události úrovně chyby v události prohlížeč.

![Brána správy dat protokolů v prohlížeči událostí](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
