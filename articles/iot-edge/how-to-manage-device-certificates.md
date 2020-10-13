---
title: Správa certifikátů zařízení – Azure IoT Edge | Microsoft Docs
description: Vytvářejte testovací certifikáty, nainstalujte je a spravujte je na zařízení Azure IoT Edge, abyste se připravili na produkční nasazení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4159b464493a34e17a04f17540b3f9c7a20f4740
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971791"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Správa certifikátů na zařízení IoT Edge

Všechna zařízení IoT Edge k vytváření zabezpečených připojení mezi modulem runtime a všemi moduly spuštěnými na zařízení využívají certifikáty. IoT Edge zařízení fungující jako brány používají stejné certifikáty pro připojení k jejich navazujícím zařízením.

## <a name="install-production-certificates"></a>Instalace produkčních certifikátů

Když poprvé nainstalujete IoT Edge a zřídíte zařízení, zařízení se nastaví s dočasnými certifikáty, abyste mohli službu otestovat.
Platnost těchto dočasných certifikátů vyprší za 90 dní nebo se dá resetovat restartováním počítače.
Až přejdete do produkčního scénáře nebo chcete vytvořit zařízení brány, musíte zadat vlastní certifikáty.
Tento článek popisuje kroky pro instalaci certifikátů do zařízení IoT Edge.

Další informace o různých typech certifikátů a jejich rolích najdete v tématu [Vysvětlení způsobu, jakým Azure IoT Edge používá certifikáty](iot-edge-certs.md).

>[!NOTE]
>Pojem "Kořenová CA", který se používá v celém tomto článku, odkazuje na veřejný certifikát certifikační autority pro vaše řešení IoT. Nemusíte používat kořen certifikátu pro neoprávněnou certifikační autoritu nebo kořen certifikační autority vaší organizace. V mnoha případech je ve skutečnosti veřejný certifikát zprostředkující certifikační autority.

### <a name="prerequisites"></a>Předpoklady

* Zařízení IoT Edge.

  Pokud nemáte nastavené zařízení IoT Edge, můžete ho vytvořit na virtuálním počítači Azure. Použijte postup v jednom z článků rychlý Start k [Vytvoření virtuálního zařízení](quickstart-linux.md) se systémem Linux nebo [Vytvoření virtuálního zařízení s Windows](quickstart.md).

