---
title: Příprava na nasazení řešení v produkčním prostředí – Azure IoT Edge
description: Naučte se, jak převést Azure IoT Edge řešení z vývoje do produkčního prostředí, včetně nastavení zařízení s příslušnými certifikáty a vytvoření plánu nasazení pro budoucí aktualizace kódu.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 58959310a0daec3835585f64c53f2b8853eb553e
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548590"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Příprava k nasazení svého řešení IoT Edge v produkčním prostředí

Až budete připraveni udělat vašeho řešení IoT Edge z vývojového do produkčního prostředí, ujistěte se, že je nakonfigurovaný pro probíhající výkonu.

Informace uvedené v tomto článku nejsou stejné. Můžete určit prioritu, každá část začíná seznamy, které rozdělení práce na dva oddíly: **důležité** dokončit před přechodem do produkčního prostředí, nebo **užitečné** vám vědět.

## <a name="device-configuration"></a>Konfigurace zařízení

Zařízení IoT Edge můžou být čímkoli od Raspberry Pi až po přenosný počítač na virtuální počítač běží na serveru. Buď fyzicky, nebo prostřednictvím připojení virtuální sítě se můžou mít přístup k zařízení, nebo může být izolované dlouhou dobu. V obou případech se chcete ujistit, že je správně nakonfigurovaný tak, aby fungoval.

* **Důležité upozornění**
  * Instalace produkčních certifikátů
  * Nepotřebujete žádný další plán správy zařízení
  * Použití Moby jako modul kontejneru

* **Užitečné**
  * Vybrat nadřazený protokolu

### <a name="install-production-certificates"></a>Instalace produkčních certifikátů

Každé zařízení IoT Edge v produkčním prostředí potřebuje certifikát zařízení certifikát certifikační autority na něm nainstalován. Tento certifikát certifikační Autority je pak deklarován modul runtime IoT Edge v souboru config.yaml. Pro scénáře vývoje a testování vytvoří modul runtime IoT Edge dočasné certifikáty, pokud nejsou v souboru config. yaml deklarovány žádné certifikáty. Ale tyto dočasné certifikáty vyprší po uplynutí tří měsíců a nejsou pro produkční scénáře bezpečné.

Seznamte se s rolí zařízení certifikační Autority certifikátu, naleznete v tématu [používá certifikáty, jak Azure IoT Edge](iot-edge-certs.md).

Další informace o tom, jak nainstalovat certifikáty na zařízení IoT Edge a odkázat je ze souboru config. yaml, najdete v tématu [instalace produkčních certifikátů na IoT Edge zařízení](how-to-create-transparent-gateway.md).

### <a name="have-a-device-management-plan"></a>Nepotřebujete žádný další plán správy zařízení

Předtím, než začleníte libovolného zařízení v produkčním prostředí byste měli vědět, jak budete spravovat budoucí aktualizace. Pro zařízení IoT Edge může obsahovat seznam součástí k aktualizaci:

* Firmwaru zařízení
* Knihovny operačního systému
* Modul kontejneru, jako je Moby
* Démon IoT Edge
* Certifikáty certifikační autority

Další informace najdete v tématu [aktualizace modulu runtime IoT Edge](how-to-update-iot-edge.md). Aktuální metody pro aktualizaci démona hraničních zařízeních IoT vyžadují fyzické nebo přístup přes SSH do zařízení IoT Edge. Pokud máte mnoho zařízení, která se mají aktualizovat, zvažte přidání kroků aktualizace do skriptu nebo použití automatizačního nástroje, jako je Ansible.

### <a name="use-moby-as-the-container-engine"></a>Použití Moby jako modul kontejneru

Kontejnerový modul je předpokladem pro jakékoli IoT Edge zařízení. V produkčním prostředí je podporován pouze moby-engine. Další moduly kontejneru, jako je Docker, pracovat s IoT Edge a je v pořádku. Chcete-li použít tyto moduly pro vývoj. Při použití s Azure IoT Edge se můžou znovu distribuovat modul moby a údržba pro tento modul poskytuje Microsoft.

### <a name="choose-upstream-protocol"></a>Vybrat nadřazený protokolu

Můžete nakonfigurovat protokol (který určuje použitý port) pro IoT Hub pro odesílání dat pro IoT Edge agenta i pro Centrum IoT Edge. Je výchozím protokolem AMQP, ale můžete chtít změnit v závislosti na nastavení sítě.

