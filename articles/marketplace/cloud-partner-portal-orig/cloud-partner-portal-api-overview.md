---
title: Odkaz na rozhraní API portálu cloudových partnerů | Azure Marketplace
description: Popis, předpoklady pro použití a seznam operací rozhraní API marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: b9f698ea81830aaa8761c05012cf6843d07ad5a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256412"
---
# <a name="cloud-partner-portal-api-reference"></a>Reference k rozhraní API portál partnerů cloudu

> [!NOTE]
> Api portálu pro partnery cloudu jsou integrovaná s Partnerským centrem a budou fungovat i po migraci nabídek do Centra partnerů. Integrace přináší malé změny. Zkontrolujte [změny cpp API](#changes-to-cpp-apis-after-the-migration-to-partner-center) uvedené v tomto dokumentu a ujistěte se, že váš kód bude fungovat i po migraci do Centra partnerů.

Api REST portálu cloudových partnerů umožňují programové načítání a manipulaci s úlohami, nabídkami a profily vydavatelů. Api používají řízení přístupu na základě rolí (RBAC) k vynucení správných oprávnění v době zpracování.

Tento odkaz poskytuje technické podrobnosti pro cloudpartner portál u rest API. Ukázky datové části v tomto dokumentu jsou pouze orientační a mohou se změnit při přidání nových funkcí.

## <a name="prerequisites-and-considerations"></a>Předpoklady a úvahy

Před použitím api byste měli zkontrolovat:

- Požadavky článku se [dozvíte,](./cloud-partner-portal-api-prerequisites.md) jak přidat instanční objekt do vašeho účtu a získat přístupový token služby Azure Active Directory (Azure AD) pro ověřování.
- Dvě strategie [řízení souběžnosti,](./cloud-partner-portal-api-concurrency-control.md) které jsou k dispozici pro volání těchto rozhraní API.
- Další [aspekty](./cloud-partner-portal-api-considerations.md)rozhraní API , jako je například správa verzí a zpracování chyb.

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>Změny api CPP po migraci do Partnerského centra

| **rozhraní API** | **Popis změny** | **Dopad** |
| ------- | ---------------------- | ---------- |
| POST Publikovat, GoLive, Zrušit | U migrovaných nabídek bude mít hlavička odpovědi jiný formát, ale bude nadále fungovat stejným způsobem, což bude upozorovat na relativní cestu k načtení stavu operace. | Při odesílání odpovídajících požadavků POST na nabídku bude mít hlavička Umístění jeden ze dvou formátů v závislosti na stavu migrace nabídky:<ul><li>Nemigrované nabídky<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Migrované nabídky<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Operace GET | U typů nabídek, které dříve podporovaly pole "oznamovací e-mail" v odpovědi, bude toto pole zastaralé a již nebude vráceno pro migrované nabídky. | U migrovaných nabídek již nebudeme odesílat oznámení na seznam e-mailů uvedených v požadavcích. Místo toho bude služba rozhraní API zarovnána s procesem e-mailu s oznámením v Centru partnerů pro odesílání e-mailů. Konkrétně budou oznámení odeslána na e-mailovou adresu nastavenou v části Kontaktní údaje prodejce v nastavení účtu v Centru partnerů, aby vás upozornili na průběh operace.<br><br>Zkontrolujte e-mailovou adresu nastavenou v části Informace o kontaktech prodejce v [nastavení účtu](https://partner.microsoft.com/dashboard/account/management) v Centru partnerů, abyste se ujistili, že pro oznámení je k dispozici správný e-mail.  |

## <a name="common-tasks"></a>Běžné úkoly

Tento odkaz podrobnosti API provádět následující běžné úkoly.

### <a name="offers"></a>Nabídky

- [Načíst všechny nabídky](./cloud-partner-portal-api-retrieve-offers.md)
- [Načtení konkrétní nabídky](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [Načtení stavu nabídky](./cloud-partner-portal-api-retrieve-offer-status.md)
- [Vytvoření nabídky](./cloud-partner-portal-api-creating-offer.md)
- [Publikování nabídky](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operace

- [Načtení operací](./cloud-partner-portal-api-retrieve-operations.md)
- [Zrušení operací](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publikování aplikace

- [Zveřejnění](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Další úlohy

- [Nastavení cen pro nabídky virtuálních strojů](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Řešení potíží

- [Řešení potíží s chybami ověřování](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
