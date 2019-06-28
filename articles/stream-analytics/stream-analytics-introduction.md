---
title: Přehled služby Azure Stream Analytics
description: Přečtěte si o službě Stream Analytics, která umožňuje v reálném čase analyzovat data streamovaná z platformy Internet věcí (IOT).
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: c50ad2b045f32daf53033318123b68e4b2d58db5
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329324"
---
# <a name="what-is-azure-stream-analytics"></a>Co je Azure Stream Analytics?

Azure Stream Analytics je komplexní modul pro zpracování událostí, která slouží k analýze a současně zpracovávat velké objemy rychlá streamovaná data z více zdrojů a analýzu v reálném čase. Vzory a vztahy mohou spadat do informace získané z počtu vstupních zdrojů, včetně zařízení, senzorů, záznamů o navštívených stránkách, sociálních médií a aplikací. Tato schémata lze aktivovat akce a pracovní postupy se spustí tyto vytváření výstrah, tak informací do nástrojů pro vytváření sestav nebo uložení transformovaných dat pro pozdější použití. Také Stream Analytics je k dispozici v modulu runtime Azure IoT Edge a podporuje stejný jazyk nebo syntaxe jako cloudu. 

Následující scénáře jsou uvedené příklady, když použijete Azure Stream Analytics:

* Analýzu datových proudů v reálném čase telemetrie ze zařízení IoT
* Webové protokoly/analýza navštívených webových stránek
* Geoprostorová analýza pro správu vozového parku a vozidel bez řidiče
* Vzdálené monitorování nebo prediktivní Údržba vysoce hodnotných prostředků
* Analýza dat z prodejen v reálném čase pro účely zjišťování anomálií a řízení zásob

## <a name="how-does-stream-analytics-work"></a>Jak funguje Stream Analytics?

Úlohy Azure Stream Analytics se skládá z vstup, dotaz a výstup. Stream Analytics ingestují data z Azure Event Hubs, Azure IoT Hub nebo Azure Blob Storage. Dotaz, který je založen na dotazovací jazyk SQL, je možné snadno filtrovat, řazení, agregaci a spojovat streamovaná data za určitou dobu. Můžete také rozšířit tento jazyk SQL s použitím jazyka JavaScript a C# uživatelem definované funkce (UDF). Můžete snadno upravit řazení možnosti a dobu trvání časových období při preforming agregační operace pomocí jednoduchých jazykových konstruktorů a/nebo konfigurace událostí.

Každá úloha má výstup pro Transformovaná data a můžete řídit, co se stane v reakci na informace, které jste analyzovali. Můžete například provést následující věci:

* Odesílání dat do služby, jako jsou Azure Functions, Service Bus Topics nebo fronty k aktivaci vlastních pracovních postupů směru server-klient nebo komunikace.
* Odesílání dat do řídicího panelu Power BI pro v reálném čase mnoha.
* Data Store v jiné služby Azure storage k trénování služby machine learning model na základě historických dat nebo provedení dávkové analýzy.

Následující obrázek ukazuje, jak data odesílají do Stream Analytics, analyzují a posílají k dalším akcím, jako je storage nebo prezentace:

