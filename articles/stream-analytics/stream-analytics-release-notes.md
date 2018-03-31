---
title: Poznámky k verzi služby Stream Analytics | Microsoft Docs
description: Poznámky k verzi Stream Analytics
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 5e59f893-cd2c-43fb-9eca-c146ce637203
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 05/03/2017
ms.author: jeanb
ms.openlocfilehash: 645c9e7014beba0312de3784bbc04734927929a9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="stream-analytics-release-notes"></a>Poznámky k verzi služby Stream Analytics

## <a name="notes-for-06142017-update-of-stream-analytics-tools-for-visual-studio"></a>Poznámky pro aktualizaci 06/14/2017 Stream Analytics Tools pro Visual Studio
Tato aktualizace je pro naše nástroje sady Visual Studio. Tato verze obsahuje následující nové funkce:

| Nadpis | Popis |
| --- | --- |
| Podpora editor skriptů v jazyce Java |Můžete sledovat prostředí java nativní skript editor po vytvoření skriptu funkce vaší java.|
| Zobrazit úlohy spustit čas chybová zpráva | Pokud během provádění úlohy chyby za běhu, můžete je zobrazit na kartě chyby úpravou časové okno zobrazení. Ve výchozím nastavení zobrazí chybové zprávy pro posledních 30 minut. |
| Podpora sdílených svazků clusteru a Avro pro místní testování vstup | Kromě toho formát JSON teď můžete formát souboru CSV a Avro pro místní testování vstup.|

## <a name="notes-for-05032017-update-of-stream-analytics"></a>Poznámky pro aktualizaci 03/05/2017 Stream Analytics
Tato aktualizace je naše řešení potíží dokumentaci k verzi.

[Průvodce odstraňováním potíží s](stream-analytics-troubleshooting-guide.md) a byly vydané jiné dokumenty. Zkontrolujte, zpětná vazba je úvodní.

## <a name="notes-for-04242017-update-of-stream-analytics-tools-for-visual-studio"></a>Poznámky pro aktualizaci 04/24/2017 Stream Analytics Tools pro Visual Studio
Tato aktualizace je pro naše nástroje sady Visual Studio. Tato verze obsahuje následující nové funkce:

| Nadpis | Popis |
| --- | --- |
| Zobrazení výsledků místní testů v sadě Visual Studio | Zobrazení výsledných výstupů místní testu, stiskněte klávesu ENTER v okně výstupu konzoly nebo ho zavřete. Výsledek se zobrazí v okně v sadě Visual Studio ve formátu tabulky. |
| Místní výsledných výstupů ve formátu JSON | Při spuštění testu místní výsledných výstupů je generováno JSON a CSV formátů souborů. |
| Zobrazte náhled dat vstupy/výstupy úložiště objektů Blob nebo table | Dvojím kliknutím na úložiště objektů blob nebo table vstupu a výstupu v zobrazení úloh, můžete snadno náhled data v sadě Visual Studio. |
| Zobrazení chybové zprávy pro vstupy/výstupy | Pokud jsou některé chyby za běhu související s vaší práce vstupů nebo výstupů, je zobrazena na diagramu úlohy, kde můžete podržet zobrazit podrobnou chybovou zprávu.|


## <a name="notes-for-02012017-release-of-stream-analytics"></a>Poznámky k verzi 02/01/2017 Stream Analytics
Tato verze obsahuje následující aktualizace:

