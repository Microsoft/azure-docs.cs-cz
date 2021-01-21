---
title: Připojit zařízení IoT Edge pro příjem dat – Azure IoT Edge | Microsoft Docs
description: Jak nakonfigurovat zařízení IoT Edge pro připojení k zařízením Azure IoT Edge brány.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 1258fd4b5c69b399b70d1f2db1be63765771e631
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629399"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway-preview"></a>Připojení zařízení IoT Edge pro příjem dat k bráně Azure IoT Edge (Preview)

Tento článek poskytuje pokyny pro vytvoření důvěryhodného připojení mezi IoT Edge bránou a zařízením IoT Edge pro příjem dat.

>[!NOTE]
>Tato funkce vyžaduje IoT Edge verze 1,2, která je ve verzi Public Preview, spouští se kontejnery Linux.

V případě brány může IoT Edge zařízení jak bránu, tak i zařízení pro příjem dat. Několik bran IoT Edge lze rozvrstvit a vytvořit tak hierarchii zařízení. Zařízení pro příjem dat (nebo podřízená) můžou ověřovat a odesílat nebo přijímat zprávy přes jejich bránu (nebo nadřazenou) zařízení.

Existují dvě různé konfigurace pro IoT Edge zařízení v hierarchii brány a tento článek řeší obě. První je IoT Edge zařízení **nejvyšší vrstvy** . Pokud se mezi sebou připojuje více IoT Edge zařízení, všechna zařízení, která nemají nadřazené zařízení, ale připojují se přímo k IoT Hub, se považují za v horní vrstvě. Toto zařízení zodpovídá za zpracování žádostí ze všech zařízení, která jsou pod ní. Druhá konfigurace se vztahuje na všechna IoT Edge zařízení v **nižší vrstvě** hierarchie. Tato zařízení můžou být bránou pro jiná podřízená zařízení IoT a IoT Edge, ale také musí směrovat veškerou komunikaci prostřednictvím jejich vlastních nadřízených zařízení.

Některé síťové architektury vyžadují, aby se ke cloudu mohl připojit jenom nejvyšší IoT Edge zařízení v hierarchii. V této konfiguraci můžou všechna IoT Edge zařízení v nižších vrstvách hierarchie komunikovat jenom s jejich bránou (nebo nadřazeným) zařízením a všemi podřízenými (nebo podřízenými) zařízeními.

Všechny kroky v tomto článku jsou sestavené na těch v tématu [konfigurace IoT Edge zařízení, aby fungovalo jako transparentní brána](how-to-create-transparent-gateway.md), která nastaví IoT Edge zařízení jako bránu pro podřízená zařízení IoT. Stejné základní kroky platí pro všechny scénáře brány:

* **Ověřování**: Vytvořte IoT Hub identity pro všechna zařízení v hierarchii brány.
* **Autorizace**: Nastavte vztah nadřazenosti/podřízenosti v IoT Hub k autorizaci podřízených zařízení pro připojení k jejich nadřazenému zařízení, jako by se připojili k IoT Hub.
* **Zjišťování brány**: Ujistěte se, že podřízené zařízení může najít své nadřazené zařízení v místní síti.
* **Zabezpečené připojení**: navažte zabezpečené připojení k důvěryhodným certifikátům, které jsou součástí stejného řetězce.

## <a name="prerequisites"></a>Požadavky

* Centrum IoT úrovně Free nebo Standard.
* Aspoň dvě **IoT Edge zařízení**, jednu jako zařízení nejvyšší vrstvy a jedno nebo více zařízení nižší vrstvy. Pokud nemáte dostupná IoT Edgeá zařízení, můžete [Azure IoT Edge spustit na virtuálních počítačích s Ubuntu](how-to-install-iot-edge-ubuntuvm.md).
* Pokud k vytváření a správě zařízení používáte Azure CLI, použijte Azure CLI v 2.3.1 s nainstalovaným rozšířením Azure IoT v 0.10.6 nebo novějším.

