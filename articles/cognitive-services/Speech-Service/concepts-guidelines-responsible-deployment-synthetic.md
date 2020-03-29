---
title: Pokyny pro odpovědné zavádění syntetické hlasové technologie
titleSuffix: Azure Cognitive Services
description: Obecné pokyny společnosti Microsoft pro používání syntetické hlasové technologie. Ty byly vyvinuty ve studiích, které společnost Microsoft provedla s hlasovým talentem, spotřebiteli, stejně jako jednotlivci s poruchami řeči, aby řídili zodpovědný vývoj syntetického hlasu.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836764"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Pokyny pro odpovědné zavádění syntetické hlasové technologie
Zde jsou obecné pokyny společnosti Microsoft pro použití syntetické hlasové technologie. Ty byly vyvinuty ve studiích, které společnost Microsoft provedla s hlasovým talentem, spotřebiteli, stejně jako jednotlivci s poruchami řeči, aby řídili zodpovědný vývoj syntetického hlasu.

## <a name="general-considerations"></a>Obecné aspekty
Pro nasazení technologie syntetické řeči platí následující pokyny ve většině scénářů.

### <a name="disclose-when-the-voice-is-synthetic"></a>Zveřejnit, když je hlas syntetický
Zveřejnění, že hlas je počítač vytvořený nejen minimalizuje riziko škodlivých výsledků z podvodu, ale také zvyšuje důvěru v organizaci přináší hlas. Přečtěte si další informace o [tom, jak zveřejnit](concepts-disclosure-guidelines.md).

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Výběr vhodných typů hlasu pro váš scénář
Pečlivě zvážit kontext použití a potenciální škody spojené s použitím syntetického hlasu. Syntetické hlasy s vysokou věrností nemusí být například vhodné ve vysoce rizikových scénářích, například pro osobní zasílání zpráv, finanční transakce nebo složité situace, které vyžadují lidskou přizpůsobivost nebo empatii. Uživatelé mohou mít také různá očekávání pro typy hlasu. Například při poslechu citlivých zpráv, které jsou čteny syntetickým hlasem, někteří uživatelé dávají přednost empatičtějšímu a lidskému čtení zpráv, zatímco jiní dávají přednost monotónnějšímu, nezaujatému hlasu. Zvažte testování aplikace, abyste lépe porozuměli uživatelským preferencím.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Buďte transparentní, pokud jde o možnosti a omezení
Uživatelé mají větší pravděpodobnost, že mají vyšší očekávání při interakci s vysoce věrné syntetické hlasové agenty. V důsledku toho, když systémové schopnosti nesplňují tato očekávání, důvěra může trpět a může mít za následek nepříjemné nebo dokonce škodlivé zážitky.

### <a name="provide-optional-human-support"></a>Poskytněte volitelnou lidskou podporu
V nejednoznačných transakčních scénářích (například centrum podpory volání) uživatelé ne vždy důvěřují agentovi počítače, aby odpovídajícím způsobem reagoval na jejich požadavky. Lidská podpora může být v těchto situacích nezbytná, bez ohledu na realistickou kvalitu hlasu nebo schopnosti systému.

## <a name="considerations-for-voice-talent"></a>Důležité informace pro hlasové talenty
Při práci s hlasovým talentem, jako jsou hlasoví herci, na vytvoření syntetických hlasů, platí níže uvedené pokyny.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Získejte smysluplný souhlas od hlasových talentů
Hlasový talent očekává, že bude mít kontrolu nad svým hlasovým písmem (jak a kde bude použit) a bude kompenzován kdykoli v něm bude použit. Vlastníci systému by proto měli získat výslovné písemné povolení od hlasových talentů a mít jasné smluvní specifikace týkající se případů použití, doby používání, kompenzace a tak dále. Některé hlasové talenty si nejsou vědomy potenciálního škodlivého využití technologie a měly by být vzdělávány vlastníky systému o schopnostech této technologie. Pro více informací o hlasový talent a souhlas, přečtěte si naše [zveřejnění pro hlasové talenty](https://aka.ms/disclosure-voice-talent).


## <a name="considerations-for-those-with-speech-disorders"></a>Úvahy pro osoby s poruchami řeči
Při práci s osobami s poruchami řeči, k vytvoření nebo nasazení syntetické hlasové technologie, platí následující pokyny.

### <a name="provide-guidelines-to-establish-contracts"></a>Poskytnout pokyny pro uzavírání smluv
Poskytnout pokyny pro uzavírání smluv s jednotlivci, kteří používají syntetický hlas pro pomoc při mluvení. Smlouva by měla zvážit určení stran, které vlastní hlas, dobu trvání použití, kritéria převodu vlastnictví, postupy pro odstranění hlasového písma a jak zabránit neoprávněnému přístupu. Kromě toho povolte smluvní převod vlastnictví hlasových písem po smrti rodinným příslušníkům, pokud k tomu tato osoba udělila svolení.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Účet pro nekonzistence ve vzorcích řeči
Pro osoby s poruchami řeči, kteří zaznamenávají své vlastní hlasové fonty, mohou nekonzistence v jejich řeči (slurring nebo neschopnost vyslovit určitá slova) komplikovat proces nahrávání. V těchto případech syntetické hlasové technologie a nahrávání relací by měly pojmout (to znamená, že poskytují přestávky a další počet nahrávání relací).

### <a name="allow-modification-over-time"></a>Povolit úpravy v průběhu času
Jedinci s poruchami řeči touha, aby se aktualizace jejich syntetický hlas odrážet stárnutí (například, dítě dosažení puberty). Jednotlivci mohou mít také stylistické předvolby, které se v průběhu času mění, a mohou chtít provést změny výšky, přízvuku nebo jiných hlasových charakteristik.


## <a name="reference-docs"></a>Referenční dokumenty

* [Zveřejnění pro hlasové talenty](https://aka.ms/disclosure-voice-talent)
* [Gating – přehled](concepts-gating-overview.md)
* [Jak zveřejnit](concepts-disclosure-guidelines.md)
* [Zveřejnění návrhových vzorů](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Další kroky

* [Zveřejnění pro hlasové talenty](https://aka.ms/disclosure-voice-talent)
* [Jak zveřejnit](concepts-disclosure-guidelines.md)
* [Zveřejnění návrhových vzorů](concepts-disclosure-patterns.md)
