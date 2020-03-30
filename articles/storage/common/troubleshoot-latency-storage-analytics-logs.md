---
title: Řešení potíží s latencí s využitím protokolů Analýzy úložiště
description: Identifikujte a vyřešujte problémy s latencí pomocí protokolů Azure Storage Analytic a optimalizujte klientskou aplikaci.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 2197a149235c0dca98a24a57549538b2a4cbb1c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196508"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Řešení potíží s latencí s využitím protokolů Analýzy úložiště

Diagnostika a řešení potíží je klíčovou dovedností pro vytváření a podporu klientských aplikací pomocí Azure Storage.

Vzhledem k distribuované povaze aplikace Azure může být diagnostika a řešení problémů s chybami a výkonem složitější než v tradičních prostředích.

Následující kroky ukazují, jak identifikovat a řešit problémy s latencí pomocí protokolů Azure Storage Analytic a optimalizovat klientskou aplikaci.

## <a name="recommended-steps"></a>Doporučené kroky

1. Stáhněte si [protokoly Storage Analytics](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data).

2. K převodu nezpracovaných protokolů formátu do tabulkového formátu použijte následující skript prostředí PowerShell:

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. Skript spustí okno GUI, kde můžete filtrovat informace podle sloupců, jak je znázorněno níže.

   ![Okno analyzátoru protokolů analyticstorage](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Zúžit položky protokolu na základě "operation-type" a vyhledejte položku protokolu vytvořenou během časového rámce problému.

   ![Položky protokolu typu operace](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. Během doby, kdy k problému došlo, jsou důležité následující hodnoty:

   * Typ operace = GetBlob
   * stav požadavku = Chyba SASNetworkError
   * End-to-End-Latency-In-Ms = 8453
   * Latence serveru v ms = 391

   Latence od konce se vypočítá pomocí následující rovnice:

   * Latence od konce na konec = latence serveru + latence klienta

   Výpočet latence klienta pomocí položky protokolu:

   * Latence klienta = latence mezi koncovými místy – latence serveru

          * Example: 8453 – 391 = 8062ms

   Následující tabulka obsahuje informace o výsledcích operationtype s vysokou latencí a stavu requeststatus:

   |   |RequestStatus=<br>Úspěch|RequestStatus=<br>(SAS) Chyba sítě|Doporučení|
   |---|---|---|---|
   |GetBlob|Ano|Ne|[**Operace GetBlob:** RequestStatus = Úspěch](#getblob-operation-requeststatus--success)|
   |GetBlob|Ne|Ano|[**Operace GetBlob:** Stav_požadavku = (SAS)Chyba sítě](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Ano|Ne|[**Operace Put:** RequestStatus = Úspěch](#put-operation-requeststatus--success)|
   |PutBlob|Ne|Ano|[**Operace Put:** Stav_požadavku = (SAS)Chyba sítě](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Výsledky stavu

### <a name="getblob-operation-requeststatus--success"></a>Operace GetBlob: RequestStatus = Úspěch

Zkontrolujte následující hodnoty, jak je uvedeno v kroku 5 části Doporučené kroky:

* Latence od konce na konec
* Latence serveru
* Latence klienta

V **operaci GetBlob** s **RequestStatus = Úspěch**, pokud **maximální čas** strávený v **latenci klienta**, to znamená, že Azure Storage tráví velký objem času psaní dat do klienta. Toto zpoždění označuje problém na straně klienta.

**Doporučení:**

* Prozkoumejte kód ve vašem klientovi.
* Pomocí nástroje Wireshark, Microsoft Message Analyzer nebo Tcping prozkoumejte problémy s připojením k síti z klienta. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>Operace GetBlob: RequestStatus = (SAS)NetworkError

Zkontrolujte následující hodnoty, jak je uvedeno v kroku 5 části Doporučené kroky:

* Latence od konce na konec
* Latence serveru
* Latence klienta

V **operaci GetBlob** s **RequestStatus = (SAS)NetworkError**, pokud **maximální čas** strávený v **latenci klienta**, nejčastější problém je, že klient se odpojí před vypršením časového limitu ve službě úložiště.

**Doporučení:**

* Prozkoumejte kód ve vašem klientovi, abyste pochopili, proč a kdy se klient odpojí od služby úložiště.
* Pomocí nástroje Wireshark, Microsoft Message Analyzer nebo Tcping prozkoumejte problémy s připojením k síti z klienta. 

### <a name="put-operation-requeststatus--success"></a>Operace Put: RequestStatus = Úspěch

Zkontrolujte následující hodnoty, jak je uvedeno v kroku 5 části Doporučené kroky:

* Latence od konce na konec
* Latence serveru
* Latence klienta

V **operaci Put** s **RequestStatus = Úspěch,** pokud **maximální čas** strávený v **latenci klienta**, to znamená, že klient trvá více času na odeslání dat do úložiště Azure. Toto zpoždění označuje problém na straně klienta.

**Doporučení:**

* Prozkoumejte kód ve vašem klientovi.
* Pomocí nástroje Wireshark, Microsoft Message Analyzer nebo Tcping prozkoumejte problémy s připojením k síti z klienta. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Operace Put: RequestStatus = (SAS)NetworkError

Zkontrolujte následující hodnoty, jak je uvedeno v kroku 5 části Doporučené kroky:

* Latence od konce na konec
* Latence serveru
* Latence klienta

V **operaci PutBlob** s **RequestStatus = (SAS)NetworkError**, pokud **maximální čas** strávený v **latenci klienta**, nejčastější problém je, že klient se odpojí před vypršením časového limitu ve službě úložiště.

**Doporučení:**

* Prozkoumejte kód ve vašem klientovi, abyste pochopili, proč a kdy se klient odpojí od služby úložiště.
* Pomocí nástroje Wireshark, Microsoft Message Analyzer nebo Tcping prozkoumejte problémy s připojením k síti z klienta.