Tento článek poskytuje podrobné postupy a možnosti, které vám pomůžou vytvořit správnou hierarchii brány pro váš scénář. Kurz průvodce najdete v tématu [vytvoření hierarchie IoT Edge zařízení pomocí bran](tutorial-nested-iot-edge.md).

## <a name="create-a-gateway-hierarchy"></a>Vytvoření hierarchie brány

Hierarchii služby IoT Edge Gateway vytvoříte tak, že definujete vztahy nadřazenosti/podřízenosti pro IoT Edge zařízení ve scénáři. Při vytváření nové identity zařízení můžete nastavit nadřazené zařízení nebo můžete spravovat nadřazenou a podřízenou položku stávající identity zařízení.

Krok nastavení vztahů nadřazenosti a podřízenosti opravňuje k tomu, aby se podřízená zařízení připojovala k jejich nadřazenému zařízení, jako by se připojila k IoT Hub.

Nadřízená zařízení můžou být jenom IoT Edge zařízení, ale zařízení IoT Edge a zařízení IoT můžou být podřízené. Nadřazený objekt může mít mnoho podřízených objektů, ale podřízený objekt může mít pouze jeden nadřazený objekt. Hierarchie brány se vytvoří zřetězením nadřazených a podřízených sad dohromady, aby podřízený uzel jednoho zařízení byl nadřazený jinému.

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V Azure Portal můžete spravovat vztah nadřazenosti/podřízenosti při vytváření nových identit zařízení nebo úpravou stávajících zařízení.

Když vytvoříte nové zařízení IoT Edge, máte možnost vybrat nadřazená a podřízená zařízení ze seznamu existujících zařízení IoT Edge v tomto centru.

