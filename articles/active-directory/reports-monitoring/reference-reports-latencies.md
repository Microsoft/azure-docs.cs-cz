---
title: Latence generování sestav Azure Active Directory | Dokumentace Microsoftu
description: Další informace o dobu, za jakou události vytváření sestav se zobrazí na portálu Azure
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 12/15/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 66e974e80fa6f5e002b1f54584ea48e22a7b13ce
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053183"
---
# <a name="azure-active-directory-reporting-latencies"></a>Latence generování sestav Azure Active Directory.

S [reporting](../active-directory-preview-explainer.md) v Azure Active Directory, získáte všechny informace potřebné ke zjištění stavu vašeho prostředí. Množství času, které je potřebná pro vytváření sestav dat se zobrazí na webu Azure Portal se také nazývá latence. 

Toto téma obsahuje informace o latenci pro všechny sestavy kategorie na webu Azure Portal. 


## <a name="activity-reports"></a>Sestavy aktivit

Existují dvě oblasti vytváření sestav aktivit:

- **Aktivity přihlašování** – informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů
- **Protokoly auditu** – informace aktivit systému o správě uživatelů a skupin, spravovaných aplikacích a aktivitách adresářů

V následující tabulce jsou uvedeny informace o latenci pro sestavy aktivit.

| Sestava | Latence (95 %) |Latenci (99 %)|
| :-- | --- | --- | 
| Protokoly auditu | 2 minuty  | 5 minut  |
| Přihlášení | 2 minuty  | 5 minut |







## <a name="security-reports"></a>Sestavy zabezpečení

Existují dvě oblasti vytváření sestav zabezpečení:

- **Riziková přihlášení** –Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 
- **Uživatelé označení příznakem rizika** – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. 

V následující tabulce jsou uvedeny informace o latenci pro zprávy o zabezpečení.

| Sestava | Minimální | Průměr | Maximum |
| :-- | --- | --- | --- |
| Ohrožení uživatelé          | 5 minut   | 15 minut  | 2 hodiny  |
| Riziková přihlášení         | 5 minut   | 15 minut  | 2 hodiny  |

## <a name="risk-events"></a>Rizikové události

Azure Active Directory používá algoritmy adaptivní strojového učení a heuristik ke zjištění podezřelé akce, které souvisejí s vašimi uživatelskými účty. Každé zjištěné podezřelé akce je uložen v záznamu volané rizikové události.

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

Pokud chcete získat další informace o sestavy aktivit na webu Azure Portal, naleznete v tématu:

- [Sestavy aktivit přihlašování na portálu Azure Active Directory](concept-sign-ins.md)
- [Sestavy aktivit auditu na portálu Azure Active Directory](concept-audit-logs.md)

Pokud chcete získat další informace o zabezpečení sestav na webu Azure Portal, naleznete v tématu:

- [Sestava ohrožených zabezpečení na portálu Azure Active Directory uživatelů](concept-user-at-risk.md)
- [Sestavy rizikových přihlášení na portálu Azure Active Directory](concept-risky-sign-ins.md)

Pokud se chcete dozvědět víc o rizikových událostech, naleznete v tématu [rizikových událostech Azure Active Directory](concept-risk-events.md).
