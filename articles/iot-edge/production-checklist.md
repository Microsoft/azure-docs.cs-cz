---
title: Příprava na nasazení řešení v produkčním prostředí – Azure IoT Edge
description: Naučte se, jak převést Azure IoT Edge řešení z vývoje do produkčního prostředí, včetně nastavení zařízení s příslušnými certifikáty a vytvoření plánu nasazení pro budoucí aktualizace kódu.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 65710047d5d5d1cc6b835144f7778392fb20b797
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042262"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Příprava na nasazení IoT Edge řešení v produkčním prostředí

Až budete připraveni převést IoT Edge řešení z vývoje do produkčního prostředí, ujistěte se, že je nakonfigurován pro průběžný výkon.

Informace uvedené v tomto článku nejsou stejné. Pro pomoc s určením priorit se každá část spustí se seznamy, které rozdělují práci do dvou částí: **důležité** pro dokončení před tím, než budete pokračovat v produkčním prostředí, nebo je **vhodné** znát.

## <a name="device-configuration"></a>Konfigurace zařízení

IoT Edge zařízení mohou být od maliny PI až po přenosný počítač na virtuální počítač běžící na serveru. Je možné, že budete mít přístup k zařízení buď fyzicky, nebo prostřednictvím virtuálního připojení, nebo může být izolovaný po delší dobu. V obou případech se chcete ujistit, že je správně nakonfigurovaný tak, aby fungoval.

* **Důležité upozornění**
  * Instalace produkčních certifikátů
  * Máte plán správy zařízení
  * Použití Moby jako modulu kontejneru

* **Případech**
  * Zvolit nadřazený protokol

### <a name="install-production-certificates"></a>Instalace produkčních certifikátů

Každé IoT Edge zařízení v produkčním prostředí potřebuje certifikát certifikační autority zařízení (CA), který je na něm nainstalovaný. Tento certifikát certifikační autority se pak deklaruje jako modul runtime IoT Edge v konfiguračním souboru. Pro scénáře vývoje a testování vytvoří modul runtime IoT Edge dočasné certifikáty, pokud nejsou v konfiguračním souboru deklarovány žádné certifikáty. Platnost těchto dočasných certifikátů však vyprší po třech měsících a nejsou zabezpečené pro produkční scénáře. V produkčních scénářích byste měli zadat vlastní certifikát certifikační autority pro zařízení, a to buď od certifikační autority podepsané svým držitelem, nebo zakoupené od Komerční certifikační autority.

<!--1.1-->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> V současné době omezení libiothsm brání použití certifikátů, jejichž platnost vyprší, od 1. ledna 2038.

:::moniker-end

Informace o [tom, jak Azure IoT Edge používá certifikáty](iot-edge-certs.md), najdete v tématu How of a Certificate of Certificate CA.

Další informace o tom, jak nainstalovat certifikáty na zařízení IoT Edge a odkazovat na ně z konfiguračního souboru, najdete v tématu [Správa certifikátů na IoT Edgem zařízení](how-to-manage-device-certificates.md).

### <a name="have-a-device-management-plan"></a>Máte plán správy zařízení

Než umístíte jakékoli zařízení do provozu, měli byste se seznámit s tím, jak budete spravovat budoucí aktualizace. V případě zařízení IoT Edge může seznam součástí, které se mají aktualizovat, zahrnovat:

* Firmware zařízení
* Knihovny operačního systému
* Modul pro kontejnery, jako je Moby
* IoT Edge
* Certifikáty certifikačních autorit

Další informace najdete v tématu [aktualizace modulu runtime IoT Edge](how-to-update-iot-edge.md). Aktuální metody pro aktualizaci IoT Edge vyžadují pro IoT Edge zařízení fyzický přístup nebo SSH. Pokud máte mnoho zařízení, která se mají aktualizovat, zvažte přidání kroků aktualizace do skriptu nebo použití automatizačního nástroje, jako je Ansible.

### <a name="use-moby-as-the-container-engine"></a>Použití Moby jako modulu kontejneru

