---
title: Podporované metriky ve službě Azure Monitor ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o podporované metriky pro monitorování Azure ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: mabrigg
ms.openlocfilehash: 109ac1b0d9f52211f05e5fc1b177aa775ba66aae
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889713"
---
# <a name="supported-metrics-with-azure-monitor-on-azure-stack"></a>Podporované metriky ve službě Azure Monitor ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Můžete načíst metriky z Azure monitoru ve službě Azure Stack ve stejné jako globální Azure. Míry můžete na portálu je získat z rozhraní REST API nebo dotazovat pomocí Powershellu nebo rozhraní příkazového řádku.

Následující tabulky uvádějí metriky, které jsou dostupné s Azure Monitor metriky kanálu ve službě Azure Stack. Pro dotazování a přístup k těmto metrikám, budete muset **2018-01-01** verzi api-version profilu rozhraní API. Další informace o profilech rozhraní API a služby Azure Stack najdete v tématu [profilů verzí API spravovat ve službě Azure Stack](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Metrika | Metriky zobrazovaný název | Jednotka | Typ agregace | Popis | Dimenze |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Procento CPU | Procento CPU | Procento | Průměr | Procento přidělených výpočetních jednotek, které virtuální počítače aktuálně používají | Žádné dimenze |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| Metrika | Metriky zobrazovaný název | Jednotka | Typ agregace | Popis | Dimenze |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Použitá kapacita | B | Průměr | Kapacita využitá účtem | Žádné dimenze |
| Transakce | Transakce | Počet | Celkem | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a požadavky, které došlo k chybě. Hodnota ResponseType dimenzi používejte pro počet různých typů odpovědi. | Hodnota ResponseType, GeoType, ApiName |
| Příchozí přenos dat | Příchozí přenos dat | B | Celkem | Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure. | GeoType ApiName |
| Výchozí přenos | Výchozí přenos | B | Celkem | Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat. | GeoType ApiName |
| SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr | Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje síťovou latenci určenou v AverageE2ELatency. | GeoType ApiName |
| SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr | Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. | GeoType ApiName |
| Dostupnost | Dostupnost | Procento | Průměr | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota TotalBillableRequests vydělí počtem příslušných požadavků, včetně těch, ke které došlo k neočekávané chybě. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API. | GeoType ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| Metrika | Metriky zobrazovaný název | Jednotka | Typ agregace | Popis | Dimenze |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| BlobCapacity | Kapacita služby Blob | B | Celkem | Velikost úložiště využitá službou Blob Service účtu úložiště v bajtech | BlobType |
| BlobCount | Počet objektů blob | Počet | Celkem | Počet objektů blob ve službě Blob Service účtu úložiště | BlobType |
| ContainerCount | Počet kontejnerů služby Blob | Počet | Průměr | Počet kontejnerů ve službě Blob Service účtu úložiště | Žádné dimenze |
| Transakce | Transakce | Počet | Celkem | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a požadavky, které došlo k chybě. Hodnota ResponseType dimenzi používejte pro počet různých typů odpovědi. | Hodnota ResponseType, GeoType, ApiName |
| Příchozí přenos dat | Příchozí přenos dat | B | Celkem | Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure. | GeoType ApiName |
| Výchozí přenos | Výchozí přenos | B | Celkem | Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat. | GeoType ApiName |
| SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr | Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje síťovou latenci určenou v AverageE2ELatency. | GeoType ApiName |
| SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr | Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. | GeoType ApiName |
| Dostupnost | Dostupnost | Procento | Průměr | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota TotalBillableRequests vydělí počtem příslušných požadavků, včetně těch, ke které došlo k neočekávané chybě. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API. | GeoType ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| Metrika | Metriky zobrazovaný název | Jednotka | Typ agregace | Popis | Dimenze |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Kapacita služby Table | B | Průměr | Velikost úložiště využitá službou Table Service účtu úložiště v bajtech | Žádné dimenze |
| TableCount | Počet tabulek | Počet | Průměr | Počet tabulek ve službě Table Service účtu úložiště | Žádné dimenze |
| TableEntityCount | Počet entit tabulek | Počet | Průměr | Počet entit tabulek ve službě Table Service účtu úložiště | Žádné dimenze |
| Transakce | Transakce | Počet | Celkem | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a požadavky, které došlo k chybě. Hodnota ResponseType dimenzi používejte pro počet různých typů odpovědi. | Hodnota ResponseType, GeoType, ApiName |
| Příchozí přenos dat | Příchozí přenos dat | B | Celkem | Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure. | GeoType ApiName |
| Výchozí přenos | Výchozí přenos | B | Celkem | Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat. | GeoType ApiName |
| SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr | Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje síťovou latenci určenou v AverageE2ELatency. | GeoType ApiName |
| SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr | Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. | GeoType ApiName |
| Dostupnost | Dostupnost | Procento | Průměr | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota TotalBillableRequests vydělí počtem příslušných požadavků, včetně těch, ke které došlo k neočekávané chybě. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API. | GeoType ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| Metrika | Metriky zobrazovaný název | Jednotka | Typ agregace | Popis | Dimenze |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Kapacita služby Queue | B | Průměr | Velikost úložiště využitá službou Queue účtu úložiště v bajtech | Žádné dimenze |
| QueueCount | Počet front | Počet | Průměr | Počet front ve službě Queue účtu úložiště | Žádné dimenze |
| QueueMessageCount | Počet zpráv fronty | Počet | Průměr | Přibližný počet zpráv fronty ve službě Queue účtu úložiště | Žádné dimenze |
| Transakce | Transakce | Počet | Celkem | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a požadavky, které došlo k chybě. Hodnota ResponseType dimenzi používejte pro počet různých typů odpovědi. | Hodnota ResponseType, GeoType, ApiName |
| Příchozí přenos dat | Příchozí přenos dat | B | Celkem | Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure. | GeoType ApiName |
| Výchozí přenos | Výchozí přenos | B | Celkem | Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat. | GeoType ApiName |
| SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr | Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje síťovou latenci určenou v AverageE2ELatency. | GeoType ApiName |
| SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr | Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. | GeoType ApiName |
| Dostupnost | Dostupnost | Procento | Průměr | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota TotalBillableRequests vydělí počtem příslušných požadavků, včetně těch, ke které došlo k neočekávané chybě. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API. | GeoType ApiName |

## <a name="next-steps"></a>Další postup

Další informace o [monitorování Azure ve službě Azure Stack](azure-stack-metrics-azure-data.md).