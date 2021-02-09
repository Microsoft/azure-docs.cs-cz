---
title: Použití Azure Portal k řešení potíží s Azure Stack Edge pro s grafickým procesorem | Microsoft Docs
description: Popisuje, jak řešit potíže s grafickými procesory Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: alkohli
ms.openlocfilehash: c56fcecbd850dd0add26e5d50093eea595e3d825
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833418"
---
# <a name="troubleshoot-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Řešení potíží na zařízení GPU Azure Stack Edge pro 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Tento článek popisuje, jak řešit problémy na zařízení GPU Azure Stack Edge pro. 


## <a name="run-diagnostics"></a>Spuštění diagnostiky

Pokud chcete diagnostikovat a vyřešit potíže s libovolnými chybami zařízení, můžete spustit diagnostické testy. Diagnostické testy spustíte pomocí následujících kroků v místním webovém uživatelském rozhraní vašeho zařízení.

1. V místním webovém uživatelském rozhraní přejděte na **Řešení potíží > Diagnostické testy**. Vyberte test, který chcete spustit, a vyberte **Spustit test**. Test diagnostikuje jakékoli možné problémy s nastavením vaší sítě, zařízení, webového proxy serveru, času nebo cloudu. Na spuštěné testy v zařízení budete upozorněni.

    ![Vybrat testy ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)
 
