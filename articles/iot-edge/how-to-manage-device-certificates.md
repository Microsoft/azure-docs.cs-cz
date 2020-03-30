---
title: Správa certifikátů zařízení – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Vytvořte testovací certifikáty, nainstalujte je a spravujte je na zařízení Azure IoT Edge a připravte se na produkční nasazení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c18c3d560adb3c3cae54bda808ee5842c260fd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79539205"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Správa certifikátů na zařízení IoT Edge

Všechna zařízení IoT Edge používají certifikáty k vytvoření zabezpečených připojení mezi modulem runtime a libovolnými moduly spuštěnými v zařízení. Zařízení IoT Edge fungující jako brány používají stejné certifikáty pro připojení ke svým zařízením pro příjem dat.

## <a name="install-production-certificates"></a>Instalace produkčních certifikátů

Při první instalaci IoT Edge a zřízení zařízení je zařízení nastaveno s dočasnými certifikáty, takže můžete službu otestovat.
Platnost těchto dočasných certifikátů vyprší za 90 dní nebo je lze resetovat restartováním počítače.
Jakmile budete připraveni přesunout zařízení do produkčního scénáře nebo chcete vytvořit scénář brány, musíte zadat vlastní certifikáty.
Tento článek ukazuje kroky k instalaci certifikátů na vašich zařízeních IoT Edge.

Další informace o různých typech certifikátů a jejich rolích ve scénáři IoT Edge najdete v [tématu Informace o tom, jak Azure IoT Edge používá certifikáty](iot-edge-certs.md).

>[!NOTE]
>Termín "kořenová certifikační autorita" používaný v tomto článku odkazuje na nejvyšší orgán veřejného certifikátu řetězce certifikátů pro řešení IoT. Není nutné používat kořenový adresář certifikátu syndikované certifikační autority nebo kořencertifikační autority vaší organizace. V mnoha případech se ve skutečnosti jedná o zprostředkující veřejný certifikát certifikační autority.

### <a name="prerequisites"></a>Požadavky

* Zařízení IoT Edge se systémem [Windows](how-to-install-iot-edge-windows.md) nebo [Linuxem](how-to-install-iot-edge-linux.md).
* Mít certifikát kořenové certifikační autority (CA), podepsaný svým držitelem nebo zakoupený od důvěryhodné komerční certifikační autority, jako je Baltimore, Verisign, DigiCert nebo GlobalSign.

