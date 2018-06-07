---
title: Vytvoření zařízení transparentní brány s hranou Azure IoT | Microsoft Docs
description: Použít k vytvoření zařízení transparentní brány, který dokáže zpracovat informace pro více zařízení Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/04/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fa4a2f6757a2a9dd114ea332b409759b0d4f4896
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630642"
---
# <a name="create-an-iot-edge-device-that-acts-as-a-transparent-gateway---preview"></a>Vytvoření IoT hraničním zařízením, která funguje jako brána transparentní – náhled

Tento článek obsahuje podrobné pokyny pro používání IoT hraniční zařízení jako brána transparentní. Pro zbývající část tohoto článku termín *IoT hraniční brána* odkazuje na IoT hraniční zařízení používá jako brána transparentní. Podrobnější informace najdete v části [jak IoT hraniční zařízení lze použít jako brána][lnk-edge-as-gateway], což dává koncepční přehled. 

>[!NOTE]
>Aktuálně:
> * Pokud brána je odpojená ze služby IoT Hub, podřízené zařízení nemůže ověřit pomocí brány.
> * IoT hraniční zařízení nemůže připojit k IoT hraniční brány.

## <a name="understand-the-azure-iot-device-sdk"></a>Pochopení zařízení Azure IoT SDK


Hraniční rozbočovače, které jsou nainstalovány ve všech IoT hraniční zařízení zveřejňuje následující primitivních elementů do podřízené zařízení:

* zprávy typu zařízení cloud a cloud zařízení
* přímé metody
* operace twin zařízení

Podřízené zařízení jsou v současné době není moci používat nahrávání souborů při připojení prostřednictvím IoT vstupní brána.

Při připojení zařízení k IoT vstupní brána pomocí zařízení Azure IoT SDK, budete muset:

* Nastavit podřízené zařízení s připojovacím řetězcem odkazující na název hostitele brány zařízení; a
* Ujistěte se, že podřízené zařízení důvěřuje certifikátu použít tak, aby přijímal připojení pomocí zařízení brány.

Při instalaci modulu runtime Azure IoT Edge použijte skript ovládacího prvku certifikát je vytvořen Edge rozbočovače, jako jste to udělali v tomto kurzu [nainstalovat IoT Edge v simulovaném zařízení v systému Windows] [ lnk-tutorial1-win] a [Linux][lnk-tutorial1-lin]. Tento certifikát je používaný rozbočovačem Edge tak, aby přijímal příchozí připojení protokolu TLS a musí být důvěryhodný podřízené zařízení při připojení k zařízení brány.

Můžete vytvořit všechny infrastrukturu certifikátů, která umožňuje důvěryhodnosti vyžadované pro topologii zařízení brány. V tomto článku předpokládáme nastavení stejný certifikát, který byste použili k povolení [zabezpečení certifikační Autority X.509] [ lnk-iothub-x509] IoT hub, která zahrnuje certifikát certifikační Autority X.509, který je přidružený ke konkrétní Centrum IoT ( *IoT hub vlastníka certifikační Autority*) a řadu certifikáty podepsané s touto certifikační Autoritou, nainstalovaný v zařízení IoT okraj.

>[!IMPORTANT]
>V současné době IoT hraniční zařízení a podřízené zařízení lze použít pouze [tokeny SAS] [ lnk-iothub-tokens] k ověření službou IoT Hub. Certifikáty slouží pouze k ověření připojení TLS mezi zařízením listu a brány.

Naše konfigurace používá **IoT hub vlastníka certifikační Autority** jako obě:
* Podpisový certifikát pro instalaci modulu runtime IoT Edge na všechna zařízení IoT Edge; a
* certifikát veřejného klíče nainstalované v podřízené zařízení.

Výsledkem je řešení, které umožňuje všem zařízením používat zařízení IoT okraj jako brána, tak dlouho, dokud jsou připojeny ke stejné IoT hub.

## <a name="create-the-certificates-for-test-scenarios"></a>Vytvoření certifikáty pro scénáře testování

Můžete použít ukázkové prostředí Powershell a skriptů Bash popsané v [Správa ukázka certifikát certifikační Autority] [ lnk-ca-scripts] vygenerovat certifikát podepsaný svým držitelem **IoT hub vlastníka certifikační Autority** a certifikáty zařízení podepsané s ním.

>[!IMPORTANT]
>Tato ukázka je určená jenom pro účely testování. Produkčních scénářích najdete v části [zabezpečit vaše nasazení IoT] [ lnk-iothub-secure-deployment] Azure IoT pokyny o tom, jak zabezpečit řešení IoT a odpovídajícím způsobem zřídit svůj certifikát.


1. Klonování SDK služby Microsoft Azure IoT a knihovny pro C z Githubu:

   ```cmd/sh
   git clone -b modules-preview https://github.com/Azure/azure-iot-sdk-c.git 
   ```