2. Po dokončení testů se zobrazí výsledky. 

    ![Zobrazit výsledky testu](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    Pokud bude test neúspěšný, zobrazí se adresa URL s doporučenou akcí. Vyberte adresu URL pro zobrazení doporučené akce.
 
    ![Zkontrolovat upozornění pro neúspěšné testy](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>Shromáždění balíčku pro podporu

Balíček protokolů se skládá z příslušných protokolů, které mohou podpoře Microsoftu pomoci s řešením potíží na libovolném zařízení. Balíček protokolů můžete vygenerovat prostřednictvím místního webového uživatelského rozhraní.

Pokud chcete balíček pro podporu vyzvednout, proveďte následující kroky. 

1. V místním webovém uživatelském rozhraní přejděte na **Řešení potíží > Podpora**. Vyberte **vytvořit balíček pro podporu**. Systém začne shromažďovat balíček pro podporu. Shromažďování balíčků může několik minut trvat.

    ![Vyberte Přidat uživatele.](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. Po vytvoření balíčku pro podporu vyberte **Stáhnout balíček pro podporu**. Komprimovaný balíček se stáhne do vámi vybrané cesty. Balíček můžete rozbalit a zobrazit soubory systémového protokolu.

    ![Vyberte Přidat uživatele 2.](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>Shromáždit rozšířené protokoly zabezpečení

Rozšířené protokoly zabezpečení mohou být softwarové nebo hardwarové protokoly o vniknutí pro zařízení Azure Stack Edge pro.

### <a name="software-intrusion-logs"></a>Protokoly o vniknutí softwaru

Pro příchozí a odchozí provoz jsou shromažďovány neoprávněné vniknutí softwaru nebo výchozí protokoly brány firewall. 

- V případě, že je zařízení v objektu pro vytváření bitové kopie, je povoleno výchozí protokolování brány firewall. Tyto protokoly se ve výchozím nastavení rozbalí do balíčku pro podporu, když vytvoříte balíček pro podporu prostřednictvím místního uživatelského rozhraní nebo přes rozhraní Windows PowerShell daného zařízení.

- Pokud je v balíčku pro podporu nutné zadat jenom protokoly brány firewall pro kontrolu jakéhokoli vniknutí softwaru (NW) v zařízení, použijte `-Include FirewallLog` při vytváření balíčku pro podporu možnost použít. 

- Pokud neposkytnete žádnou konkrétní možnost zahrnutí, do balíčku pro podporu se zařadí protokol brány firewall jako výchozí.

- V balíčku pro podporu je protokol brány firewall `pfirewall.log` a umístěn v kořenové složce. Tady je příklad protokolu neoprávněného vniknutí softwaru pro zařízení Azure Stack Edge pro. 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>Protokoly neoprávněného přístupu k hardwaru

K detekci neoprávněného vniknutí hardwaru do zařízení jsou v současné době protokolovány všechny události skříně, jako je například otevření nebo zavření skříně. 

- Protokol událostí systému ze zařízení je čten pomocí `racadm` rutiny. Tyto události jsou následně filtrovány pro události související se skříní v `HWIntrusion.txt` souboru.

- Pokud chcete v balíčku pro podporu získat jenom protokol neoprávněného vniknutí hardwaru, použijte `-Include HWSelLog` při vytváření balíčku pro podporu možnost. 

- Pokud neposkytnete žádnou konkrétní možnost zahrnutí, do balíčku pro podporu se zařadí protokol neoprávněného přístupu k hardwaru.

- V balíčku pro podporu je protokol neoprávněného hardwaru `HWIntrusion.txt` a umístěný v kořenové složce. Tady je příklad protokolu neoprávněného hardwaru pro zařízení Azure Stack Edge pro. 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="use-logs-to-troubleshoot"></a>Použití protokolů k řešení potíží

Veškeré chyby, ke kterým během procesu nahrávání a aktualizace došlo, budou součástí příslušných chybových souborů.

1. Soubory chyb zobrazíte tak, že přejdete do sdílené složky a vyberete sdílenou složku pro zobrazení obsahu. 


2. Vyberte _složku Microsoft Data box Edge_. Tato složka obsahuje dvě podsložky:

    - Složku nahrávání, která obsahuje soubory protokolů s chybami nahrávání.
    - Složku aktualizací s chybami, ke kterým došlo během aktualizace.

    Tady je ukázkový soubor protokolu pro aktualizace.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. Když v tomto souboru uvidíte chybu (zvýrazněné v ukázce), poznamenejte si kód chyby – v tomto případě je to 16001. Vyhledejte popis tohoto kódu chyby v následující referenci k chybám.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="use-error-lists-to-troubleshoot"></a>Použití seznamů chyb k řešení potíží

Seznamy chyb jsou kompilovány z identifikovaných scénářů a lze je použít pro účely diagnostiky a řešení potíží. 

## <a name="azure-resource-manager"></a>Azure Resource Manager

Tady jsou chyby, které se můžou zobrazit během konfigurace Azure Resource Manager pro přístup k vašemu zařízení. 

| **Problém/chyby** |  **Řešení** | 
|------------|-----------------|
|Běžné problémy|<li>[Ověřte, že hraniční zařízení je správně nakonfigurované](#verify-the-device-is-configured-properly).<li> [Ověřte, že je klient správně nakonfigurovaný.](#verify-the-client-is-configured-properly)|
|Add-AzureRmEnvironment: při odesílání žádosti došlo k chybě.<br>Na řádku: 1 znak: 1<br>+ Add-AzureRmEnvironment-Name Az3-ARMEndpoint " https://management.dbe ...|Tato chyba znamená, že vaše zařízení Azure Stack Edge pro je nedosažitelné nebo správně nakonfigurované. Ověřte, zda hraniční zařízení a klient jsou správně nakonfigurovány. Pokyny najdete v řádku **Obecné problémy** v této tabulce.|
|Služba vrátila chybu. Další podrobnosti naleznete u vlastnosti InnerException: základní připojení bylo ukončeno: nelze vytvořit vztah důvěryhodnosti pro zabezpečený kanál SSL/TLS. |   Tato chyba je pravděpodobně způsobena tím, že jedna nebo více kroků vlastního certifikátu byly nesprávně provedeny. [Tady](./azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates)najdete pokyny. |
|Operace vrátila neplatný stavový kód ServiceUnavailable. <br> Stavový kód odpovědi neindikuje úspěch: 503 (služba není k dispozici). | Tato chyba by mohla být výsledkem kterékoli z těchto podmínek.<li>ArmStsPool je v zastaveném stavu.</li><li>Některý z webů Azure Resource Manager/Security token Services je mimo provoz.</li><li>Prostředek clusteru Azure Resource Manager nefunguje.</li><br><strong>Poznámka:</strong> Restartování zařízení může problém vyřešit, ale měli byste shromáždit balíček podpory, abyste ho mohli dále ladit.|
|AADSTS50126: neplatné uživatelské jméno nebo heslo.<br>ID trasování: 29317da9-52fc-4ba0-9778-446ae5625e5a<br>ID korelace: 1b9752c4-8cbf-4304-a714-8a16527410f4<br>Časové razítko: 2019-11-15 09:21:57Z: vzdálený server vrátil chybu: (400) chybný požadavek.<br>Na řádku: 1 znak: 1 |Tato chyba by mohla být výsledkem kterékoli z těchto podmínek.<li>V případě neplatného uživatelského jména a hesla ověřte, že zákazník změnil heslo z Azure Portal pomocí následujících [kroků a potom](./azure-stack-edge-j-series-set-azure-resource-manager-password.md) pomocí správného hesla.<li>Pro neplatné ID tenanta je ID tenanta pevný identifikátor GUID a měl by být nastavený na. `c0257de7-538f-415c-993a-1b87a031879d`</li>|
|Connect-AzureRmAccount: AADSTS90056: prostředek je zakázaný nebo neexistuje. Zkontrolujte kód vaší aplikace a ujistěte se, že jste zadali přesnou adresu URL prostředku pro prostředek, ke kterému se pokoušíte získat přístup.<br>ID trasování: e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>ID korelace: 75c8ef5a-830e-48b5-b039-595a96488ff9 časové razítko: 2019-11-18 07:00:51Z: vzdálený server vrátil chybu: (400) Bad |Koncové body prostředků použité v `Add-AzureRmEnvironment` příkazu nejsou správné.|
|Nepovedlo se získat koncové body z cloudu.<br>Ujistěte se prosím, že máte síťové připojení. Podrobnosti o chybě: HTTPSConnectionPool (host = ' Management. dbg-of4k6suvm.microsoftdatabox.com ', port = 30005): maximální počet opakovaných pokusů je překročen s adresou URL:/metadata/Endpoints? API-Version = 2015-01-01 (způsobená SSLError (SSLError ("chybná Metoda handshake: Error ([" rutiny SSL "," tls_process_server_certificate "," ověření certifikátu se nezdařilo ")],) |Tato chyba se objevuje hlavně v prostředí Mac/Linux a je způsobená následujícími problémy:<li>Do úložiště certifikátů Pythonu se nepřidal certifikát formátu PEM.</li> |

### <a name="verify-the-device-is-configured-properly"></a>Ověřte, že zařízení je správně nakonfigurované.

1. Z místního uživatelského rozhraní ověřte, zda je správně nakonfigurována síť zařízení.

2. Ověřte, jestli jsou certifikáty aktualizované pro všechny koncové body, jak je uvedeno [tady](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates).

3. Získejte Azure Resource Manager koncový bod pro správu a přihlášení ze stránky **zařízení** v místním uživatelském rozhraní.

4. Ověřte, jestli je zařízení aktivované a zaregistrované v Azure.


### <a name="verify-the-client-is-configured-properly"></a>Ověřte, že je klient správně nakonfigurovaný.

1. Ověřte, jestli je nainstalovaná správná verze prostředí PowerShell, jak je uvedeno [zde](azure-stack-edge-j-series-connect-resource-manager.md#step-3-install-powershell-on-the-client).

2. Ověřte, že jsou nainstalované správné moduly PowerShellu, jak je uvedeno [tady](azure-stack-edge-j-series-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client).

3. Ověřte, že jsou dostupné koncové body Azure Resource Manager a přihlášení. Můžete zkusit testovat koncové body. Příklad:

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   Pokud nejsou dostupné, přidejte sem položky DNS/host souboru, jak je uvedeno [zde](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution).
   
4. Ověřte, že jsou nainstalované klientské certifikáty, jak je uvedeno [zde](azure-stack-edge-j-series-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell).

5. Pokud zákazník používá PowerShell, měli byste povolit předvolby ladění, aby se zobrazily podrobné zprávy spuštěním tohoto příkazu PowerShellu. 

    `$debugpreference = "continue"`

## <a name="blob-storage-on-device"></a>Blob Storage na zařízení 

Tady jsou chyby související s úložištěm objektů BLOB v Azure Stack hraničních zařízení pro/Data Box Gateway.

| **Problém/chyby** |  **Řešení** | 
|--------------------|-----------------|
|Nelze načíst podřízené prostředky. Hodnota pro jednu z hlaviček protokolu HTTP není ve správném formátu.| V nabídce **Upravit** vyberte **cílová Azure Stack rozhraní API**. Pak restartujte Průzkumník služby Azure Storage.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Ověřte, že se název koncového bodu `<accountname>.blob.<serialnumber>.microsoftdatabox.com` přidal do souboru hostitelů v této cestě: `C:\Windows\System32\drivers\etc\hosts` ve Windows nebo `/etc/hosts` v systému Linux.|
|Nelze načíst podřízené prostředky.<br> Podrobnosti: certifikát podepsaný svým držitelem |Importujte certifikát SSL pro vaše zařízení do Průzkumník služby Azure Storage: <ol><li>Stáhněte si certifikát z Azure Portal. Další informace najdete v tématu [stažení certifikátu](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>V nabídce **Upravit** vyberte certifikáty SSL a pak vyberte **importovat certifikáty**.</li></ol>|
|Příkaz AzCopy se zdá, že před zobrazením této chyby přestane reagovat na minutu:<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Ověřte, že se název koncového bodu `<accountname>.blob.<serialnumber>.microsoftdatabox.com` přidal do souboru hostitelů v umístění: `C:\Windows\System32\drivers\etc\hosts` .|
|Příkaz AzCopy se zdá, že před zobrazením této chyby přestane reagovat na minutu:<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |Importujte certifikát SSL pro vaše zařízení do úložiště certifikátů systému. Další informace najdete v tématu [stažení certifikátu](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|Příkaz AzCopy se zdá, že před zobrazením této chyby přestane reagovat na 20 minut:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |Ověřte, že se název koncového bodu `<accountname>.blob.<serialnumber>.microsoftdatabox.com` přidal do souboru hostitelů v umístění: `/etc/hosts` .|
|Příkaz AzCopy se zdá, že před zobrazením této chyby přestane reagovat na 20 minut:<br>`Error parsing source location… The SSL connection could not be established`. |Importujte certifikát SSL pro vaše zařízení do úložiště certifikátů systému. Další informace najdete v tématu [stažení certifikátu](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|Příkaz AzCopy se zdá, že před zobrazením této chyby přestane reagovat na 20 minut:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|Ověřte, že se název koncového bodu `<accountname>.blob.<serialnumber>.microsoftdatabox.com` přidal do souboru hostitelů v umístění: `/etc/hosts` .|
|Příkaz AzCopy se zdá, že před zobrazením této chyby přestane reagovat na 20 minut: `Error parsing source location… The SSL connection could not be established` .|Importujte certifikát SSL pro vaše zařízení do úložiště certifikátů systému. Další informace najdete v tématu [stažení certifikátu](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|Hodnota pro jednu z hlaviček protokolu HTTP není ve správném formátu.|Data Box není podporována nainstalovaná verze knihovny Microsoft Azure Storage pro Python. Podporované verze najdete v tématu Azure Data Box požadavky na úložiště objektů BLOB.|
|… [SSL: CERTIFICATE_VERIFY_FAILED]...| Před spuštěním Pythonu nastavte proměnnou prostředí REQUESTS_CA_BUNDLE na cestu k souboru certifikátu SSL s kódováním Base64 (viz jak [Stáhnout certifikát](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate). Příklad:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>Alternativně přidejte certifikát do úložiště certifikátů systému a pak nastavte tuto proměnnou prostředí na cestu k danému úložišti. Například na Ubuntu:<br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`.|
|Vypršel časový limit připojení.|Přihlaste se k Azure Stack Edge pro a potom zkontrolujte, že je odemčený. Kdykoli se zařízení restartuje, zůstane uzamčeno, dokud se někdo přihlásí.|

## <a name="troubleshoot-iot-edge-errors"></a>Řešení chyb IoT Edge

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o tom, jak [řešit problémy s aktivací zařízení](azure-stack-edge-gpu-troubleshoot-activation.md).