Máte dva moduly runtime obě **UpstreamProtocol** proměnné prostředí. Platné hodnoty pro proměnné jsou:

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Nakonfigurujte proměnnou UpstreamProtocol pro agenta IoT Edge v souboru config. yaml na samotném zařízení. Pokud se například vaše zařízení IoT Edge za proxy server, které blokuje porty AMQP, může být nutné nakonfigurovat agenta IoT Edge na používání AMQP přes WebSocket (AMQPWS) a vytvořit tak počáteční připojení k IoT Hub.

Po připojení zařízení IoT Edge, je potřeba pokračovat v konfiguraci UpstreamProtocol proměnnou pro oba moduly runtime v budoucích nasazeních. Příklad tohoto procesu je k dispozici v [nakonfigurovat nastavení zařízení IoT Edge pro komunikaci přes proxy server](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Nasazení

* **Užitečné**
  * Bylo v souladu s nadřazeného protokolu
  * Nastavení hostitelského úložiště pro systémové moduly
  * Zmenšení místa v paměti, které používá centrum IoT Edge
  * Nepoužívejte ladicí verze bitové kopie modulu

### <a name="be-consistent-with-upstream-protocol"></a>Bylo v souladu s nadřazeného protokolu

Pokud jste agenta IoT Edge v zařízení IoT Edge nakonfigurovali tak, aby používal jiný protokol než výchozí AMQP, měli byste deklarovat stejný protokol ve všech budoucích nasazeních. Například pokud vaše zařízení IoT Edge je za proxy serverem, které blokují porty AMQP, pravděpodobně nakonfigurovali zařízení pro připojení přes AMQP přes WebSocket (AMQPWS). Když nasadíte moduly do zařízení, nakonfigurujete stejný protokol AMQPWS pro IoT Edge agenta a centrum IoT Edge, jinak výchozí AMQP přepíše nastavení a zabráníte opětovnému připojení.

Musíte nakonfigurovat pouze proměnnou prostředí UpstreamProtocol pro agenty IoT Edge a moduly IoT Edge hub. Všechny moduly, které další přijmout, ať protokolem nastaveným v modulech runtime.

Příklad tohoto procesu je k dispozici v [nakonfigurovat nastavení zařízení IoT Edge pro komunikaci přes proxy server](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Nastavení hostitelského úložiště pro systémové moduly

Rozbočovače IoT Edge a agenti používají místní úložiště k udržení stavu a povolení zasílání zpráv mezi moduly, zařízeními a cloudem. Pro zajištění vyšší spolehlivosti a výkonu nakonfigurujte systémové moduly tak, aby v hostitelském systému souborů používaly úložiště.

Další informace najdete v tématu [úložiště hostitele pro systémové moduly](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Zmenšení místa v paměti, které používá centrum IoT Edge

Pokud nasazujete omezená zařízení s omezenou dostupnou pamětí, můžete nakonfigurovat centrum IoT Edge tak, aby běželo efektivněji a používalo méně místa na disku. Tyto konfigurace omezují výkon centra IoT Edge, ale vyhledají správný zůstatek, který funguje pro vaše řešení.

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Nechcete optimalizaci výkonu na zařízení s omezením

Ve výchozím nastavení je centrum IoT Edge optimalizované pro výkon, takže se pokusí přidělit velké bloky paměti. Tato konfigurace může způsobit problémy se stabilitou na menších zařízeních, jako je Raspberry Pi. Pokud nasazujete zařízení s omezenými prostředky, možná budete chtít nastavit proměnnou prostředí **OptimizeForPerformance** na **hodnotu false** v centru IoT Edge.

Pokud je **OptimizeForPerformance** nastaveno na **hodnotu true**, hlavní hlavička protokolu MQTT používá PooledByteBufferAllocator, který má lepší výkon, ale přiděluje více paměti. Alokátor nefunguje dobře na 32 operačních systémech nebo na zařízeních s nedostatkem paměti. Kromě toho, pokud je optimalizován pro výkon, RocksDb přiděluje více paměti pro svou roli jako místní poskytovatel úložiště.

Další informace najdete v tématu [problémy se stabilitou u prostředku omezené zařízení](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Zakázat nepoužívané protokoly

Dalším způsobem, jak optimalizovat výkon centra IoT Edge a snížit jeho využití paměti, je vypnout pro všechny protokoly, které nepoužíváte ve vašem řešení, hlavičky protokolu.

Hlavičky protokolu jsou nakonfigurovány nastavením logických proměnných prostředí pro modul IoT Edge hub v manifestech nasazení. Jsou tří proměnných:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Mají všechny tři proměnné *dvěma podtržítky* a je možné nastavit na hodnotu true nebo false.

#### <a name="reduce-storage-time-for-messages"></a>Zkrátit čas, úložiště pro zprávy

Modul IoT Edge hub ukládá zprávy dočasně, pokud je nelze doručit do IoT Hub z jakéhokoli důvodu. Můžete nakonfigurovat, jak dlouho bude Centrum IoT Edge obsahovat nedoručené zprávy, a teprve potom jim vyprší jejich platnost. Pokud máte v zařízení problémy s pamětí, můžete snížit hodnotu **timeToLiveSecs** v modulu centra IoT Edge s dvojitou platností.

Výchozí hodnota parametru timeToLiveSecs je 7200 sekund, což je dvě hodiny.

### <a name="do-not-use-debug-versions-of-module-images"></a>Nepoužívejte ladicí verze bitové kopie modulu

Při přesunu z testovacích scénářů na produkční scénáře, nezapomeňte odebrat konfiguraci ladění z manifesty nasazení. Zkontrolujte, že žádný z bitové kopie modulu v manifestech nasazení nemá  **\.ladění** příponu. Pokud jste přidali možnosti vystavit porty modulů pro ladění, vytvoření odebrat ty vytvořit také možnosti.

## <a name="container-management"></a>Správa kontejnerů

* **Důležité upozornění**
  * Správa přístupu k registru kontejneru
  * Použití značek ke správě verzí

### <a name="manage-access-to-your-container-registry"></a>Správa přístupu k registru kontejneru

Předtím, než ho nasadíte do zařízení IoT Edge produkční moduly, ujistěte se tak, aby netradiční způsob nelze získat přístup nebo změníte imagí kontejnerů v řízení přístupu do vašeho registru kontejneru. Pomocí registru kontejnerů privátní, veřejná, Správa imagí kontejnerů.

V kurzech a další dokumentace nám dáte pokyn, aby vám používat stejné přihlašovací údaje registru kontejneru na vašem zařízení IoT Edge, jaké používáte na svém vývojovém počítači. Tyto pokyny jsou určeny pouze pro vám pomůže snadněji nastavit prostředí pro vývoj a testování a by neměl následovat v produkční scénář. Služba Azure Container Registry doporučuje [ověřování pomocí instančních objektů](../container-registry/container-registry-auth-service-principal.md) při aplikace nebo služby o přijetí změn imagí kontejnerů v automatických nebo jinak bezobslužném, stejně jako zařízení IoT Edge. Vytvoření instančního objektu s přístupem jen pro čtení do vašeho registru kontejneru a poskytují tímto uživatelským jménem a heslem v manifestu nasazení.

### <a name="use-tags-to-manage-versions"></a>Použití značek ke správě verzí

Značka je koncept Docker, který můžete použít k rozlišení mezi verzemi kontejnerů Docker. Značky jsou přípony jako **1.0** , přejděte na konci kontejneru úložiště. Například **mcr.microsoft.com/azureiotedge-agent:1.0**. Značky jsou měnitelné a může změnit tak, aby odkazoval na jiný kontejner v okamžiku, takže váš tým musí shodnout na konvenci dodržovat při aktualizaci vaší bitové kopie modulu v budoucnu.

Značky pomáhají také k vynucení aktualizací na zařízení IoT Edge. Když nahrajete aktualizovanou verzi modulu do vašeho registru kontejneru, zvýšte značky. Potom push nové nasazení do zařízení se značkou zvýší. Modul kontejneru rozpozná zvýšena značky jako novou verzi a získávat nejnovější verze modulu do svého zařízení stáhli.

Příklad konvence značek, naleznete v tématu [aktualizujte modul runtime IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags) se dozvíte, jak IoT Edge používá ke sledování verzí postupné značky a konkrétními značkami.

## <a name="networking"></a>Sítě

* **Užitečné**
  * Konfigurace odchozího/příchozího revize
  * Povolení připojení ze zařízení IoT Edge
  * Konfigurace komunikace prostřednictvím proxy serveru

### <a name="review-outboundinbound-configuration"></a>Konfigurace odchozího/příchozího revize

Komunikační kanály mezi Azure IoT Hub a hraničními zařízeními IoT jsou vždy nakonfigurované jako odchozí. Pro většinu scénářů IoT Edge jsou potřeba jenom tři připojení. Modul kontejner potřebuje pro připojení k registru kontejneru (nebo registry), které obsahuje bitové kopie modulu. Modul runtime IoT Edge je potřeba připojit pomocí služby IoT Hub načíst informace o konfiguraci zařízení a odesílání zpráv a telemetrie. A pokud používáte automatické zřizování, démona IoT Edge potřebuje pro připojení ke službě Device Provisioning. Další informace najdete v tématu [brány Firewall a pravidla konfigurace portu](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="allow-connections-from-iot-edge-devices"></a>Povolení připojení ze zařízení IoT Edge

Pokud instalace sítě vyžaduje, abyste výslovně povolili připojení vytvořená ze zařízení IoT Edge, Projděte si následující seznam IoT Edge součástí:

* **Agenta IoT Edge** otevře trvalé připojení AMQP/MQTT do služby IoT Hub, případně přes WebSockets.
* **Centrum IoT Edge** otevře samostatné připojení AMQP trvalý nebo více připojení MQTT do služby IoT Hub, případně přes WebSockets.
* **IoT Edge démon** zavolá přerušované HTTPS do služby IoT Hub.

Ve všech třech případech název DNS by odpovídat vzoru \*.azure devices.net.

Kromě toho **modul Container** provede volání do registrů kontejnerů přes protokol HTTPS. Pokud chcete načíst Image kontejnerů modulu runtime IoT Edge, název DNS je mcr.microsoft.com. Modul kontejneru se připojí k další registry podle konfigurace v nasazení.

Tento kontrolní seznam je výchozím bodem pro pravidla brány firewall:

   | Adresa URL (\* = zástupný znak) | Odchozí porty TCP | Využití |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft Container Registry |
   | global.azure-devices-provisioning.net  | 443 | Přístup k DPS (volitelné) |
   | \*.azurecr.io | 443 | Osobní a Registry kontejnerů třetích stran |
   | \*.blob.core.windows.net | 443 | Stažení rozdílových rozdílů Azure Container Registry imagí ze služby Blob Storage |
   | \*.azure-devices.net | 5671, 8883, 443 | Přístup k IoT Hub |
   | \*.docker.io  | 443 | Přístup k Docker Hub (volitelné) |

Některá z těchto pravidel brány firewall jsou zděděná z Azure Container Registry. Další informace najdete v tématu [Konfigurace pravidel pro přístup ke službě Azure Container Registry za bránou firewall](../container-registry/container-registry-firewall-access-rules.md).

### <a name="configure-communication-through-a-proxy"></a>Konfigurace komunikace prostřednictvím proxy serveru

Pokud vaše zařízení se chystáte nasadit v síti, která používá proxy server, musí být schopný komunikovat přes proxy server pro dosažení služby IoT Hub a registry kontejnerů. Další informace najdete v tématu [nakonfigurovat nastavení zařízení IoT Edge pro komunikaci přes proxy server](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Správa řešení

* **Užitečné**
  * Nastavení diagnostiky a protokolování
  * Vezměte v úvahu testy a kanálů CI/CD

### <a name="set-up-logs-and-diagnostics"></a>Nastavení diagnostiky a protokolování

V systému Linux používá démon IoT Edge jako výchozí ovladač protokolování deníky. Můžete použít nástroj příkazového řádku `journalctl` k dotazování démona protokoly. Démon IoT Edge na Windows, používá diagnostiky prostředí PowerShell. Použití `Get-IoTEdgeLog` do protokolů dotazu z démona. IoT Edge moduly používají ovladač JSON pro protokolování, což je výchozí nastavení.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Při testování nasazení IoT Edge, obvykle možné získat přístup zařízení k načtení protokolů a řešení potíží. V případě nasazení nemusí mít tuto možnost. Zvažte, jak budete shromažďovat informace o zařízeních v produkčním prostředí. Jednou z možností je použít modul protokolování, který shromažďuje informace z jiných modulů a odešle ji do cloudu. Jedním z příkladů modulu protokolování je [logspout loganalytics](https://github.com/veyalla/logspout-loganalytics), nebo můžete navrhnout vlastní.

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

* Nakonfigurujte modul kontejnerů tak, aby odesílal protokoly do [deníku](https://docs.docker.com/config/containers/logging/journald/) `systemd` nastavením `journald` jako výchozího ovladače protokolování.

* Pomocí nástroje logrotate pravidelně odeberte staré protokoly ze zařízení. Použijte následující specifikace souboru:

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

### <a name="consider-tests-and-cicd-pipelines"></a>Vezměte v úvahu testy a kanálů CI/CD

Nejúčinnější scénář nasazení IoT Edge zvažte možnost integrace produkčního nasazení testování a kanálů CI/CD. Azure IoT Edge podporuje více platforem CI/CD, včetně Azure DevOps. Další informace najdete v tématu [průběžnou integraci a průběžné nasazování do Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Další kroky

* Další informace o [automatického nasazení IoT Edge](module-deployment-monitoring.md).
* Podívejte se, jak IoT Edge podporuje [průběžnou integraci a průběžné nasazování](how-to-ci-cd.md).
