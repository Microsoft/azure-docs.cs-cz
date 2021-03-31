---
title: Úložiště dat – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS ukládá data zašifrovaná v úložišti dat Azure, která odpovídají oblasti určené klíčem.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: a58bcff4e39c4a4a907cd8567b47b074ff299bd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "97008448"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Ukládání a odebírání dat v Language Understanding (LUIS) Cognitive Services

LUIS ukládá data zašifrovaná v úložišti dat Azure, která odpovídají [oblasti](luis-reference-regions.md) určené klíčem. 

* Data používaná pro výuku modelu, jako jsou entity, záměry a projevy, se uloží v LUIS po dobu života aplikace. Pokud vlastník nebo přispěvatel odstraní aplikaci, tato data se odstraní. Pokud se aplikace nepoužila v 90 dnech, odstraní se. 

* Autoři aplikací se můžou rozhodnout [Povolit protokolování](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning) do projevy, které se odesílají do publikované aplikace. Pokud je povoleno, projevy bude uloženo po dobu 30 dnů a může si ho prohlédnout Autor aplikace. Pokud není protokolování povoleno, když je aplikace publikována, tato data nejsou uložena.

## <a name="export-and-delete-app"></a>Exportovat a odstranit aplikaci
Uživatelé mají plnou kontrolu nad [exportem](luis-how-to-start-new-app.md#export-app) a [odstraněním](luis-how-to-start-new-app.md#delete-app) aplikace. 

## <a name="utterances"></a>Výroky

Projevy může být uložená na dvou různých místech. 

* Během **procesu vytváření obsahu** jsou projevy vytvořeny a uloženy v záměru. Projevy v záměrech se vyžadují pro úspěšnou aplikaci LUIS. Po publikování aplikace a přijetí dotazů na koncovém bodu určuje dotaz QueryString požadavku koncového bodu, `log=false` zda je uložen koncový bod utterance. Pokud je koncový bod uložený, bude se jednat o součást aktivního výukového projevyu, která se nachází v části **Build** na portálu, v části **Kontrola koncového bodu projevy** . 
* Když provedete **kontrolu koncového bodu projevy** a do záměru přidáte utterance, utterance se už neukládá jako součást koncového bodu projevy, který se má zkontrolovat. Přidá se do záměrů aplikace. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Odstranění příkladu projevy z záměru

Odstraňte příklad projevy, který se používá pro školení [Luis](luis-reference-regions.md). Pokud odstraníte příklad utterance z aplikace LUIS, odebere se z webové služby LUIS a nebude k dispozici pro export.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Odstranit projevy v recenzi z aktivního učení

Projevy je možné odstranit ze seznamu uživatelských projevy, který LUIS navrhuje na **[stránce zkontrolovat koncový bod pro projevy](luis-how-to-review-endpoint-utterances.md)**. Odstraněním projevy z tohoto seznamu zabráníte jejich navržení, ale neodstraníte je z protokolů.

Pokud nechcete aktivní výukové projevy, můžete [zakázat aktivní učení](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Zakázáním aktivního učení také zakážete protokolování.

### <a name="disable-logging-utterances"></a>Zakázat protokolování projevy
[Zakázáním aktivního učení](luis-how-to-review-endpoint-utterances.md#disable-active-learning) zakážete protokolování.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Odstranění účtu
Pokud se vám migrace nemigruje, můžete účet odstranit a všechny vaše aplikace se odstraní spolu s příklady projevy a protokolů. Data se uchovávají po dobu 90 dnů před tím, než se účet trvale odstraní.

Odstranění účtu je k dispozici na stránce **Nastavení** . Na stránce **Nastavení** se dostanete tak, že vyberete název svého účtu v pravém horním navigačním panelu.

## <a name="delete-an-authoring-resource"></a>Odstranění prostředku pro vytváření
Pokud jste [migrovali na prostředek pro vytváření obsahu](./luis-migration-authoring.md), odstraní se samotný prostředek z Azure Portal všechny aplikace přidružené k tomuto prostředku a jejich příklady projevy a protokolů. Data se uchovávají po dobu 90 dnů, než se trvale odstraní.    

Pokud chcete prostředek odstranit, otevřete [Azure Portal](https://ms.portal.azure.com/#home) a vyberte prostředek pro vytváření Luis. Přejděte na kartu **Přehled** a klikněte na tlačítko **Odstranit** v horní části stránky. Pak ověřte, že se prostředek odstranil. 

## <a name="data-inactivity-as-an-expired-subscription"></a>Neaktivity dat jako předplatného s vypršenou platností
Pro účely uchovávání a odstranění dat může být neaktivní aplikace LUIS na _uvážení společnosti Microsoft_ považována za předplatné s vypršenou platností. Aplikace se považuje za neaktivní, pokud splňuje následující kritéria za posledních 90 dnů: 

* Na ni se neudělala **žádná** volání.
* Se nezměnilo.
* Nemá přiřazen aktuální klíč.
* Uživatel se k němu neměl přihlašovat.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o exportu a odstraňování aplikace](luis-how-to-start-new-app.md)