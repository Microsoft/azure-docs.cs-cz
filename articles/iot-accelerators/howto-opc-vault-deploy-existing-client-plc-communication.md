---
title: Zabezpečená komunikace klienta OPC a PLC OPC s trezorem OPC – Azure | Dokumentace Microsoftu
description: Zabezpečená komunikace klienta OPC a OPC PLC podepsáním svoje certifikáty pomocí certifikační Autority OPC trezoru.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 30eedd982fa0536ce45506c159de6d04132e9a14
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59493993"
---
# <a name="secure-the-communication-of-opc-client-and-opc-plc"></a>Zabezpečená komunikace klienta OPC a OPC PLC

OPC trezor je mikroslužeb, která můžete konfigurovat, registrace a správa životního cyklu certifikátu pro server OPC UA a klientských aplikací v cloudu. Tento článek ukazuje, jak zabezpečit komunikaci klienta OPC a OPC PLC podepisování svoje certifikáty pomocí certifikační Autority OPC trezoru.

V tomto nastavení klienta OPC testuje připojení k OPC PLC. Ve výchozím nastavení připojení není možné protože obě komponenty nebyly zřízeny správné certifikáty. Pokud jako součást OPC UA ještě nezřídil s certifikátem, vygeneruje certifikát podepsaný svým držitelem při spuštění. Však může být certifikát podepsaný pomocí certifikační autority (CA) a nainstalován v komponentě OPC UA. Po dokončení pro klienta OPC a OPC PLC, je povoleno připojení. Následující pracovní postup popisuje proces. Některé základní informace o zabezpečení OPC UA najdete v [tento dokument](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf) dokument white paper. Podrobnější informace najdete ve specifikaci OPC UA.

Testbed: Toto prostředí je nakonfigurován pro testování.

Trezor OPC skriptů:
- Zabezpečená komunikace klienta OPC a OPC PLC podepsáním svoje certifikáty pomocí certifikační Autority OPC trezoru.

