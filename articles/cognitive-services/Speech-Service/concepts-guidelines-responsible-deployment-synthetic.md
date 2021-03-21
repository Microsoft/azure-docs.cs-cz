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
ms.openlocfilehash: 3a0b645acd7c21ff0416c748cdd2caf7041be508
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730809"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Pokyny pro zodpovědnost nasazení syntetické hlasové technologie

V tomto článku se dozvíte o obecných pokynech pro návrh Microsoftu pro používání syntetické hlasové technologie. Tyto pokyny byly vyvinuty ve studiích, které společnost Microsoft provedla s talentůmi, spotřebiteli a jednotlivci, kteří mají na starosti zodpovědný vývoj syntetických hlasů.

Pro nasazení syntetické technologie řeči se ve většině scénářů platí následující pokyny.

### <a name="disclose-when-the-voice-is-synthetic"></a>Rozvírat po syntetickém hlasu
V případě, že hlas je vygenerovaný počítačem, je vygenerována nejen minimalizace rizika škodlivých výsledků, ale také zvyšuje důvěryhodnost v organizaci, která hlas poskytuje. Přečtěte si další informace o [tom, jak zveřejnit](concepts-disclosure-guidelines.md).

Společnost Microsoft vyžaduje, aby její zákazníci uzavřeli syntetickou povahu vlastního neuronové hlasu svým uživatelům. 
* Ujistěte se, že máte k dispozici dostatečné informace pro cílové skupiny, zejména v případě, že používáte hlas známek známých osob, na základě informací o osobě, která ji doručuje, ať už je v vědomí nebo nevědomá.  Zveřejnění může být například na začátku všesměrového vysílání sdíleno. Další informace najdete v části [vzory zveřejnění](concepts-disclosure-patterns.md).   
* Zvažte správné zpřístupnění rodičům nebo jiným stranám v případech použití, které jsou určeny pro mladistvé a podřízené položky – Pokud je váš případ použití určený pro mladistvé nebo podřízené objekty, budete muset zajistit, aby rodiče nebo právní strážce dokázali porozumět informacím o používání syntetického média a učinit správné rozhodnutí pro nezletilých nebo podřízených, ať už se jedná o používání prostředí. 

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Vyberte vhodné typy hlasu pro váš scénář.
Pečlivě zvažte kontext použití a potenciální poškození související s používáním syntetického hlasu. Například vysoce věrné syntetické hlasy nemusí být vhodné ve vysoce rizikových scénářích, například pro osobní zasílání zpráv, finanční transakce nebo složité situace, které vyžadují lidskou přizpůsobivost nebo soucit. 

Uživatelé mohou mít také různá očekávání pro typy hlasu. Například při poslechu citlivých zpráv čtených syntetickým hlasem si někteří uživatelé upřednostňují další empatického a lidský tón, zatímco jiné upřednostňují nevyvážený hlas. Zvažte testování aplikace pro lepší pochopení uživatelských předvoleb.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Mít transparentní informace o možnostech a omezeních
Uživatelé mají větší očekávání při komunikaci s vysoce věrnými syntetickými hlasovými agenty. Když systémové možnosti tyto očekávání nesplňují, může to mít za následek nespokojenost nebo dokonce škodlivá prostředí.

### <a name="provide-optional-human-support"></a>Poskytněte volitelnou podporu pro člověka.
V nejednoznačných transakčních scénářích (například na portálu pro podporu volání) uživatelé nikdy nedůvěřují agentovi počítače, aby na jejich požadavky správně reagovali. V těchto situacích může být nutná lidská podpora, a to bez ohledu na realistickou kvalitu hlasu nebo schopnosti systému.

## <a name="considerations-for-voice-talent"></a>Předpoklady pro hlasový talentů
Pokud při práci se službou Voice talentů, jako jsou třeba hlasové objekty Actors, vytváříte syntetické hlasy, platí níže uvedená směrnice.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Získání smysluplného souhlasu ze služby Voice talentů
Hlasový zkušeností by měl mít kontrolu nad svým hlasovým modelem (jak a kde se bude používat) a bude kompenzovat jeho použití. Společnost Microsoft vyžaduje, aby zákazníci s vlastním hlasem získali explicitní zapsané oprávnění ze svých hlasových talentůů, aby mohli vytvořit syntetický hlas, a jeho smlouvu se šablonou Voice zkušeností pro dobu trvání, používání a omezení obsahu.  Pokud vytváříte syntetický hlas známé osoby, měli byste určit způsob, jakým může osoba za hlasem upravovat nebo schvalovat obsah.

Některé hlasové zkušenostíy nevědí o potenciálních škodlivých použitích technologií a měly by být určené pro vlastníky systému na schopnosti technologie. Společnost Microsoft vyžaduje, aby zákazníci sdíleli [poskytování hlasu společnosti Microsoft pro hlasové talentů](/legal/cognitive-services/speech-service/disclosure-voice-talent) pomocí hlasových talentůů přímo nebo prostřednictvím autorizovaného zástupce hlasového talentůu, který popisuje, jak se syntetické hlasy vyvíjí a pracují ve spojení s textem pro služby řeči.

## <a name="considerations-for-those-with-speech-disorders"></a>Požadavky na ty s poruchami řeči
Při práci s jednotlivými uživateli s poruchami řeči, aby mohli vytvářet nebo nasazovat syntetickou hlasovou technologii, platí následující pokyny.

### <a name="provide-guidelines-to-establish-contracts"></a>Poskytněte pokyny k navázání smluv.
Poskytněte pokyny pro navázání smluv s jednotlivci, kteří používají syntetické hlasy pro pomoc při mluvení. Kontrakt by měl zvážit určení stran, které vlastní hlas, dobu trvání použití, kritéria přenosu vlastnictví, postupy pro odstranění hlasového písma a zabránění neoprávněnému přístupu. Kromě toho povolte přenos vlastnictví hlasového písma po smrti rodinným příslušníkům, pokud bylo uděleno oprávnění.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Účet pro nekonzistence ve vzorcích pro rozpoznávání řeči
Pro jednotlivce, kteří mají zaznamenaná vlastní hlasová písma, můžou nekonzistence ve vzorku řeči (zpracování nebo neschopnost vyslovit určitá slova) zkomplikovat proces zaznamenávání. V těchto případech by se na ně měli zajišťovat syntetická hlasová technologie a relace záznamu (tj. přerušit a další počet relací záznamu).

### <a name="allow-modification-over-time"></a>Povolení změny v průběhu času
Uživatelé s poruchami v rozpoznávání řeči chtějí dělat aktualizace jejich syntetického hlasu, aby odrážely jejich splatnost (například dítě dosáhne puberty). Jednotlivci také můžou mít stylistické předvolby, které se v průběhu času mění a můžou chtít udělat změny v rámci sklonu, zvýraznění nebo dalších hlasových charakteristik.


## <a name="see-also"></a>Viz také

* [Zveřejnění pro hlasový talentů](/legal/cognitive-services/speech-service/disclosure-voice-talent?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)
* [Jak zveřejnit](concepts-disclosure-guidelines.md)
* [Vzory návrhu zpřístupnění](concepts-disclosure-patterns.md)