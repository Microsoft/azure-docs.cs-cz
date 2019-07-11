---
title: Škálovatelnost a výkonnostní - Personalizer
titleSuffix: Azure Cognitive Services
description: 'Vysoce výkonné a navštěvované weby a aplikace mají dva hlavní faktory vzít v úvahu s Personalizer pro zajištění škálovatelnosti a výkonu: latenci a propustnost školení.'
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: edjez
ms.openlocfilehash: 06c2e65c723e18acc515dd7effc61aae0564f411
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722415"
---
# <a name="scalability-and-performance"></a>Škálovatelnost a výkon

Vysoce výkonné a navštěvované weby a aplikace mají dva hlavní faktory vzít v úvahu s Personalizer pro zajištění škálovatelnosti a výkonu:

* Udržování nízkou latenci při volání rozhraní API pořadí
* Ujistěte se, že propustnost školení drží krok s vstupní události

Přizpůsobení můžete velmi rychle vrátit pořadí s většinu doby trvání volání vyhrazený pro komunikaci přes rozhraní REST API. Azure bude automaticky škálovat schopnost rychle reagovat na požadavky.

##  <a name="low-latency-scenarios"></a>Scénáře s nízkou latencí

Některé aplikace vyžadují nízkou latenci při vrácení pořadí. To je nezbytné:

* Chcete-li uživateli zabránit ve čekání na určitou dobu před zobrazením seřazené obsah.
* Abychom server, na kterém dochází k provoz extrémně vyhnout obsadit omezených výpočetní čas a připojení k síti.

<!--

If your web site is scaled on your infrastructure, you can avoid making HTTP calls by hosting the Personalizer API in your own servers running a Docker container.

This change would be transparent to your application, other than using an endpoint URL referring to the running docker instances as opposed to an online service in the cloud.



### Extreme Low Latency Scenarios

If you require latencies under a millisecond, and have already tested using Personalizer via containers, please contact our support team so we can assess your scenario and provide guidance suited to your needs.

-->

## <a name="scalability-and-training-throughput"></a>Škálovatelnost a propustnost školení

Funguje personalizer prostřednictvím aktualizace modelu, který je retrained podle zprávy asynchronně odeslané Personalizer po hodnocení a potřebu rozhraní API. Tyto zprávy se odešlou pomocí centra událostí Azure pro aplikaci.

 Není pravděpodobné, že většina aplikací bude dosáhnout maximální připojení a propustnost Personalizer školení. Při dosažení tohoto maximální nebude ke zpomalení aplikace, by to znamenat, že fronty centra událostí jsou získávání vyplněny interně rychleji, než že je možné vymazat.

## <a name="how-to-estimate-your-throughput-requirements"></a>Jak odhadovat vašim požadavkům na propustnost

* Odhad průměrný počet bajtů na událost pořadí přidávání délky dokumentů JSON kontextu a akce.
* Tento odhad průměrného počtu bajtů dělení 20MB za sekundu.

Například pokud váš průměrný datová část obsahuje 500 funkce a všechny jsou odhadované 20 znaků, pak každá událost představuje přibližně 10kb. Tyto odhady 20000000 / 10 000 = 2 000 událostí za sekundu, což je přibližně 173 milionů událostí za den. 

Pokud jsou překročení těchto omezení, kontaktujte prosím náš tým podpory pro architekturu poradenství.

## <a name="next-steps"></a>Další postup

[Vytvoření a konfigurace Personalizer](how-to-settings.md).