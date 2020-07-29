---
title: Azure Active Directory latencí hlášení | Microsoft Docs
description: Přečtěte si, jak dlouho trvá generování sestav událostí v Azure Portal
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "74007708"
---
# <a name="azure-active-directory-reporting-latencies"></a>Latence generování sestav v Azure Active Directory

Latence představuje dobu potřebnou k tomu, aby se data sestav Azure Active Directory (Azure AD) zobrazovala v [Azure Portal](https://portal.azure.com). V tomto článku jsou uvedeny očekávané latence pro různé typy sestav. 

## <a name="activity-reports"></a>Sestavy aktivit

Existují dva typy sestav aktivit:

- [Přihlášení](concept-sign-ins.md) – poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
- [Protokoly auditu](concept-audit-logs.md) – poskytuje informace o činnosti systému týkající se uživatelů a skupin, spravovaných aplikací a aktivit adresáře.

V následující tabulce jsou uvedeny informace o latenci pro sestavy aktivit. 

> [!NOTE]
> **Latence (95. percentil)** odkazuje na čas, podle kterého budou hlášeny 95% protokolů, a **latence (99 percentil)** odkazuje na čas, který bude hlášen 99% protokolů. 
>

| Sestava | Latence (95. percentil) |Latence (99 percentil)|
| :-- | --- | --- |
| Protokoly auditu | 2 minuty  | 5 minut  |
| Přihlášení | 2 minuty  | 5 minut |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Jak brzy můžu zobrazit data o aktivitách po získání licence na prémii?

Pokud již máte data o aktivitách s bezplatnou licencí, můžete ji okamžitě zobrazit při upgradu. Pokud nemáte žádná data, bude trvat jeden nebo dva dny, než se data zobrazí v sestavách po upgradu na licenci Premium.

## <a name="security-reports"></a>Sestavy zabezpečení

Existují dva typy sestav zabezpečení:

- [Riziková přihlášení](concept-risky-sign-ins.md) – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 
- [Uživatelé označení příznakem rizika](concept-user-at-risk.md) – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. 

V následující tabulce jsou uvedeny informace o latenci pro sestavy zabezpečení.

| Sestava | Minimální | Průměr | Maximum |
| :-- | --- | --- | --- |
| Ohrožení uživatelé          | 5 minut   | 15 minut  | 2 hodiny  |
| Riziková přihlášení         | 5 minut   | 15 minut  | 2 hodiny  |

## <a name="risk-detections"></a>Detekce rizik

Azure AD pomocí adaptivních algoritmů strojového učení a heuristiky detekuje podezřelé akce, které souvisejí s vašimi uživatelskými účty. Každá zjištěná podezřelá akce je uložená v záznamu s názvem **detekce rizik**.

V následující tabulce jsou uvedeny informace o latenci pro detekci rizik.

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
* [Azure Active Directory detekce rizik](concept-risk-events.md)
