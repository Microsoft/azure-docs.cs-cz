---
title: Pokyny pro zodpovědnost nasazení syntetické hlasové technologie
titleSuffix: Azure Cognitive Services
description: Obecné pokyny pro návrh pro používání syntetické hlasové technologie společnosti Microsoft. Tyto aplikace byly vyvinuty v studiích, které společnost Microsoft provedla s hlasovým talentů, s využitím spotřebitelů i s využitím hlasových poruch, aby se mohli řídit odpovědný vývoj syntetického
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "73836764"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Pokyny pro zodpovědnost nasazení syntetické hlasové technologie
Tady jsou obecné pokyny pro návrh Microsoftu pro používání syntetické hlasové technologie. Tyto aplikace byly vyvinuty v studiích, které společnost Microsoft provedla s talentůmi hlasem, spotřebiteli a také jednotlivcům s poruchami řeči.

## <a name="general-considerations"></a>Obecné aspekty
Pro nasazení syntetické technologie řeči se ve většině scénářů platí následující pokyny.

### <a name="disclose-when-the-voice-is-synthetic"></a>Rozvírat po syntetickém hlasu
V případě, že hlas je vygenerovaný počítačem, je vygenerována nejen minimalizace rizika škodlivých výsledků, ale také zvyšuje důvěryhodnost v organizaci, která hlas poskytuje. Přečtěte si další informace o [tom, jak zveřejnit](concepts-disclosure-guidelines.md).

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Vyberte vhodné typy hlasu pro váš scénář.
Pečlivě zvažte kontext použití a potenciální poškození související s používáním syntetického hlasu. Například vysoce věrné syntetické hlasy nemusí být vhodné ve vysoce rizikových scénářích, například pro osobní zasílání zpráv, finanční transakce nebo složité situace, které vyžadují lidskou přizpůsobivost nebo soucit. Uživatelé mohou mít také různá očekávání pro typy hlasu. Například při naslouchání citlivých zpráv, které jsou čteny syntetickým hlasem, někteří uživatelé upřednostňují další empatického a podobné čtení zpráv, zatímco ostatní jsou upřednostňovány ještě více monotone, nevyváženého hlasu. Zvažte testování aplikace pro lepší pochopení uživatelských předvoleb.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Mít transparentní informace o možnostech a omezeních
Uživatelé mají větší očekávání při komunikaci s vysoce věrnými syntetickými hlasovými agenty. V důsledku toho platí, že pokud systémové možnosti tyto očekávání nesplňují, může to mít za následek nepříjemný nebo dokonce škodlivou činnost.

### <a name="provide-optional-human-support"></a>Poskytněte volitelnou podporu pro člověka.
V nejednoznačných transakčních scénářích (například na portálu pro podporu volání) uživatelé nikdy nedůvěřují agentovi počítače, aby na jejich požadavky správně reagovali. V těchto situacích může být nutná lidská podpora, a to bez ohledu na realistickou kvalitu hlasu nebo schopnosti systému.

## <a name="considerations-for-voice-talent"></a>Předpoklady pro hlasový talentů
Pokud při práci se službou Voice talentů, jako jsou třeba hlasové objekty Actors, vytváříte syntetické hlasy, platí níže uvedená směrnice.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Získání smysluplného souhlasu ze služby Voice talentů
Hlasový talentů očekává, že má ovládací prvek pro hlasová písma (jak a kde se bude používat) a že se bude na jejich použití kompenzovat kdykoli. Vlastníci systému by proto měli získat explicitní zapsané oprávnění ze služby Voice talentů a mít jasné smluvní specifikace pro případy použití, dobu trvání použití, kompenzaci atd. Některé hlasové talentůy nevědí o potenciálních škodlivých použitích technologií a měly by být určené pro vlastníky systému na schopnosti technologie. Pokud chcete získat další informace o talentů a souhlasech, přečtěte si naše [zveřejnění pro Voice talentů](https://aka.ms/disclosure-voice-talent).


## <a name="considerations-for-those-with-speech-disorders"></a>Požadavky na ty s poruchami řeči
Při práci s jednotlivými uživateli s poruchami řeči, aby mohli vytvářet nebo nasazovat syntetickou hlasovou technologii, platí následující pokyny.

### <a name="provide-guidelines-to-establish-contracts"></a>Poskytněte pokyny k navázání smluv.
Poskytněte pokyny pro navázání smluv s jednotlivci, kteří používají syntetické hlasy pro pomoc při mluvení. Kontrakt by měl zvážit určení stran, které vlastní hlas, dobu trvání použití, kritéria přenosu vlastnictví, postupy pro odstranění hlasového písma a zabránění neoprávněnému přístupu. Navíc můžete povolit smluvní přenos vlastnictví hlasového písma po smrti rodinným příslušníkům, pokud tato osoba má udělené oprávnění.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Účet pro nekonzistence ve vzorcích pro rozpoznávání řeči
Pro jednotlivce, kteří mají zaznamenaná vlastní hlasová písma, můžou nekonzistence ve vzorku řeči (zpracování nebo neschopnost vyslovit určitá slova) zkomplikovat proces zaznamenávání. V těchto případech by se na ně měli zajišťovat syntetická hlasová technologie a relace záznamu (tj. přerušit a další počet relací záznamu).

### <a name="allow-modification-over-time"></a>Povolení změny v průběhu času
Uživatelé s poruchami v rozpoznávání řeči chtějí dělat aktualizace jejich syntetického hlasu, aby odrážely jejich splatnost (například dítě dosáhne puberty). Jednotlivci také můžou mít stylistické předvolby, které se v průběhu času mění a můžou chtít udělat změny v rámci sklonu, zvýraznění nebo dalších hlasových charakteristik.


## <a name="reference-docs"></a>Referenční dokumenty

* [Zveřejnění pro hlasový talentů](https://aka.ms/disclosure-voice-talent)
* [Přehled o uzavírání](concepts-gating-overview.md)
* [Jak zveřejnit](concepts-disclosure-guidelines.md)
* [Vzory návrhu zpřístupnění](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Další kroky

* [Zveřejnění pro hlasový talentů](https://aka.ms/disclosure-voice-talent)
* [Jak zveřejnit](concepts-disclosure-guidelines.md)
* [Vzory návrhu zpřístupnění](concepts-disclosure-patterns.md)