2. Pokud chcete nainstalovat certifikát skripty, postupujte podle pokynů v **krok 1 – počáteční nastavení** z [Správa ukázka certifikát certifikační Autority][lnk-ca-scripts]. 
3. Ke generování **IoT hub vlastníka certifikační Autority**, postupujte podle pokynů v **krok 2 – vytvoření řetězu certifikátů**. Tento soubor se používá podřízené zařízení k ověření připojení.
4. Pro vytvoření certifikátu pro vaše zařízení brány, použijte pokyny Bash nebo prostředí PowerShell:

### <a name="bash"></a>Bash

Vytvořte nový certifikát zařízení.  **NECHCETE** název `myGatewayCAName` být stejný jako název hostitele brány.  To způsobí, že certifikát klienta proti selhání těchto certifikátů.

   ```bash
   ./certGen.sh create_edge_device_certificate myGatewayCAName
   ```

Se vytváří nové soubory:.\certs\new-edge-device.* obsahuje veřejný klíč a PFX a.\private\new-edge-device.key.pem obsahuje privátní klíč zařízení.
 
V `certs` adresáře, spusťte následující příkaz získat úplný řetěz veřejný klíč zařízení:

   ```bash
   cd ./certs
   cat ./new-edge-device.cert.pem ./azure-iot-test-only.intermediate.cert.pem ./azure-iot-test-only.root.ca.cert.pem > ./new-edge-device-full-chain.cert.pem
   ```

### <a name="powershell"></a>PowerShell

Vytvořte nový certifikát zařízení: 
   ```powershell
   New-CACertsEdgeDevice myGateway
   ```

Vytváří se nové myEdgeDevice * soubory, které obsahují veřejný klíč, privátní klíč a PFX tohoto certifikátu. 

Po zobrazení výzvy k zadání hesla během procesu podepisování, zadejte "1234".

## <a name="configure-a-gateway-device"></a>Nakonfigurujte zařízení brány

Chcete-li nakonfigurovat zařízení IoT okraj jako brána stačí nakonfigurovat na použití certifikátu zařízení vytvořené v předchozí části.

Předpokládáme následující názvy souborů z výše uvedené ukázkové skripty:

| Výstup | Název souboru |
| ------ | --------- |
| Certifikát pro zařízení | `certs/new-edge-device.cert.pem` |
| Privátní klíč zařízení | `private/new-edge-device.cert.pem` |
| Zařízení řetěz certifikátů | `certs/new-edge-device-full-chain.cert.pem` |
| IoT hub vlastníka certifikační Autority | `certs/azure-iot-test-only.root.ca.cert.pem`  |

Zadejte informace o zařízení a certifikátu do hraniční IoT runtime. 
 
V systému Linux pomocí Bash výstup:

   ```bash
   sudo iotedgectl setup --connection-string {device connection string} \
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com} \
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem \
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem \
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem \
        --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem
   ```

V systému Windows pomocí prostředí PowerShell výstup:

   ```powershell
   iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/RootCA.pem
   ```

Ve výchozím nastavení ukázkové skripty nenastavujte přístupové heslo soukromého klíče zařízení. Pokud jste nastavili heslo, přidejte následující parametr: `--device-ca-passphrase {passphrase}`.

Skript vyzve k nastavit heslo pro certifikát agenta okraj. Modul runtime IoT Edge restartujte po tento příkaz:

   ```cmd/sh
   iotedgectl restart
   ```

## <a name="configure-a-downstream-device"></a>Konfigurovat podřízené zařízení

Podřízené zařízení může být z jakékoliv aplikace pomocí [zařízení Azure IoT SDK][lnk-devicesdk], tak jak je popsáno jednoduché jeden v [připojení zařízení do služby IoT hub pomocí rozhraní .NET] [ lnk-iothub-getstarted].

První, má aplikace podřízené zařízení tak, aby důvěřoval **IoT hub vlastníka certifikační Autority** certifikát k ověřování TLS připojení k zařízení brány. Tento krok můžete obvykle provést dvěma způsoby: na úrovni operačního systému, nebo (pro určité jazyky) na úrovni aplikace.

Například pro aplikace .NET, můžete přidat následující fragment kódu důvěřovat certifikátu ve formátu PEM uložené v cestě `certPath`. V závislosti na tom, kterou verzi skript, který jste použili cesta odkazuje buď `certs/azure-iot-test-only.root.ca.cert.pem` (Bash) nebo `RootCA.pem` (Powershell).

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   
   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

Provedením tohoto kroku na úrovni operačního systému se liší mezi Windows a mezi distribucí Linux.

Druhým krokem je k chybě při inicializaci sady sdk zařízení IoT Hub s připojovacím řetězcem odkazující na název hostitele zařízení brány.
To se provádí přidáním `GatewayHostName` vlastnost, která má připojovací řetězec zařízení. Pro instanci zde je ukázka zařízení připojovací řetězec pro zařízení, na kterou jsme připojí `GatewayHostName` vlastnost:

   ```
   HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com
   ```

Tyto dva kroky povolit aplikaci zařízení pro připojení k zařízení brány.

## <a name="next-steps"></a>Další postup
[Pochopení požadavků a nástrojů pro vývoj modulů IoT Edge][lnk-module-dev].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/modules-preview/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
