---
title: Příprava na nasazení řešení v produkčním prostředí – Azure IoT Edge
description: Zjistěte, jak převést vaše řešení Azure IoT Edge z vývoje do produkčního prostředí, včetně nastavení zařízení s příslušnými certifikáty a vytvoření plánu nasazení pro budoucí aktualizace kódu.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f1de8330b950ffa09ce3e8ae168f05021b2ad80c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729455"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Příprava na nasazení řešení IoT Edge v produkčním prostředí

Až budete připraveni vzít své řešení IoT Edge z vývoje do produkčního prostředí, ujistěte se, že je nakonfigurované pro trvalý výkon.

Informace uvedené v tomto článku není všechny stejné. Abychom vám pomohli určit prioritu, každá část začíná seznamy, které rozdělují práci do dvou částí: **je důležité** ji dokončit před vstupem do produkčního prostředí nebo které vám **pomohou** poznat.

## <a name="device-configuration"></a>Konfigurace zařízení

Zařízení IoT Edge může být cokoliod Raspberry Pi přes notebook až po virtuální počítač běžící na serveru. Můžete mít přístup k zařízení fyzicky nebo prostřednictvím virtuálního připojení, nebo může být izolován o delší dobu. Ať tak či onak, chcete se ujistit, že je nakonfigurován tak, aby správně fungovat.

* **Důležité**
  * Instalace produkčních certifikátů
  * Mít plán správy zařízení
  * Použijte Moby jako motor kontejneru

* **Užitečné**
  * Vyberte si upstream protokol

### <a name="install-production-certificates"></a>Instalace produkčních certifikátů

Každé zařízení IoT Edge v produkčním prostředí potřebuje certifikát certifikační autority (CA) nainstalovaný. Tento certifikát certifikační autority je pak deklarován runtime IoT Edge v souboru config.yaml. Pro scénáře vývoje a testování vytvoří runtime IoT Edge dočasné certifikáty, pokud nejsou v souboru config.yaml deklarovány žádné certifikáty. Tyto dočasné certifikáty však vyprší po třech měsících a nejsou zabezpečené pro produkční scénáře.

Informace o úloze certifikátu certifikační autority zařízení najdete v tématu [Jak Azure IoT Edge používá certifikáty](iot-edge-certs.md).

Další informace o instalaci certifikátů na zařízení IoT Edge a jejich odkazování ze souboru config.yaml naleznete v [tématu Instalace produkčních certifikátů na zařízení IoT Edge](how-to-manage-device-certificates.md).

### <a name="have-a-device-management-plan"></a>Mít plán správy zařízení

Než vložíte jakékoli zařízení do produkčního prostředí, měli byste vědět, jak budete spravovat budoucí aktualizace. Pro zařízení IoT Edge může seznam součástí, které chcete aktualizovat, zahrnovat:

* Firmware zařízení
* Knihovny operačního systému
* Kontejnerový motor, jako Moby
* Déon IoT Edge
* Certifikáty certifikačních autorit

Další informace naleznete [v tématu Aktualizace runtime IoT Edge](how-to-update-iot-edge.md). Aktuální metody pro aktualizaci daemonu IoT Edge vyžadují fyzický nebo SSH přístup k zařízení IoT Edge. Pokud máte mnoho zařízení k aktualizaci, zvažte přidání kroků aktualizace do skriptu nebo použijte nástroj pro automatizaci, jako je Ansible.

### <a name="use-moby-as-the-container-engine"></a>Použijte Moby jako motor kontejneru

Kontejnerový motor je předpokladem pro jakékoli zařízení IoT Edge. Ve výrobě je podporován pouze motor moby. Ostatní kontejnerové motory, jako je Docker, pracují s IoT Edge a je v pořádku používat tyto moduly pro vývoj. Moby-engine lze přerozdělit při použití s Azure IoT Edge a Microsoft poskytuje údržbu pro tento modul.

### <a name="choose-upstream-protocol"></a>Vyberte si upstream protokol

Můžete nakonfigurovat protokol (který určuje použitý port) pro upstream komunikaci s ioT hubem pro agenta IoT Edge i centrum IoT Edge. Výchozí protokol je AMQP, ale můžete to změnit v závislosti na nastavení sítě.