![Kanál Stream Analytics (úvod)](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Klíčové funkce a výhody

Služba Azure Stream Analytics byla navržena jako snadno použitelná, flexibilní, spolehlivá a škálovatelná na libovolnou velikost úlohy. Je k dispozici ve víc oblastech Azure. Následující obrázek ukazuje klíčové funkce Azure Stream Analytics:

![Klíčové funkce Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Snadný začátek

Azure Stream Analytics je snadné začít. Trvá jen několik kliknutí pro připojení k více zdroji a jímkami, vytváří kanál začátku do konce. Stream Analytics se můžete připojit k [Azure Event Hubs](/azure/event-hubs/) a [Azure IoT Hub](/azure/iot-hub/) ingestovat streamovaná data, stejně jako [úložiště objektů Blob v Azure](/azure/storage/storage-introduction) ingestovat historická data. Vstup úlohy může také zahrnovat referenční statická nebo pomalu se měnící data z úložiště objektů Blob v Azure nebo [SQL Database](stream-analytics-use-reference-data.md#azure-sql-database) , že se můžete připojit k streamovaná data a provádět operace vyhledávání.

Stream Analytics může směrovat výstup úlohy na mnoho úložných systémů, jako [úložiště objektů Blob v Azure](/azure/storage/storage-introduction), [Azure SQL Database](/azure/sql-database/), [Azure Data Lake Store](/azure/data-lake-store/), a [Azure Cosmos DB](/azure/cosmos-db/introduction). Můžete spustit dávkovou analýzu na uložený výstup s Azure HDInsight, nebo můžete odeslat výstup do jiné služby, jako jsou Event Hubs pro používání nebo [Power BI](https://docs.microsoft.com/power-bi/) pro vizualizace v reálném čase.

Celý seznam výstupy Stream Analytics najdete v tématu [pochopit vytvořené jako výstupy z Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Produktivita programátorů

Azure Stream Analytics používá k analýze nestatických dat jednoduchý dotaz SQL na základě jazyk, který je rozšířený o výkonná dočasná omezení. Pokud chcete definovat transformace úloh, použijete jednoduchý deklarativní [dotazovací jazyk Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference), který umožňuje vytváření složitých dočasných dotazů a analýz pomocí jednoduchých konstruktorů SQL. Protože dotazovací jazyk Stream Analytics je konzistentní s jazykem SQL, znalost SQL je dostačující a začněte vytvářet úlohy. Úlohy můžete vytvořit také pomocí vývojářských nástrojů jako Azure PowerShell, [nástrojů sady Visual Studio pro Stream Analytics](stream-analytics-tools-for-visual-studio-install.md), [Stream Analytics Visual Studio Code příponou](quick-create-vs-code.md), nebo šablony Azure Resource Manageru . Použití vývojářských nástrojů umožňuje vyvíjet transformační dotazy offline a pomocí [kanálu CI/CD](stream-analytics-tools-for-visual-studio-cicd.md) odesílat úlohy do Azure.

Dotazovací jazyk Stream Analytics nabízí širokou škálu funkcí pro analýzy a zpracování streamovaných dat. Tento dotazovací jazyk podporuje jednoduchou manipulaci s daty, agregační funkce a komplexní geoprostorové funkce. Můžete upravit dotazy na portálu a otestovat je pomocí ukázkových dat, která se extrahují z živého datového proudu.

Možnosti dotazovacího jazyka můžete rozšířit definováním a vyvoláním dalších funkcí. Můžete definovat volání funkcí ve službě Azure Machine Learning a využijte výhod řešení Azure Machine Learning a integrovat JavaScript nebo C# uživatelem definované funkce (UDF) nebo uživatelem definovaných agregacích provádět složité výpočty v rámci Dotazu Stream Analytics.

## <a name="fully-managed"></a>S plnou správou

Služba Azure Stream Analytics je plně spravovaná nabídka bez serveru (PaaS) v Azure. Nemusíte zřizovat žádný hardware nebo spravovat clustery na spuštění svých úloh. Azure Stream Analytics plně řídí vaši úlohu tak, že nastavení komplexních výpočetních clusterů v cloudu a přitom se bezpečně postará výkon ladění potřebné ke spuštění úlohy. Integrace s Azure Event Hubs a Azure IoT Hub umožňuje vaši úlohu, aby dokázala zpracovat miliony událostí za sekundu, které přicházejí z mnoha různých zdrojů, zahrnují připojených zařízení, webů a souborů protokolů. Pomocí centra událostí nabízejí funkci dělení, které můžete výpočty na logické kroky dělit, s možností dělit ještě podrobněji, pokud chcete zvýšit škálovatelnost.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Běží v cloudu nebo na inteligentních hraničních zařízení

Azure Stream Analytics můžete spouštět v cloudu pro rozsáhlé analýzy, nebo spustit na hraničních zařízeních IoT pro analýzu mimořádně nízkou latencí. Azure Stream Analytics používá stejné dotazovací jazyk v cloudu i hraničních zařízeních, který vývojářům umožňuje vytvořit skutečně hybridní architektury pro zpracování datových proudů. 

## <a name="low-total-cost-of-ownership"></a>Nízké celkové náklady na vlastnictví

Stream Analytics je cloudová služba, takže je optimalizovaná z hlediska nákladů. Se využívá řada bez počátečních nákladů – platíte jenom za [spotřebované jednotky streamování](stream-analytics-streaming-unit-consumption.md)a objemu zpracovaných dat. Neexistuje žádný závazek nebo zřizování clusterů a můžete škálovat úlohy směrem nahoru nebo dolů podle potřeb svého podniku.

## <a name="mission-critical-ready"></a>Klíčové připravené

Azure Stream Analytics je dostupná v několika oblastech po celém světě a je navržen pro spouštění klíčových úloh, které podporuje požadavky na spolehlivost, zabezpečení a dodržování předpisů.

### <a name="reliability"></a>Spolehlivost

Azure Stream Analytics zaručuje právě-po zpracování událostí a na alespoň jedno doručení událostí, takže události se nikdy ztratí. Přesně – jedno zpracování je zaručeno, že se vybrané výstupem jak je popsáno v [záruky doručení událostí](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

Azure Stream Analytics je vestavěným funkcím pro obnovení v případě se doručení události nezdaří. Stream Analytics také poskytuje integrované kontrolní body pro uchování stavu vašich úloh a poskytuje opakovatelné výsledky.

Stream Analytics je spravovaná služba, zaručuje zpracování událostí s 99,9 % dostupnost na minutu úrovni členitosti. Další informace najdete v tématu [Stream Analytics SLA](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) stránky. 

### <a name="security"></a>Zabezpečení

Z hlediska zabezpečení Azure Stream Analytics šifruje všechny příchozí a odchozí komunikace a podporuje TLS 1.2. Integrované kontrolní body je také šifrovaný. Stream Analytics nejsou uloženy příchozích dat, protože probíhá veškeré zpracování v paměti.

### <a name="compliance"></a>Dodržování předpisů

Azure Stream Analytics následuje několik certifikací dodržování předpisů, jak je popsáno v [přehled o dodržování předpisů Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Výkon

Stream Analytics dokáže zpracovat miliony událostí za sekundu a dokáže poskytovat výsledky s nízkou latencí ultra. Umožňuje vertikálně navýšit kapacitu a horizontální navýšení kapacity pro zpracování velkých událostí v reálném čase a složitých aplikací pro zpracování. Stream Analytics podporuje vyšší výkon díky dělení, což složitých dotazů paralelizovaná a spustit na několika uzlech pro streamování. Azure Stream Analytics je postavená na [Trill](https://github.com/Microsoft/Trill), streamování analytics stroj v paměti výkonné vyvinuty ve spolupráci s Microsoft Research.

## <a name="next-steps"></a>Další postup

Teď máte přehled o službě Azure Stream Analytics. V dalším kroku se můžete do tématu ponořit hlouběji a vytvořit si svoji první úlohu Stream Analytics:

* [Vytvořit úlohu Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
* [Vytvořit úlohu Stream Analytics pomocí Azure PowerShellu](stream-analytics-quick-create-powershell.md)
* [Vytvoření úlohy Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md).
* [Vytvoření úlohy Stream Analytics pomocí Visual Studio Code](quick-create-vs-code.md).
