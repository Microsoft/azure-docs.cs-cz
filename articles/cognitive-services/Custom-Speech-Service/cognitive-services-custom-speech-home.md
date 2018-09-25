---
title: Přehled vlastních Speech Service v Azure | Dokumentace Microsoftu
description: Custom Speech Service je Cloudová služba, která umožňuje uživatelům přizpůsobit modely řeči pro určené k transkripci řeči na text.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/07/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: da88989753069f7ba8ca2c2e2806a648f3df4e3c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948362"
---
# <a name="what-is-custom-speech-service"></a>Co je Custom Speech Service?

Custom Speech Service je Cloudová služba, která poskytuje uživatelům možnost přizpůsobit si modely řeči pro určené k transkripci řeči na Text.
Pomocí služby Custom Speech Service, najdete v tématu [vlastní Speech Service Portal](https://cris.ai).

Custom Speech Service umožňuje vytvářet vlastní jazykové modely a akustických modelů přizpůsobená pro vaši aplikaci a uživatele. Tím, že nahrajete váš konkrétní řeči a/nebo textových dat pro Custom Speech Service, můžete vytvořit vlastní modely, které lze použít ve spojení s existující modely řeči stavu moderní společnosti Microsoft.

Například pokud přidáváte hlasové interakce na mobilním telefonu, tabletu nebo počítači aplikace, můžete vytvořit vlastního jazykového modelu, který je možné kombinovat s akustický model od Microsoftu k vytvoření koncového bodu převodu řeči na text určený speciálně pro vaši aplikaci. Pokud vaše aplikace je navržen pro použití v konkrétním prostředí nebo prostřednictvím naplnění určitého uživatele, můžete také vytvořit a nasadit vlastního akustického modelu s touto službou.


## <a name="how-do-speech-recognition-systems-work"></a>Jak systémy rozpoznávání řeči funguje?
Systémy rozpoznávání řeči se skládají z několika komponent, které vzájemně spolupracují. Jsou dva nejdůležitější součástí akustický model a jazykový model.

Akustický model je klasifikátor, který označuje krátké zvukové fragmenty do jednoho několika fonémů, nebo zvukových jednotek v daném jazyce. Například slovo "speech" je tvořeno čtyřmi fonémy "s p iy ch". Tyto klasifikace se provádějí řádově stokrát za sekundu.

Jazykový model je pravděpodobnostní rozdělení přes posloupnosti slov. Jazykový model pomáhá systému zvolit takovou posloupnost slov, která zní podobně, a to na základě pravděpodobnosti jednotlivých posloupností slov. Například posloupnosti „recognize speech“ a „wreck a nice beach“ znějí podobně, ale výskyt první z nich je mnohem pravděpodobnější, takže jí bude jazykovým modelem přiřazené vyšší ohodnocení.

Jak akustických a jazykových modelů jsou statistických modelů zjistili díky spolupráci trénovací data. V důsledku toho fungují nejlépe při rozpoznávání řeči, které mohou nastat při použití v aplikacích jsou podobná datům, během kterých se zjistí během cvičení. Akustických a jazykových modelů v modulu Microsoft Speech-To-Text byl trénovaných na obrovském kolekce pro zpracování řeči a text a poskytují výkon stavu techniky pro nejběžnější scénáře použití, jako je například interakci s Cortanou, na vaší smart telefonu, tabletu nebo PC, hlasové dotazy vyhledávání na webu nebo diktování příteli textové zprávy.

## <a name="why-use-the-custom-speech-service"></a>Proč používat služby Custom Speech Service?
Modul Microsoft řeči na Text je prvotřídní, je zaměřeny na scénáře popsané výše. Ale pokud očekáváte, že hlasové dotazy na vaši aplikaci budou obsahovat určité slovníkové položky, jako jsou názvy produktů nebo žargonu, ke které dochází jen zřídka v běžné řeči, je pravděpodobné, že můžete získat lepší výkon, že úpravou jazykového modelu.

Pokud například vytváříte aplikaci umožňující hlasové dotazy na web MSDN, je pravděpodobné, že termíny jako „object-oriented“, „namespace“ nebo „dot net“ se budou vyskytovat častěji než v klasických hlasových aplikacích. Přizpůsobení jazykového modelu umožní systému se to naučit.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak pomocí služby Custom Speech Service, najdete v článku [Custom Speech portálu služby] (https://cris.ai).

* [Začínáme](cognitive-services-custom-speech-get-started.md)
* [Nejčastější dotazy](cognitive-services-custom-speech-faq.md)
* [Glosář](cognitive-services-custom-speech-glossary.md)