* Mít certifikát kořenové certifikační autority (CA), buď podepsaný svým držitelem, nebo zakoupený od důvěryhodné Komerční certifikační autority, jako je Baltimore, VeriSign, DigiCert nebo GlobalSign.

  Pokud zatím nemáte kořenovou certifikační autoritu, ale chcete si vyzkoušet IoT Edge funkce, které vyžadují provozní certifikáty (například scénáře bran), můžete [Vytvořit Ukázkové certifikáty pro testování IoT Edge funkcí zařízení](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Vytvoření produkčních certifikátů

Pro vytvoření následujících souborů byste měli použít vlastní certifikační autoritu:

* Kořenová CA
* Certifikát certifikační autority zařízení
* Privátní klíč certifikační autority zařízení

V tomto článku, na který odkazujeme jako na *kořenovou certifikační autoritu* , není pro organizaci nejvyšší certifikační autorita. Je to nejvyšší certifikační autorita pro IoT Edge scénář, kterou modul IoT Edge hub, uživatelské moduly a jakákoli podřízená zařízení používají k navázání vztahu důvěryhodnosti mezi sebou.

> [!NOTE]
> V současné době omezení libiothsm brání použití certifikátů, jejichž platnost vyprší, od 1. ledna 2038.

Pokud chcete zobrazit příklad těchto certifikátů, přečtěte si téma Vytvoření ukázkových certifikátů v tématu [Správa certifikátů testovací CA pro ukázky a kurzy](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

### <a name="install-certificates-on-the-device"></a>Instalace certifikátů na zařízení

Nainstalujte svůj řetěz certifikátů na zařízení IoT Edge a nakonfigurujte modul runtime IoT Edge tak, aby odkazoval na nové certifikáty.

Pokud jste například použili ukázkové skripty k [Vytvoření ukázkových certifikátů](how-to-create-test-certificates.md), zkopírujte do zařízení IoT-Edge následující soubory:

* Certifikát certifikační autority zařízení: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Privátní klíč certifikační autority zařízení: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Kořenová certifikační autorita: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Zkopírujte tři certifikáty a soubory klíčů do zařízení IoT Edge.

   K přesunutí souborů certifikátů můžete použít službu, jako je [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) , nebo funkci, jako je [protokol Secure Copy](https://www.ssh.com/ssh/scp/) .  Pokud jste certifikáty vygenerovali na samotném IoT Edge zařízení, můžete tento krok přeskočit a použít cestu k pracovnímu adresáři.

1. Otevřete konfigurační soubor démona zabezpečení IoT Edge.

   * Systému `C:\ProgramData\iotedge\config.yaml`
   * Linux `/etc/iotedge/config.yaml`

1. V souboru config. yaml nastavte vlastnosti **certifikátu** na cestu k identifikátoru URI souboru certifikátu a souborů klíčů na zařízení IoT Edge. Odeberte `#` znak předtím, než vlastnosti certifikátu Odkomentujte čtyři řádky. Ujistěte se, že **certifikáty:** řádek neobsahuje žádné předchozí prázdné znaky a že vnořené položky jsou odsazeny o dva mezery. Například:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///C:/<path>/<device CA cert>"
        device_ca_pk: "file:///C:/<path>/<device CA key>"
        trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "file:///<path>/<device CA cert>"
        device_ca_pk: "file:///<path>/<device CA key>"
        trusted_ca_certs: "file:///<path>/<root CA cert>"
      ```

1. V zařízeních se systémem Linux se ujistěte, že uživatel **iotedge** má oprávnění ke čtení pro adresář, který obsahuje certifikáty.

1. Pokud jste na zařízení používali jiné certifikáty pro IoT Edge, před spuštěním nebo restartováním IoT Edge odstraňte soubory z následujících dvou adresářů:

   * Windows: `C:\ProgramData\iotedge\hsm\certs` a `C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` a `/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>Přizpůsobení životnosti certifikátu

IoT Edge automaticky vygeneruje certifikáty v zařízení v několika případech, včetně:

* Pokud při instalaci a zřízení IoT Edge neposkytnete vlastní provozní certifikáty, IoT Edge Security Manager automaticky vygeneruje **certifikát certifikační autority zařízení**. Tento certifikát podepsaný svým držitelem je určen pouze pro scénáře vývoje a testování, nikoli pro produkční prostředí. Platnost tohoto certifikátu vyprší po 90 dnech.
* Správce zabezpečení IoT Edge taky vygeneruje **certifikát certifikační autority pro úlohy** , který je podepsaný certifikátem certifikační autority zařízení.

Další informace o funkci různých certifikátů na zařízení IoT Edge najdete v tématu [Vysvětlení způsobu, jakým Azure IoT Edge používá certifikáty](iot-edge-certs.md).

Pro tyto dva automaticky vygenerované certifikáty máte možnost nastavit příznak **auto_generated_ca_lifetime_days** v souboru config. yaml a nakonfigurovat tak počet dní pro dobu života certifikátů.

>[!NOTE]
>K dispozici je třetí automaticky generovaný certifikát, který IoT Edge Security Manager vytvoří, **certifikát serveru IoT Edge hub**. Tento certifikát má vždy dobu 90 dne, ale před vypršením platnosti se automaticky obnoví. Hodnota **auto_generated_ca_lifetime_days** nemá vliv na tento certifikát.

Chcete-li nakonfigurovat vypršení platnosti certifikátu na jinou hodnotu než výchozí 90 dní, přidejte hodnotu ve dnech do části **certifikáty** v souboru **config. yaml** .

Po vypršení platnosti po zadaném počtu dnů se musí restartovat démon zabezpečení IoT Edge, aby se znovu vygeneroval certifikát certifikační autority zařízení. nebude se automaticky obnovovat.

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

> [!NOTE]
> V současné době omezení libiothsm brání použití certifikátů, jejichž platnost vyprší, od 1. ledna 2038.

Po zadání hodnoty v souboru config. yaml proveďte následující kroky:

1. Odstraňte obsah `hsm` složky.

   Windows: `C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys` Linux: `/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

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

   Podívejte se na výstup kontroly **připravenosti na provoz:** vypíše počet dní, než vyprší platnost automaticky generovaných certifikátů certifikační autority zařízení.

## <a name="next-steps"></a>Další kroky

Instalace certifikátů na zařízení IoT Edge je nutný krok před nasazením řešení v produkčním prostředí. Přečtěte si další informace o tom, jak [připravit nasazení IoT Edge řešení v produkčním prostředí](production-checklist.md).
