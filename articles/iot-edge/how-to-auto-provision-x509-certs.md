---
title: Automatické zřizování zařízení s DPS pomocí certifikátů X. 509 – Azure IoT Edge | Microsoft Docs
description: Použití certifikátů X. 509 k otestování automatického zřizování zařízení pro Azure IoT Edge se službou Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: 44ea6546eb2099165071fd493ec8f890820c0688
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199838"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Vytvoření a zřízení zařízení IoT Edge pomocí certifikátů X. 509

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Pomocí [Azure IoT Hub Device Provisioning Service (DPS)](../iot-dps/index.yml)můžete automaticky zřizovat IoT Edge zařízení pomocí certifikátů X. 509. Pokud neznáte proces automatického zřizování, přečtěte si přehled [zřizování](../iot-dps/about-iot-dps.md#provisioning-process) a teprve potom pokračujte.

V tomto článku se dozvíte, jak vytvořit registraci služby Device Provisioning pomocí certifikátů X. 509 na zařízení IoT Edge pomocí následujících kroků:

* Generování certifikátů a klíčů.
* Vytvoří buď jednotlivou registraci zařízení, nebo registraci skupiny pro sadu zařízení.
* Nainstalujte modul runtime IoT Edge a zaregistrujte zařízení v IoT Hub.

Používání certifikátů X. 509 jako mechanismu ověřování je skvělým způsobem, jak škálovat produkční prostředí a zjednodušit zřizování zařízení. Obvykle jsou certifikáty X. 509 uspořádány v řetězu certifikátů důvěryhodnosti. Počínaje certifikátem podepsaným svým držitelem nebo důvěryhodným kořenovým certifikátem podepisuje každý certifikát v řetězu další nižší certifikát. Tento model vytvoří delegovaný řetěz vztahu důvěryhodnosti od kořenového certifikátu až po každý zprostředkující certifikát s konečným certifikátem "list" nainstalovaným na zařízení.

## <a name="prerequisites"></a>Požadavky

* Aktivní IoT Hub.
* Fyzické nebo virtuální zařízení, které se má IoT Edge zařízení.
* Je nainstalovaná nejnovější verze [Gitu](https://git-scm.com/download/) .
* Instance IoT Hub Device Provisioning Service v Azure propojená se službou IoT Hub.
  * Pokud nemáte instanci služby Device Provisioning, postupujte podle pokynů v [části nastavení IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).
  * Po spuštění služby Device Provisioning zkopírujte na stránce Přehled hodnotu **Rozsah ID** . Tuto hodnotu použijete při konfiguraci modulu runtime IoT Edge.

## <a name="generate-device-identity-certificates"></a>Generování certifikátů identit zařízení

Certifikát identity zařízení je listový certifikát, který se připojuje prostřednictvím řetězu certifikátů s nejvyšším certifikátem certifikační autority (CA) X. 509. Certifikát identity zařízení musí mít běžný název (CN) nastavený na ID zařízení, které má mít zařízení ve službě IoT Hub.

Certifikáty identit zařízení se používají jenom ke zřízení IoT Edge zařízení a ověřování zařízení s využitím Azure IoT Hub. Nepodepisují certifikáty, na rozdíl od certifikátů certifikační autority, které IoT Edge zařízení prezentuje modulům nebo listovým zařízením k ověření. Další informace najdete v tématu informace o [využití certifikátu Azure IoT Edge](iot-edge-certs.md).

Po vytvoření certifikátu identity zařízení byste měli mít dva soubory: soubor. cer nebo. pem, který obsahuje veřejnou část certifikátu, a soubor. cer nebo. pem s privátním klíčem certifikátu. Pokud plánujete použít registraci skupin v DPS, budete také potřebovat veřejnou část certifikátu zprostředkující nebo kořenové certifikační autority ve stejném řetězu certifikátů.

K nastavení automatického zřizování pomocí X. 509 potřebujete následující soubory:

* Certifikát identity zařízení a jeho certifikát privátního klíče. Certifikát identity zařízení se nahraje do DPS, pokud vytvoříte jednotlivou registraci. Privátní klíč se předává do modulu runtime IoT Edge.
* Úplný řetězový certifikát, který by měl mít alespoň identitu zařízení a zprostředkující certifikáty. Úplný certifikát řetězu se předává modulu runtime IoT Edge.
* Certifikát zprostředkující nebo kořenové certifikační autority z řetězu certifikátů, který je důvěryhodný. Tento certifikát se nahraje do DPS, pokud vytvoříte registraci skupiny.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
> [!NOTE]
> V současné době omezení libiothsm brání použití certifikátů, jejichž platnost vyprší, od 1. ledna 2038.

:::moniker-end

### <a name="use-test-certificates-optional"></a>Použití testovacích certifikátů (volitelné)

Pokud nemáte k dispozici certifikační autoritu pro vytváření nových certifikátů identit a chcete tento scénář vyzkoušet, Azure IoT Edge úložiště Git obsahuje skripty, které můžete použít k vygenerování testovacích certifikátů. Tyto certifikáty jsou navržené jenom pro vývojové testování a nesmí se používat v produkčním prostředí.

Chcete-li vytvořit testovací certifikáty, postupujte podle kroků v části [Vytvoření ukázkových certifikátů k otestování IoT Edgech funkcí zařízení](how-to-create-test-certificates.md). Chcete-li nastavit skripty generování certifikátů a vytvořit certifikát kořenové certifikační autority, postupujte podle těchto dvou požadovaných oddílů. Pak postupujte podle pokynů a vytvořte certifikát identity zařízení. Až budete hotovi, měli byste mít následující řetěz certifikátů a pár klíčů:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Tyto certifikáty budete potřebovat na zařízení IoT Edge. Pokud budete chtít použít jednotlivou registraci v DPS, nahrajete soubor. CERT. pem. Pokud se chystáte použít registraci skupin v DPS, budete také potřebovat certifikát zprostředkující nebo kořenové certifikační autority ve stejném řetězu certifikátů, který chcete odeslat. Pokud používáte ukázkové certifikáty, použijte `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` certifikát pro zápis skupiny.

## <a name="create-a-dps-individual-enrollment"></a>Vytvořte si jednotlivou registraci DPS

Pomocí vygenerovaných certifikátů a klíčů můžete vytvořit jednotlivou registraci v DPS pro jedno IoT Edge zařízení. Jednotlivé registrace přijímají veřejnou část certifikátu identity zařízení a shodují s certifikátem v zařízení.

Pokud chcete zřídit více IoT Edge zařízení, postupujte podle kroků v následující části a [vytvořte registraci skupiny DPS](#create-a-dps-group-enrollment).

Když vytvoříte registraci v DPS, budete mít možnost deklarovat **počáteční stav** dopředných zařízení. V případě zařízení můžete nastavit značky pro seskupení zařízení podle libovolné metriky, kterou potřebujete ve vašem řešení, jako je oblast, prostředí, umístění nebo typ zařízení. Tyto značky slouží k vytváření [automatických nasazení](how-to-deploy-at-scale.md).

Další informace o registraci ve službě Device Provisioning najdete v tématu [Správa registrace zařízení](../iot-dps/how-to-manage-enrollments.md).

   > [!TIP]
   > V Azure CLI můžete vytvořit [registraci](/cli/azure/ext/azure-iot/iot/dps/enrollment) nebo [skupinu registrace](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) a pomocí příznaku s **povoleným okrajem** určit, že zařízení nebo skupina zařízení je IoT Edge zařízení.

1. V [Azure Portal](https://portal.azure.com)přejděte do vaší instance IoT Hub Device Provisioning Service.

1. V části **Nastavení** vyberte **spravovat registrace**.

1. Vyberte **přidat jednotlivou registraci** a potom proveďte následující kroky, abyste nakonfigurovali registraci:  

   * **Mechanismus**: vyberte **X. 509**.

   * **Primární soubor certifikátu. pem nebo. cer**: Nahrajte veřejný soubor z certifikátu identity zařízení. Pokud jste použili skripty pro vygenerování testovacího certifikátu, vyberte následující soubor:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **ID zařízení IoT Hub**: Pokud chcete, zadejte ID zařízení. Pomocí ID zařízení můžete cílit na jednotlivá zařízení pro nasazení modulů. Pokud ID zařízení nezadáte, použije se běžný název (CN) v certifikátu X. 509.

   * **IoT Edge zařízení**: výběrem **hodnoty true** deklarujete, že registrace je určena pro IoT Edge zařízení.

   * **Vyberte centra IoT, ke kterým se má toto zařízení přiřadit**: zvolte propojené centrum IoT, ke kterému chcete zařízení připojit. Můžete zvolit více rozbočovačů a zařízení bude přiřazeno k jednomu z nich podle vybrané zásady přidělování.

   * **Počáteční stav vlákna zařízení**: přidejte hodnotu značky, která se má přidat do vlákna zařízení v případě, že chcete. Pomocí značek můžete cílit na skupiny zařízení pro automatické nasazení. Například:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Vyberte **Uložit**.

Teď, když pro toto zařízení existuje registrace, IoT Edge modul runtime může zařízení během instalace automaticky zřídit. Pokračujte k nastavení IoT Edge zařízení v části [Instalace modulu runtime IoT Edge](#install-the-iot-edge-runtime) .

## <a name="create-a-dps-group-enrollment"></a>Vytvoření registrace skupiny DPS

Pomocí vygenerovaných certifikátů a klíčů vytvořte registraci skupiny v DPS pro více IoT Edgech zařízení. Skupinové registrace používají certifikát zprostředkující nebo kořenové certifikační autority z řetězu certifikátů, který se používá k vygenerování individuálních certifikátů identit zařízení.

Pokud místo toho chcete zřídit jedno IoT Edge zařízení, postupujte podle kroků v předchozí části a [Vytvořte si jednotlivou registraci DPS](#create-a-dps-individual-enrollment).

Když vytvoříte registraci v DPS, budete mít možnost deklarovat **počáteční stav** dopředných zařízení. V případě zařízení můžete nastavit značky pro seskupení zařízení podle libovolné metriky, kterou potřebujete ve vašem řešení, jako je oblast, prostředí, umístění nebo typ zařízení. Tyto značky slouží k vytváření [automatických nasazení](how-to-deploy-at-scale.md).

### <a name="verify-your-root-certificate"></a>Ověření kořenového certifikátu

Při vytváření skupiny registrací máte možnost použít ověřený certifikát. Certifikát s DPS můžete ověřit tak, že prokážete, že máte vlastnictví kořenového certifikátu. Další informace najdete v tématu [postup pro kontrolu vlastnictví certifikátů certifikační autority X. 509](../iot-dps/how-to-verify-certificates.md).

1. V [Azure Portal](https://portal.azure.com)přejděte do vaší instance IoT Hub Device Provisioning Service.

1. V nabídce na levé straně vyberte **certifikáty** .

1. Vyberte **Přidat** a přidejte nový certifikát.

1. Zadejte popisný název certifikátu, vyhledejte soubor. cer nebo. pem, který představuje veřejnou část vašeho certifikátu X. 509.

   Pokud používáte ukázkové certifikáty, nahrajte `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` certifikát.

1. Vyberte **Uložit**.

1. Váš certifikát by měl být teď uvedený na stránce **certifikáty** . Vyberte ho a otevřete podrobnosti certifikátu.

1. Vyberte možnost **Generovat ověřovací kód** a potom zkopírujte generovaný kód.

1. Bez ohledu na to, zda jste si položili vlastní certifikát certifikační autority nebo používáte ukázkové certifikáty, můžete k ověření vlastnictví použít ověřovací nástroj, který je k dispozici v úložišti IoT Edge. Nástroj pro ověření používá certifikát certifikační autority k podepsání nového certifikátu, který má poskytnutý ověřovací kód jako název subjektu.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Na stránce s podrobnostmi o certifikátu v Azure Portal nahrajte nově vygenerovaný ověřovací certifikát.

1. Vyberte **Ověřit**.

### <a name="create-enrollment-group"></a>Vytvořit skupinu registrace

Další informace o registraci ve službě Device Provisioning najdete v tématu [Správa registrace zařízení](../iot-dps/how-to-manage-enrollments.md).

1. V [Azure Portal](https://portal.azure.com)přejděte do vaší instance IoT Hub Device Provisioning Service.

1. V části **Nastavení** vyberte **spravovat registrace**.

1. Vyberte **Přidat skupinu** registrací a potom proveďte následující kroky, abyste nakonfigurovali registraci:

   * **Název skupiny**: zadejte zapamatovatelné jméno pro zápis této skupiny.

   * **Typ ověření identity**: vyberte **certifikát**.

   * **IoT Edge zařízení**: vyberte **true**. V případě registrace skupiny musí být všechna zařízení IoT Edge nebo žádná z nich nemůžete.

   * **Typ certifikátu**: vyberte **certifikát certifikační** autority, pokud máte ověřený certifikát CA uložený ve službě DPS nebo **zprostředkující certifikát** , pokud chcete nahrát nový soubor jenom pro tento zápis.

   * **Primární certifikát**: Pokud jste v poslední části zvolili certifikát certifikační autority, zvolte svůj certifikát z rozevíracího seznamu. Pokud jste zvolili zprostředkující certifikát, nahrajte veřejný soubor z certifikátu certifikační autority v řetězu certifikátů, který se použil k vygenerování certifikátů identit zařízení.

   * **Vyberte centra IoT, ke kterým se má toto zařízení přiřadit**: zvolte propojené centrum IoT, ke kterému chcete zařízení připojit. Můžete zvolit více rozbočovačů a zařízení bude přiřazeno k jednomu z nich podle vybrané zásady přidělování.

   * **Počáteční stav vlákna zařízení**: přidejte hodnotu značky, která se má přidat do vlákna zařízení v případě, že chcete. Pomocí značek můžete cílit na skupiny zařízení pro automatické nasazení. Například:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Vyberte **Uložit**.

Teď, když pro toto zařízení existuje registrace, IoT Edge modul runtime může zařízení během instalace automaticky zřídit. Pokračujte k další části a nastavte zařízení IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instalace modulu runtime IoT Edge

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Jeho komponenty se spouštějí v kontejnerech a umožňují na zařízení nasadit další kontejnery, abyste mohli spustit kód na hraničních zařízeních.

Postupujte podle kroků v části [Instalace modulu runtime Azure IoT Edge](how-to-install-iot-edge.md)a pak se vraťte k tomuto článku a zřiďte zařízení.

Zřizování X. 509 s DPS se podporuje jenom v IoT Edge verze 1.0.9 nebo novější.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurace zařízení pomocí informací o zřizování

Po nainstalování modulu runtime do zařízení nakonfigurujte zařízení s informacemi, které používá pro připojení ke službě Device Provisioning a IoT Hub.

Připravte si následující informace:

* Hodnota **rozsahu ID** DPS. Tuto hodnotu můžete načíst ze stránky přehled vaší instance DPS v Azure Portal.
* Soubor řetězu certifikátů identity zařízení v zařízení.
* Soubor klíče identity zařízení na zařízení.
* Volitelné ID registrace. Pokud není zadaný, ID se načte ze společného názvu v certifikátu identity zařízení.

### <a name="linux-device"></a>Zařízení se systémem Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Otevřete konfigurační soubor na zařízení IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. V souboru vyhledejte část konfigurace zřizování. Odkomentujte řádky pro zřizování certifikátů DPS X. 509 a zajistěte, aby byly všechny další řádky pro zřizování zakomentovány.

   `provisioning:`Řádek by neměl mít žádné předchozí prázdné znaky a vnořené položky by měly být odsazeny dvěma mezerami.

   ```yml
   # DPS X.509 provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Aktualizujte hodnoty `scope_id` , `identity_cert` a `identity_pk` pomocí informací DPS a Device.

   Když přidáte certifikát X. 509 a informace o klíči do souboru config. yaml, cesty by se měly zadat jako identifikátory URI souborů. Například:

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. Volitelně můžete zadat `registration_id` pro zařízení. V opačném případě ponechte řádek Zakomentovat a zaregistrujte zařízení s názvem CN certifikátu identity.

1. Volitelně můžete pomocí `always_reprovision_on_startup` řádků nebo `dynamic_reprovisioning` nakonfigurovat chování při opětovném zřizování zařízení. Pokud je zařízení nastavené tak, aby se při spuštění znovu zřídilo, vždy se nejprve pokusí zřídit pomocí DPS a pak se vrátit k záložnímu zálohování, pokud se nezdaří. Pokud je zařízení nastavené tak, aby se dynamicky znovu zřídilo, IoT Edge se restartuje a znovu zřídí, pokud se zjistí událost opětovného zřízení. Další informace najdete v tématu [IoT Hub konceptů opětovného zřízení zařízení](../iot-dps/concepts-device-reprovision.md).

1. Soubor config. yaml uložte a zavřete.

1. Restartujte modul runtime IoT Edge, aby provedl všechny změny konfigurace, které jste v zařízení provedli.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Vytvořte konfigurační soubor pro vaše zařízení na základě souboru šablony, který je součástí instalace IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Otevřete konfigurační soubor na zařízení IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Vyhledejte oddíl **zřizování** souboru. Odkomentujte řádky pro zřizování DPS pomocí certifikátu X. 509 a zajistěte, aby byly všechny další řádky pro zřizování zakomentovány.

   ```toml
   # DPS provisioning with X.509 certificate
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "x509"
   # registration_id = "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"

   identity_cert = "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

1. Aktualizujte hodnoty `id_scope` , `identity_cert` a `identity_pk` pomocí informací DPS a Device.

   Hodnota certifikátu identity může být poskytnuta jako identifikátor URI souboru nebo se může dynamicky vydávat pomocí nástroje EST nebo místní certifikační autority. Odkomentovat pouze jeden řádek, a to podle formátu, který se rozhodnete použít.

   Hodnota privátního klíče identity může být poskytnuta jako identifikátor URI souboru nebo identifikátor URI PKCS # 11. Odkomentovat pouze jeden řádek, a to podle formátu, který se rozhodnete použít.

   Pokud používáte jakékoli identifikátory URI PKCS # 11, vyhledejte v konfiguračním souboru oddíl **PKCS # 11** a poskytněte informace o konfiguraci PKCS # 11.

1. Volitelně můžete zadat `registration_id` pro zařízení. V opačném případě ponechte řádek Zakomentovat a zaregistrujte zařízení s běžným názvem certifikátu identity.

1. Uložte soubor a zavřete ho.

1. Použijte změny konfigurace, které jste provedli v IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Zařízení s Windows

1. Otevřete okno PowerShellu v režimu správce. Při instalaci IoT Edge, ne pomocí PowerShellu (x86) nezapomeňte použít relaci AMD64 prostředí PowerShell.

1. Příkaz **Initialize-IoTEdge** nakonfiguruje IoT Edge modul runtime na vašem počítači. Příkaz se standardně zastavuje ručním zřizováním kontejnerů Windows, takže použijte `-DpsX509` příznak pro Automatické zřizování s ověřováním pomocí certifikátu X. 509.

   Nahraďte zástupné hodnoty pro `{scope_id}` , `{identity cert chain path}` a `{identity key path}` odpovídajícími hodnotami z instance DPS a cesty k souborům na vašem zařízení.

   `-RegistrationId {registration_id}`Pokud chcete ID zařízení nastavit jako jinou hodnotu než název cn certifikátu identity, přidejte ho.

   Pokud používáte `-ContainerOs Linux` kontejnery Linux ve Windows, přidejte parametr.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >Konfigurační soubor uloží certifikát a informace o klíči jako identifikátory URI souboru. Příkaz Initialize-IoTEdge ale tento krok formátování zpracovává za vás, takže můžete zadat absolutní cestu k certifikátu a souborům klíčů na zařízení.

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud se modul runtime úspěšně spustil, můžete přejít do svého IoT Hub a začít nasazovat IoT Edge moduly do svého zařízení.

Můžete ověřit, že se použil jednotlivý zápis, který jste vytvořili v rámci služby Device Provisioning. V Azure Portal přejděte na instanci služby Device Provisioning. Otevřete podrobnosti registrace pro jednotlivou registraci, kterou jste vytvořili. Všimněte si, že je **přiřazený** stav registrace a že je uvedené ID zařízení.

Pomocí následujících příkazů na zařízení ověřte, že modul runtime byl úspěšně nainstalován a spuštěn.

### <a name="linux-device"></a>Zařízení se systémem Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Zkontrolujte stav služby IoT Edge.

```cmd/sh
systemctl status iotedge
```

Projděte si protokoly služby.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Vypíše spuštěné moduly.

```cmd/sh
iotedge list
```
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Zkontrolujte stav služby IoT Edge.

```cmd/sh
sudo iotedge system status
```

Projděte si protokoly služby.

```cmd/sh
sudo iotedge system logs
```

Vypíše spuštěné moduly.

```cmd/sh
sudo iotedge list
```
:::moniker-end

### <a name="windows-device"></a>Zařízení s Windows

Zkontrolujte stav služby IoT Edge.

```powershell
Get-Service iotedge
```

Projděte si protokoly služby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Vypíše spuštěné moduly.

```powershell
iotedge list
```

## <a name="next-steps"></a>Další kroky

Proces registrace služby Device Provisioning umožňuje nastavit ID zařízení a nedokončené značky zařízení současně, když zřizujete nové zařízení. Tyto hodnoty můžete použít k zaměření jednotlivých zařízení nebo skupin zařízení pomocí automatické správy zařízení. Naučte se, jak [nasadit a monitorovat IoT Edge moduly ve velkém měřítku pomocí Azure Portal](how-to-deploy-at-scale.md) nebo [pomocí Azure CLI](how-to-deploy-cli-at-scale.md).
