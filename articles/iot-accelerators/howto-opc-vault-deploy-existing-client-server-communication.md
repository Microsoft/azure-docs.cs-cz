---
title: Zabezpečení OPC UA klienta a aplikačního serveru OPC UA pomocí OPC trezor – Azure | Dokumentace Microsoftu
description: Zabezpečení aplikace server se nový pár klíčů a certifikátů trezor OPC pomocí klienta OPC UA a OPC UA.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 5ba2dba02585598b3797dd1b490976ebe34b489e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61450660"
---
# <a name="secure-opc-ua-client-and-opc-ua-server-application"></a>Zabezpečení OPC UA klienta a aplikačního serveru OPC UA 
OPC trezor je mikroslužeb, která můžete konfigurovat, registrace a správa životního cyklu certifikátu pro server OPC UA a klientských aplikací v cloudu. Tento článek ukazuje, jak zabezpečit serverové aplikace se nový pár klíčů a certifikátů trezor OPC pomocí klienta OPC UA a OPC UA.

V tomto nastavení se klient OPC testování připojení ke službě OPC PLC. Ve výchozím nastavení připojení není možné protože obě komponenty ještě nebyly zřízeny správné certifikáty. V tomto pracovním postupu jsme nepoužívají certifikáty podepsané svým držitelem součásti serveru OPC UA a podepsat prostřednictvím OPC trezoru. Viz předchozí [testbed](howto-opc-vault-deploy-existing-client-plc-communication.md). Místo toho tento testbed zřídí součásti pomocí nového certifikátu, a také s novou privátní klíč, které jsou generovány pomocí OPC trezoru. Některé základní informace o zabezpečení OPC UA najdete v tomto [dokument White Paper](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf). Podrobnější informace najdete ve specifikaci OPC UA.

Testbed: Toto prostředí je nakonfigurován pro testování.

Trezor OPC skriptů:
- Zabezpečení serverových aplikací s nový pár klíčů a certifikátů trezor OPC pomocí klienta OPC UA a OPC UA.

