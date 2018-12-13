---
title: Příprava zařízení a nasazení pro produkční prostředí – Azure IoT Edge | Dokumentace Microsoftu
description: Zjistěte, jak využít vaše řešení Azure IoT Edge z vývojového do produkčního prostředí, včetně nastavení zařízení pomocí vhodných certifikátů a vytváření plánu nasazení pro aktualizace budoucí kód.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: d39a271f33cb86bf870c3a7692c38d780093efa2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100034"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Příprava k nasazení svého řešení IoT Edge v produkčním prostředí

Až budete připraveni udělat vašeho řešení IoT Edge z vývojového do produkčního prostředí, ujistěte se, že je nakonfigurovaný pro probíhající výkonu.

Informace uvedené v tomto článku není stejné úrovni. Můžete určit prioritu, každá část začíná seznamy, které rozdělení práce na dva oddíly: **důležité** dokončit před přechodem do produkčního prostředí, nebo **užitečné** vám vědět.

## <a name="device-configuration"></a>Konfigurace zařízení

Zařízení IoT Edge můžou být čímkoli od Raspberry Pi až po přenosný počítač na virtuální počítač běží na serveru. Buď fyzicky, nebo prostřednictvím připojení virtuální sítě se můžou mít přístup k zařízení, nebo může být izolované dlouhou dobu. V obou případech nechcete se ujistit, že je nakonfigurovaná k provedení odpovídajícím způsobem. 

* **Důležité upozornění**
    * Instalace certifikátů v produkčním prostředí
    * Nepotřebujete žádný další plán správy zařízení
    * Použití Moby jako modul kontejneru

* **Užitečné**
    * Vybrat nadřazený protokolu

### <a name="install-production-certificates"></a>Instalace certifikátů v produkčním prostředí

Každé zařízení IoT Edge v produkčním prostředí potřebuje certifikát zařízení certifikát certifikační autority na něm nainstalován. Tento certifikát certifikační Autority je pak deklarován modul runtime IoT Edge v souboru config.yaml. Pro vývoj a testování snadnější, IoT Edge modul runtime vytvoří dočasné certifikáty, pokud se žádné certifikáty jsou deklarovány v souboru config.yaml. Ale tyto dočasné certifikáty vyprší po uplynutí tří měsíců a nejsou pro produkční scénáře bezpečné. 

Seznamte se s rolí zařízení certifikační Autority certifikátu, naleznete v tématu [používá certifikáty, jak Azure IoT Edge](iot-edge-certs.md).

Další informace o tom, jak nainstalovat certifikáty na zařízení IoT Edge a odkazovat ze souboru config.yaml najdete v tématu [nakonfigurovat nastavení zařízení IoT Edge tak, aby fungoval jako transparentní brána](how-to-create-transparent-gateway.md). Kroky pro konfiguraci certifikátů jsou stejné, jestli zařízení je bude používat jako brána nebo ne. Tento článek obsahuje skripty pro generování certifikátů ukázka pouze pro testování. Tyto certifikáty ukázka nepoužívejte v produkčním prostředí. 

### <a name="have-a-device-management-plan"></a>Nepotřebujete žádný další plán správy zařízení

Předtím, než začleníte libovolného zařízení v produkčním prostředí byste měli vědět, jak budete spravovat budoucí aktualizace. Pro zařízení IoT Edge může obsahovat seznam součástí k aktualizaci:

* Firmwaru zařízení
* Knihovny operačního systému
* Modul kontejneru, jako je Moby
* Démon IoT Edge
* Certifikáty certifikační autority

Postup k aktualizaci démona IoT Edge najdete v tématu [aktualizujte modul runtime IoT Edge](how-to-update-iot-edge.md). Aktuální metody pro aktualizaci démona hraničních zařízeních IoT vyžadují fyzické nebo přístup přes SSH do zařízení IoT Edge. Pokud máte velký počet zařízení k aktualizaci, zvažte přidání kroků aktualizace skriptu nebo použít automatizační nástroj, jako jsou Ansible k provedení aktualizace ve velkém měřítku.

### <a name="use-moby-as-the-container-engine"></a>Použití Moby jako modul kontejneru

Modul kontejneru na zařízení je předpokladem pro libovolné zařízení IoT Edge. V produkčním prostředí je podporován pouze moby-engine. Další moduly kontejneru, jako je Docker, pracovat s IoT Edge a je v pořádku. Chcete-li použít tyto moduly pro vývoj. Při použití s Azure IoT Edge se můžou znovu distribuovat modul moby a údržba pro tento modul poskytuje Microsoft. Použití ostatních vyhledávacích strojů kontejneru na zařízení IoT Edge se nepodporuje.

### <a name="choose-upstream-protocol"></a>Vybrat nadřazený protokolu

Protokol (a tedy port používaný) pro nadřazený komunikaci do služby IoT Hub je možné nakonfigurovat pro agenta Edge a Centrum Edge. Je výchozím protokolem AMQP, ale můžete chtít změnit v závislosti na nastavení sítě. 

