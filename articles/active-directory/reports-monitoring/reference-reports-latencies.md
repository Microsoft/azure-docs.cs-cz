---
title: Latence vytváření sestav služby Azure Active Directory | Dokumenty společnosti Microsoft
description: Přečtěte si, kolik času trvá, než se na webu Azure Portal zobrazí reportování událostí.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d031546bb4f1f05e9ea2abb5b74fe911b0b507f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74007708"
---
# <a name="azure-active-directory-reporting-latencies"></a>Latence generování sestav v Azure Active Directory

Latence je doba, kterou trvá, než se data sestav služby Azure Active Directory (Azure AD) zobrazí na [webu Azure Portal](https://portal.azure.com). Tento článek uvádí očekávanou latenci pro různé typy sestav. 

## <a name="activity-reports"></a>Sestavy aktivit

Existují dva typy zpráv o aktivitách:

- [Přihlášení](concept-sign-ins.md) – poskytuje informace o využití spravovaných aplikací a aktivity přihlášení uživatelů
- [Protokoly auditu](concept-audit-logs.md) – poskytuje informace o aktivitách systému o uživatelích a skupinách, spravovaných aplikacích a aktivitách adresářů.

V následující tabulce jsou uvedeny informace o latenci pro sestavy aktivit. 

> [!NOTE]
> **Latence (95. percentil)** označuje čas, do kterého bude hlášeno 95 % protokolů, a **latence (99. percentil)** označuje čas, do kterého bude hlášeno 99 % protokolů. 
>

| Sestava | Latence (95. percentil) |Latence (99. percentil)|
| :-- | --- | --- |
| Protokoly auditu | 2 minuty  | 5 minut  |
| Přihlášení | 2 minuty  | 5 minut |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Za jak dlouho se mi po získání prémiové licence zobrazí údaje o aktivitách?

Pokud již máte údaje o aktivitách s bezplatnou licencí, můžete je okamžitě vidět při upgradu. Pokud nemáte žádná data, bude trvat jeden nebo dva dny, než se data zobrazí v přehledech po upgradu na prémiovou licenci.

## <a name="security-reports"></a>Sestavy zabezpečení

Existují dva typy zpráv o zabezpečení:

- [Riziková přihlášení](concept-risky-sign-ins.md) – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 
- [Uživatelé označení příznakem rizika](concept-user-at-risk.md) – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. 

V následující tabulce jsou uvedeny informace o latenci pro sestavy zabezpečení.

| Sestava | Minimální | Průměr | Maximum |
| :-- | --- | --- | --- |
| Ohrožení uživatelé          | 5 minut   | 15 minut  | 2 hodiny  |
| Riziková přihlášení         | 5 minut   | 15 minut  | 2 hodiny  |

## <a name="risk-detections"></a>Detekce rizik

Azure AD používá adaptivní algoritmy strojového učení a heuristiky k detekci podezřelých akcí, které souvisejí s vašimi uživatelskými účty. Každá zjištěná podezřelá akce je uložena v záznamu nazývaném **detekce rizik**.

V následující tabulce jsou uvedeny informace o latenci pro zjišťování rizik.

| Sestava | Minimální | Průměr | Maximum |
| :-- | --- | --- | --- |
| Přihlášení z anonymních IP adres |5 minut |15 minut |2 hodiny |
| Přihlášení z neznámých míst |5 minut |15 minut |2 hodiny |
| Uživatelé s uniklými přihlašovacími údaji |2 hodiny |4 hodiny |8 hodin |
| Nemožná cesta do netypických míst |5 minut |1 hodina |8 hodin  |
| Přihlášení z nakažených zařízení |2 hodiny |4 hodiny |8 hodin  |
| Přihlášení z IP adres s podezřelou aktivitou |2 hodiny |4 hodiny |8 hodin  |


## <a name="next-steps"></a>Další kroky

* [Přehled sestav Azure AD](overview-reports.md)
* [Programový přístup k sestavám Azure AD](concept-reporting-api.md)
* [Detekce rizik služby Azure Active Directory](concept-risk-events.md)