Oba moduly runtime mají proměnnou prostředí **UpstreamProtocol.** Platné hodnoty pro proměnnou jsou:

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Nakonfigurujte proměnnou UpstreamProtocol pro agenta IoT Edge v souboru config.yaml na samotném zařízení. Například pokud vaše zařízení IoT Edge je za proxy server, který blokuje porty AMQP, budete muset nakonfigurovat agenta IoT Edge pro použití AMQP přes WebSocket (AMQPWS) k navázání počátečního připojení k ioT hubu.

Jakmile se vaše zařízení IoT Edge připojí, nezapomeňte pokračovat v konfiguraci proměnné UpstreamProtocol pro oba moduly runtime v budoucích nasazeních. Příklad tohoto procesu je uveden v [části Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy serveru](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Nasazení

* **Užitečné**
  * Buďte v souladu s upstream protokolem
  * Nastavení hostitelského úložiště pro systémové moduly
  * Zmenšete místo v paměti využívaný centrem IoT Edge
  * Nepoužívejte ladicí verze bitových kopií modulů

### <a name="be-consistent-with-upstream-protocol"></a>Buďte v souladu s upstream protokolem

Pokud jste nazařízeníovali agenta IoT Edge na zařízení IoT Edge tak, aby používal jiný protokol než výchozí AMQP, měli byste deklarovat stejný protokol ve všech budoucích nasazeních. Pokud je například vaše zařízení IoT Edge za proxy serverem, který blokuje porty AMQP, pravděpodobně jste nakonfigurovali zařízení pro připojení přes AMQP přes WebSocket (AMQPWS). Při nasazování modulů do zařízení nakonfigurujte stejný protokol AMQPWS pro agenta IoT Edge a rozbočovač IoT Edge, jinak výchozí amqp přepíše nastavení a zabrání opětovnému připojení.

Jenom nakonfigurovat proměnnou prostředí UpstreamProtocol pro moduly agenta IoT Edge a Moduly rozbočovače IoT Edge. Všechny další moduly přijmout bez ohledu na protokol je nastaven v modulech runtime.

Příklad tohoto procesu je uveden v [části Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy serveru](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Nastavení hostitelského úložiště pro systémové moduly

Moduly centra IoT Edge a modulů agentů používají místní úložiště k udržování stavu a povolování zasílání zpráv mezi moduly, zařízeními a cloudem. Pro lepší spolehlivost a výkon nakonfigurujte systémové moduly tak, aby používaly úložiště v hostitelském souborovém systému.

Další informace naleznete [v tématu Host storage for system modules](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Snížení paměti používaného centrem IoT Edge

Pokud nasazujete omezená zařízení s omezenou pamětí, můžete nakonfigurovat rozbočovač IoT Edge tak, aby běžel s efektivnější kapacitou a využíval méně místa na disku. Tyto konfigurace však omezují výkon centra IoT Edge, takže najděte správnou rovnováhu, která funguje pro vaše řešení.

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Neoptimalizujte výkon na omezených zařízeních

Rozbočovač IoT Edge je ve výchozím nastavení optimalizovaný pro výkon, takže se pokusí přidělit velké bloky paměti. Tato konfigurace může způsobit problémy se stabilitou na menších zařízeních, jako je Raspberry Pi. Pokud nasazujete zařízení s omezenými prostředky, můžete nastavit proměnnou prostředí **OptimizeForPerformance** na **false** v rozbočovači IoT Edge.

Když **OptimizeForPerformance** je nastavena na **hodnotu true**, hlava protokolu MQTT používá PooledByteBufferAllocator, který má lepší výkon, ale přiděluje více paměti. Alokátor nefunguje dobře na 32bitových operačních systémech nebo na zařízeních s nízkou pamětí. Navíc při optimalizaci pro výkon RocksDb přiděluje více paměti pro svou roli jako zprostředkovatele místního úložiště.

Další informace naleznete v [tématu Problémy se stabilitou u zařízení s omezenými prostředky](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Zakázání nepoužívaných protokolů

Dalším způsobem, jak optimalizovat výkon centra IoT Edge a snížit jeho využití paměti, je vypnout hlavičky protokolů pro všechny protokoly, které nepoužíváte ve vašem řešení.

Hlavy protokolů jsou konfigurovány nastavením logických proměnných prostředí pro modul centra IoT Edge v manifestech nasazení. Tři proměnné jsou:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Všechny tři proměnné mají *dvě podtržítka* a lze nastavit na hodnotu true nebo false.

#### <a name="reduce-storage-time-for-messages"></a>Zkrácení doby ukládání zpráv

Modul centra IoT Edge ukládá zprávy dočasně, pokud je z nějakého důvodu nelze doručit do centra IoT Hub. Můžete nakonfigurovat, jak dlouho centrum IoT Edge uchovává nedoručené zprávy, než je necháme vypršet. Pokud máte problémy s pamětí v zařízení, můžete snížit hodnotu **timeToLiveSecs** v dvojčete modulu centra IoT Edge.

Výchozí hodnota parametru timeToLiveSecs je 7200 sekund, což jsou dvě hodiny.

### <a name="do-not-use-debug-versions-of-module-images"></a>Nepoužívejte ladicí verze bitových kopií modulů

Při přechodu z testovacích scénářů do produkčních scénářů nezapomeňte odebrat konfigurace ladění z manifestů nasazení. Zkontrolujte, zda žádná z bitových kopií ** \.** modulu v manifestech nasazení nemá příponu ladění. Pokud jste přidali možnosti vytvoření vystavit porty v modulech pro ladění, odeberte tyto možnosti vytvořit také.

## <a name="container-management"></a>Správa kontejnerů

* **Důležité**
  * Správa přístupu k registru kontejnerů
  * Správa verzí pomocí značek

### <a name="manage-access-to-your-container-registry"></a>Správa přístupu k registru kontejnerů

Před nasazením modulů do produkčních zařízení IoT Edge se ujistěte, že řídíte přístup k registru kontejnerů, aby uživatelé nemohli přistupovat k iobrazům kontejnerů ani je provádět. Ke správě iblkopií kontejnerů použijte soukromý, nikoli veřejný registr kontejnerů.

V kurzech a další dokumentaci vás poučíme, abyste používali stejná přihlašovací údaje registru kontejneru na zařízení IoT Edge jako ve vývojovém počítači. Tyto pokyny jsou určeny pouze k tomu, aby vám pomohly snadněji nastavit testovací a vývojová prostředí a neměly by být dodržovány v produkčním scénáři.

Chcete-li získat bezpečnější přístup k registru, můžete si vybrat [možnosti ověřování](../container-registry/container-registry-authentication.md). Oblíbené a doporučené ověřování je použití objektu zabezpečení služby Active Directory, který je vhodný pro aplikace nebo služby k vytahování imagí kontejnerů automatizovaným nebo jinak bezobslužným (bezhlavým) způsobem, jako to dělají zařízení IoT Edge.

Chcete-li vytvořit instanční objekt, spusťte dva skripty, jak je popsáno v [vytvoření instančního objektu](../container-registry/container-registry-auth-service-principal.md#create-a-service-principal). Tyto skripty plní následující úkoly:

* První skript vytvoří instanční objekt. Výstupy ID instančního objektu a heslo instančního objektu. Ukládejte tyto hodnoty bezpečně do svých záznamů.

* Druhý skript vytvoří přiřazení rolí udělit instanční objekt, který lze spustit následně v případě potřeby. Doporučujeme použít roli uživatele **acrPull** pro `role` parametr. Seznam rolí najdete v tématu [Role a oprávnění registru kontejnerů Azure](../container-registry/container-registry-roles.md).

Chcete-li ověřit pomocí instančního objektu, zadejte ID instančního objektu a heslo, které jste získali z prvního skriptu. Zadejte tato pověření v manifestu nasazení.

* Pro uživatelské jméno nebo ID klienta zadejte ID instančního objektu.

* Pro heslo nebo tajný klíč klienta zadejte heslo instančního objektu.

> [!NOTE]
> Po implementaci rozšířeného ověřování zabezpečení zakažte nastavení **uživatele správce,** aby již nebylo k dispozici výchozí uživatelské jméno nebo přístup k heslu. V registru kontejnerů na webu Azure Portal vyberte v nabídce levého podokna v části **Nastavení** **přístupové klávesy**.

### <a name="use-tags-to-manage-versions"></a>Správa verzí pomocí značek

Značka je koncept dockeru, který můžete použít k rozlišení mezi verzemi kontejnerů dockeru. Značky jsou přípony jako **1.0,** které jdou na konci úložiště kontejnerů. Například **mcr.microsoft.com/azureiotedge-agent:1.0**. Značky jsou proměnlivé a lze je kdykoli změnit tak, aby ukazovaly na jiný kontejner, takže váš tým by se měl dohodnout na konvenci, kterou bude následovat při aktualizaci obrázků modulů.

Značky vám taky pomůžou vynutit aktualizace na vašich zařízeních IoT Edge. Při nabízení aktualizovanou verzi modulu do registru kontejneru, přírůstek značky. Potom můžete do svých zařízení vsazené na nové nasazení s přírůstek značky. Modul kontejneru rozpozná přírůstající značku jako novou verzi a vytáhne nejnovější verzi modulu do vašeho zařízení.

Příklad konvence značek najdete v [tématu Aktualizace runtime IoT Edge,](how-to-update-iot-edge.md#understand-iot-edge-tags) kde se dozvíte, jak IoT Edge používá postupující značky a konkrétní značky ke sledování verzí.

## <a name="networking"></a>Sítě

* **Užitečné**
  * Kontrola odchozí/příchozí konfigurace
  * Povolení připojení ze zařízení IoT Edge
  * Konfigurace komunikace prostřednictvím serveru proxy

### <a name="review-outboundinbound-configuration"></a>Kontrola odchozí/příchozí konfigurace

Komunikační kanály mezi Azure IoT Hub a IoT Edge jsou vždy nakonfigurované tak, aby byly odchozí. Pro většinu scénářů IoT Edge jsou nezbytné pouze tři připojení. Modul kontejneru se musí připojit k registru kontejneru (nebo registry), který obsahuje image modulu. Runtime IoT Edge se potřebuje připojit pomocí služby IoT Hub, aby načetl informace o konfiguraci zařízení a odesíll zprávy a telemetrii. A pokud používáte automatické zřizování, demon IoT Edge se musí připojit ke službě zřizování zařízení. Další informace naleznete v [tématu Brána firewall a pravidla konfigurace portu](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="allow-connections-from-iot-edge-devices"></a>Povolení připojení ze zařízení IoT Edge

Pokud vaše síťové nastavení vyžaduje, abyste explicitně povolili připojení ze zařízení IoT Edge, přečtěte si následující seznam součástí IoT Edge:

* **Agent IoT Edge** otevře trvalé připojení AMQP/MQTT k iot hubu, případně přes WebSockets.
* **Centrum IoT Edge** otevře jedno trvalé připojení AMQP nebo více připojení MQTT k ioT hubu, případně přes WebSockets.
* **Daemon IoT Edge** provádí přerušovaná volání HTTPS do ioT hubu.

Ve všech třech případech by název \*DNS odpovídal vzoru .azure-devices.net.

Kromě toho **kontejnerový modul** volá do registrů kontejnerů přes protokol HTTPS. Chcete-li načíst bitové kopie kontejneru IoT Edge runtime, je název DNS mcr.microsoft.com. Modul kontejneru se připojí k jiným registrům, jak je nakonfigurováno v nasazení.

Tento kontrolní seznam je výchozím bodem pro pravidla brány firewall:

   | Adresa\* URL ( = zástupný znak) | Odchozí porty TCP | Využití |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Registr kontejnerů společnosti Microsoft |
   | global.azure-devices-provisioning.net  | 443 | Přístup k DPS (nepovinné) |
   | \*.azurecr.io | 443 | Osobní kontejnerové registry a registry kontejnerů třetích stran |
   | \*.blob.core.windows.net | 443 | Stažení rozdílů image registru kontejnerů Azure z úložiště objektů blob |
   | \*.azure-devices.net | 5671, 8883, 443 | Přístup k centru IoT Hub |
   | \*.docker.io  | 443 | Přístup k Docker Hubu (volitelný) |

Některá z těchto pravidel brány firewall jsou zděděna z registru kontejnerů Azure. Další informace [najdete v tématu Konfigurace pravidel pro přístup k registru kontejnerů Azure za bránou firewall](../container-registry/container-registry-firewall-access-rules.md).

### <a name="configure-communication-through-a-proxy"></a>Konfigurace komunikace prostřednictvím serveru proxy

Pokud se vaše zařízení nasazují v síti, která používá proxy server, musí být schopná komunikovat přes proxy server, aby se dostala do registrů služby IoT Hub a kontejnerů. Další informace naleznete [v tématu Konfigurace komunikace zařízení IoT Edge prostřednictvím serveru proxy](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Správa řešení

* **Užitečné**
  * Nastavení protokolů a diagnostiky
  * Zvažte testy a ci/CD kanály

### <a name="set-up-logs-and-diagnostics"></a>Nastavení protokolů a diagnostiky

V Linuxu používá daemon IoT Edge deníky jako výchozí ovladač protokolování. Pomocí nástroje `journalctl` příkazového řádku můžete zadat dotaz na protokoly daemonu. V systému Windows používá daemon IoT Edge diagnostiku prostředí PowerShell. Slouží `Get-IoTEdgeLog` k dotazování protokoly z daemon. Moduly IoT Edge používají ovladač JSON pro protokolování, což je výchozí nastavení.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Když testujete nasazení IoT Edge, můžete obvykle přistupovat k vašim zařízením k načtení protokolů a řešení potíží. Ve scénáři nasazení pravděpodobně nemáte tuto možnost. Zamyslete se nad tím, jak budete shromažďovat informace o svých zařízeních ve výrobě. Jednou z možností je použití modulu protokolování, který shromažďuje informace z jiných modulů a odesílá je do cloudu. Jedním z příkladů modulu protokolování je [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics), nebo můžete navrhnout vlastní.

### <a name="place-limits-on-log-size"></a>Umístit omezení pro velikost protokolu

Ve výchozím nastavení moby kontejneru modul nenastaví omezení velikosti protokolu kontejneru. V průběhu času to může vést k zařízení zaplnění protokoly a nedostatek místa na disku. Zvažte následující možnosti, jak tomu zabránit:

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>Možnost: Nastavení globálních limitů, které platí pro všechny moduly kontejneru

Můžete omezit velikost všech souborů protokolu kontejneru v možnostech protokolu modulu kontejneru. Následující příklad nastaví ovladač `json-file` protokolu na (doporučeno) s omezeními velikosti a počtu souborů:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Přidejte (nebo přidejte) tyto `daemon.json` informace do souboru s názvem a umístěte je na správné místo pro platformu zařízení.

| Platforma | Umístění |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

Aby se změny projevily, musí být motor kontejneru restartován.

#### <a name="option-adjust-log-settings-for-each-container-module"></a>Možnost: Úprava nastavení protokolu pro každý modul kontejneru

Můžete tak učinit v **createOptions** každého modulu. Příklad:

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

#### <a name="additional-options-on-linux-systems"></a>Další možnosti v systémech Linux

* Nakonfigurujte modul kontejneru pro odesílání protokolů do `systemd` [deníku](https://docs.docker.com/config/containers/logging/journald/) nastavením `journald` jako výchozí ovladač protokolování.

* Pravidelně odeberte staré protokoly ze zařízení instalací nástroje logrotate. Použijte následující specifikaci souboru:

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

### <a name="consider-tests-and-cicd-pipelines"></a>Zvažte testy a ci/CD kanály

Pro nejefektivnější scénář nasazení IoT Edge zvažte integraci produkčního nasazení do testovacích a ci/CD kanálů. Azure IoT Edge podporuje několik platforem CI/CD, včetně Azure DevOps. Další informace najdete [v tématu průběžná integrace a průběžné nasazování do Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Další kroky

* Další informace o [automatickém nasazení IoT Edge](module-deployment-monitoring.md).
* Podívejte se, jak IoT Edge podporuje [průběžnou integraci a průběžné nasazování](how-to-ci-cd.md).