Kontejnerový modul je předpokladem pro jakékoli IoT Edge zařízení. V produkčním prostředí se podporuje jenom Moby-Engine. Jiné kontejnerové moduly, jako je Docker, fungují s IoT Edge a jsou v pořádku, aby je bylo možné použít pro vývoj. Moby-Engine lze znovu distribuovat při použití s Azure IoT Edge a Microsoft poskytuje údržbu tohoto stroje.

### <a name="choose-upstream-protocol"></a>Zvolit nadřazený protokol

Můžete nakonfigurovat protokol (který určuje použitý port) pro IoT Hub pro odesílání dat pro IoT Edge agenta i pro Centrum IoT Edge. Výchozí protokol je AMQP, ale možná budete chtít změnit v závislosti na nastavení sítě.

Oba moduly modul runtime mají proměnnou prostředí **UpstreamProtocol** . Platné hodnoty pro proměnnou jsou:

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Nakonfigurujte proměnnou UpstreamProtocol pro agenta IoT Edge v konfiguračním souboru na samotném zařízení. Pokud se například vaše zařízení IoT Edge za proxy server, které blokuje porty AMQP, může být nutné nakonfigurovat agenta IoT Edge na používání AMQP přes WebSocket (AMQPWS) a vytvořit tak počáteční připojení k IoT Hub.