Máte dva moduly runtime obě **UpstreamProtocol** proměnné prostředí. Platné hodnoty pro proměnné jsou: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

UpstreamProtocol proměnné konfigurace pro agenta Edge v souboru config.yaml na samotném zařízení. Pokud vaše zařízení IoT Edge je za proxy serverem, které blokují porty AMQP, budete muset nakonfigurovat agenta Edge k použití protokolu AMQP přes WebSocket (AMQPWS) k vytvoření počátečního připojení ke službě IoT Hub. 

Po připojení zařízení IoT Edge, je potřeba pokračovat v konfiguraci UpstreamProtocol proměnnou pro oba moduly runtime v budoucích nasazeních. Příklad tohoto procesu je k dispozici v [nakonfigurovat nastavení zařízení IoT Edge pro komunikaci přes proxy server](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Nasazení

* **Užitečné**
    * Bylo v souladu s nadřazeného protokolu
    * Snižte paměťový prostor využitý Centrum Edge
    * Nepoužívejte ladicí verze bitové kopie modulu

### <a name="be-consistent-with-upstream-protocol"></a>Bylo v souladu s nadřazeného protokolu

Pokud jste nakonfigurovali se agent Edge na zařízení IoT Edge pro použití jiného protokolu než výchozí AMQP, by měla deklarovat stejný protokol ve všech dalších nasazeních. Například pokud vaše zařízení IoT Edge je za proxy serverem, které blokují porty AMQP, pravděpodobně nakonfigurovali zařízení pro připojení přes AMQP přes WebSocket (AMQPWS). Při nasazování modulů do zařízení, pokud nenakonfigurujete stejný protokol APQPWS Edge agent a Centrum Edge, bude výchozí AMQP přepsat nastavení a by vám bránily v připojení později. 

Pouze musíte nakonfigurovat proměnné prostředí UpstreamProtocol Edge agent a moduly Edge hub. Všechny moduly, které další přijmout, ať protokolem nastaveným v modulech runtime. 

Příklad tohoto procesu je k dispozici v [nakonfigurovat nastavení zařízení IoT Edge pro komunikaci přes proxy server](how-to-configure-proxy-support.md).

### <a name="reduce-memory-space-used-by-edge-hub"></a>Snižte paměťový prostor využitý Centrum Edge

Pokud nasazujete omezené zařízení s omezenou pamětí, které jsou k dispozici, můžete nakonfigurovat Centrum Edge pro spouštění v kapacitě jednodušší a používá méně místa na disku. Tyto konfigurace však omezit výkon Centrum Edge, takže najít správnou rovnováhu, která funguje pro vaše řešení. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Nechcete optimalizaci výkonu na zařízení s omezením

Centrum Edge je optimalizován pro výkon ve výchozím nastavení, takže se pokusí o přidělení velké množství paměti. Tato konfigurace může způsobit problémy se stabilitou na menších zařízeních, jako je Raspberry Pi. Pokud nasazujete zařízení s omezenými prostředky, můžete nastavit **OptimizeForPerformance** proměnnou prostředí, aby **false** Edge hub. 

Další informace najdete v tématu [problémy se stabilitou u prostředku omezené zařízení](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Zakázat nepoužívané protokoly

Dalším způsobem, jak optimalizovat výkon Centrum Edge a snížit využití paměti je vypnout hlavy protokolu pro všechny protokoly, které nepoužíváte v rámci vašeho řešení. 

Protokol hlav jsou nakonfigurované tak, že nastavíte logické proměnné pro modul Edge hub ve vašich manifesty nasazení. Jsou tří proměnných:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Mají všechny tři proměnné *dvěma podtržítky* a je možné nastavit na hodnotu true nebo false. 

#### <a name="reduce-storage-time-for-messages"></a>Zkrátit čas, úložiště pro zprávy

Modul Centrum Edge ukládá zprávy dočasně, když se nepodaří doručit do služby IoT Hub, z jakéhokoli důvodu. Můžete nakonfigurovat, jak dlouho Centrum Edge udržuje nedoručené zprávy před umožňuje jejich platnost vyprší. Pokud máte obavy paměti na vašem zařízení, můžete snížit **timeToLiveSecs** hodnotu ve dvojčeti modulu Edge hub. 

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

Klíčové slovo je koncept Dockeru, můžete použít k rozlišení mezi verzemi kontejnery Dockeru. Značky jsou přípony jako **1.0** , přejděte na konci kontejneru úložiště. Například **mcr.microsoft.com/azureiotedge-agent:1.0**. Značky jsou měnitelné a může změnit tak, aby odkazoval na jiný kontejner v okamžiku, takže váš tým musí shodnout na konvenci dodržovat při aktualizaci vaší bitové kopie modulu v budoucnu. 

Značky pomáhají také k vynucení aktualizací na zařízení IoT Edge. Když nahrajete aktualizovanou verzi modulu do vašeho registru kontejneru, zvýšte značky. Potom push nové nasazení do zařízení se značkou zvýší. Modul kontejneru rozpozná zvýšena značky jako novou verzi a získávat nejnovější verze modulu do svého zařízení stáhli. 

Příklad konvence značek, naleznete v tématu [aktualizujte modul runtime IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags) se dozvíte, jak IoT Edge používá ke sledování verzí postupné značky a konkrétními značkami. 

## <a name="networking"></a>Sítě

* **Užitečné**
    * Konfigurace odchozího/příchozího revize
    * Připojení seznamu povolených IP adres
    * Konfigurace komunikace prostřednictvím proxy serveru

### <a name="review-outboundinbound-configuration"></a>Konfigurace odchozího/příchozího revize

Komunikační kanály mezi Azure IoT Hub a hraničními zařízeními IoT jsou vždy nakonfigurované jako odchozí. Pro většinu scénářů IoT Edge jsou potřeba jenom tři připojení. Modul kontejner potřebuje pro připojení k registru kontejneru (nebo registry), které obsahuje bitové kopie modulu. Modul runtime IoT Edge je potřeba připojit pomocí služby IoT Hub načíst informace o konfiguraci zařízení a odesílání zpráv a telemetrie. A pokud používáte automatické zřizování, démona IoT Edge potřebuje pro připojení ke službě Device Provisioning. Další informace najdete v tématu [brány Firewall a pravidla konfigurace portu](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="whitelist-connections"></a>Připojení seznamu povolených IP adres

Pokud nastavení sítě vyžaduje, že je explicitně seznamu povolených IP adres vytvořit připojení ze zařízení IoT Edge, přečtěte si následující seznam součástí IoT Edge:

* **Agenta IoT Edge** otevře trvalé připojení AMQP/MQTT do služby IoT Hub, případně přes WebSockets. 
* **Centrum IoT Edge** otevře samostatné připojení AMQP trvalý nebo více připojení MQTT do služby IoT Hub, případně přes WebSockets. 
* **IoT Edge démon** zavolá přerušované HTTPS do služby IoT Hub. 

Ve všech třech případech název DNS by odpovídat vzoru \*.azure devices.net. 

Kromě toho **modul Container** provede volání do registrů kontejnerů přes protokol HTTPS. Pokud chcete načíst Image kontejnerů modulu runtime IoT Edge, název DNS je mcr.microsoft.com. Modul kontejneru se připojí k další registry podle konfigurace v nasazení. 

### <a name="configure-communication-through-a-proxy"></a>Konfigurace komunikace prostřednictvím proxy serveru

Pokud vaše zařízení se chystáte nasadit v síti, která používá proxy server, musí být schopný komunikovat přes proxy server pro dosažení služby IoT Hub a registry kontejnerů. Další informace najdete v tématu [nakonfigurovat nastavení zařízení IoT Edge pro komunikaci přes proxy server](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Správa řešení

* **Užitečné**
    * Nastavení diagnostiky a protokolování
    * Vezměte v úvahu testy a kanálů CI/CD

### <a name="set-up-logs-and-diagnostics"></a>Nastavení diagnostiky a protokolování

Démon IoT Edge v Linuxu, používá journald jako výchozí protokolování ovladač. Můžete použít nástroj příkazového řádku `journalctl` k dotazování démona protokoly. Démon IoT Edge na Windows, používá diagnostiky prostředí PowerShell. Použití `Get-WinEvent` do protokolů dotazu z démona. Moduly IoT Edge použít ovladač JSON pro protokolování, což je výchozí Dockeru.  

Při testování nasazení IoT Edge, obvykle možné získat přístup zařízení k načtení protokolů a řešení potíží. V případě nasazení nemusí mít tuto možnost. Zvažte, jak budete shromažďovat informace o zařízeních v produkčním prostředí. Jednou z možností je použít modul protokolování, který shromažďuje informace z jiných modulů a odešle ji do cloudu. Jedním z příkladů modulu protokolování je [logspout loganalytics](https://github.com/veyalla/logspout-loganalytics), nebo můžete navrhnout vlastní. 

Pokud máte obavy o protokolech příliš velká na zařízení prostředků omezené, máte několik možností ke snížení využití paměti. 

* Konkrétně můžete omezit velikost všech docker logfiles v samotné démona Dockeru. Pro Linux, konfigurace démona v `/etc/docker/daemon.json`. Pro Windows `C:\ProgramData\docker\confige\daemon.json`. 
* Pokud chcete upravit velikost souboru protokolu pro každý kontejner, provést v CreateOptions field každého modulu. 
* Konfigurace Dockeru k automatické správě protokoly podle journald nastavení jako výchozí protokolování ovladače pro Docker. 
* Pravidelně odstraňuje staré protokoly ze zařízení nainstalováním nástroje logrotate pro Docker. Použijte následující specifikace souboru: 

   ```
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

## <a name="next-steps"></a>Další postup

* Další informace o [automatického nasazení IoT Edge](module-deployment-monitoring.md).
* Podívejte se, jak IoT Edge podporuje [průběžnou integraci a průběžné nasazování](how-to-ci-cd.md).