| Nadpis | Popis |
| --- | --- |
| Představení JavaScript uživatelsky definované funkce (UDF) |[Funkce definované uživatelem Java](stream-analytics-javascript-user-defined-functions.md) jsou nyní k dispozici pro větší flexibilita při vytváření dotazů. |
| Představení nástroje pro Visual Studio a Stream Analytics |[Nástroje pro sadu Visual Studio](stream-analytics-tools-for-visual-studio.md) jsou nyní k dispozici pro nástroj pro ladění a větší. |
| Představení protokolování diagnostiky |[Protokolování diagnostiky](stream-analytics-job-diagnostic-logs.md) je nyní k dispozici pro další možnosti řešení problémů. |
| Představení geoprostorové funkce |[Funkce geoprostorové](http://msdn.microsoft.com/library/mt778980(Azure.100).aspx) jsou nyní obecně k dispozici. |

## <a name="notes-for-04152016-release-of-stream-analytics"></a>Poznámky k verzi 04/15 2016 služby Stream Analytics
Tato verze obsahuje následující aktualizace:

| Nadpis | Popis |
| --- | --- |
| Obecné dostupnosti pro výstupů Power BI |[Power BI výstupy](stream-analytics-power-bi-dashboard.md) jsou nyní obecně k dispozici. Odebrali jsme vypršení platnosti-90denní autorizace pro Power BI. Další informace o scénářích, kde autorizace je třeba obnovit, najdete v článku [obnovit autorizace](stream-analytics-power-bi-dashboard.md#renew-authorization) části vytváření řídicí panel Power BI. |

## <a name="notes-for-03032016-release-of-stream-analytics"></a>Poznámky k verzi 03 03. 2016 Stream Analytics
Tato verze obsahuje následující aktualizace:

| Nadpis | Popis |
| --- | --- |
| Nové položky Stream Analytics Query Language |SAQL má nyní [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "stránka MSDN GetType"), [TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "stránka MSDN TRY_CAST") a [REGEXMATCH] (https://msdn.microsoft.com/library/azure/mt643891.aspx "Stránka MSDN REGEXMATCH"). |

## <a name="notes-for-12102015-release-of-stream-analytics"></a>Poznámky k verzi 12/10/2015 Stream Analytics
Tato verze obsahuje následující aktualizace:

| Nadpis | Popis |
| --- | --- |
| Aktualizace verze rozhraní REST API |Verze rozhraní REST API se aktualizovala na 2015-10-01. Podrobnosti najdete na webu MSDN na [referenční příručka Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx) a [integrace Machine Learning v Stream Analytics](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md). |
| Integrace Azure Machine Learning |Tato verze obsahuje podporu pro Azure Machine Learning uživatelsky definované funkce. Najdete v článku [kurzu](stream-analytics-machine-learning-integration-tutorial.md) Další informace a taky [obecné blog oznámení](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx). |

## <a name="notes-for-11122015-release-of-stream-analytics"></a>Poznámky k verzi 11. 12/2015 Stream Analytics
Tato verze obsahuje následující aktualizace:

| Nadpis | Popis |
| --- | --- |
| Nové chování vyberte |Vyberte v Stream Analytics se rozšířily umožňující * jako přistupujícího objektu vlastnosti vnořené záznamu. Další informace najdete v tématu [ http://msdn.microsoft.com/library/mt622759.aspx ] (http://msdn.microsoft.com/library/mt622759.aspx "komplexní typy dat"). |

## <a name="notes-for-10222015-release-of-stream-analytics"></a>Poznámky k verzi 22/10/2015 Stream Analytics
Tato verze obsahuje následující aktualizace:

| Nadpis | Popis |
| --- | --- |
| Další dotaz jazykové funkce |Stream Analytics obsahuje dotazovací jazyk rozšiřovat, včetně následujících funkcí: [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [mezní hodnoty](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [PODLAŽÍ](https://msdn.microsoft.com/library/azure/mt605240.aspx), [ NAPÁJENÍ](https://msdn.microsoft.com/library/azure/mt605287.aspx), [PŘIHLAŠOVACÍ](https://msdn.microsoft.com/library/azure/mt605290.aspx), [HRANATÉ](https://msdn.microsoft.com/library/azure/mt605288.aspx), a [SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx). |
| Odebrat agregační omezení |Tato verze odebere omezení 15 agregací v dotazu. Teď neexistuje žádné omezení počtu agregace na jeden dotaz. |
| Funkce skupiny podle System.Timestamp |[GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) funkce teď umožňuje buď window_type nebo [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx). |
| Přidání POSUNUTÍ pro Přeskakujícího a přepínání windows |Ve výchozím nastavení [Přeskakujícího](https://msdn.microsoft.com/library/azure/dn835055.aspx) a [Hopping](https://msdn.microsoft.com/library/azure/dn835041.aspx) windows jsou v souladu s přechodu do režimu (1/1/0001 12:00:00 AM UTC). Nový parametr (volitelné), offsetsize' umožňuje zadání vlastní posun (nebo zarovnání). |

## <a name="notes-for-09292015-release-of-stream-analytics"></a>Poznámky k verzi 29/09/2015 Stream Analytics
Tato verze obsahuje následující aktualizace:

| Nadpis | Popis |
| --- | --- |
| Verzi Public Preview služby Azure IoT Suite |Stream Analytics je zahrnutá ve verzi Public Preview služby Azure IoT Suite. |
| Portál integrace se službou Azure |Kromě trvalá přítomnosti na portálu Azure Stream Analytics je teď plně integrovaný v [portál Azure](https://azure.microsoft.com/overview/preview-portal/). Všimněte si, že funkce Stream Analytics na portálu Preview je aktuálně že podmnožinu funkce nabízené na portálu Azure bez podpory pro testování dotazů v prohlížeči, že Power BI výstup konfigurace a procházením můžete nebo vytvořit nový vstupní a výstupní prostředky v odběry, které máte přístup. |
| Podpora pro výstup Cosmos DB |Úlohy Stream Analytics můžete teď výstupní k [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/). |
| Podpora pro vstup IoT Hub |Úlohy Stream Analytics můžete nyní načítání dat z centra IoT. |
| TIMESTAMP BY pro heterogenní události |Když jeden datový proud obsahuje více typů událostí s časová razítka v různých polí, teď můžete použít [TIMESTAMP BY](http://msdn.microsoft.com/library/mt573293.aspx) s výrazy zadat jiný časové razítko pole pro každý případ. |

## <a name="notes-for-09102015-release-of-stream-analytics"></a>Poznámky k verzi 09/10/2015 Stream Analytics
Tato verze obsahuje následující aktualizace:

| Nadpis | Popis |
| --- | --- |
| Podpora pro PowerBI skupiny |Pokud chcete povolit sdílení dat s dalšími uživateli Power BI, můžete úlohy Stream Analytics teď zapisovat do [PowerBI skupiny](stream-analytics-define-outputs.md#power-bi) uvnitř svůj účet Power BI. |

## <a name="notes-for-08202015-release-of-stream-analytics"></a>Poznámky k verzi 08/20/2015 Stream Analytics
Tato verze obsahuje následující aktualizace:

| Nadpis | Popis |
| --- | --- |
| Přidat poslední – funkce |[Poslední](http://msdn.microsoft.com/library/mt421186.aspx) funkce je nyní k dispozici v úlohy Stream Analytics, umožňuje načíst nejnovější událost v datového proudu událostí v daném časovém rámci. |
| Nové funkce pole |Pole funkce [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) a [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) jsou nyní k dispozici. |
| Nové funkce v záznamu |Zaznamenejte funkce [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) a [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) jsou nyní k dispozici. |

## <a name="notes-for-07302015-release-of-stream-analytics"></a>Poznámky k verzi 07/30/2015 Stream Analytics
Tato verze obsahuje následující aktualizace:

| Nadpis | Popis |
| --- | --- |
| Power BI Org Id odpojené od Azure Id |Tato funkce umožňuje [výstup Power BI](stream-analytics-power-bi-dashboard.md) pro úlohy ASA pod libovolného typu účet Azure (Id organizace nebo Live Id). Kromě toho můžete mít jeden Org Id pro účet Azure a použijte jiný určovat výstup Power BI. |
| Podpora pro výstupní fronty služby Service Bus |[Fronty služby Service Bus](stream-analytics-define-outputs.md#service-bus-queues) výstupy jsou nyní k dispozici v úlohy Stream Analytics. |
| Podpora pro výstup témata služby Service Bus |[Témata služby Service Bus](stream-analytics-define-outputs.md#service-bus-topics) výstupy jsou nyní k dispozici v úlohy Stream Analytics. |

## <a name="notes-for-07092015-release-of-stream-analytics"></a>Poznámky k verzi 07/09/2015 Stream Analytics
Tato verze obsahuje následující aktualizace:

| Nadpis | Popis |
| --- | --- |
| Vlastní Blob výstup dělení |Výstupy úložiště objektů BLOB teď povolit možnost pro určení četnosti tento výstup, které jsou zapsány objekty BLOB a struktura a formát struktura složek cesta výstupní data. |

## <a name="notes-for-05032015-release-of-stream-analytics"></a>Poznámky k verzi 03/05/2015 Stream Analytics
Tato verze obsahuje následující aktualizace:

| Nadpis | Popis |
| --- | --- |
| Zvýšit maximální hodnota pro interval Tolerance pořádku |Maximální velikost pro interval Tolerance pořádku je nyní 59:59 (mm: ss) |
| Výstupní formát JSON: Řádcích nebo pole |Nyní je možnost při výstupu do úložiště objektů Blob nebo Centrum událostí na výstup jako buď pole objektů JSON nebo oddělením objekty JSON s novým řádkem. |

## <a name="notes-for-04162015-release-of-stream-analytics"></a>Poznámky k verzi 04/16/2015 Stream Analytics
| Nadpis | Popis |
| --- | --- |
| Zpoždění v konfiguraci účtu Azure Storage |Při vytváření úlohy Stream Analytics v oblasti poprvé, zobrazí se výzva k vytvoření nového účtu úložiště nebo zadat účet existujícího pro monitorování úlohy Stream Analytics v této oblasti. Z důvodu čekací doby v konfiguraci monitorování, vytváření jiná úloha Stream Analytics ve stejné oblasti v rámci řádku 30 minut pro určení druhého účtu úložiště místo zobrazující nedávno nakonfigurovaný jeden v rozevíracím seznamu monitorování účtu úložiště. Abyste se vyhnuli, vytvoření nepotřebné účtu úložiště, počkejte, než 30 minut po vytvoření úlohy v oblasti poprvé před zřizováním další úlohy v této oblasti. |
| Úlohy upgradu |V tomto okamžiku Stream Analytics nepodporuje živé úprav definici nebo konfiguraci běžící úlohy. Chcete-li změnit vstupní, výstupní, dotaz, škálování nebo konfigurace běžící úlohy, je třeba nejprve ukončit úlohu. |
| Datové typy odvozené ze vstupního zdroje |Pokud příkaz CREATE TABLE se nepoužívá, Vstupní typ je odvozen od formát vstupu, například všechna pole ze souboru CSV jsou řetězec. Pole musí být explicitně převést na správný typ. pomocí funkce CAST, aby se zabránilo chybám Neshoda typu. |
| Chybějící pole jsou výstupem jako hodnoty null |Odkazování na pole, které se nenachází v vstupního zdroje výsledkem hodnot null ve výstupní událost. |
| S příkazy musí předcházet příkazů SELECT |V dotazu musí následovat příkazů SELECT poddotazy definované pomocí příkazů. |
| Problém z důvodu nedostatku paměti |Úloha streamování analýzy velkých toleranci události mimo pořadí nebo složitých dotazů údržbu, že velké množství stavu může způsobit, že tato úloha spustila nedostatek paměti, což vede k úlohu restartujte. Operace zahájení a ukončení jsou viditelné v protokolech úlohy operace. Chcete-li tomu zabránit, škálovat napříč více oddílů dotazu. V budoucí verzi je řešit došlo ke snížení výkonu u ovlivněné úlohy místo restartování, je toto omezení. |
| Vstupy velkých objektů blob bez časového razítka datové části může způsobit problém z důvodu nedostatku paměti |Použití velkých souborů z úložiště objektů Blob může způsobit úlohy Stream Analytics chyby, pokud pole časového razítka není zadaný prostřednictvím TIMESTAMP BY. K tomuto problému vyhnout, mějte velikost jednotlivých objektů blob v části 10 MB. |
| Omezení svazku událostí databáze SQL |Při použití SQL Database jako cíl výstupu, může způsobit velmi velký objem výstupních dat úlohy služby Stream Analytics vypršení časového limitu. Chcete-li tento problém vyřešit, snižte objem výstup pomocí agregací nebo operátory filtru, nebo zvolte úložiště objektů Blob v Azure nebo Event Hubs jako cíl výstupu místo toho. |
| Datové sady PowerBI může obsahovat pouze jednu tabulku |PowerBI nepodporuje více než jedné tabulky v dané datové sadě. |

## <a name="get-help"></a>Podpora
Další podporu naleznete v našem [fóru služby Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
