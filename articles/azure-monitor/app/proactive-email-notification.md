---
title: Změna oznámení inteligentního zjišťování – Azure Application Insights
description: Přejděte na výchozí příjemce oznámení z inteligentního zjišťování. Inteligentní zjišťování umožňuje monitorovat trasování aplikací pomocí Azure Application Insights pro neobvyklé vzory v telemetrie trasování.
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 41298be1b0288f429647d8b38a1cc75dd2c40b0a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86516749"
---
# <a name="smart-detection-e-mail-notification-change"></a>Změna e-mailových oznámení inteligentního zjišťování

Na základě zpětné vazby od zákazníků od 1. dubna 2019 měníme výchozí role, které přijímají e-mailová oznámení od inteligentního zjišťování.

## <a name="what-is-changing"></a>Co se mění?

V současné době se e-mailová oznámení inteligentní detekce standardně odesílají do rolí _vlastník_předplatného, _přispěvatele_předplatného a _modulu pro čtení předplatného_ Tyto role často zahrnují uživatele, kteří nejsou aktivně zapojeni do monitorování, což způsobí, že mnohé z těchto uživatelů dostanou oznámení zbytečně. Pro zlepšení tohoto prostředí provádíme změnu, aby e-mailová oznámení ve výchozím nastavení přešla jenom na role [Čtenář monitorování](../../role-based-access-control/built-in-roles.md#monitoring-reader) a [sledování](../../role-based-access-control/built-in-roles.md#monitoring-contributor) .

## <a name="scope-of-this-change"></a>Rozsah této změny

Tato změna bude mít vliv na všechna pravidla inteligentního zjišťování, s výjimkou následujících:

* Pravidla inteligentního zjišťování označená jako náhled Tato pravidla inteligentního zjišťování nepodporují e-mailová oznámení ještě dnes.

* Pravidlo anomálií selhání. Toto pravidlo začne cílit na nové výchozí role, jakmile se migruje z klasické výstrahy na platformu Unified Alerts (Další informace jsou k dispozici [zde](../platform/monitoring-classic-retirement.md).)

## <a name="how-to-prepare-for-this-change"></a>Jak připravit tuto změnu?

Aby bylo zajištěno, že budou pro příslušné uživatele zasílána e-mailová oznámení od inteligentního zjišťování, musí být uživatelé přiřazeni ke službě [Sledování monitorování](../../role-based-access-control/built-in-roles.md#monitoring-reader) nebo k rolím [přispěvatele](../../role-based-access-control/built-in-roles.md#monitoring-contributor) v předplatném.

Pokud chcete přiřazovat uživatele k rolím přispěvatele monitorování nebo monitorování pomocí Azure Portal, postupujte podle kroků popsaných v článku [Přidání přiřazení role](../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) . Ujistěte se, že jste jako roli, ke které jsou přiřazeni uživatelé, vybrali možnost _Čtenář monitorování_ nebo _Přispěvatel monitorování_ .

> [!NOTE]
> Tato změna nebude mít vliv na konkrétní příjemce oznámení inteligentní detekce nakonfigurovaných pomocí možnosti _Další příjemci e-mailu_ v nastavení pravidla. Tito příjemci budou dál dostávat e-mailová oznámení.

Pokud máte nějaké dotazy nebo obavy týkající se této změny, váhají [nás kontaktujte](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Další kroky

Další informace o inteligentní detekci:

- [Anomálie selhání](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Nevracení paměti](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomálie výkonu](../../azure-monitor/app/proactive-performance-diagnostics.md)
