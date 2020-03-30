---
title: Změna oznámení inteligentní detekce – Přehledy aplikací Azure
description: Změňte na výchozí příjemce oznámení z inteligentního zjišťování. Inteligentní detekce umožňuje sledovat trasování aplikací pomocí Azure Application Insights pro neobvyklé vzory v telemetrii trasování.
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: f73e5bbdd8585b3367e529a8fa00630042e56cac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671779"
---
# <a name="smart-detection-e-mail-notification-change"></a>Změna oznámení e-mailem inteligentní detekce

Na základě zpětné vazby od zákazníků měníme 1.

## <a name="what-is-changing"></a>Co se mění?

V současné době inteligentní detekce e-mailová oznámení jsou odesílány ve výchozím nastavení _vlastník předplatného_, _přispěvatel předplatného_, a _čtečka předplatného_ role. Tyto role často zahrnují uživatele, kteří nejsou aktivně zapojeni do monitorování, což způsobuje, že mnoho z těchto uživatelů dostávat oznámení zbytečně. Chcete-li zlepšit toto prostředí, provádíme změnu tak, aby e-mailová oznámení ve výchozím nastavení přecštrápěla pouze do rolí [čtečky monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) a [přispěvatele monitorování.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)

## <a name="scope-of-this-change"></a>Rozsah této změny

Tato změna ovlivní všechna pravidla inteligentní detekce, s výjimkou následujících:

* Pravidla inteligentní detekce označená jako náhled. Tato pravidla inteligentní detekce dnes nepodporují e-mailová oznámení.

* Pravidlo anomálií selhání. Toto pravidlo začne cílit na nové výchozí role po migraci z klasické výstrahy na platformu sjednocených výstrah (další informace jsou k dispozici [zde](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).)

## <a name="how-to-prepare-for-this-change"></a>Jak se připravit na tuto změnu?

Chcete-li zajistit, aby e-mailová oznámení z inteligentní detekce jsou odesílány příslušným uživatelům, musí být tito uživatelé [přiřazeny k monitorování reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) nebo [sledování přispěvatele](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) role předplatného.

Pokud chcete přiřadit uživatele k rolím čtečky monitorování nebo přispěvatele monitorování prostřednictvím portálu Azure, postupujte podle kroků popsaných v článku [Přidat přiřazení role.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) Nezapomeňte vybrat _monitorovací čtečku_ nebo _přispěvatele monitorování_ jako roli, ke které jsou uživatelé přiřazeni.

> [!NOTE]
> Konkrétní příjemci oznámení inteligentní detekce, nakonfigurovaní pomocí _možnosti Další příjemci e-mailu_ v nastavení pravidla, nebudou touto změnou ovlivněni. Tito příjemci budou nadále dostávat e-mailová oznámení.

Máte-li jakékoli dotazy nebo obavy týkající se této změny, neváhejte [nás kontaktovat](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Další kroky

Další informace o inteligentní detekci:

- [Anomálie selhání](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Nevracení paměti](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomálie výkonu](../../azure-monitor/app/proactive-performance-diagnostics.md)
