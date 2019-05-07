---
title: Konfigurace nastavení
titleSuffix: Azure Cognitive Services
description: Konfigurace služby obsahuje jak služba zpracovává odměny, popisuje, jak často služby, jak často retrained modelu a jaká data se ukládají.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: bce260d2a6b897d3e45b231df4eecd7ecb31ba31
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143247"
---
# <a name="personalizer-settings"></a>Nastavení personalizer

Konfigurace služby obsahuje jak služba zpracovává odměny, popisuje, jak často služby, jak často retrained modelu a jaká data se ukládají.

## <a name="create-personalizer-resource"></a>Vytvořit prostředek Personalizer

Vytvořte prostředek Personalizer pro každou smyčku zpětné vazby. 

1. Přihlaste se k [portálu Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Předchozí odkaz přejdete na **vytvořit** stránku Personlizer služby. 
1. Zadejte název vaší služby, vyberte předplatné, umístění, cenová úroveň a skupinu prostředků.
1. Vyberte potvrzení a pak vyberte **vytvořit**.

## <a name="configure-service-settings-in-the-azure-portal"></a>Konfigurace nastavení služby na webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Najdete Personalizer prostředku. 
1. V **správy prostředků** vyberte **nastavení**.

    Před byste museli opustit Azure portal, zkopírujte jeden z klíčů prostředků **klíče** stránky. Budete ho potřebovat k použití [Personalizer SDK](https://aka.ms/personalizer-dotnet-sdk-docs).

### <a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>Konfigurace nastavení reward pro smyčku zpětné vazby, který je založený na případu použití

Konfigurace nastavení služby pro použití odměny smyčka zpětné vazby. Změny následujícího nastavení se obnovit aktuální model Personalizer a přeučování s využitím poslední 2 dny dat:

![Konfigurace nastavení reward pro smyčku zpětné vazby](media/settings/configure-model-reward-settings.png)

|Nastavení|Účel|
|--|--|
|Doba čekání rewards|Nastaví dobu, během které Personalizer shromáždí reward hodnoty pořadí volání, od chvíle, kdy pořadí volání se stane. Tato hodnota nastavená požádá: "Jak dlouho má Personalizer čekat pro volání odměny?" Jakékoli reward přicházejících po tomto okně budou protokolovány ale není možné použít u učení.|
|Výchozí rewards|Pokud neobdrží žádná volání reward podle Personalizer během časového intervalu Reward čekací doby přidružený k pořadí volání, Personalizer, přiřadí se výchozí účet. Ve výchozím nastavení a ve většině scénářů výchozí účet je nula.|
|Agregace rewards|Pokud více odměny přijme stejné pořadí rozhraní API volat, tato metoda agregace se používá: **součet** nebo **nejdřívější**. Nejdříve Vybere nejbližší skóre přijata a zbytek zahodí. To je užitečné, pokud chcete potřebu jedinečný mezi možná duplicitní volání. |

Po změně těchto nastavení, je nutné vybrat **Uložit**.

### <a name="exploration-setting"></a>Zkoumání nastavení 

Přizpůsobení je možné zjišťovat nová schémata a reagovat na změny chování uživatelů v čase, ve kterých alternativy. **Zkoumání** nastavení určuje, jaké je procento pořadí volání s zkoumání odpovědi. 

Změnou tohoto nastavení se obnovit aktuální model Personalizer a přeučování s využitím poslední 2 dny dat.

![Zkoumání nastavení určuje, jaké je procento pořadí volání odpovědi s průzkum](media/settings/configure-exploration-setting.png)

Jakmile změníte toto nastavení, je nutné vybrat **Uložit**.

### <a name="model-update-frequency"></a>Četnost aktualizace modelu

**Model frekvenci aktualizací** nastaví, jak často je nový model Personalizer retrained. 

![Četnost aktualizace modelu nastaví, jak často je nový model Personalizer retrained.](media/settings/configure-model-update-frequency-settings.png)

Jakmile změníte toto nastavení, je nutné vybrat **Uložit**.

### <a name="data-retention"></a>Uchovávání dat

**Období uchovávání dat** nastaví, kolik dní uchovává Personalizer protokoly s daty. Za data protokolů jsou požadované k provedení [offline hodnocení](concepts-offline-evaluation.md), které se používají k změřte efektivitu Personalizer a optimalizovat Learning zásad.

Jakmile změníte toto nastavení, je nutné vybrat **Uložit**.

## <a name="export-the-personalizer-model"></a>Export Personalizer modelu

V části Správa prostředků pro **modelu a zásady**zkontrolujte vytvoření modelu a datum poslední aktualizace a exportovat aktuální model.

![Exportovat aktuální model Personalizer](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Import a export zásad učení

V části Správa prostředků pro **modelu a zásady**, nové zásady learning importovat nebo exportovat aktuální zásady učení.

## <a name="next-steps"></a>Další postup

[Jak používat Personalizer kontejneru](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