Po připojení zařízení IoT Edge nezapomeňte v budoucích nasazeních pokračovat v konfiguraci proměnné UpstreamProtocol pro oba moduly runtime. Příklad tohoto procesu je k dispozici v části [Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy server](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Nasazení

* **Případech**
  * Konzistentní s nadřazeným protokolem
  * Nastavení hostitelského úložiště pro systémové moduly
  * Zmenšení místa v paměti, které používá centrum IoT Edge
  * Nepoužívat ladicí verze imagí modulu

### <a name="be-consistent-with-upstream-protocol"></a>Konzistentní s nadřazeným protokolem

Pokud jste agenta IoT Edge v zařízení IoT Edge nakonfigurovali tak, aby používal jiný protokol než výchozí AMQP, měli byste deklarovat stejný protokol ve všech budoucích nasazeních. Pokud se například vaše zařízení IoT Edge za proxy server blokující porty AMQP, pravděpodobně jste zařízení nakonfigurovali pro připojení přes AMQP přes WebSocket (AMQPWS). Když nasadíte moduly do zařízení, nakonfigurujete stejný protokol AMQPWS pro IoT Edge agenta a centrum IoT Edge, jinak výchozí AMQP přepíše nastavení a zabráníte opětovnému připojení.

Musíte nakonfigurovat pouze proměnnou prostředí UpstreamProtocol pro agenty IoT Edge a moduly IoT Edge hub. Jakékoli další moduly přijmou jakýkoli protokol v modulech runtime.

Příklad tohoto procesu je k dispozici v části [Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy server](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Nastavení hostitelského úložiště pro systémové moduly

Rozbočovače IoT Edge a agenti používají místní úložiště k udržení stavu a povolení zasílání zpráv mezi moduly, zařízeními a cloudem. Pro zajištění vyšší spolehlivosti a výkonu nakonfigurujte systémové moduly tak, aby v hostitelském systému souborů používaly úložiště.

Další informace najdete v tématu [úložiště hostitele pro systémové moduly](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Zmenšení místa v paměti, které používá centrum IoT Edge

Pokud nasazujete omezená zařízení s omezenou dostupnou pamětí, můžete nakonfigurovat centrum IoT Edge tak, aby běželo efektivněji a používalo méně místa na disku. Tyto konfigurace omezují výkon centra IoT Edge, ale vyhledají správný zůstatek, který funguje pro vaše řešení.

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Neoptimalizujte pro výkon u omezených zařízení.

Ve výchozím nastavení je centrum IoT Edge optimalizované pro výkon, takže se pokusí přidělit velké bloky paměti. Tato konfigurace může způsobit problémy s stabilitou na menších zařízeních, jako je třeba malin. PI. Pokud nasazujete zařízení s omezenými prostředky, možná budete chtít nastavit proměnnou prostředí **OptimizeForPerformance** na **hodnotu false** v centru IoT Edge.

Pokud je **OptimizeForPerformance** nastaveno na **hodnotu true**, hlavní hlavička protokolu MQTT používá PooledByteBufferAllocator, který má lepší výkon, ale přiděluje více paměti. Alokátor nefunguje dobře na 32 operačních systémech nebo na zařízeních s nedostatkem paměti. Kromě toho, pokud je optimalizován pro výkon, RocksDb přiděluje více paměti pro svou roli jako místní poskytovatel úložiště.

Další informace najdete v tématu [problémy se stabilitou na menších zařízeních](troubleshoot-common-errors.md#stability-issues-on-smaller-devices).

#### <a name="disable-unused-protocols"></a>Zakázat nepoužívané protokoly

Dalším způsobem, jak optimalizovat výkon centra IoT Edge a snížit jeho využití paměti, je vypnout pro všechny protokoly, které nepoužíváte ve vašem řešení, hlavičky protokolu.

Hlavičky protokolu jsou nakonfigurovány nastavením logických proměnných prostředí pro modul IoT Edge hub v manifestech nasazení. Tři proměnné jsou:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Všechny tři proměnné mají *dvě podtržítka* a lze je nastavit buď na hodnotu true, nebo false.

#### <a name="reduce-storage-time-for-messages"></a>Snížení doby úložiště pro zprávy

Modul IoT Edge hub ukládá zprávy dočasně, pokud je nelze doručit do IoT Hub z jakéhokoli důvodu. Můžete nakonfigurovat, jak dlouho bude Centrum IoT Edge obsahovat nedoručené zprávy, a teprve potom jim vyprší jejich platnost. Pokud máte v zařízení problémy s pamětí, můžete snížit hodnotu **timeToLiveSecs** v modulu centra IoT Edge s dvojitou platností.

Výchozí hodnota parametru timeToLiveSecs je 7200 sekund, což je dvě hodiny.

### <a name="do-not-use-debug-versions-of-module-images"></a>Nepoužívat ladicí verze imagí modulu

Při přechodu z testovacích scénářů do produkčních scénářů nezapomeňte odebrat konfigurace ladění z manifestů nasazení. Ověřte, že žádný z imagí modulu v manifestech nasazení nemá příponu **\. ladění** . Pokud jste přidali možnosti vytváření pro vystavování portů v modulech pro ladění, odeberte taky tyto možnosti vytváření.

## <a name="container-management"></a>Správa kontejnerů

* **Důležité upozornění**
  * Správa přístupu k registru kontejneru
  * Použití značek ke správě verzí
* **Případech**
  * Ukládání kontejnerů za běhu do privátního registru

### <a name="manage-access-to-your-container-registry"></a>Správa přístupu k registru kontejneru

Než nasadíte moduly do produkčních IoT Edge zařízení, ujistěte se, že máte pod kontrolou přístup k registru kontejneru, aby k nim nemohly přistupovat ani dělat změny v imagích kontejnerů. Pro správu imagí kontejneru použijte privátní registr kontejnerů, nikoli veřejný.

V kurzech a další dokumentaci vám pomůžeme, abyste na svém zařízení IoT Edge používali stejné přihlašovací údaje registru kontejneru jako při použití ve vývojovém počítači. Tyto pokyny jsou určené jenom pro snazší nastavení testovacích a vývojových prostředí a neměli byste je dodržovat v produkčním scénáři.

Pro bezpečnější přístup k vašemu registru máte možnost volby [ověřování](../container-registry/container-registry-authentication.md). Oblíbeným a doporučeným ověřováním je použití instančního objektu služby Active Directory, který je vhodný pro aplikace nebo služby pro vyžádání imagí kontejneru v automatizovaném nebo jiném bezobslužném režimu (bez periferních), jako IoT Edge zařízení.

Chcete-li vytvořit instanční objekt, spusťte dva skripty, jak je popsáno v tématu [Vytvoření instančního objektu](../container-registry/container-registry-auth-service-principal.md#create-a-service-principal). Tyto skripty dělají následující úlohy:

* První skript vytvoří instanční objekt. Vypíše ID objektu služby a heslo instančního objektu. Tyto hodnoty bezpečně ukládejte do svých záznamů.

* Druhý skript vytvoří přiřazení rolí, které se udělí instančnímu objektu, který se v případě potřeby může spustit později. Doporučujeme použít pro parametr roli uživatele **acrPull** `role` . Seznam rolí najdete v tématu [Azure Container Registry role a oprávnění](../container-registry/container-registry-roles.md).

K ověřování pomocí instančního objektu zadejte ID a heslo instančního objektu, které jste získali z prvního skriptu. Zadejte tyto přihlašovací údaje v manifestu nasazení.

* Pro uživatelské jméno nebo ID klienta zadejte ID objektu služby.

* Pro heslo nebo tajný klíč klienta zadejte heslo objektu služby.

> [!NOTE]
> Po implementaci rozšířeného ověřování zabezpečení zakažte nastavení **uživatele pro správu** , aby nebylo k dispozici výchozí přístup k uživatelskému jménu nebo heslu. V registru kontejneru v Azure Portal v nabídce levé podokno v části **Nastavení** vyberte **přístupové klíče**.

### <a name="use-tags-to-manage-versions"></a>Použití značek ke správě verzí

Značka je koncept Docker, který můžete použít k rozlišení mezi verzemi kontejnerů Docker. Značky jsou přípony jako **1,0** , které směřují na konec úložiště kontejnerů. Například **MCR.Microsoft.com/azureiotedge-agent:1.0**. Značky jsou proměnlivé a dají se kdykoli změnit tak, aby odkazovala na jiný kontejner, takže váš tým by měl souhlasit s konvencí, která se má při aktualizaci imagí modulu pokračovat.

Značky vám také pomůžou vymáhat aktualizace vašich IoT Edgech zařízení. Když nahrajete aktualizovanou verzi modulu do registru kontejneru, zvyšte značku. Pak na zařízení Nahrajte nové nasazení se zvýšenými značkami. Kontejnerový modul rozpozná zvětšenou značku jako novou verzi a stáhne nejnovější verzi modulu dolů na vaše zařízení.

Příklad konvence značek najdete v tématu [aktualizace modulu runtime IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags) , kde zjistíte, jak IoT Edge používá ke sledování verzí značky válcování a specifické značky.

### <a name="store-runtime-containers-in-your-private-registry"></a>Ukládání kontejnerů za běhu do privátního registru

Víte o ukládání imagí kontejneru pro vlastní moduly kódu do privátního registru Azure, ale můžete ji také použít k ukládání veřejných imagí kontejneru, jako jsou například moduly runtime edgeAgent a edgHub. V takovém případě může být vyžadováno, pokud máte přísná omezení brány firewall, protože tyto kontejnery runtime jsou uloženy v Microsoft Container Registry (MCR).

Získejte Image pomocí příkazu Docker Pull k umístění do privátního registru. Mějte na paměti, že budete muset image aktualizovat pomocí každé nové vydané verze IoT Edge runtime.

| Kontejner IoT Edge runtime | Příkaz Docker pull |
| --- | --- |
| [Agent Azure IoT Edge](https://hub.docker.com/_/microsoft-azureiotedge-agent) | `docker pull mcr.microsoft.com/azureiotedge-agent` |
| [Centrum Azure IoT Edge](https://hub.docker.com/_/microsoft-azureiotedge-hub) | `docker pull mcr.microsoft.com/azureiotedge-hub` |

Dále nezapomeňte aktualizovat odkazy na image v deployment.template.jssouboru pro systémové moduly edgeAgent a edgeHub. Nahraďte `mcr.microsoft.com` názvem a serverem registru pro oba moduly.

* edgeAgent:

    `"image": "<registry name and server>/azureiotedge-agent:1.1",`

* edgeHub:

    `"image": "<registry name and server>/azureiotedge-hub:1.1",`

## <a name="networking"></a>Sítě

* **Případech**
  * Kontrola odchozí/příchozí konfigurace
  * Povolení připojení ze zařízení IoT Edge
  * Konfigurace komunikace prostřednictvím proxy serveru

### <a name="review-outboundinbound-configuration"></a>Kontrola odchozí/příchozí konfigurace

Komunikační kanály mezi Azure IoT Hub a IoT Edge jsou vždycky nakonfigurované tak, aby byly odchozí. U většiny scénářů IoT Edge je potřeba jenom tři připojení. Modul kontejneru musí být připojen pomocí registru kontejnerů (nebo registrů), které obsahují image modulu. Modul runtime IoT Edge musí být připojen pomocí IoT Hub pro načtení informací o konfiguraci zařízení a k odesílání zpráv a telemetrie. A pokud používáte Automatické zřizování, IoT Edge se musí připojit ke službě Device Provisioning. Další informace najdete v tématu [pravidla konfigurace pro bránu firewall a port](troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="allow-connections-from-iot-edge-devices"></a>Povolení připojení ze zařízení IoT Edge

Pokud instalace sítě vyžaduje, abyste výslovně povolili připojení vytvořená ze zařízení IoT Edge, Projděte si následující seznam IoT Edge součástí:

* **Agent IoT Edge** otevírá trvalé připojení AMQP/MQTT k IoT Hub, možná prostřednictvím WebSockets.
* **Centrum IoT Edge** otevře jedno trvalé připojení AMQP nebo několik připojení MQTT k IoT Hub, možná přes objekty WebSockets.
* **Služba IoT Edge** zajišťuje přerušované volání https IoT Hub.

Ve všech třech případech by měl název DNS odpovídat vzoru \* . Azure-Devices.NET.

Kromě toho **kontejnerový modul** provádí volání registrů kontejnerů přes protokol HTTPS. Pokud chcete načíst image kontejneru IoT Edge runtime, název DNS je mcr.microsoft.com. Kontejnerový modul se připojuje k jiným registrům, jak jsou nakonfigurované v nasazení.

Tento kontrolní seznam je výchozím bodem pro pravidla brány firewall:

   | Adresa URL ( \* = zástupný znak) | Odchozí porty TCP | Využití |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Container Registry Microsoftu |
   | global.azure-devices-provisioning.net  | 443 | Přístup k DPS (volitelné) |
   | \*. azurecr.io | 443 | Osobní a Registry kontejnerů třetích stran |
   | \*.blob.core.windows.net | 443 | Stažení rozdílových rozdílů Azure Container Registry imagí ze služby Blob Storage |
   | \*. azure-devices.net | 5671, 8883, 443 | Přístup k IoT Hub |
   | \*. docker.io  | 443 | Přístup k Docker Hub (volitelné) |

Některá z těchto pravidel brány firewall jsou zděděná z Azure Container Registry. Další informace najdete v tématu [Konfigurace pravidel pro přístup ke službě Azure Container Registry za bránou firewall](../container-registry/container-registry-firewall-access-rules.md).

> [!NOTE]
> Pokud chcete zajistit konzistentní plně kvalifikovaný název domény mezi koncovými body REST a daty od **15. června 2020** , změní se koncový bod služby Microsoft Container Registry data z `*.cdn.mscr.io` na. `*.data.mcr.microsoft.com`  
> Další informace najdete v tématu [Konfigurace pravidel brány firewall klienta Microsoft Container Registry](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md) .

Pokud nechcete bránu firewall nakonfigurovat tak, aby povolovala přístup k veřejným registrům kontejnerů, můžete ukládat image do soukromého registru kontejnerů, jak je popsáno v [kontejnerech runtime úložiště v privátním registru](#store-runtime-containers-in-your-private-registry).

### <a name="configure-communication-through-a-proxy"></a>Konfigurace komunikace prostřednictvím proxy serveru

Pokud budou vaše zařízení nasazená v síti, která používá proxy server, musí být schopná komunikovat prostřednictvím proxy serveru, aby dosáhly IoT Hub a registrů kontejnerů. Další informace najdete v tématu [Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy server](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Správa řešení

* **Případech**
  * Nastavení protokolů a diagnostiky
  * Zvažte testy a kanály CI/CD.

### <a name="set-up-logs-and-diagnostics"></a>Nastavení protokolů a diagnostiky

V systému Linux používá démon IoT Edge jako výchozí ovladač protokolování deníky. `journalctl`K dotazování protokolů démona můžete použít nástroj příkazového řádku.

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

Počínaje verzí 1,2 IoT Edge spoléhá na více procesů démonů. I když se každý z protokolů démona dá jednotlivě dotazovat `journalctl` , `iotedge system` příkazy poskytují pohodlný způsob dotazování na kombinované protokoly.

* Konsolidovaný `iotedge` příkaz:

  ```bash
  sudo iotedge system logs
  ```

* Ekvivalentní `journalctl` příkaz:

  ```bash
  journalctl -u aziot-edge -u aziot-identityd -u aziot-keyd -u aziot-certd -u aziot-tpmd
  ```

:::moniker-end

V systému Windows démon IoT Edge používá diagnostiku prostředí PowerShell. Slouží `Get-IoTEdgeLog` k dotazování protokolů z procesu démon. IoT Edge moduly používají ovladač JSON pro protokolování, což je výchozí nastavení.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Při testování nasazení IoT Edge můžete obvykle získat přístup k zařízením, abyste mohli načítat protokoly a řešit potíže. V případě nasazení nemusí být tato možnost k dispozici. Vezměte v úvahu, jak budete shromažďovat informace o vašich zařízeních v produkčním prostředí. Jednou z možností je použití protokolovacího modulu, který shromažďuje informace z jiných modulů a odesílá je do cloudu. Jedním z příkladů protokolovacího modulu je [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics), nebo můžete navrhnout vlastní.

### <a name="place-limits-on-log-size"></a>Omezit omezení velikosti protokolu

Ve výchozím nastavení nenastavuje Moby kontejnerového modulu limity velikosti protokolu kontejneru. V průběhu času to může vést k tomu, že zařízení zaplňuje protokoly a uvolní místo na disku. Pokud tomu chcete zabránit, vezměte v úvahu následující možnosti:

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>Možnost: Nastavte globální limity, které platí pro všechny moduly kontejneru.

Velikost všech protokolů kontejnerů můžete omezit v možnostech protokolu modulu container Engine. Následující příklad nastaví ovladač protokolu na `json-file` (doporučeno) s omezeními velikosti a počtu souborů:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Přidejte (nebo přidejte) tyto informace do souboru s názvem `daemon.json` a umístěte je do správného umístění pro platformu zařízení.

| Platforma | Umístění |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

Aby se změny projevily, musí být modul kontejneru restartován.

#### <a name="option-adjust-log-settings-for-each-container-module"></a>Možnost: upravit nastavení protokolu pro každý modul kontejneru

To můžete provést v **createOptions** každého modulu. Například:

```yml
"createOptions": {
    "HostConfig": {
        "LogConfig": {
            "Type": "json-file",
            "Config": {
                "max-size": "10m",
                "max-file": "3"
            }
        }
    }
}
```

#### <a name="additional-options-on-linux-systems"></a>Další možnosti pro systémy Linux

* Nakonfigurujte modul kontejneru tak, aby odesílal protokoly do `systemd` [deníku](https://docs.docker.com/config/containers/logging/journald/) nastavením `journald` jako výchozí ovladač protokolování.

* Pomocí nástroje logrotate pravidelně odeberte staré protokoly ze zařízení. Použijte následující specifikaci souboru:

   ```txt
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>Zvažte testy a kanály CI/CD.

Pro nejúčinnější scénář nasazení IoT Edge zvažte integraci nasazení v produkčním prostředí do kanálů pro testování a CI/CD. Azure IoT Edge podporuje několik platforem CI/CD, včetně Azure DevOps. Další informace najdete v tématu [průběžná integrace a průběžné nasazování do Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [IoT Edge automatickém nasazení](module-deployment-monitoring.md).
* Podívejte se, jak IoT Edge podporuje [průběžnou integraci a průběžné nasazování](how-to-continuous-integration-continuous-deployment.md).