> [!NOTE]
> Další informace najdete v článku Githubu [úložiště](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-new-certificate-and-private-key"></a>Vygenerovat nový certifikát a privátní klíč 
**Příprava**
- Ujistěte se, že proměnné prostředí `$env:_PLC_OPT` a `$env:_CLIENT_OPT` nejsou definovány. Například `$env:_PLC_OPT=""` v Powershellu
- Nastavte proměnnou prostředí `$env:_OPCVAULTID` na řetězec, který umožňuje najít data znovu v trezoru OPC. Doporučujeme nastavit ho na 6 číslic. V našem příkladu byla "123456" použít jako hodnotu proměnné.
- Ujistěte se, neexistuje žádný svazek docker `opcclient` nebo `opcplc`. Obraťte se na `docker volume ls` a odebrat pomocí `docker volume rm <volumename>`. Budete muset odebrat také kontejnery s `docker rm <containerid>` Pokud svazky se pořád používá kontejner.

**Rychlý start**
1. Přejděte [OPC trezor webu](https://opcvault.azurewebsites.net/)

1. Vyberte `Register New`

1. Zadejte informace OPC PLC se zobrazilo ve výstupu protokolu předchozí testbed `CreateSigningRequest information` oblasti do vstupního pole na `Register New OPC UA Application` stránce `Server` jako ApplicationType.

1. Vyberte `Register`

1. Na další stránce `Request New Certificate for OPC UA Application` vyberte `Request new KeyPair and Certificate`

1. Na další stránce `Generate a new Certificate with a Signing Request` vložte `CSR (base64 encoded)` řetězec z výstupu protokolu do `CreateRequest` vstupní pole. Ujistěte se, že zkopírujete úplný řetězec.

1. Na další stránce `Request New Certificate for OPC UA Application` vyberte `Request new Certificate with Signing Request`

1. Na další stránce `Generate a new KeyPair and for an OPC UA Application` zadejte `CN=OpcPlc` jako SubjectName, `opcplc-<_OPCVAULTID>` (nahradit `<_OPCVAULTID>` s vaším) jako název_domény, vyberte `PEM` jako PrivateKeyFormat a zadejte heslo (označujeme později ho jako `<certpassword-string>`)

1. Vyberte `Generate New KeyPair`

1. Můžete se nyní v budoucnu k `View Certificate Request Details`. Na této stránce si můžete stáhnout všechny požadované informace ke zřízení úložišť certifikátů z `opc-plc`.

1. Na této stránce:  
    - Vyberte `Certificate` v `Download as Base64` a zkopírujte řetězec uvedené v `EncodedBase64` pole a uloží jej pro pozdější použití. Označujeme je jako `<applicationcertbase64-string>` později. Vyberte `Back`.
    - Vyberte `PrivateKey` v `Download as Base64` a zkopírujte řetězec uvedené v `EncodedBase64` pole a uloží jej pro pozdější použití. Označujeme je jako `<privatekeybase64-string>` později. Vyberte `Back`.
    - Vyberte `Issuer` v `Download as Base64` a zkopírujte řetězec uvedené v `EncodedBase64` pole a uloží jej pro pozdější použití. Označujeme je jako `<addissuercertbase64-string>` později. Vyberte `Back`.
    - Vyberte `Crl` v `Download as Base64` a zkopírujte řetězec uvedené v `EncodedBase64` pole a uloží jej pro pozdější použití. Označujeme je jako `<updatecrlbase64-string>` později. Vyberte `Back`.

1. V Powershellu teď nastavit proměnnou s názvem `$env:_PLC_OPT`:

   `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --privatekeybase64 <privatekeybase64-string> --certpassword <certpassword-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  

    Nahraďte řetězce předané jako možnost hodnoty ve formátu Base64 řetězců, které můžete načíst z webu.  

1. Postupujte stejně jako kompletní počínaje `Register New` pro klienta OPC. Existují pouze, které je potřeba mít na paměti následující rozdíly:
    - Výstup protokolu využít `opcclient`.
    - Vyberte `Client` jako ApplicationType během registrace.
    - Použití `$env:_CLIENT_OPT` jako název proměnné prostředí PowerShell.

    > [!NOTE] 
    > Při práci v tomto scénáři, které může mít rozpoznala, `<addissuercertbase64-string>` a `<updatecrlbase64-string>` hodnoty jsou stejné pro `opcplc` a `opcclient`. To platí pro naše případy použití a si ušetřit čas při provádění kroků.

**Rychlý start**

V kořenovém adresáři úložiště spusťte následující příkaz Powershellu:

```
docker-compose -f connecttest.yml up
```

**Ověření**

Ověřte, že dvě součásti nedošlo ke stávající certifikát aplikace. Zkontrolujte výstup protokolu. Níže je výstup OPC PLC a klienta OPC se podobný výstup protokolu.

```
opcplc-123456 | [13:40:08 INF] There is no existing application certificate.
```
Pokud je certifikát aplikace, odstraňte svazky dockeru, jak je popsáno v postupu přípravy.

V protokolu ověřte, že certifikát certifikační Autority OPC trezor byl nainstalován do vystavitele úložiště certifikátů stejně jako v úložišti certifikátů důvěryhodných peer. Níže je výstup protokolu OPC PLC a klienta OPC se podobný výstup protokolu. 

```
opcplc-123456 | [13:40:09 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Trusted peer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Rejected certificate store contains 0 certs
```
OPC PLC teď důvěřovat všichni klienti OPC UA s certifikáty podepsány OPC trezoru.

V protokolu ověřte, že formátu soukromého klíče je rozpoznán jako PEM a byl nainstalován nový certifikát aplikace. Níže je výstup protokolu OPC PLC a klienta OPC se podobný výstup protokolu. 

```
opcplc-123456 | [13:40:09 INF] The private key for the new certificate was passed in using PEM format.
opcplc-123456 | [13:40:09 INF] Remove the existing application certificate.
opcplc-123456 | [13:40:09 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C' was added to the application certificate store.
opcplc-123456 | [13:40:09 INF] Activating the new application certificate with thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C'.
```

Certifikát aplikace a privátního klíče jsou nyní nainstalován v úložišti certifikátů aplikace a používá aplikace OPC UA.

Ověřte, zda lze úspěšně navázat připojení mezi klientem OPC a OPC PLC a klienta OPC můžou číst data z OPC PLC. Byste měli vidět následující výstup ve výstupu protokolu klienta OPC:
```
opcclient-123456 | [13:40:12 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [13:40:12 INF] Session successfully created with Id ns=3;i=941910499.
opcclient-123456 | [13:40:12 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [13:40:12 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [13:40:12 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [13:40:12 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [13:40:12 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [13:40:12 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [13:40:12 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [13:40:12 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [13:40:12 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/21/2018 13:40:12
```
Pokud se zobrazí tento výstup, pak OPC PLC je nyní důvěřující klienta a naopak versa OPC, protože obě mají nyní certifikáty podepsané certifikační Autoritou a oba důvěřovat certifikátům, které jsou podepsané touto certifikační autoritou.

### <a name="a-testbed-for-opc-publisher"></a>Testbed pro vydavatele OPC ###

**Rychlý start**

V kořenovém adresáři úložiště spusťte následující příkaz Powershellu:
```
docker-compose -f testbed.yml up
```

**Ověření**
- Ověřte, že data se odesílají do IOT hub, můžete nakonfigurovat tak, že nastavíte `_HUB_CS` pomocí [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) nebo [iothub-explorer](https://github.com/Azure/iothub-explorer).
- OPC testovacího klienta je prostřednictvím přímé metody volání IOT hub a volání metod OPC konfigurace vydavatele OPC pro publikování a zrušení publikování uzly z testovacího serveru OPC.
- Podívejte se na výstup pro chybové zprávy.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak nasazovat trezor OPC do existujícího projektu, je zde navrhované další krok:

> [!div class="nextstepaction"]
> [Nasazení do existujícího projektu Dvojčete OPC](howto-opc-twin-deploy-existing.md)