> [!NOTE]
> Další informace najdete v článku Githubu [úložiště](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-self-signed-certificate-on-startup"></a>Vygenerovat certifikát podepsaný svým držitelem při spuštění

**Příprava**

- Ujistěte se, že proměnné prostředí `$env:_PLC_OPT` a `$env:_CLIENT_OPT` nejsou definovány, například `$env:_PLC_OPT=""` v Powershellu.

- Nastavte proměnnou prostředí `$env:_OPCVAULTID` na řetězec, který umožňuje najít data znovu v trezoru OPC. Jsou povolené jenom alfanumerické znaky. V našem příkladu byl použit "123456" jako hodnotu této proměnné.

- Ujistěte se, nejsou žádné svazky docker `opcclient` nebo `opcplc`. Obraťte se na `docker volume ls` a odebrat pomocí `docker volume rm <volumename>`. Budete muset odebrat také kontejnery s `docker rm <containerid>` Pokud svazky se pořád používá kontejner.

**Rychlý start**

V kořenovém adresáři úložiště spusťte následující příkaz Powershellu:

```
docker-compose -f connecttest.yml up
```

**Ověření**

Zkontrolujte v protokolu, že nemáte žádné certifikáty, nainstalován při prvním spuštění. Tady výstup protokolu PLC OPC (podobně jako se zobrazí klienta OPC):...
```
opcplc-123456 | [20:51:32 INF] Trusted issuer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted issuer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Trusted peer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted peer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Rejected certificate store contains 0 certs
```
Pokud se zobrazí certifikáty, které jsou hlášeny, postupujte podle výše uvedených kroků přípravy a odstraňte svazky dockeru.

Ověřte, že připojení k OPC PLC se nezdařilo. Byste měli vidět následující výstup ve výstupu protokolu klienta OPC:

```
opcclient-123456 | [20:51:35 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:51:36 ERR] Session creation to endpoint 'opc.tcp://opcplc-123456:50000/' failed 1 time(s). Please verify if server is up and OpcClient configuration is correct.
opcclient-123456 | Opc.Ua.ServiceResultException: Certificate is not trusted.
```
Důvod chyby je, že certifikát není důvěryhodný. To znamená, že `opc-client` nepokusili se připojit k `opc-plc` a obdržel odpověď zpět, což indikuje, že `opc-plc` nedůvěřuje `opc-client`, výsledkem `opc-plc` nelze ověřit certifikát, který `opc-client` má k dispozici. Toto je certifikát podepsaný svým držitelem bez další konfigurace certifikátu na `opc-plc`, a proto připojení se nezdařilo.

## <a name="sign-and-install-certificates-in-opc-ua-components"></a>Podepsat a nainstalovat certifikáty v součástech OPC UA

**Příprava**
1. Podívejte se na výstup protokolu z kroku 1 a načíst "CreateSigningRequest informace" OPC PLC a klienta OPC. Výstup se tady zobrazuje pouze pro OPC PLC:

    ```
    opcplc-123456 | [20:51:32 INF] ----------------------- CreateSigningRequest information ------------------
    opcplc-123456 | [20:51:32 INF] ApplicationUri: urn:OpcPlc:opcplc-123456
    opcplc-123456 | [20:51:32 INF] ApplicationName: OpcPlc
    opcplc-123456 | [20:51:32 INF] ApplicationType: Server
    opcplc-123456 | [20:51:32 INF] ProductUri: https://github.com/azure-samples/iot-edge-opc-plc
    opcplc-123456 | [20:51:32 INF] DiscoveryUrl[0]: opc.tcp://opcplc-123456:50000
    opcplc-123456 | [20:51:32 INF] ServerCapabilities: DA
    opcplc-123456 | [20:51:32 INF] CSR (base64 encoded):
    opcplc-123456 | MIICmzCCAYMCAQAwETEPMA0GA1UEAwwGT3BjUGxjMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwTvlbinAPWPxR9Lw1ndGsrLGy8GiqVOxyGaDpPUcMchX0k0/ncg28h7xrB2H1PThdiZxUJuUwsNM74HrVgt
    ofmXhA4dLM1cTxZHyJVFjl2L3vK5M58NNf6UNdKcB0x3LyuoT6mAIMXmCioqymFCk1TMzIMzbAe7JVAdUaSRBP1vuqQ1rV/cfNAe35dKQW4aPYgl7pR5f1hqprcDu/oca67X8L4kTl4oN0/bCYTk+Ibcd9cG462oAN+bSwbHn8a2jNky8rGsofA
    o9DOT+0ALPhk6CApCYIP2yxoI/kT188eqUUxzAFF9nyU79AyCkpGPuY8DGMyf56pDofgtGpfY3wQIDAQABoEUwQwYJKoZIhvcNAQkOMTYwNDAyBgNVHREEKzAphhh1cm46T3BjUGxjOm9wY3BsYy0xMjM0NTaCDW9wY3BsYy0xMjM0NTYwDQYJK
    oZIhvcNAQELBQADggEBAAsZLoOLzS2VhDcQRu0QhRbG7CGAxX19l7fDCG2WjU7lTFnCvYVTWTYyaY61ljmrWc7IbCaQdMJM8GRnAnvAzUh/PBDxkOX7NqI2+8F1yQOHgs/AfKuppOd6DIP8EzFAHnc0H85jay6zFdmIDWoWwpy0ACqOVooOTKST
    7uty0mT87bj8Cdy1yf4mvBNQx+nsuTbKgxWCBxGYAyg9dIL2uKL0aeB/ROW5Gkelz5sCEzQ1fFDokUA4oC5QiATQBN3cY7EmvRbPgdToY7CpRN3iiO7J+7bC7BP9YKfuE34E8xOFpskHPHAPf3r002/L0S67HyuVSXLUj1+Jc0LeAAF9Bw0=
    opcplc-123456 | [20:51:32 INF] ---------------------------------------------------------------------------
    ```
    
1. Přejděte [OPC trezor webu](https://opcvault.azurewebsites.net/).

1. Vyberte `Register New`

1. Zadejte informace o OPC PLC z protokolů výstupů `CreateSigningRequest information` oblasti do vstupního pole na `Register New OPC UA Application` stránce `Server` jako ApplicationType.

1. Vyberte `Register`.

1. Na další stránce `Request New Certificate for OPC UA Application` vyberte `Request new Certificate with Signing Request`.

1. Na další stránce `Generate a new Certificate with a Signing Request` vložte `CSR (base64 encoded)` řetězec z výstupu protokolu do `CreateRequest` vstupní pole. Ujistěte se, že zkopírujete úplný řetězec.

1. Vyberte `Generate New Certificate`.

1. Můžete se nyní v budoucnu k `View Certificate Request Details`. Na této stránce si můžete stáhnout všechny požadované informace ke zřízení úložišť certifikátů z `opc-plc`.

1. Na této stránce:  
    - Vyberte `Certificate` v `Download as Base64` a zkopírujte řetězec uvedené v `EncodedBase64` pole a uloží jej pro pozdější použití. Označujeme je jako `<applicationcertbase64-string>` později. Vyberte `Back`.

    - Vyberte `Issuer` v `Download as Base64` a zkopírujte řetězec uvedené v `EncodedBase64` pole a uloží jej pro pozdější použití. Označujeme je jako `<addissuercertbase64-string>` později. Vyberte `Back`.

    - Vyberte `Crl` v `Download as Base64` a zkopírujte řetězec uvedené v `EncodedBase64` pole a uloží jej pro pozdější použití. Označujeme je jako `<updatecrlbase64-string>` později. Vyberte `Back`.

1. V Powershellu teď nastavit proměnnou s názvem `$env:_PLC_OPT`:

    ```
    `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  
    ```
    > [!NOTE] 
    > Nahraďte řetězce předané jako možnost hodnoty ve formátu Base64 řetězců, které můžete načíst z webu.

Postupujte stejně jako kompletní počínaje `Register New` (krok 3 výše) pro klienta OPC. Existují pouze, které je potřeba mít na paměti následující rozdíly:

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

**Ověření** ověřte, že dvě součásti mají nyní podepsané certifikáty k aplikaci. Zkontrolujte výstup protokolu pro následující výstup:

```
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Starting to update the current CRL.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted peer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted issuer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Start updating the current application certificate.
opcplc-123456 | [20:54:38 INF] The current application certificate has SubjectName 'CN=OpcPlc' and thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] Remove the existing application certificate with thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' was added to the application certificate store.
opcplc-123456 | [20:54:39 INF] Activating the new application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586'.
opcplc-123456 | [20:54:39 INF] Application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' found in the application certificate store.
opcplc-123456 | [20:54:39 INF] Application certificate is for ApplicationUri 'urn:OpcPlc:opcplc-123456', ApplicationName 'OpcPlc' and Subject is 'OpcPlc'
 ```
Certifikát aplikace je k dispozici a podepsaný Certifikační autoritou.

V protokolu ověřte, že jsou teď nainstalované certifikáty. Níže je výstup protokolu OPC PLC a klienta OPC se zobrazí podobný výstup.
```
opcplc-123456 | [20:54:39 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Trusted peer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Rejected certificate store contains 0 certs
```
Vystavitel certifikátu aplikace je certifikační Autorita `CN=Azure IoT OPC Vault CA, O=Microsoft Corp.` a OPC PLC důvěřovat také všechny certifikáty podepsané touto certifikační autoritou.


Ověřte, že připojení k OPC PLC se úspěšně vytvořil a klienta OPC může číst data z OPC PLC. Byste měli vidět následující výstup ve výstupu protokolu klienta OPC:
```
opcclient-123456 | [20:54:42 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:54:42 INF] Session successfully created with Id ns=3;i=1085867946.
opcclient-123456 | [20:54:42 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [20:54:42 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [20:54:42 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [20:54:42 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [20:54:42 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [20:54:42 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [20:54:42 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [20:54:42 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [20:54:42 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/20/2018 20:54:42
```
Pokud se zobrazí tento výstup, pak OPC PLC se teď důvěřovat OPC versa klienta a naopak, protože obě mají nyní certifikáty podepsané Certifikační autoritou a vztahem důvěryhodnosti certifikáty, které se podepsané touto certifikační autoritou.

> [!NOTE] 
> I když jsme vám ukázali prvních dvou kroků pouze pro OPC PLC jsou třeba taky ověřit klienta OPC.


## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak nasazovat trezor OPC do existujícího projektu, je zde navrhované další krok:

> [!div class="nextstepaction"]
> [Nasazení do existujícího projektu Dvojčete OPC](howto-opc-twin-deploy-existing.md)