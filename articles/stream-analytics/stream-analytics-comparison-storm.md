---
title: 'Analýza platformy: porovnání Apache Storm do služby Stream Analytics | Microsoft Docs'
description: Získáte pokyny výběr cloudové platformy analýzy pomocí porovnávání Apache Storm do služby Stream Analytics. Pochopit funkce a rozdíly.
keywords: analytické platformě, analýzy platformy, Cloudová platforma analýzy, storm porovnání
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: b9aac017-9866-4d0a-b98f-6f03881e9339
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/27/2017
ms.author: jeanb
ms.openlocfilehash: 48599c362c5db50ebec3acd0aaa4359439edb00b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="choosing-a-streaming-analytics-platform-comparing-apache-storm-and-azure-stream-analytics"></a>Výběr streamování analytické platformě: porovnání Apache Storm a Azure Stream Analytics
Azure poskytuje několik řešení pro analýzu dat: [Azure streamování Analytics](https://docs.microsoft.com/azure/stream-analytics/) a [Apache Storm v Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-storm/). Obě platformy analytics poskytovat výhody PaaS řešení. Ale platformy mít některé významné rozdíly v jejich schopnosti stejně jako v tom, jak konfigurovat a spravovat je. 

Tento článek obsahuje vedle sebe porovnání funkcí, které vám pomohou zvolit mezi Apache Storm a Azure Stream Analytics jako cloudové platformy analytics. 

## <a name="general-features"></a>Obecné funkce

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm v HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Otevřít zdroj?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Ne. Azure Stream Analytics je Microsoft nechráněný nabídky.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Ano. Apache Storm je technologie Apache licenci.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Podporu společnosti Microsoft?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Ano </p>
            </td>
            <td width="246" valign="top">
                <p>
Ano </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Požadavky na hardware</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Žádné. Azure Stream Analytics je služba Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Žádné. Apache Storm je služba Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Nejvyšší úrovně jednotky</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Uživatelé nasazení a monitorování úloh streamování.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Uživatelé nasazení a monitorování celý cluster, který může být hostitelem více úloh Storm, jakož i jiné úlohy (včetně batch).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Ceny</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Za cenu podle objemu dat, zpracování a počet jednotek streamování, které jsou požadované za hodinu, která je spuštěna úloha. 
                </p>
                    <p>Další informace najdete v tématu <a href="http://azure.microsoft.com/pricing/details/stream-analytics/">Stream Analytics ceny</a>.</p>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Jednotka nákupu je založená na clusteru a je účtován na základě času, které cluster používá, nezávisle na úlohy nasazení.
                </p>
                <p>
Další informace najdete v tématu <a href="http://azure.microsoft.com/pricing/details/hdinsight/">HDInsight ceny</a>.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="authoring"></a>Vytváření obsahu

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm v HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Možnosti: DSL SQL?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Ano. Stream Analytics poskytuje podobné jazyku SQL jazyk pro vytváření transformace.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Ne. Uživatelé psaní kódu v jazyce Java nebo C#, nebo pomocí rozhraní API Trident.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Možnosti: Dočasné operátory?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Ve výchozím nastavení jsou podporovány agregací v časových oknech a dočasných spojení.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Dočasné operátory musí být implementované uživatelem.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Vývojové prostředí</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Uživatelé mohou vytvořit, ladění a monitorujte úlohy prostřednictvím portálu Azure pomocí ukázkových dat, které jsou odvozené od živý datový proud.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Uživatelé pomocí rozhraní .NET můžete vyvíjet, ladit a sledovat pomocí sady Visual Studio. Uživatele, kteří používají Java nebo jiných jazyků, můžete použít rozhraní IDE, které si sami vyberou.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Podpora ladění</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Protokoly stavu a operací základní úlohy jsou k dispozici pomáhají ladit. Stream Analytics aktuálně neumožňuje uživatelům zadejte, který obsah nebo kolik obsahu je zahrnuta v protokolech (tj. podrobné režim).
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Podrobné protokoly jsou k dispozici. Uživatelé můžou používat protokoly v sadě Visual Studio nebo přihlášení do clusteru a přístup k protokoly přímo.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Rozšiřitelnost pomocí vlastního kódu?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Částečně podporovat funkce JavaScript UDF. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-javascript-user-defined-functions">integrace jazyka JavaScript UDF</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Ano. Uživatele můžete psát vlastní kód v C#, Java nebo dalších jazyků na Storm podporována.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="data-sources-inputs-and-outputs"></a>Zdroje dat (vstupy) a výstupy ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm v HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>Vstupní datové zdroje</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Úložiště Azure Event Hubs a objektů Blob v Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Konektory jsou k dispozici pro Azure Event Hubs, Azure Service Bus, Kafka a další. Uživatelé mohou vytvářet další konektorů pomocí vlastní kód.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Vstupní data formátů</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Avro, JSON, CSV </p>
            </td>
            <td width="246" valign="top">
                <p>
Uživatele můžete implementovat libovolném formátu pomocí vlastního kódu.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Výstupy</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Úloha streamování může mít několik výstupů. Podporované výstupy jsou Azure Event Hubs, Azure Blob úložiště, Azure Table storage, Azure SQL DB a Power BI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Storm podporuje mnoho výstupy v topologii a každý výstup může mít vlastní logiky pro příjem dat zpracování. Storm obsahuje konektory pro Power BI, Azure Event Hubs, úložiště objektů Blob v Azure, Azure Cosmos DB, SQL a HBase. Uživatelé mohou vytvářet další konektorů pomocí vlastní kód.    
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Kódování dat formátů</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Data musí být naformátován pomocí znakové sady UTF-8.
                </p>
            </td>   
            <td width="246" valign="top">
                <p>
Uživatele můžete implementovat formát pro kódování data pomocí vlastního kódu.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="management-and-operations"></a>Operace a Správa ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm v HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Úloha nasazení modelu</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Portál Azure, PowerShell a rozhraní REST API.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Portál Azure, prostředí PowerShell, sady Visual Studio a rozhraní REST API.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Monitorování (statistiky, čítače atd.)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Monitorování je implementovaná pomocí portálu Azure a rozhraní REST API. Uživatelé také mohou nakonfigurovat Azure výstrahy.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Monitorování je implementovaná pomocí rozhraní REST API a uživatelské rozhraní Storm.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Škálovatelnost</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Škálovatelnost je určen podle počtu jednotek streamování (SUs) pro každou úlohu. Každá jednotka streamování zpracovává až 1 MB za sekundu, s maximální počet jednotek 50. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-jobs">škálování zvýšení propustnosti</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Škálovatelnost je určen podle počtu uzlů v clusteru HDInsight Storm. Horní limit počtu uzlů je definována Azure kvóty uživatele.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Omezení zpracování dat</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Uživatelé mohou zvýšit zpracování dat nebo optimalizovat náklady zvýšením nebo snížením počet jednotek streamování, s maximální limit je 1 GB za sekundu.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Uživatele můžete škálovat velikost clusteru nahoru nebo dolů.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Zastavení nebo obnovení</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Zastavení a obnovení z poslední místo byla zastavena.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Zastavení a obnovení z poslední umístit zastaven podle vodoznak.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Aktualizace služby a framework</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Automatické opravy bez výpadků.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Automatické opravy bez výpadků.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Kontinuita podnikových procesů pomocí vysoce dostupné služby s zaručenou SLA</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <ul>
                <li>Smlouva SLA na úrovni 99,9 % doby provozu</li>
                <li>Automatické obnovení po selhání</li>
                <li>Předdefinované obnovení stavová dočasné operátory</li>
                </ul>
            </td>
            <td width="246" valign="top">
                <p>
Smlouva SLA na úrovni 99,9 % doby provozu clusteru Storm. 
                </p>
                <p>
Apache Storm je odolný proti chybám streamování platforma. Je však uživatele odpovědnost zajistit, že úloha streamování spustit bez přerušení.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="advanced-features"></a>Pokročilé funkce ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm v HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Pozdní příchodem a na více systémů pořadí zpracování událostí</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Konfigurovat integrovaných zásad můžete změnit pořadí událostí, odstraňte události nebo upravit čas události.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Uživatelé musí implementovat logiku tohoto scénáře.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Referenční data</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Referenční data jsou k dispozici z Azure Blob storage s maximálně 100 MB mezipaměti v paměti. Referenční data se aktualizují pomocí služby.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Žádné omezení velikosti dat. Konektory jsou k dispozici pro HBase, Azure Cosmos DB, SQL Server a Azure. Uživatelé mohou vytvářet další konektorů pomocí vlastní kód. Referenční data musí aktualizovat pomocí vlastního kódu.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Integrace s Machine Learning</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Publikovat Azure Machine Learning modely můžete nakonfigurovat jako funkce při vytvoření úlohy. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-with-machine-learning-functions">škálování pro Machine Learning funkce</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
K dispozici prostřednictvím funkce Bolts Storm.
                </p>
            </td>
        </tr>
    </tbody>
</table>
