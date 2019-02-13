---
title: Latence generování sestav Azure Active Directory | Dokumentace Microsoftu
description: Další informace o dobu, za jakou události vytváření sestav se zobrazí na portálu Azure
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66fc890e72c32cbe605d49f458a875cb692e6182
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56174329"
---
# <a name="azure-active-directory-reporting-latencies"></a>Latence generování sestav Azure Active Directory.

Latence je doba je potřebná pro Azure Active Directory (Azure AD) data pro generování sestav se zobrazí v [webu Azure portal](https://portal.azure.com). Tento článek uvádí očekávaná latence pro různé typy sestav. 

## <a name="activity-reports"></a>Sestavy aktivit

Existují dva typy sestav aktivit:

- [Přihlášení](concept-sign-ins.md) – poskytuje informace o využití spravovaných aplikací a uživatel aktivit přihlašování
- [Protokoly auditu](concept-audit-logs.md) – poskytuje informace aktivit systému o uživatelů a skupin, spravovaných aplikacích a aktivitách adresářů

V následující tabulce jsou uvedeny informace o latenci pro sestavy aktivit. 

> [!NOTE]
> **Latence (95. percentil)** odkazuje na čas, podle kterého se ohlásí 95 % protokolů, a **latenci (99. percentilu)** odkazuje na čas, podle kterého se ohlásí 99 % protokoly. 
>

| Sestava | Latence (95. percentil) |Latenci (99. percentilu)|Časový rozsah, ve kterém se ohlásí protokoly|
| :-- | --- | --- | --- |
| Protokoly auditu | 2 minuty  | 5 minut  | 2 až 60 minut |
| Přihlášení | 2 minuty  | 5 minut | 2 – 120 minut. |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Jak rychle můžete zobrazit data aktivity po získání licence premium?

Pokud již máte data aktivity s bezplatnou licencí, pak uvidíte ho okamžitě při upgradu. Pokud nemáte k dispozici žádná data, bude to trvat jeden nebo dva dny pro daná data zobrazit v sestavách po upgradu na licenci premium.

## <a name="security-reports"></a>Sestavy zabezpečení

Existují dva typy sestav zabezpečení:

- [Riziková přihlášení](concept-risky-sign-ins.md) –Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 
- [Uživatelé označení příznakem rizika](concept-user-at-risk.md) – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. 

V následující tabulce jsou uvedeny informace o latenci pro zprávy o zabezpečení.

| Sestava | Minimální | Průměr | Maximum |
| :-- | --- | --- | --- |
| Ohrožení uživatelé          | 5 minut   | 15 minut  | 2 hodiny  |
| Riziková přihlášení         | 5 minut   | 15 minut  | 2 hodiny  |

## <a name="risk-events"></a>Rizikové události

Azure AD používá adaptivní algoritmy strojového učení a heuristik ke zjištění podezřelé akce, které souvisejí s vašimi uživatelskými účty. Každou zjištěnou podezřelé akce, které jsou uloženy v záznam nazvaný **riziková událost**.

V následující tabulce jsou uvedeny informace o latenci pro rizikové události.

| Sestava | Minimální | Průměr | Maximum |
| :-- | --- | --- | --- |
| Přihlášení z anonymních IP adres |5 minut |15 minut |2 hodiny |
| Přihlášení z neznámých míst |5 minut |15 minut |2 hodiny |
| Uživatelé s uniklými přihlašovacími údaji |2 hodiny |4 hodiny |8 hodin |
| Nemožná cesta do netypických míst |5 minut |1 hodina |8 hodin  |
| Přihlášení z nakažených zařízení |2 hodiny |4 hodiny |8 hodin  |
| Přihlášení z IP adres s podezřelou aktivitou |2 hodiny |4 hodiny |8 hodin  |


## <a name="next-steps"></a>Další postup

* [Přehled sestav Azure AD](overview-reports.md)
* [Programový přístup k sestavám Azure AD](concept-reporting-api.md)
* [Rizikové události v Azure Active Directory](concept-risk-events.md)
