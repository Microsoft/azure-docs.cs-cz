---
title: Možnosti architektury uživatelského rozhraní služby Azure Communication Services
titleSuffix: An Azure Communication Services conceptual document
description: Další informace o možnostech rozhraní UI Framework
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5b1aab8b38614249d6b502044b5c4c8170f46b3c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492213"
---
# <a name="ui-framework-capabilities"></a>Možnosti architektury uživatelského rozhraní

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Architektura uživatelského rozhraní služby Azure Communication Services umožňuje sestavovat komunikační prostředí pomocí sady opakovaně použitelných komponent. Tyto komponenty jsou dodávány ve dvou charakterech: **základní** komponenty jsou nejvíce základní stavební kameny prostředí uživatelského rozhraní, zatímco kombinace těchto základních komponent se nazývají **složené** součásti.

## <a name="ui-framework-composite-components"></a>Složené součásti architektury uživatelského rozhraní

| Složený               | Description                                               | Web   | Android | iOS   |
|-------------------------|-----------------------------------------------------------|-------|---------|-------|
| Skupina, která volá složený | Špičkové odchozí volání hlasu a videa pro komunikační služby Azure, které volají používání prostředků návrhu uživatelského rozhraní Fluent. Podporuje volání skupin pomocí ID skupiny služby Azure Communication Services. Složený prvek umožňuje použití volání 1:1 odkazem na identitu komunikačních služeb Azure nebo na telefonní číslo pro veřejnou telefonní síť pomocí telefonního čísla získaného prostřednictvím Azure.                                    | React |  |  |
| Neseparovaný skupinový chat    | Špičkové možnosti chatu pro komunikační služby Azure pomocí prostředků návrhu Fluent UI. Toto prostředí se zaměřuje na doručení jednoduchého konverzačního klienta, který se může připojit k vláknům služby Azure Communication Services. Umožňuje uživatelům odesílat zprávy a zobrazovat přijaté zprávy pomocí indikátorů psaní a čtení. Škáluje se od 1:1 do seskupení scénářů chatu. Podporuje jedno vlákno chatu.                         | React |  |  |

## <a name="ui-framework-base-components"></a>Základní komponenty rozhraní UI Framework

| Komponenta             | Popis                                                                                                                                                                                                                                                                        | Web   | Android | iOS |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------|---------|-----|
| Volání zprostředkovatele    | Základní inicializace součásti pro volání. Požadovaná součást k následnému inicializaci dalších komponent nad ní. Zpracovává základní logiku pro inicializaci volajícího klienta pomocí přístupových tokenů služby Azure Communication Services. Podporuje spojení se skupinami. | React | N/A     | N/A |
| Ovládací prvky multimédií   | Umožňuje uživatelům spravovat aktuální volání přepnutím ztlumení, zapnutím nebo vypnutím videa a ukončením volání.                                                                                                                                                              | React | N/A     | N/A |
| Galerie médií   | Prezentujte všechny účastníky volání v jediné galerii. Galerie podporuje i statické účastníky s podporou videa. Pro účastníky s povoleným videem se vykreslí video.                                                                                                                | React | N/A     | N/A |
| Nastavení mikrofonu | Vyberte mikrofon, který se má použít pro volání. Tento ovládací prvek lze použít před a během volání k výběru zařízení mikrofonu.                                                                                                                                               | React | N/A     | N/A |
| Nastavení kamery     | Vyberte kameru, která se má použít pro volání videa. Tento ovládací prvek lze použít před a během volání k výběru videozařízení.                                                                                                                                             | React | N/A     | N/A |
| Nastavení zařízení     | Kombinuje nastavení mikrofonu a kamery do jediné součásti.                                                                                                 | React | N/A     | N/A |
| Poskytovatel chatu       | Základní inicializace součásti pro chat Požadovaná součást k následnému inicializaci dalších komponent nad ní. Zpracovává základní logiku pro inicializaci chatovacího klienta pomocí přístupového tokenu služby Azure Communication Services a vlákna, ke kterému se připojí.                                     | React | N/A     | N/A |
| Odeslat box          | Vstupní komponenta, která umožňuje uživatelům odesílat zprávy do konverzačního vlákna. Vstup podporuje text, hypertextové odkazy, Emoji a další znaky Unicode, včetně dalších abeced.                                                                                                                         | React | N/A     | N/A |
| Chatovací vlákno           | Komponenta Thread, která zobrazuje uživateli přijaté i odeslané zprávy s informacemi o odesílateli. Vlákno podporuje zadávání ukazatelů a oznámení o čtení. Můžete posouvat Tato vlákna a zkontrolovat historii chatu.
| Seznam účastníků      | Zobrazit všechny účastníky volání nebo konverzačního vlákna jako seznam.  | React | N/A     | N/A |

## <a name="ui-framework-capabilities"></a>Možnosti architektury uživatelského rozhraní

| Funkce                                                             | Skupina, která volá složený | Neseparovaný skupinový chat | Základní komponenty |
|---------------------------------------------------------------------|-------------------------|----------------------|-----------------|
| Připojit se k týmem schůzky                                                  |                         |                      |           
| Spojit týmy – živá událost                                               |                         |                      | 
| Spustit volání VoIP pro uživatele týmů                                       |                         |                      | 
| Připojení týmů k chatu                                           |                         |                      |            
| Připojit se k volání služby Azure Communication Services pomocí ID skupiny                | ✔                      |                      | ✔
| Spuštění volání VoIP jednomu nebo více uživatelům služby Azure Communication Services |                         |                      |           
| Připojit se ke konverzačnímu vláknu služby Azure Communication Services                    |                         | ✔                   | ✔
| Ztlumení a ztlumení volání                                                    | ✔                       |                      | ✔
| Zapnuté/vypnuté video při volání                                                | ✔                       |                      | ✔
| Sdílení obrazovky                                                      | ✔                       |                      | ✔
| Galerie účastníků                                                 | ✔                       |                      | ✔
| Správa mikrofonu                                               | ✔                       |                      | ✔
| Správa kamery                                                   | ✔                       |                      | ✔
| Volat předsálí                                                          |                         |                      | ✔
| Poslat zprávu chatu                                                   |                         | ✔                   |            
| Přijmout zprávu chatu                                                |                         | ✔                   | ✔
| Indikátory psaní                                                   |                         | ✔                   | ✔
| Stvrzenka pro čtení                                                        |                         | ✔                   | ✔
| Seznam účastníků                                                    |                         |                      | ✔


## <a name="customization-support"></a>Podpora přizpůsobení

| Typ součásti            | Motivy     | Layout                                                              | Datové modely |
|---------------------------|------------|---------------------------------------------------------------------|-------------|
| Složená součást       |     N/A    | N/A                                                                 |     N/A     |
| Základní komponenta            |     –    | Rozložení komponent se dá upravovat pomocí externích stylů.         |     –     |


## <a name="platform-support"></a>Podpora platformy

| Sada SDK    | Windows            | macOS                | Ubuntu   | Linux    | Android  | iOS        |
|--------|--------------------|----------------------|----------|----------|----------|------------|
| SADA SDK UŽIVATELSKÉHO ROZHRANÍ | Chrome \* , nová hrana | Chrome \* , Safari\*\* | Chrome\* | Chrome\* | Chrome\* | Prohlížeče\*\* |

\*Všimněte si, že kromě předchozích dvou verzí je podporována nejnovější verze Chrome.

\*\*Upozorňujeme, že verze Safari 13.1 + jsou podporované. Odchozí video pro Safari macOS ještě není podporované, ale podporuje se v iOS. Sdílení odchozí obrazovky se podporuje jenom v desktopové iOS.
