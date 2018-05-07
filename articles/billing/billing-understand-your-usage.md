---
title: Porozumět Azure podrobné používání
description: Zjistěte, jak ke čtení a pochopení v částech vaší podrobné informace o použití sdíleného svazku clusteru pro vaše předplatné Azure
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: 0f698bff1506484e1dd4afe22f7c14e0a710937c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="understand-terms-on-your-microsoft-azure-detailed-usage-charges"></a>Pochopit podmínky na vaše poplatky Azure podrobné informace o použití Microsoftu 

Soubor CSV poplatky podrobné informace o použití obsahuje denně a měření poplatky za používání úrovně pro aktuální fakturační období. 

Chcete-li získat podrobné informace o použití souboru, přečtěte si téma [jak získat vaše Azure fakturace faktury a denní data o využití](billing-download-azure-invoice-daily-usage-date.md).
Je k dispozici ve formátu souboru hodnot oddělených čárkami (.csv), který můžete otevřít v aplikaci tabulky. Pokud se zobrazují dvě verze, které jsou k dispozici, stáhněte si verze 2. Který je formátem aktuálního souboru.

Poplatky za používání jsou celkový počet **měsíční** poplatky na předplatném. Poplatky za používání nemáte vzít v úvahu žádné kredity ani slevy.

>[!VIDEO https://www.youtube.com/embed/p13S350M2Vk]

## <a name="detailed-terms-and-descriptions-of-your-detailed-usage-file"></a>Podrobné podmínky a popisy podrobné informace o použití souboru

Následující části popisují důležité termíny ukazuje verze 2 podrobné informace o použití souboru.

### <a name="statement"></a>Výraz

V horní části ze souboru CSV podrobné informace o použití zobrazí služby, které jste použili při v měsíci fakturačního období. V následující tabulce jsou uvedeny podmínky a popisy uvedené v této sekci.

| Označení | Popis |
| --- | --- |
|Fakturační období |Fakturační období, kdy byly použity měřidla |
|Kategorie měření |Identifikuje nejvyšší úrovně služby využití |
|Podkategorie měření |Definuje typ služba Azure, která může mít vliv na rychlost |
|Název měření |Identifikuje pro měření spotřebovávanou měrné jednotky |
|Oblast měření |Určuje umístění datového centra pro určité služby, které jsou za cenu na základě umístění datového centra |
|Skladová jednotka (SKU) |Určuje identifikátor jedinečný systému pro každou Azure měření |
|Jednotka |Určuje jednotky, ve kterých se služba účtuje. Například GB, hodin, 10 000 s. |
|Spotřebované množství |Množství měření používá během fakturačního období |
|Zahrnuté množství |Množství monitorování, která je zahrnutá ve vaší aktuální fakturační období bez poplatku |
|Překročené množství |Ukazuje rozdíl mezi množství spotřebované a množství zahrnuty. Fakturuje se toto množství. Průběžné platby nabídky se žádné zahrnuté množství s nabídkou tato hodnota je stejný jako objemu spotřebovaného. |
|V rámci závazku |Zobrazuje měření poplatky, které jsou odečten od vaší závazků částky přidružené vaši nabídku 6 nebo 12 měsíců. Měřicí poplatky jsou odčítat v chronologickém pořadí. |
|Měna |Měny používanou ve vaší aktuální fakturační období |
|Překročení |Zobrazuje měření poplatky, které překračují velikost vašeho závazků přidružené vaši nabídku 6 nebo 12 měsíců |
|Sazba závazku |Zobrazuje míru závazků založenou na velikosti celkový závazků přidružené vaši nabídku 6 nebo 12 měsíců |
|Sazba |Rychlost, kterou jste se vám účtovat na jednotku fakturovatelného času |
|Hodnota |Zobrazuje výsledek násobení sloupci Nadlimitní množství podle sloupce míry. Pokud množství spotřebované není překročit množství zahrnuty, je bezplatná v tomto sloupci. |

### <a name="daily-usage"></a>Denní využití

Denní využití části ze souboru CSV se zobrazují podrobnosti o použití, které ovlivňují fakturační sazby. V následující tabulce jsou uvedeny podmínky a popisy uvedené v této sekci.

| Označení | Popis |
| --- | --- |
|Datum využití |Datum, kdy byl použit měřidla |
|Kategorie měření |Identifikuje nejvyšší úrovně služby, pro kterou toto použití patří |
|ID měření |Identifikátor fakturovaná měření, který se používá k cena fakturace využití |
|Podkategorie měření |Definuje typ služby Azure, který může mít vliv na rychlost |
|Název měření |Identifikuje pro měření spotřebovávanou měrné jednotky |
|Oblast měření |Určuje umístění datového centra pro určité služby, které jsou za cenu na základě umístění datového centra |
|Jednotka |Identifikuje jednotka, ve které je účtován měřidla v. Například GB, hodin, 10 000 s. |
|Spotřebované množství |Množství monitorování, které bylo spotřebováno pro daný den |
|Umístění prostředku |Identifikuje datacenter, kde je spuštěna měřidla |
|Spotřebovaná služba |Služba Azure platform, který jste použili |
|Skupina prostředků |Skupinu prostředků, ve které je nasazený měření spuštěný v. <br/><br/>Další informace naleznete v tématu [Přehled Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
|ID instance | Identifikátor pro měřidla. <br/><br/> Identifikátor obsahuje název, který jste zadali pro měřidla v okamžiku vytvoření. Je buď v názvu prostředku nebo plně kvalifikovaný ID prostředku. Další informace najdete v tématu [rozhraní API služby Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). |
|Značky | Značky, který přiřadíte měřidla. Použití značek k seskupení záznamů fakturace.<br/><br/>Můžete například značky k distribuci náklady oddělení, který používá měřidla. Služby, které podporují generování značek jsou virtuální počítače, úložiště a síťové služby, které jsou zajišťované pomocí [rozhraní API služby Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). Další informace najdete v tématu [uspořádání prostředků Azure pomocí značek](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/). |
|Další informace |Metadata specifická pro služby. Například bitové kopie typ pro virtuální počítač. |
|Informace o službě 1 |Název projektu, který služba patří vaše předplatné |
|Informace o službě 2 |Starší verze pole, které jsou zaznamenány volitelná metadata specifickou pro službu |

## <a name="how-do-i-make-sure-that-the-charges-in-my-detailed-usage-file-are-correct"></a>Jak mohu učinit se, že poplatky v mé podrobné informace o použití souboru jsou správné?
Pokud je v souboru podrobné informace o použití, která chcete podrobnosti na zpoplatněny, přečtěte si téma [porozumět vaší faktuře pro Microsoft Azure.](./billing-understand-your-bill.md)

## <a name="external"></a>Co externí poplatky za služby?
Externích služeb (také označované jako Marketplace objednávky) jsou k dispozici nezávislé služby dodavatelé a se účtují samostatně. Poplatků nezobrazovat Azure faktury. Další informace najdete v tématu [pochopit Azure externí poplatky za](billing-understand-your-azure-marketplace-charges.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?) získat rychle vyřešit problém.
