---
title: Azure Application Insights inteligentní zjišťování – nadcházející změny výchozích příjemců oznámení | Dokumentace Microsoftu
description: Monitorování trasování aplikací pomocí Azure Application Insights pro neobvyklé vzory v telemetrická data trasování.
services: application-insights
author: harelbr
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/13/2019
ms.author: harelbr
ms.openlocfilehash: f984a34be1c5d5fdd18a00812107318df8f5d9bf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889280"
---
# <a name="smart-detection-e-mail-notification-change"></a>Změnit inteligentní zjišťování e-mailové oznámení

Na základě názorů zákazníků, 1. dubna 2019 měníme výchozí role, kteří obdrží e-mailových oznámení z inteligentního zjišťování.

## <a name="what-is-changing"></a>Co se mění?

V současné době inteligentního zjišťování e-mailové oznámení se posílají ve výchozím nastavení _vlastník předplatného_, _přispěvatele předplatného_, a _Čtenář předplatného s oprávněním_ role. Tyto role často zahrnují uživatele, kteří se aktivně nepodílejí na monitorování, a řada z těchto uživatelů tak dostává oznámení zbytečně. Na zdokonalování tohoto prostředí, provádíme změny tak, aby e-mailová oznámení jdou jenom do [Čtenář monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) a [Přispěvatel monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) role ve výchozím nastavení.

## <a name="scope-of-this-change"></a>Rozsah této změny

Tato změna se projeví u všech pravidel inteligentního zjišťování s výjimkou následujících:

* Pravidla inteligentního zjišťování označená jako verze Preview. Tato pravidla inteligentního zjišťování nepodporují e-mailová oznámení ještě dnes.

* Pravidlo anomálií selhání. Toto pravidlo se spustí, cílení na nové výchozí role, jakmile se migroval z klasického upozornění na výstrahy jednotné platformy (Další informace jsou k dispozici [tady](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).)

## <a name="how-to-prepare-for-this-change"></a>Jak tuto změnu připravit?

Ujistěte se, že se příslušné uživatelům pošlou e-mailových oznámení z inteligentního zjišťování, tito uživatelé musí mít přiřazenou k [Čtenář monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) nebo [Přispěvatel monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) role předplatného.

Přiřazení uživatelů k rolím monitorování přispěvatele nebo čtenáře monitorování prostřednictvím webu Azure portal, postupujte podle kroků popsaných v [přidat přiřazení role](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) článku. Je nutné vybrat _Čtenář monitorování_ nebo _Přispěvatel monitorování_ jako roli, ke které jsou přiřazeni uživatelé.

> [!NOTE]
> Konkrétním příjemcům inteligentního zjišťování, nakonfigurovaná pomocí oznámení _další e-mailové příjemce_ možnost nastavení pravidel, nebude touto změnou ovlivněny. Příjemci budou i nadále přijímat e-mailová oznámení.

Pokud máte jakékoli dotazy nebo pochybnostmi k této změně, neváhejte [kontaktujte nás](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Další postup

Další informace o Chytré detekci:

- [Anomálie selhání](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Nevracení paměti](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomálie výkonu](../../azure-monitor/app/proactive-performance-diagnostics.md)