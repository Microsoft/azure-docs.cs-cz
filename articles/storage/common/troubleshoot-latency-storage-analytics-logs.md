---
title: Řešení potíží s latencí s využitím protokolů Analýzy úložiště
description: Identifikujte a vyřešte potíže s latencí pomocí Azure Storage analytických protokolů a optimalizujte klientskou aplikaci.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: efae9cd2a73bf6df89007ac313ca6dfe6efe6ddd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87075952"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Řešení potíží s latencí s využitím protokolů Analýzy úložiště

Diagnostikování a řešení potíží je klíčovou dovedností pro sestavování a podporu klientských aplikací pomocí Azure Storage.

Z důvodu distribuované povahy aplikace Azure může být Diagnostika a řešení potíží s chybami a problémy s výkonem složitější než v tradičních prostředích.

Následující kroky ukazují, jak identifikovat a řešit potíže s latencí pomocí Azure Storage analytických protokolů a optimalizovat klientskou aplikaci.

## <a name="recommended-steps"></a>Doporučené kroky

1. Stáhněte si [protokoly analýza úložiště](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data).

2. Pomocí následujícího skriptu PowerShellu převeďte protokoly nezpracovaných formátů do tabulkového formátu:

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

3. Skript spustí okno grafického uživatelského rozhraní, kde můžete filtrovat informace podle sloupců, jak je znázorněno níže.

   ![Okno analyzátoru protokolů pro analýzu úložiště](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Zužte položky protokolu na základě typu "typ operace" a vyhledejte položku protokolu vytvořenou během časového rámce problému.

   ![Položky protokolu typu operace](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. V době, kdy k tomuto problému došlo, jsou důležité následující hodnoty:

   * Typ operace = getblob
   * požadavek – stav = SASNetworkError
   * Koncová latence-in-MS = 8453
   * Server – latence-in-MS = 391

   Koncová latence se počítá pomocí následující rovnice:

   * Koncová latence = Server-Latency + latence klienta

   Vypočítat latenci klienta pomocí položky protokolu:

   * Latence klienta = koncová latence – Server-Latency

        Příklad: 8453 – 391 = 8062ms

   Následující tabulka poskytuje informace o typem operace OperationType a výsledcích stavem žádosti s vysokou latencí:

   | Typ objektu BLOB |Stavem žádosti =<br>Success|Stavem žádosti =<br>VEDE NetworkError|Doporučení|
   |---|---|---|---|
   |GetBlob|Yes|No|[**Operace getblob:** Stavem žádosti = úspěch](#getblob-operation-requeststatus--success)|
   |GetBlob|No|Yes|[**Operace getblob:** Stavem žádosti = (SAS) NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Yes|No|[**Operace Put:** Stavem žádosti = úspěch](#put-operation-requeststatus--success)|
   |PutBlob|No|Yes|[**Operace Put:** Stavem žádosti = (SAS) NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Výsledky stavu

### <a name="getblob-operation-requeststatus--success"></a>Operace getblob: stavem žádosti = Success

Ověřte následující hodnoty, jak je uvedeno v kroku 5 části doporučený postup:

* Koncová latence
* Server-Latency
* Client-Latency

Pokud v **operaci getblob** s **stavem žádosti = Success**dojde k **překročení maximální doby** v **latenci klienta**, znamená to, že Azure Storage stráví velkou dobu zápisu dat do klienta. Toto zpoždění indikuje Client-Side problém.

**Základě**

* Prozkoumejte kód ve vašem klientovi.
* K prozkoumání problémů s připojením k síti od klienta použijte Nástroj Wireshark, Microsoft Message Analyzer nebo Tcping. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>Operace getblob: stavem žádosti = (SAS) NetworkError

Ověřte následující hodnoty, jak je uvedeno v kroku 5 části doporučený postup:

* Koncová latence
* Server-Latency
* Client-Latency

Pokud je v **operaci getblob** s **stavem žádosti = (SAS) NetworkError** **Maximální doba** strávená v **latenci klienta**, Nejběžnějším problémem je to, že se klient odpojí před vypršením časového limitu ve službě úložiště.

**Základě**

* Prozkoumejte kód ve vašem klientovi, abyste zjistili, proč a kdy se klient odpojí od služby úložiště.
* K prozkoumání problémů s připojením k síti od klienta použijte Nástroj Wireshark, Microsoft Message Analyzer nebo Tcping. 

### <a name="put-operation-requeststatus--success"></a>Operace Put: stavem žádosti = Success

Ověřte následující hodnoty, jak je uvedeno v kroku 5 části doporučený postup:

* Koncová latence
* Server-Latency
* Client-Latency

Pokud je v **operaci Put** s **stavem žádosti = Success**vyčerpána **Maximální doba** v **latenci klienta**, znamená to, že klient trvá déle, než pošle data do Azure Storage. Toto zpoždění indikuje Client-Side problém.

**Základě**

* Prozkoumejte kód ve vašem klientovi.
* K prozkoumání problémů s připojením k síti od klienta použijte Nástroj Wireshark, Microsoft Message Analyzer nebo Tcping. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Operace Put: stavem žádosti = (SAS) NetworkError

Ověřte následující hodnoty, jak je uvedeno v kroku 5 části doporučený postup:

* Koncová latence
* Server-Latency
* Client-Latency

Pokud je v **operaci PutBlob** s **NetworkErrorem stavem žádosti = (SAS)**, je-li **Maximální doba** strávena při **latenci klienta**, Nejběžnějším problémem je, že se klient odpojí, než vyprší časový limit ve službě úložiště.

**Základě**

* Prozkoumejte kód ve vašem klientovi, abyste zjistili, proč a kdy se klient odpojí od služby úložiště.
* K prozkoumání problémů s připojením k síti od klienta použijte Nástroj Wireshark, Microsoft Message Analyzer nebo Tcping.

