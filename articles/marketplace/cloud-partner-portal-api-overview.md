---
title: Reference k rozhraní API portál partnerů cloudu – komerční tržiště Microsoftu
description: Popis, předpoklady k použití a seznam operací rozhraní API Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 57d3d1c6bb14db3eb2ca499069934a628d2f7fea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92425781"
---
# <a name="cloud-partner-portal-api-reference"></a>Reference k rozhraní API portál partnerů cloudu

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná s a budou pokračovat v práci v partnerském centru. Přechod přináší malé změny. Zkontrolujte [změny v rozhraních API cpp](#changes-to-cpp-apis-after-the-migration-to-partner-center) uvedených v tomto dokumentu, abyste zajistili, že váš kód pokračuje v práci po přechodu do partnerského centra. Rozhraní API CPP by se mělo používat jenom pro existující produkty, které už jsou integrované před přechodem do partnerského centra; nové produkty by měly používat rozhraní API pro odesílání v partnerském centru.

Rozhraní REST API pro portál partnerů cloudu umožňují programové načítání a manipulaci s pracovními postupy, nabídkami a profily vydavatelů. Rozhraní API používají řízení přístupu na základě role Azure (Azure RBAC) k prosazování správných oprávnění v době zpracování.

Tento odkaz poskytuje technické podrobnosti o portál partnerů cloudu rozhraní REST API. Ukázky datové části v tomto dokumentu jsou jenom pro referenci a můžou se změnit, protože se přidávají nové funkce.

## <a name="prerequisites-and-considerations"></a>Předpoklady a požadavky

Před použitím rozhraní API byste si měli projít:

- V článku [požadavky](./cloud-partner-portal-api-prerequisites.md) se dozvíte, jak přidat instanční objekt k vašemu účtu a získat přístupový token Azure Active Directory (Azure AD) pro ověřování.
- Dvě strategie [řízení souběžnosti](./cloud-partner-portal-api-concurrency-control.md) , které jsou k dispozici pro volání těchto rozhraní API.
- Další [požadavky](./cloud-partner-portal-api-considerations.md)rozhraní API, jako je například Správa verzí a zpracování chyb.

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>Změny v rozhraních API CPP po migraci do partnerského centra

| **Rozhraní API** | **Popis změny** | **Dopad** |
| ------- | ---------------------- | ---------- |
| Publikovat, GoLive, zrušit | V případě migrovaných nabídek bude hlavička odpovědi v jiném formátu, ale bude i nadále fungovat stejným způsobem, aby se zaznamenala relativní cesta pro načtení stavu operace. | Při odesílání kterékoli z odpovídajících požadavků POST pro nabídku bude mít hlavička umístění v závislosti na stavu migrace v jednom ze dvou formátů:<ul><li>Nemigrované nabídky<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Migrované nabídky<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Operace GET | V případě typů nabídek, které dříve podporovaly pole "Notification-e-mail" v odpovědi, bude toto pole zastaralé a již nebude vráceno pro migrované nabídky. | U migrovaných nabídek už nebudeme odesílat oznámení na seznam e-mailů zadaných v požadavcích. Místo toho bude služba API v partnerském centru pro posílání e-mailů zarovnávat s e-mailovým procesem oznámení. Konkrétně se oznámení odešlou na e-mailovou adresu nastavenou v části kontaktní informace prodejce v nastavení vašeho účtu v partnerském centru, aby vás upozornila na průběh operace.<br><br>Zkontrolujte prosím e-mailovou adresu nastavenou v části kontaktní údaje prodejce v [Nastavení účtu](https://partner.microsoft.com/dashboard/account/management) v partnerském centru, abyste měli jistotu, že je pro oznámení k dispozici správný e-mail.  |

## <a name="common-tasks"></a>Běžné úkoly

Tato reference odkazuje na rozhraní API, aby prováděla následující běžné úlohy.

### <a name="offers"></a>Nabídky

- [Načíst všechny nabídky](./cloud-partner-portal-api-retrieve-offers.md)
- [Načíst konkrétní nabídku](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [Načtení stavu nabídky](./cloud-partner-portal-api-retrieve-offer-status.md)
- [Vytvoření nabídky](./cloud-partner-portal-api-creating-offer.md)
- [Publikování nabídky](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operace

- [Načtení operací](./cloud-partner-portal-api-retrieve-operations.md)
- [Zrušení operací](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publikování aplikace

- [Zveřejnění](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Další úlohy

- [Nastavení cen pro nabídky virtuálních počítačů](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Poradce při potížích

- [Řešení potíží s chybami ověřování](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
