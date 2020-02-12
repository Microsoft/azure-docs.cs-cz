---
title: Instalace certifikátů na zařízení – Azure IoT Edge | Microsoft Docs
description: Vytvořte testovací certifikáty a Naučte se, jak je nainstalovat na zařízení Azure IoT Edge pro přípravu na produkční nasazení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fe46e968aa2dcebaa483cd38fd2e050ccfe43054
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149894"
---
# <a name="install-production-certificates-on-an-iot-edge-device"></a>Instalace produkčních certifikátů na zařízení IoT Edge

Všechna IoT Edge zařízení používají certifikáty k vytváření zabezpečených připojení mezi modulem runtime a všemi moduly běžícími na zařízení.
IoT Edge zařízení fungující jako brány používají stejné certifikáty pro připojení k jejich navazujícím zařízením.

Když poprvé nainstalujete IoT Edge a zřídíte zařízení, zařízení se nastaví s dočasnými certifikáty, abyste mohli službu otestovat.
Platnost těchto dočasných certifikátů vyprší za 90 dní nebo se dá resetovat restartováním počítače.
Jakmile budete připraveni přesunout vaše zařízení do produkčního scénáře nebo chcete vytvořit scénář brány, musíte zadat vlastní certifikáty.
Tento článek popisuje kroky pro instalaci certifikátů do zařízení IoT Edge.

Další informace o různých typech certifikátů a jejich rolích ve IoT Edge scénáři najdete v tématu [Vysvětlení způsobu, jakým Azure IoT Edge používá certifikáty](iot-edge-certs.md).

>[!NOTE]
>Pojem "Kořenová CA", který se používá v celém tomto článku, odkazuje na veřejný certifikát certifikační autority pro vaše řešení IoT. Nemusíte používat kořen certifikátu pro neoprávněnou certifikační autoritu nebo kořen certifikační autority vaší organizace. V mnoha případech je ve skutečnosti veřejný certifikát zprostředkující certifikační autority.

## <a name="prerequisites"></a>Předpoklady

* Zařízení IoT Edge spuštěné v [systému Windows](how-to-install-iot-edge-windows.md) nebo [Linux](how-to-install-iot-edge-linux.md).
* Mít certifikát kořenové certifikační autority (CA), buď podepsaný svým držitelem, nebo zakoupený od důvěryhodné Komerční certifikační autority, jako je Baltimore, VeriSign, DigiCert nebo GlobalSign.

Pokud zatím nemáte kořenovou certifikační autoritu, ale chcete si vyzkoušet IoT Edge funkce, které vyžadují provozní certifikáty (například scénáře bran), můžete [Vytvořit Ukázkové certifikáty pro testování IoT Edge funkcí zařízení](how-to-create-test-certificates.md).

## <a name="create-production-certificates"></a>Vytvoření produkčních certifikátů

Pro vytvoření následujících souborů byste měli použít vlastní certifikační autoritu:

* Kořenová CA
* Certifikát certifikační Autority zařízení
* Privátní klíč certifikační autority zařízení

V tomto článku, na který odkazujeme jako na *kořenovou certifikační autoritu* , není pro organizaci nejvyšší certifikační autorita. Je to nejvyšší certifikační autorita pro IoT Edge scénář, kterou modul IoT Edge hub, uživatelské moduly a jakákoli podřízená zařízení používají k navázání vztahu důvěryhodnosti mezi sebou.

Pokud chcete zobrazit příklad těchto certifikátů, přečtěte si téma Vytvoření ukázkových certifikátů v tématu [Správa certifikátů testovací CA pro ukázky a kurzy](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

## <a name="install-certificates-on-the-device"></a>Instalace certifikátů na zařízení

Nainstalujte svůj řetěz certifikátů na zařízení IoT Edge a nakonfigurujte modul runtime IoT Edge tak, aby odkazoval na nové certifikáty.

Pokud jste například použili ukázkové skripty k [Vytvoření ukázkových certifikátů](how-to-create-test-certificates.md), jsou tři soubory, které potřebujete zkopírovat do zařízení IoT Edge, následující:

* Certifikát certifikační autority zařízení: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Privátní klíč certifikační autority zařízení: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Kořenová certifikační autorita: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Zkopírujte tři certifikáty a soubory klíčů do zařízení IoT Edge.

   K přesunutí souborů certifikátů můžete použít službu, jako je [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) , nebo funkci, jako je [protokol Secure Copy](https://www.ssh.com/ssh/scp/) .  Pokud jste certifikáty vygenerovali na samotném IoT Edge zařízení, můžete tento krok přeskočit a použít cestu k pracovnímu adresáři.

2. Otevřete konfigurační soubor démon zabezpečení IoT Edge.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Nastavte vlastnosti **certifikátu** v souboru config. yaml na soubor s identifikátorem URI certifikátu a souborů klíčů na zařízení IoT Edge. Odeberte znak `#` před tím, než vlastnosti certifikátu Odkomentujte čtyři řádky. Ujistěte se, že **certifikáty:** řádek neobsahuje žádné předchozí prázdné znaky a že vnořené položky jsou odsazeny o dva mezery. Například:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///c:/path/device-ca.cert.pem"
        device_ca_pk: "file:///c:/path/device-ca.key.pem"
        trusted_ca_certs: "file:///c:/path/root-ca.root.ca.cert.pem"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "file:///path/device-ca.cert.pem"
        device_ca_pk: "file:///path/device-ca.key.pem"
        trusted_ca_certs: "file:///path/root-ca.root.ca.cert.pem"
      ```

4. V zařízeních se systémem Linux se ujistěte, že uživatel **iotedge** má oprávnění ke čtení pro adresář, který obsahuje certifikáty.

5. Pokud jste na zařízení používali jiné certifikáty pro IoT Edge, před spuštěním nebo restartováním IoT Edge odstraňte soubory z následujících dvou adresářů:

   * Windows: `C:\ProgramData\iotedge\hsm\certs` a `C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` a `/var/lib/iotedge/hsm/cert_keys`

## <a name="next-steps"></a>Další kroky

Instalace certifikátů na zařízení IoT Edge je nutný krok před nasazením řešení v produkčním prostředí. Přečtěte si další informace o tom, jak [připravit nasazení IoT Edge řešení v produkčním prostředí](production-checklist.md).
