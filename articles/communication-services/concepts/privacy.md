---
title: Dostupnost oblasti a umístění dat pro komunikační služby Azure
description: Informace o zajímání dat a aspektech souvisejících s ochranou osobních údajů na komunikačních službách Azure
author: chpalm
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 10/03/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4e9ea1ec85067a59c5ee6bcd27993e294559f410
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232082"
---
# <a name="region-availability-and-data-residency"></a>Rezidence dat a dostupnost oblastí

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Komunikační služby Azure se zavazují, aby naši zákazníci mohli splnit požadavky na ochranu osobních údajů a osobní údaje. Jako vývojář, který používá komunikační služby s přímým vztahem na lidi, kteří používají aplikaci, jste si potenciálně kontrolují svá data. Vzhledem k tomu, že služba Azure Communication Services ukládá a šifruje tato data v klidovém znění, je nejpravděpodobnější procesor těchto dat. Tato stránka shrnuje, jak služba uchovává data a jak můžete tato data identifikovat, exportovat a odstranit.

## <a name="data-residency"></a>Rezidence dat

Při vytváření prostředku komunikačních služeb zadáte **geografickou** hodnotu (ne datové centrum Azure). Všechna data uložená v rámci služby Communication Services v klidovém znění budou v tomto geografickém umístění zachována v datovém centru, které je vybráno interně komunikačními službami Data mohou být přepravována nebo zpracována v jiných geografických oblastech. Tyto globální koncové body jsou nezbytné k zajištění vysoce výkonného prostředí s nízkou latencí pro koncové uživatele bez ohledu na jejich umístění.

## <a name="data-residency-and-events"></a>Zasídlení dat a události

Všechna Event Grid systémová témata nakonfigurovaná pomocí komunikačních služeb Azure budou vytvořena v globálním umístění. Pro podporu spolehlivého doručování může globální Event Grid systému ukládat data událostí do libovolného datového centra společnosti Microsoft. Když nakonfigurujete Event Grid s komunikačními službami Azure, dodáváte data události do Event Grid, což je prostředek Azure pod vaším ovládacím prvkem. I když může být služba Azure Communication Services nakonfigurovaná tak, aby využívala Azure Event Grid, zodpovídáte za správu prostředků Event Grid a dat uložených v této službě.

## <a name="relating-humans-to-azure-communication-services-identities"></a>Identity spojené s lidmi a službami Azure Communication Services

Vaše aplikace spravuje vztah mezi identitami lidských uživatelů a komunikačních služeb. Pokud chcete odstranit data pro lidského uživatele, musíte odstranit data zahrnující všechny identity komunikačních služeb, které jsou v relaci pro daného uživatele.

Existují dvě kategorie dat služby Communication Service:
- **Data rozhraní API.** Tato data se vytvářejí a spravují pomocí rozhraní API služby Communications Service, což je typický příklad pro zprávy chatu spravované prostřednictvím rozhraní API pro chat.
- **Protokoly Azure monitor** Tato data vytvoří služba a spravuje ji prostřednictvím Azure Monitor datovou platformou. Tato data zahrnují telemetrii a metriky, které vám pomůžou pochopit využití komunikačních služeb. Toto není spravováno rozhraními API komunikační služby.

## <a name="api-data"></a>Data rozhraní API

### <a name="identities"></a>Identity

Komunikační služby Azure uchovávají adresář identit, k jejich odebrání slouží rozhraní [DeleteIdentity](/rest/api/communication/communicationidentity/delete) API. Odstranění identity zruší všechny přidružené přístupové tokeny a odstraní jejich zprávy chatu. Další informace o tom, jak odebrat identitu, [najdete na této stránce](../quickstarts/access-tokens.md).

- DeleteIdentity

### <a name="azure-resource-manager"></a>Azure Resource Manager

Pomocí Azure Portal nebo Azure Resource Manager rozhraní API s komunikačními službami může vytvořit osobní údaje. [Pomocí této stránky se naučíte spravovat osobní údaje v Azure Resource Managerch systémech.](../../azure-resource-manager/management/resource-manager-personal-data.md)

### <a name="telephone-number-management"></a>Správa telefonního čísla

Komunikační služby Azure uchovávají adresář telefonních čísel přidružených k prostředku komunikačních služeb. Pomocí [rozhraní API pro správu telefonního čísla](/rest/api/communication/phonenumberadministration) načtěte telefonní čísla a odstraňte je:

- `Get All Phone Numbers`
- `Release Phone Number`

### <a name="chat"></a>Chat

Vlákna a zprávy chatu se uchovávají, dokud je explicitně neodstraníte. Po 30 dnech se automaticky odstraní plně nečinné vlákno. K získání, vypsání, aktualizaci a odstranění zpráv použijte [rozhraní API chatu](/rest/api/communication/chat/chatthread) .

- `Get Thread`
- `Get Message`
- `Delete Thread`
- `Delete Message`

### <a name="sms"></a>SMS

Odeslané a přijaté zprávy SMS jsou ephemerally zpracovávány službou a neuchovávají se.

### <a name="pstn-voice-calling"></a>Volání hlasu veřejné telefonní sítě

Zvuková a obrazová komunikace je ephemerally zpracovávána službou a žádná data se v prostředku neuchovávají kromě protokolů Azure Monitor.

### <a name="internet-voice-and-video-calling"></a>Volání internetového hlasu a videa

Zvuková a obrazová komunikace je ephemerally zpracovávána službou a žádná data se v prostředku neuchovávají kromě protokolů Azure Monitor.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor a Log Analytics

Komunikační služby Azure budou předávány do Azure Monitor dat protokolování pro porozumění provoznímu stavu a využití služby. Některé z těchto protokolů zahrnují identity komunikačních služeb a telefonní čísla jako data polí. Chcete-li odstranit všechny potenciálně osobní údaje, [použijte tyto postupy pro Azure monitor](../../azure-monitor/logs/personal-data-mgmt.md). Můžete také chtít nakonfigurovat [výchozí dobu uchování pro Azure monitor](../../azure-monitor/logs/manage-cost-storage.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Žádosti subjektu dat Azure pro GDPR a CCPA](/microsoft-365/compliance/gdpr-dsr-azure)
- [Centrum zabezpečení Microsoftu](https://www.microsoft.com/trust-center/privacy/data-location)
- [Interaktivní mapa Azure – kde jsou moje zákaznická data?](https://azuredatacentermap.azurewebsites.net/)
