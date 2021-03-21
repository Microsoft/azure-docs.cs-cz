---
title: Azure Functions výstup z Azure Stream Analytics
description: Tento článek popisuje službu Azure Functions jako výstup pro Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: e5ea7a1abbbd6ab4be32955179227fbd539cf641
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019614"
---
# <a name="azure-functions-output-from-azure-stream-analytics"></a>Azure Functions výstup z Azure Stream Analytics

Azure Functions je výpočetní služba bez serveru, kterou můžete použít ke spouštění kódu na vyžádání bez nutnosti explicitně zřizovat nebo spravovat infrastrukturu. Umožňuje implementovat kód, který se aktivuje událostmi, ke kterým dochází v Azure nebo v partnerských službách. Tato schopnost Azure Functions reagovat na triggery, vytvoří přirozený výstup pro Azure Stream Analytics. Tento výstupní adaptér umožňuje uživatelům připojení Stream Analytics k Azure Functions a spuštění skriptu nebo části kódu v reakci na nejrůznější události.

Azure Functions výstup z Stream Analytics není k dispozici v oblastech Azure Čína 21Vianet a Azure Německo (mezinárodní systémy). Nepodporují se taky připojení k Azure Functions uvnitř virtuální sítě (VNet) z úlohy Stream Analytics, která běží v clusteru s více klienty.

Azure Stream Analytics vyvolá Azure Functions prostřednictvím triggerů HTTP. Azure Functions výstupní adaptér je k dispozici s následujícími konfigurovatelnými vlastnostmi:

| Název vlastnosti | Description |
| --- | --- |
| Aplikace funkcí |Název vaší aplikace Azure Functions. |
| Funkce |Název funkce v aplikaci Azure Functions. |
| Klíč |Pokud chcete používat funkci Azure Functions z jiného předplatného, můžete to udělat tak, že pro přístup k funkci poskytnete klíč. |
| Maximální velikost dávky |Vlastnost, která umožňuje nastavit maximální velikost pro každou výstupní dávku, která se odesílá do funkce Azure Functions. Vstupní jednotka je v bajtech. Ve výchozím nastavení je tato hodnota 262 144 bajtů (256 KB). |
| Maximální počet dávek  |Vlastnost, která umožňuje určit maximální počet událostí v každé dávce, která je odeslána do Azure Functions. Výchozí hodnota je 100. |

U dávek, které byly úspěšně zpracovány, Azure Stream Analytics očekává stav HTTP 200 z aplikace Functions.

Když Azure Stream Analytics obdrží výjimku ("413 je příliš velká entita požadavku HTTP) z funkce Azure, zmenší velikost balíků, které posílá do Azure Functions. V kódu funkce Azure použijte tuto výjimku k tomu, abyste se ujistili, že Azure Stream Analytics neodesílají dávky s větší velikostí. Také se ujistěte, že maximální počet dávek a hodnoty velikosti použité ve funkci jsou konzistentní s hodnotami zadanými na portálu Stream Analytics.

> [!NOTE]
> Během testovacího připojení Stream Analytics odešle prázdnou dávku, aby Azure Functions testování, pokud propojení mezi nimi funguje. Ujistěte se, že vaše aplikace Functions zpracovává prázdné požadavky na dávky, aby bylo zajištěno, že test připojení projde.

V situaci, kdy není žádné přistání události v časovém intervalu, není vygenerován žádný výstup. V důsledku toho není volána funkce **computeResult** . Toto chování je konzistentní s předdefinovanými agregačními funkcemi v okně.

## <a name="partitioning"></a>Dělení

Klíč oddílu je založen na klauzuli PARTITION BY v dotazu. Počet zapisovačů výstupu následuje za vstupními oddíly pro [plně paralelní dotazy](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Velikost výstupní dávky

Výchozí velikost dávky je 262 144 bajtů (256 KB). Výchozí počet událostí na jednu dávku je 100. Velikost dávky je konfigurovatelná a je možné ji zvýšit nebo snížit v možnostech výstupu Stream Analytics.

## <a name="next-steps"></a>Další kroky

* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí Azure CLI](quick-create-azure-cli.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí šablony ARM](quick-create-azure-resource-manager.md)
* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics v Visual Studio Code](quick-create-visual-studio-code.md)