Pokud ještě nemáte kořenovou certifikační autoritu, ale chcete vyzkoušet funkce IoT Edge, které vyžadují produkční certifikáty (jako jsou scénáře brány), můžete [vytvořit ukázkové certifikáty pro testování funkcí zařízení IoT Edge](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Vytvořit výrobní certifikáty

K vytvoření následujících souborů byste měli použít vlastní certifikační autoritu:

* Kořenová CA
* Certifikát certifikační autority zařízení
* Soukromý klíč certifikační autority zařízení

V tomto článku to, co označujeme jako *kořenový certifikační úřad,* není nejvyšší certifikační autoritou pro organizaci. Je to nejvyšší certifikační autorita pro scénář IoT Edge, který modul centra IoT Edge, uživatelské moduly a všechna navazující zařízení používají k vytvoření vzájemné důvěryhodnosti.

Chcete-li zobrazit příklad těchto certifikátů, přečtěte si skripty, které vytvářejí ukázkové certifikáty v [části Správa testovacích certifikátů certifikační autority pro ukázky a kurzy](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

### <a name="install-certificates-on-the-device"></a>Instalace certifikátů do zařízení

Nainstalujte řetězec certifikátů na zařízení IoT Edge a nakonfigurujte runtime IoT Edge tak, aby odkazoval na nové certifikáty.

Pokud jste například použili ukázkové skripty k [vytvoření ukázkových certifikátů](how-to-create-test-certificates.md), zkopírujte do zařízení IoT-Edge následující soubory:

* Certifikát certifikační autority zařízení:`<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Soukromý klíč certifikační autority zařízení:`<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Kořenový certifikační autorita:`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Zkopírujte tři soubory certifikátu a klíče do zařízení IoT Edge.

   K přesunutí souborů certifikátů můžete použít službu, jako je [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) nebo funkce, jako je protokol [zabezpečenékopie.](https://www.ssh.com/ssh/scp/)  Pokud jste vygenerovali certifikáty na samotném zařízení IoT Edge, můžete tento krok přeskočit a použít cestu k pracovnímu adresáři.

1. Otevřete konfigurační soubor daemonu zabezpečení IoT Edge.

   * Windows:`C:\ProgramData\iotedge\config.yaml`
   * Linux:`/etc/iotedge/config.yaml`

1. Nastavte vlastnosti **certifikátu** v souboru config.yaml na úplnou cestu k certifikátu a klíčovým souborům na zařízení IoT Edge. Odeberte `#` znak před vlastnostmi certifikátu, abyste odkomentovali čtyři řádky. Ujistěte se, že **certifikáty:** řádek nemá žádné předchozí mezery a že vnořené položky jsou odsazeny dvěma mezerami. Například:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "c:\\<path>\\device-ca.cert.pem"
        device_ca_pk: "c:\\<path>\\device-ca.key.pem"
        trusted_ca_certs: "c:\\<path>\\root-ca.root.ca.cert.pem"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "<path>/device-ca.cert.pem"
        device_ca_pk: "<path>/device-ca.key.pem"
        trusted_ca_certs: "<path>/root-ca.root.ca.cert.pem"
      ```

1. Na linuxových zařízeních se ujistěte, že uživatel **iotedge** má oprávnění ke čtení adresáře, který obsahuje certifikáty.

1. Pokud jste dříve na zařízení použili jiné certifikáty pro IoT Edge, odstraňte soubory v následujících dvou adresářích před spuštěním nebo restartováním IoT Edge:

   * Windows: `C:\ProgramData\iotedge\hsm\certs` a`C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` a`/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>Přizpůsobení životnosti certifikátu

IoT Edge automaticky generuje certifikáty na zařízení v několika případech, včetně:

* Pokud při instalaci a zřizování IoT Edge neposkytnete vlastní produkční certifikáty, správce zabezpečení IoT Edge automaticky vygeneruje **certifikát certifikační autority zařízení**. Tento certifikát podepsaný svým držitelem je určen pouze pro scénáře vývoje a testování, nikoli pro produkční prostředí. Platnost tohoto certifikátu vyprší po 90 dnech.
* Správce zabezpečení IoT Edge také generuje **certifikát certifikační autority úlohy** podepsaný certifikátem certifikační autority zařízení

Další informace o funkci různých certifikátů na zařízení IoT Edge najdete v [tématu Informace o tom, jak Azure IoT Edge používá certifikáty](iot-edge-certs.md).

U těchto dvou automaticky generovaných certifikátů máte možnost nastavit příznak **auto_generated_ca_lifetime_days** v souboru config.yaml a nakonfigurovat počet dní životnosti certifikátů.

>[!NOTE]
>Existuje třetí automaticky generovaný certifikát, který vytvoří správce zabezpečení IoT Edge, **certifikát serveru centra IoT Edge**. Tento certifikát má vždy 90 dní, ale před vypršením platnosti se automaticky obnoví. Hodnota **auto_generated_ca_lifetime_days** nemá vliv na tento certifikát.

Chcete-li nakonfigurovat vypršení platnosti certifikátu na něco jiného než výchozí 90 dnů, přidejte hodnotu ve dnech do části **certifikáty** souboru config.yaml.

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

Pokud jste zadali certifikáty certifikační autority vlastního zařízení, tato hodnota se stále vztahuje na certifikát certifikační autority pracovního vytížení za předpokladu, že nastavená životnost je kratší než životnost certifikátu certifikační autority zařízení.

Po zadání příznaku v souboru config.yaml postupujte takto:

1. Odstraňte obsah `hsm` složky.

   Windows: `C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys` Linux:`/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

1. Restartujte službu IoT Edge.

   Windows:

   ```powershell
   Restart-Service iotedge
   ```

   Linux:

   ```bash
   sudo systemctl restart iotedge
   ```

1. Potvrďte nastavení životnosti.

   Windows:

   ```powershell
   iotedge check --verbose
   ```

   Linux:

   ```bash
   sudo iotedge check --verbose
   ```

   Zkontrolujte výstup **připravenosti výroby: kontrola certifikátů,** která uvádí počet dní do vypršení platnosti automaticky generovaných certifikátů certifikační autority zařízení.

## <a name="next-steps"></a>Další kroky

Instalace certifikátů na zařízení IoT Edge je nezbytnýkrok před nasazením vašeho řešení v produkčním prostředí. Přečtěte si další informace o tom, jak [se připravit na nasazení řešení IoT Edge v produkčním prostředí](production-checklist.md).
