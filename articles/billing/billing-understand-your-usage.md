---
title: Pochopení vašeho využití Azure podrobné | Dokumentace Microsoftu
description: Zjistěte, jak si a porozuměli jim části Podrobné využití sdíleného svazku clusteru u předplatného Azure
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: 88518bee879d01966813fc89e8095d2237476a15
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54901305"
---
# <a name="understand-terms-on-your-microsoft-azure-detailed-usage-charges"></a>Vysvětlení podmínek vašeho Microsoft Azure podrobných poplatků za využití 

Podrobné informace o použití poplatky sdíleného svazku clusteru obsahuje poplatky za využívání úrovně denně a měřiče pro aktuální fakturační období. 

Chcete-li získat podrobné informace o použití souboru, naleznete v tématu [jak získat Azure billing invoice a denní data o využití](billing-download-azure-invoice-daily-usage-date.md).
Je k dispozici ve formátu souboru hodnot oddělených čárkami (CSV), které můžete otevřít v aplikaci tabulky. Pokud se zobrazí dvě dostupné verze, stáhněte si verzi 2. To je aktuální formát souboru.

Poplatky za využívání jsou celkové **měsíční** poplatky za využívání předplatného. Poplatky za využívání není vezměte v úvahu všechny kredity a slevy.

>[!VIDEO https://www.youtube.com/embed/p13S350M2Vk]

## <a name="detailed-terms-and-descriptions-of-your-detailed-usage-file"></a>Podrobné podmínky a popisy podrobné informace o využití souboru

Následující části popisují důležité podmínky uvedené ve verzi 2 podrobné informace o použití souboru.

### <a name="statement"></a>Výraz

V horní části souboru CSV podrobné informace o využití obsahuje seznam služeb, které jste použili během fakturačního období v měsíci. V následující tabulce jsou uvedeny podmínky a popisy, které jsou uvedené v této části.

| Označení | Popis |
| --- | --- |
|Fakturační období |Fakturační období, kdy byly použity měřiče |
|Kategorie měření |Identifikuje službu nejvyšší úrovně za využití |
|Podkategorie měření |Definuje typ služeb Azure, který může mít vliv na sazbu |
|Název měření |Určuje měrnou jednotku pro měření spotřebovávanou |
|Oblast měření |Určuje polohu datacentra pro určité služby, které se počítají na základě umístění datového centra |
|Skladová jednotka (SKU) |Určuje jedinečný identifikátor systému pro každou měřičů Azure |
|Jednotka |Určuje jednotky, ve kterých se služba účtuje. Například GB, hodiny, 10 000 operací s. |
|Spotřebované množství |Množství měřiče použít během fakturačního období |
|Zahrnuté množství |Množství měřiče, které je zdarma v aktuálním fakturačním období |
|Překročené množství |Ukazuje rozdíl mezi spotřebované množství a zahrnuté množství. Tuto částku vám budeme fakturovat. U nabídek s průběžnými platbami se žádné zahrnuté množství s nabídkou je tento součet stejný jako spotřebované množství. |
|V rámci závazku |Ukazuje náklady měřiče, které jsou odečtena od výše závazku spojeného s nabídkou na 6 nebo 12 měsíců. Poplatky za měřiče odečtení v chronologickém pořadí. |
|Měna |Měna použitá v aktuálním fakturačním období |
|Překročení |Zobrazuje poplatky za měřiče, které překračují výši závazku spojeného s nabídkou na 6 nebo 12 měsíců |
|Sazba závazku |Ukazuje sazbu závazku založenou na celkové výši závazku spojeného s nabídkou na 6 nebo 12 měsíců |
|Sazba |Sazba se účtuje za fakturovatelnou jednotku |
|Hodnota |Zobrazuje výsledek součinu sloupce Nadlimitní množství sloupcem sazba. Pokud spotřebované množství nepřekročí zahrnuté množství, neplatí žádné poplatky v tomto sloupci. |

### <a name="daily-usage"></a>Denní využívání

V části denní využívání ze souboru CSV se zobrazí podrobnosti o použití, které ovlivňují fakturační sazby. V následující tabulce jsou uvedeny podmínky a popisy, které jsou uvedené v této části.

| Označení | Popis |
| --- | --- |
|Datum využití |Datum, kdy byl použit měřič |
|Kategorie měření |Identifikuje službu nejvyšší úrovně, pro které se využití týká. |
|ID měření |Identifikátor fakturovaného měření, který slouží k určení ceny fakturovaného využití |
|Podkategorie měření |Definuje typ služeb Azure, který může mít vliv na sazbu |
|Název měření |Určuje měrnou jednotku pro měření spotřebovávanou |
|Oblast měření |Určuje polohu datacentra pro určité služby, které se počítají na základě umístění datového centra |
|Jednotka |Určuje jednotky, ve kterých měřič účtuje. Například GB, hodiny, 10 000 operací s. |
|Spotřebované množství |Množství měřič spotřebované za příslušný den |
|Umístění prostředku |Určuje datové centrum, ve kterém běží měřič |
|Spotřebovaná služba |Služby platformy Azure, který jste použili |
|Skupina prostředků |Skupina prostředků, ve kterém je spuštěn měřič nasazené v. <br/><br/>Další informace naleznete v tématu [Přehled Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
|ID instance | Identifikátor pro měřidla. <br/><br/> Identifikátor obsahuje název, který zadáte pro měřidla při vytvoření rovnou uložil. Je název prostředku, nebo plně kvalifikované ID prostředku. Další informace najdete v tématu [API Azure Resource Manageru](https://docs.microsoft.com/rest/api/resources/resources). |
|Značky | Značka, která přiřadíte k měřidla. Použití značek k seskupení záznamů fakturace.<br/><br/>Například můžete použít značky distribuovat náklady podle oddělení, které používá měřidla. Jsou služby generování značek podporují virtuální počítače, úložiště a síťové služby zřízené pomocí [API Azure Resource Manageru](https://docs.microsoft.com/rest/api/resources/resources). Další informace najdete v tématu [uspořádání prostředků Azure pomocí značek](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/). |
|Další informace |Metadata konkrétních služeb. Například typ image u virtuálního počítače. |
|Informace o službě 1 |Název projektu, který služba patří v rámci předplatného |
|Informace o službě 2 |Starší verze pole, které zachycuje volitelná metadata konkrétních služeb |

## <a name="how-do-i-make-sure-that-the-charges-in-my-detailed-usage-file-are-correct"></a>Jak jsem se ujistit, že poplatky v podrobné informace o použití souboru jsou správné?
Pokud v souboru podrobné informace o využití, která chcete podrobnosti na poplatek, přečtěte si téma [vysvětlení vašeho vyúčtování služeb Microsoft Azure.](./billing-understand-your-bill.md)

## <a name="external"></a>A co poplatků za externí služby?
Externí služby (označované také jako Marketplace objednávky) jsou k dispozici služba nezávislá dodavatelé a služby se účtují zvlášť. Poplatky se nezobrazují na faktuře Azure. Další informace najdete v tématu [vysvětlení Azure poplatků za externí služby](billing-understand-your-azure-marketplace-charges.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
