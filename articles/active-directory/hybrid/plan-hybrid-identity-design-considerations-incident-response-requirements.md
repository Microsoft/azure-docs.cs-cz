---
title: Hybridní identita návrh – reakce na incidenty požadavky Azure | Dokumentace Microsoftu
description: Určete možnosti monitorování a vytváření sestav pro hybridní řešení identit, které mohou využívat IT k provádění akcí na identifikaci a reagujte na potenciální hrozby
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32efe76ac2fce4c4541287e96c3a495f3eb9031b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56161487"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Určení požadavků na reakce na incidenty pro vaše řešení hybridní identity
Střední a velké organizace pravděpodobně budou mít [reakce na incidenty zabezpečení](https://technet.microsoft.com/library/cc700825.aspx) která vám pomůžou IT odpovídajícím způsobem provést akce na úrovni incident. Systém správy identit je důležitou součástí procesu reakce na incidenty, protože ho lze použít k určení, kdo provedl konkrétní akce s cílem pomoct. Řešení hybridní identity musí být schopný poskytnout možnosti monitorování a vytváření sestav, které mohou využívat IT k provádění akcí na identifikaci a zmírnění potenciální hrozbu. V plánu reakcí na incidenty typické máte následující fáze jako součást plánu:

1. Počáteční vyhodnocení.
2. Komunikace mezi incidenty.
3. Ovládací prvek poškození a snížení rizika.
4. Identifikace co bylo ohrožení zabezpečení a závažnosti.
5. Legitimace uchování.
6. Oznámení o příslušné stranám.
7. Obnovení systému.
8. Dokumentace ke službě.
9. Vyhodnocování poškození a náklady.
10. Revize procesu a plánu.

Při identifikaci co IT oddělení byl ohrožení zabezpečení a závažnost fáze, bude potřeba identifikovat systémy, které jsou ohrožené, soubory, které byly zobrazeny a určete citlivost takových souborů. Vaše hybridní identity systém by měl být schopen splnit tyto požadavky určit uživatele, který tyto změny. 

## <a name="monitoring-and-reporting"></a>Monitorování a vytváření sestav
V mnoha případech systém identit může také pomoct ve fázi počáteční vyhodnocení, hlavně v případě, že systém má integrované auditování a vytváření sestav funkce. Během počáteční vyhodnocení správce IT musí být schopen identifikovat podezřelé aktivity nebo systému měli být schopni aktivační události, které se automaticky podle předem nakonfigurované úloh. Řada aktivit může znamenat možný útok, ale v jiných případech chybně nakonfigurovaný systém může vést k počet falešně pozitivních událostí v systémech zjišťování neoprávněného vniknutí. 

Systém správy identit by měl pomoc správci IT identifikovat a sestavy těchto podezřelých aktivit. Obvykle tyto technické požadavky lze splnit všechny systémy pro monitorování a s vykazovací funkci, můžete upozorňující na potenciální hrozby. Pomocí níže uvedené otázky můžete navrhnout řešení hybridní identity při berou v úvahu požadavky reakce na incidenty:

* Má vaše společnost reakce na incidenty zabezpečení na místě?
  * Pokud ano, je použit aktuální systém správy identit jako součást procesu?
* Potřebuje vaše společnost k identifikaci podezřelých pokusů o přihlášení uživatelů v různých zařízeních?
* Potřebuje vaše společnost ke zjišťování potenciálních ohrožení zabezpečení přihlašovacích údajů uživatele?
* Potřebuje vaše společnost auditovat přístup a akce uživatele?
* Potřebuje vaše společnost vědět, kdy uživatel resetovat své heslo?

## <a name="policy-enforcement"></a>Vynucování zásad
Během kontroly poškození a snížení rizika – fáze je potřeba rychle snížit skutečné a potenciální účinky útoku. Tuto akci, která bude trvat v tomto okamžiku může být rozdíl mezi za a hlavní jeden. Přesná reakce bude záviset na vaší organizaci a povahu útoku, kterými se setkáváte. Pokud počáteční vyhodnocení dospělo k závěru, že došlo k napadení účet, je potřeba vynucovat zásady, aby blokovaly tento účet. To je jenom jeden příklad, kde se využívají systém správy identit. Při návrhu hybridní řešení identit přitom v úvahu, jak se bude vynucovat zásady reagovat na incident probíhající použijte níže uvedené otázky:

* Má vaše společnost nějaké zásady na místě k blokování uživatelů před přístupem k síti v případě potřeby?
  * Pokud ano, aktuální řešení integrace s hybridní identity systému pro správu, která se má přijmout
* Potřebuje vaše společnost pro vynucení podmíněného přístupu pro uživatele, kteří jsou v karanténě? 

> [!NOTE]
> Ujistěte se, že každá odpověď a pochopení odůvodnění odpověď. [Definování strategie ochrany dat](plan-hybrid-identity-design-considerations-data-protection-strategy.md) možností a výhod i nevýhod každé z.  Po zodpovězení těchto otázek, které vyberete která možnost nejlépe vyhovuje stylu vaší firmě potřebuje.
> 
> 

## <a name="next-steps"></a>Další postup
[Definování strategie ochrany dat](plan-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Viz také
[Přehled aspektů návrhu](plan-hybrid-identity-design-considerations-overview.md)