1. V [Azure Portal](https://portal.azure.com)přejděte do svého centra IoT.
1. V navigační nabídce vyberte **IoT Edge** .
1. Vyberte **Přidat zařízení IoT Edge**.
1. Spolu s nastavením ID zařízení a nastavení ověřování můžete **nastavit nadřazené zařízení** nebo **zvolit podřízená zařízení**.
1. Vyberte zařízení, která chcete mít jako nadřazenou nebo podřízenou položku.

Můžete také vytvořit nebo spravovat vztahy nadřazenosti a podřízenosti pro existující zařízení.

1. V [Azure Portal](https://portal.azure.com)přejděte do svého centra IoT.
1. V navigační nabídce vyberte **IoT Edge** .
1. Ze seznamu **IoT Edge zařízení** vyberte zařízení, které chcete spravovat.
1. Vyberte možnost **nastavit nadřazené zařízení** nebo **Spravovat podřízená zařízení**.
1. Přidejte nebo odeberte všechna nadřazená nebo podřízená zařízení.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Rozšíření [Azure Azure](/cli/azure/ext/azure-iot) pro Azure CLI nabízí příkazy pro správu prostředků IoT. Pokud vytváříte nové identity zařízení nebo upravujete stávající zařízení, můžete spravovat vztah nadřazenosti a podřízenosti zařízení IoT a IoT Edge.

Pomocí příkazu [AZ IoT Hub Device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) Set of Commands můžete spravovat vztahy nadřazenosti a podřízenosti daného zařízení.

`create`Příkaz obsahuje parametry pro přidání podřízených zařízení a nastavení nadřazeného zařízení v době vytváření zařízení.

Další příkazy identity zařízení, včetně `add-children` , a `list-children` `remove-children` nebo `get-parent` a `set-parent` , umožňují správu vztahů nadřazenosti a podřízenosti pro existující zařízení.

---

## <a name="prepare-certificates"></a>Příprava certifikátů

Aby bylo možné navázat zabezpečenou komunikaci mezi sebou, musí být v rámci zařízení ve stejné hierarchii brány nainstalovány konzistentní řetězce certifikátů. Všechna zařízení v hierarchii bez ohledu na to, jestli IoT Edge zařízení nebo zařízení na list IoT, potřebují kopii stejného kořenového certifikátu certifikační autority. Každé zařízení IoT Edge v hierarchii potom používá tento certifikát kořenové certifikační autority jako kořenový adresář pro svůj certifikát certifikační autority zařízení.

V případě tohoto nastavení může IoT Edge každé zařízení s edgeHub nebo zařízením IoT list ověřit identitu svého nadřazeného serveru tím, že ověří, jestli se, ke kterým se připojují, certifikát serveru, který je podepsaný certifikátem sdílené kořenové certifikační autority.

<!-- TODO: certificate graphic -->

Vytvořte následující certifikáty:

* **Certifikát kořenové certifikační autority**, což je nejvyšší sdílený certifikát pro všechna zařízení v dané hierarchii brány. Tento certifikát je nainstalovaný na všech zařízeních.
* Všechny **zprostředkující certifikáty** , které chcete zahrnout do kořenového řetězu certifikátů.
* **Certifikát certifikační autority zařízení** a jeho **privátní klíč** vygenerovaný kořenovými a zprostředkujícími certifikáty. Pro každé zařízení IoT Edge v hierarchii brány potřebujete jeden jedinečný certifikát CA zařízení.

>[!NOTE]
>V současné době omezení libiothsm brání použití certifikátů, jejichž platnost vyprší, od 1. ledna 2038.

Můžete použít buď certifikační autoritu podepsané svým držitelem, nebo si ji koupit od důvěryhodné Komerční certifikační autority, jako je Baltimore, VeriSign, DigiCert nebo GlobalSign.

Pokud nemáte vlastní certifikáty, které byste měli použít, můžete [Vytvořit Ukázkové certifikáty pro testování IoT Edgech funkcí zařízení](how-to-create-test-certificates.md). Podle kroků v tomto článku vytvořte jednu sadu kořenových a zprostředkujících certifikátů a pak vytvořte certifikáty IoT Edge zařízení pro každé z vašich zařízení.

## <a name="configure-iot-edge-on-devices"></a>Konfigurace IoT Edge na zařízeních

Postup nastavení IoT Edge jako brány se velmi podobá postupům nastavení IoT Edge jako zařízení pro příjem dat.

Aby bylo možné povolit zjišťování bran, musí být každé zařízení IoT Edge brány nakonfigurované s **názvem hostitele** , který budou jeho podřízená zařízení používat k jeho vyhledání v místní síti. U každého zařízení IoT Edge pro příjem dat je potřeba nakonfigurovat **parent_hostname** , ke kterému se chcete připojit. Pokud je jedno zařízení IoT Edge nadřazené i podřízené zařízení, potřebuje oba parametry.

Aby bylo možné povolit zabezpečená připojení, musí být každé IoT Edge zařízení ve scénáři brány nakonfigurované s jedinečným certifikátem certifikační autority zařízení a kopií kořenového certifikátu certifikační autority sdílené všemi zařízeními v hierarchii brány.

V zařízení už byste měli mít nainstalované IoT Edge. Pokud ne, postupujte podle pokynů pro [registraci zařízení IoT Edge v IoT Hub](how-to-register-device.md) a pak [nainstalujte modul runtime Azure IoT Edge](how-to-install-iot-edge.md).

Kroky v této části odkazují na certifikát **kořenové certifikační autority** a **certifikát certifikační autority zařízení a soukromý klíč** , které byly popsány dříve v tomto článku. Pokud jste tyto certifikáty vytvořili na jiném zařízení, měli byste je mít na tomto zařízení k dispozici. Soubory můžete přenést fyzicky, například s jednotkou USB, pomocí služby, jako je například [Azure Key Vault](../key-vault/general/overview.md), nebo pomocí funkce jako [zabezpečené kopírování souborů](https://www.ssh.com/ssh/scp/).

Pomocí následujících kroků můžete na svém zařízení nakonfigurovat IoT Edge.

V systému Linux se ujistěte, že uživatel **iotedge** má oprávnění ke čtení pro adresář, který obsahuje certifikáty a klíče.

1. Nainstalujte **certifikát kořenové certifikační autority** do tohoto IoT Edge zařízení.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem
   ```

1. Aktualizujte úložiště certifikátů.

   ```bash
   sudo update-ca-certificates
   ```

   Tento příkaz by měl mít výstup, který do/etc/SSL/certs. přidal jeden certifikát.

1. Otevřete konfigurační soubor démona zabezpečení IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. V souboru config. yaml vyhledejte část **certifikáty** . Aktualizujte pole tří certifikátů tak, aby odkazovala na vaše certifikáty. Zadejte cesty k identifikátorům URI souboru, které mají formát `file:///<path>/<filename>` .

   * **device_ca_cert**: cesta k identifikátoru URI souboru pro certifikát certifikační autority zařízení je pro toto zařízení jedinečná.
   * **device_ca_pk**: cesta k identifikátoru URI souboru pro toto zařízení je pro privátní klíč certifikační autority zařízení jedinečná.
   * **trusted_ca_certs**: cesta k identifikátoru URI souboru pro kořenový certifikát certifikační autority, kterou sdílí všechna zařízení v hierarchii brány.

1. V souboru config. yaml vyhledejte parametr **hostname** . Aktualizujte název hostitele tak, aby byl plně kvalifikovaný název domény (FQDN) nebo IP adresa IoT Edgeho zařízení.

   Hodnota tohoto parametru je to, co budou podřízená zařízení používat k připojení k této bráně. Název hostitele ve výchozím nastavení používá název počítače, ale k připojení podřízených zařízení se vyžaduje plně kvalifikovaný název domény nebo IP adresa.

   Použijte název hostitele kratší než 64 znaků, což je limit znaků pro běžný název certifikátu serveru.

   Je konzistentní se vzorem názvu hostitele napříč hierarchií brány. Použijte buď plně kvalifikované názvy domény nebo IP adresy, ale ne obojí.

1. **Pokud je toto zařízení podřízené**, vyhledejte parametr **parent_hostname** . Aktualizujte pole **parent_hostname** tak, aby se jednalo o plně kvalifikovaný název domény nebo IP adresu nadřazeného zařízení, a to podle toho, co bylo zadáno jako název hostitele v souboru config. yaml nadřazeného objektu.

1. I když je tato funkce ve verzi Public Preview, musíte zařízení IoT Edge nakonfigurovat tak, aby při spuštění používala verzi Public Preview agenta IoT Edge.

   Vyhledejte část **Agent** YAML a aktualizujte hodnotu image na obrázek verze Public Preview:

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. Uložte ( `Ctrl+O` ) a zavřete ( `Ctrl+X` ) soubor config. yaml.

1. Pokud jste předtím používali jiné certifikáty pro IoT Edge, odstraňte soubory z následujících dvou adresářů, abyste se ujistili, že se nové certifikáty použijí:

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. Restartujte službu IoT Edge, aby se změny projevily.

   ```bash
   sudo systemctl restart iotedge
   ```

1. Vyhledejte všechny chyby v konfiguraci.

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >Nástroj pro kontrolu IoT Edge používá kontejner k provedení některých kontrol diagnostiky. Pokud chcete tento nástroj použít na zařízeních IoT Edge pro příjem dat, ujistěte se, že k němu mají přístup `mcr.microsoft.com/azureiotedge-diagnostics:latest` , nebo jestli má image kontejneru ve vašem privátním registru kontejneru.

## <a name="configure-runtime-modules-for-public-preview"></a>Konfigurace běhových modulů pro verzi Public Preview

I když je tato funkce ve verzi Public Preview, musíte zařízení IoT Edge nakonfigurovat tak, aby používala verze Public Preview modulů IoT Edge runtime. V předchozí části najdete postup konfigurace edgeAgent při spuštění. Také je nutné nakonfigurovat moduly modulu runtime v nasazeních pro vaše zařízení.

1. Nakonfigurujte modul edgeHub tak, aby používal image Public Preview: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2` .

1. Pro modul edgeHub nakonfigurujte následující proměnné prostředí:

   | Name | Hodnota |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Nakonfigurujte modul edgeAgent tak, aby používal image Public Preview: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2` .

## <a name="network-isolate-downstream-devices"></a>Síť s izolací pro příjem dat

Kroky v tomto článku nastavily IoT Edge zařízení jako bránu nebo jako zařízení pro příjem dat a mezi nimi vzdálení a mezi nimi můžete vytvořit důvěryhodné připojení. Zařízení brány zajišťuje interakce mezi zařízením a IoT Hub pro příjem dat, včetně ověřování a směrování zpráv. Moduly nasazené do zařízení IoT Edge pro příjem dat můžou pořád vytvářet vlastní připojení ke cloudovým službám.

Některé síťové architektury, jako jsou ty, které následují po standardu ISA-95, se snaží minimalizovat počet připojení k Internetu. V těchto scénářích můžete nakonfigurovat zařízení IoT Edge pro příjem dat bez přímého připojení k Internetu. Kromě směrování IoT Hub komunikace prostřednictvím jejich zařízení brány můžou zařízení pro příjem dat IoT Edge spoléhat na zařízení brány pro všechna cloudová připojení.

Tato konfigurace sítě vyžaduje, aby v horní vrstvě hierarchie brány byla přímá připojení ke cloudu jenom zařízení IoT Edge. Zařízení IoT Edge v nižších vrstvách můžou komunikovat jenom s jejich nadřazeným zařízením nebo všemi podřízenými zařízeními. Speciální moduly na zařízeních brány umožňují tento scénář, včetně:

* **Modul API proxy** je vyžadován na kterékoli bráně IoT Edge, která má jiné zařízení IoT Edge pod ní. To znamená, že se musí nacházet v *každé vrstvě* hierarchie brány s výjimkou spodní vrstvy. Tento modul používá reverzní proxy server [Nginx](https://nginx.org) ke směrování dat HTTP prostřednictvím síťových vrstev přes jeden port. Je možné ji snadno konfigurovat prostřednictvím svých modulů a proměnných prostředí, takže se dají upravit tak, aby vyhovovala vašim požadavkům na vaše brány.

* **Modul registru Docker** se dá nasadit na bránu IoT Edge v *horní vrstvě* hierarchie brány. Tento modul zodpovídá za načítání a ukládání imagí kontejneru do mezipaměti jménem všech zařízení IoT Edge v nižších vrstvách. Alternativou k nasazení tohoto modulu v horní vrstvě je použití místního registru nebo k ručnímu načtení imagí kontejneru do zařízení a nastavení zásad pro vyžádané čtení modulu na hodnotu **nikdy**.

* Službu **Azure Blob Storage on IoT Edge** lze nasadit na IoT Edge bránu v *horní vrstvě* hierarchie brány. Tento modul zodpovídá za nahrávání objektů BLOB jménem všech zařízení IoT Edge v nižších vrstvách. Možnost nahrávat objekty blob také umožňuje užitečnou funkci pro řešení potíží pro IoT Edge zařízení v nižších vrstvách, jako je nahrání protokolu modulu a podpora nahrávání sady prostředků.

### <a name="network-configuration"></a>Konfigurace sítě

Pro každé zařízení brány v horní vrstvě musí operátoři sítě:

* Zadejte statickou IP adresu nebo plně kvalifikovaný název domény (FQDN).
* Pomocí portů 443 (HTTPS) a 5671 (AMQP) autorizujete odchozí komunikaci z této IP adresy na název hostitele Azure IoT Hub.
* Udělte odchozí komunikaci z této IP adresy k vašemu Azure Container Registry názvu hostitele přes port 443 (HTTPS).

  Modul API proxy může současně zpracovávat pouze připojení k jednomu registru kontejneru. Doporučujeme, aby všechny image kontejneru, včetně veřejných imagí poskytovaných Microsoft Container Registry (mcr.microsoft.com), byly uloženy v privátním registru kontejneru.

Pro každé zařízení brány v nižší vrstvě musí operátoři sítě:

* Zadejte statickou IP adresu.
* Autorizovat odchozí komunikaci z této IP adresy na IP adresu nadřazené brány přes porty 443 (HTTPS) a 5671 (AMQP).

### <a name="deploy-modules-to-top-layer-devices"></a>Nasazení modulů na zařízení s nejvyšší vrstvou

Zařízení IoT Edge v horní vrstvě hierarchie brány obsahuje sadu požadovaných modulů, které se do nich musí nasadit, kromě libovolných modulů úloh, které můžete na zařízení spouštět.

Modul API proxy byl navržený tak, aby zpracovával nejběžnější scénáře brány. Tento článek popisuje a příklad nastavení modulů v základní konfiguraci. Podrobnější informace a příklady najdete [v tématu Konfigurace modulu API proxy pro scénář hierarchie brány](how-to-configure-api-proxy-module.md) .

1. V [Azure Portal](https://portal.azure.com)přejděte do svého centra IoT.
1. V navigační nabídce vyberte **IoT Edge** .
1. Vyberte zařízení nejvyšší vrstvy, které konfigurujete ze seznamu **IoT Edge zařízení**.
1. Vyberte **Set modules** (Nastavit moduly).
1. V části **IoT Edge moduly** vyberte **Přidat** a pak vyberte **modul Marketplace**.
1. Vyhledejte a vyberte modul **proxy rozhraní API IoT Edge** .
1. V seznamu nasazených modulů vyberte název modulu API proxy a aktualizujte následující nastavení modulu:
   1. Na kartě **proměnné prostředí** aktualizujte hodnotu **NGINX_DEFAULT_PORT** na `443` .
   1. Na kartě **možnosti vytvoření kontejneru** aktualizujte vazby portů na odkaz port 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Tyto změny konfigurují modul proxy rozhraní API tak, aby naslouchal na portu 443. Chcete-li zabránit kolizím vazeb portů, je nutné nakonfigurovat modul edgeHub tak, aby nenaslouchal na portu 443. Místo toho bude modul proxy serveru API směrovat jakýkoli edgeHub provoz na portu 443.

1. Vyberte **nastavení modulu runtime** a vyhledejte možnosti vytvořit modul edgeHub. Odstraňte vazbu portu pro port 443 a nechejte vazby pro porty 5671 a 8883.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Vyberte **Uložit** a uložte změny do nastavení modulu runtime.
1. Vyberte znovu **Přidat** a pak zvolte **IoT Edge modul**.
1. Zadejte následující hodnoty pro přidání modulu Docker registru do nasazení:
   1. **Název modulu IoT Edge**: `registry`
   1. Na kartě **nastavení modulu** , **identifikátor URI obrázku**: `registry:latest`
   1. Na kartě **proměnné prostředí** přidejte následující proměnné prostředí:

      * **Název**: `REGISTRY_PROXY_REMOTEURL` **hodnota**: adresa URL registru kontejneru, na který chcete mapovat tento modul registru. Například `https://myregistry.azurecr`.

        Modul registru lze mapovat pouze na jeden registr kontejneru, proto doporučujeme mít všechny image kontejneru v jednom privátním registru kontejneru.

      * **Název**: `REGISTRY_PROXY_USERNAME` **hodnota**: uživatelské jméno pro ověření v registru kontejnerů.

      * **Name**: `REGISTRY_PROXY_PASSWORD` **hodnota**: heslo pro ověření v registru kontejnerů.

   1. Na kartě **možnosti vytvoření kontejneru** vložte:

      ```json
      {
          "HostConfig": {
              "PortBindings": {
                  "5000/tcp": [
                      {
                          "HostPort": "5000"
                      }
                  ]
              }
          }
      }
      ```

1. Vyberte **Přidat** a přidejte modul do nasazení.
1. Vyberte **Další: trasy** , které chcete přejít k dalšímu kroku.
1. Pokud chcete povolit zprávy typu zařízení-Cloud ze zařízení se systémem IoT Hub, přidejte trasu, která všem zprávám projde IoT Hub. Například:
    1. **Název**: `Route`
    1. **Hodnota**: `FROM /messages/* INTO $upstream`
1. Výběrem možnosti **zkontrolovat + vytvořit** přejdete k poslednímu kroku.
1. Vyberte **vytvořit** a nasaďte ho do svého zařízení.

### <a name="deploy-modules-to-lower-layer-devices"></a>Nasazení modulů do nižších vrstev zařízení

IoT Edge zařízení v nižších vrstvách hierarchie brány mají jeden požadovaný modul, který musí být do nich nasazený, kromě libovolných modulů zatížení, které můžete na zařízení spouštět.

#### <a name="route-container-image-pulls"></a>Žádosti o přijetí imagí kontejneru trasy

Předtím, než se podíváte na požadovaný modul proxy pro zařízení IoT Edge v hierarchiích bran, je důležité pochopit, jak IoT Edge zařízení v nižších vrstvách získávají své image v modulech.

Pokud se vaše zařízení s nižší vrstvou nemůžou připojit ke cloudu, ale chcete, aby vyčetla image modulu obvyklým způsobem, musí být zařízení nejvyšší vrstvy hierarchie brány nakonfigurované tak, aby zpracovávala tyto požadavky. Zařízení nejvyšší vrstvy potřebuje spustit modul Docker **registru** , který je namapovaný na váš registr kontejneru. Pak nakonfigurujte modul proxy rozhraní API na směrování požadavků na kontejner. Tyto podrobnosti jsou popsány v předchozích částech tohoto článku. V této konfiguraci by zařízení nižší vrstvy neměla ukazovat na Registry kontejnerů cloudu, ale do registru běžícího v horní vrstvě.

Například namísto volání `mcr.microsoft.com/azureiotedge-api-proxy:latest` musí být na nižší vrstvě zavolána `$upstream:443/azureiotedge-api-proxy:latest` .

Parametr **$upstream** odkazuje na nadřazenou položku nižší vrstvy, takže požadavek bude směrovat přes všechny vrstvy, dokud nedosáhne nejvyšší vrstvy, která obsahuje požadavky kontejneru směrování na proxy prostředí do modulu registru. `:443`Port v tomto příkladu by měl být nahrazen libovolným portem, na kterém naslouchá modul API proxy na nadřazeném zařízení.

Modul proxy serveru rozhraní API může směrovat jenom na jeden modul registru a každý modul registru se dá mapovat jenom na jeden registr kontejneru. Proto musí být všechny bitové kopie, které zařízení nižší vrstvy potřebují, uloženy v jediném registru kontejneru.

Pokud nechcete, aby zařízení s nižší vrstvou prováděla žádosti o přijetí změn prostřednictvím hierarchie brány, je další možností spravovat místní řešení registru. Nebo nahrajte image modulu do zařízení před vytvořením nasazení a pak nastavte **imagePullPolicy** na **nikdy**.

#### <a name="bootstrap-the-iot-edge-agent"></a>Zavedení agenta IoT Edge

Agent IoT Edge je první komponenta modulu runtime, která se má spustit na jakémkoli IoT Edgem zařízení. Musíte zajistit, aby všechna IoT Edgeá zařízení, která při spuštění mají přístup k imagi modulu edgeAgent, a pak mohli získat přístup k nasazením a začít zbytek imagí modulu.

Když přejdete do souboru config. yaml na zařízení IoT Edge, abyste mohli poskytovat informace o jeho ověřování, certifikátech a nadřazeném názvu hostitele, aktualizujte také image kontejneru edgeAgent.

Pokud je zařízení brány nejvyšší úrovně nakonfigurované tak, aby zpracovávala požadavky na Image kontejneru, nahraďte `mcr.microsoft.com` nadřazeným názvem hostitele a portem naslouchání proxy serveru rozhraní API. V manifestu nasazení můžete použít `$upstream` jako zástupce, ale to vyžaduje, aby modul edgeHub zpracovávala směrování a tento modul v tomto okamžiku nezačal. Například:

```yml
agent:
  name: "edgeAgent"
  type: "docker"
  env: {}
  config:
    image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2.0-rc2"
    auth: {}
```

Pokud používáte místní registr kontejnerů nebo pokud chcete na zařízení ručně poskytnout image kontejneru, aktualizujte soubor config. yaml odpovídajícím způsobem.

#### <a name="configure-runtime-and-deploy-proxy-module"></a>Konfigurace modulu runtime a nasazení proxy

**Proxy modul rozhraní API** se vyžaduje pro směrování veškeré komunikace mezi cloudem a všemi navazujícími IoT Edge zařízeními. Zařízení IoT Edge v dolní vrstvě hierarchie bez IoT Edge zařízení pro příjem dat nepotřebuje tento modul.

Modul API proxy byl navržený tak, aby zpracovával nejběžnější scénáře brány. Tento článek se krátce dotýká kroků nastavení modulů v základní konfiguraci. Podrobnější informace a příklady najdete [v tématu Konfigurace modulu API proxy pro scénář hierarchie brány](how-to-configure-api-proxy-module.md) .

1. V [Azure Portal](https://portal.azure.com)přejděte do svého centra IoT.
1. V navigační nabídce vyberte **IoT Edge** .
1. V seznamu **IoT Edge zařízení** vyberte nižší vrstvu, kterou konfigurujete.
1. Vyberte **Set modules** (Nastavit moduly).
1. V části **IoT Edge moduly** vyberte **Přidat** a pak vyberte **modul Marketplace**.
1. Vyhledejte a vyberte modul **proxy rozhraní API IoT Edge** .
1. V seznamu nasazených modulů vyberte název modulu API proxy a aktualizujte následující nastavení modulu:
   1. Na kartě **nastavení modulu** aktualizujte hodnotu **identifikátoru URI bitové kopie**. Nahraďte `mcr.microsoft.com` za `$upstream:443` (Jak velká může být moje znalostní báze?).
   1. Na kartě **proměnné prostředí** aktualizujte hodnotu **NGINX_DEFAULT_PORT** na `443` .
   1. Na kartě **možnosti vytvoření kontejneru** aktualizujte vazby portů na odkaz port 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Tyto změny konfigurují modul proxy rozhraní API tak, aby naslouchal na portu 443. Chcete-li zabránit kolizím vazeb portů, je nutné nakonfigurovat modul edgeHub tak, aby nenaslouchal na portu 443. Místo toho bude modul proxy serveru API směrovat jakýkoli edgeHub provoz na portu 443.

1. Vyberte **nastavení modulu runtime**.
1. Aktualizujte nastavení modulu edgeHub:

   1. V poli **Obrázek** Nahraďte parametr `mcr.microsoft.com` `$upstream:443` .
   1. V poli **vytvořit možnosti** Odstraňte vazbu portu pro port 443 a nechejte vazby pro porty 5671 a 8883.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Aktualizujte nastavení modulu edgeAgent:
   1. V poli **Obrázek** Nahraďte parametr `mcr.microsoft.com` `$upstream:443` .

1. Vyberte **Uložit** a uložte změny do nastavení modulu runtime.
1. Vyberte **Další: trasy** , které chcete přejít k dalšímu kroku.
1. Pokud chcete povolit zprávy typu zařízení-Cloud ze zařízení, která budou mít IoT Hub, přidejte trasu, do které se budou všechny zprávy předávat `$upstream` . Parametr pro odesílání odkazuje na nadřazené zařízení v případě nižší vrstvy zařízení. Například:
    1. **Název**: `Route`
    1. **Hodnota**: `FROM /messages/* INTO $upstream`
1. Výběrem možnosti **zkontrolovat + vytvořit** přejdete k poslednímu kroku.
1. Vyberte **vytvořit** a nasaďte ho do svého zařízení.

## <a name="next-steps"></a>Další kroky

[Jak se dá zařízení IoT Edge využít jako brána](iot-edge-as-gateway.md)

[Konfigurace modulu API proxy pro scénář hierarchie brány](how-to-configure-api-proxy-module.md)