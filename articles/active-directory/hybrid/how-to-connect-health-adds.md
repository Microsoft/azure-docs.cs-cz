---
title: Používání služby Azure AD Connect Health se službou AD DS | Dokumentace Microsoftu
description: Toto je stránka o službě Azure AD Connect Health, která popisuje monitorování služby AD DS.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 19e3cf15-f150-46a3-a10c-2990702cd700
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73f30f4f16ad879468a424d6e5cbe81e68b7c33d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217577"
---
# <a name="using-azure-ad-connect-health-with-ad-ds"></a>Používání služby Azure AD Connect Health se službou AD DS
Následující dokumentace se věnuje monitorování služby Active Directory Domain Services pomocí služby Azure AD Connect Health. Podporované verze služby AD DS jsou: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows serveru 2016.

Další informace o sledování služby AD FS pomocí služby Azure AD Connect Health najdete v článku [Používání služby Azure AD Connect Health se službou AD FS](how-to-connect-health-adfs.md). Kromě toho informace o monitorování služby Azure AD Connect (synchronizace) pomocí služby Azure AD Connect Health najdete v článku [Používání služby Azure AD Connect Health pro synchronizaci](how-to-connect-health-sync.md).

![Azure AD Connect Health pro službu AD DS](./media/how-to-connect-health-adds/domainservicesnapshot.PNG)

## <a name="alerts-for-azure-ad-connect-health-for-ad-ds"></a>Upozornění služby Azure AD Connect Health pro službu AD DS
Část pojednávající o výstrahách v rámci služby Azure AD Connect Health pro AD DS obsahuje seznam aktivních a vyřešených výstrah, které souvisejí s řadiči domény. Výběrem aktivní nebo vyřešené výstrahy otevřete nové okno, které obsahuje další informace, postup řešení a odkazy na doprovodnou dokumentaci. Každý typ výstrahy může mít jednu nebo více instancí, které odpovídají jednotlivým řadičům domény, kterých se konkrétní výstraha týká. V dolní části okna s výstrahou můžete dvakrát kliknout na ovlivněný řadič domény. Otevře se další okno s dalšími podrobnosti o příslušné instanci výstrahy.

V rámci tohoto okna můžete povolit e-mailová oznámení pro výstrahy a změnit časový rozsah v zobrazení. Zvětšení časového rozsahu vám umožňuje zobrazit předchozí vyřešené výstrahy.

![Chyba synchronizace služby Azure AD Connect](./media/how-to-connect-health-adds/aadconnect-health-adds-alerts.png)

## <a name="domain-controllers-dashboard"></a>Řídicí panel Řadiče domény
Tento řídicí panel nabízí topologické zobrazení prostředí společně s klíčovými provozními metrikami a stavem jednotlivých monitorovaných řadičů domény. Zobrazené metriky vám pomůžou rychle identifikovat libovolné řadiče domény, které můžou vyžadovat další šetření. Ve výchozím nastavení se zobrazí pouze podmnožina sloupců. Celou množinu dostupných sloupců však můžete zobrazit dvojitým kliknutím na příkaz sloupce. Výběrem sloupců, které vás zajímají nejvíce, se tento řídicí panel promění v jedno přehledné místo, kde si zobrazíte stav prostředí služby AD DS.

![Řadiče domény](./media/how-to-connect-health-adds/aadconnect-health-adds-domainsandsites-dashboard.png)

Řadiče domény můžete seskupit podle příslušné domény nebo webu, což je užitečné k pochopení topologie prostředí. Pokud dvakrát kliknete na záhlaví okna, řídicí panel se roztáhne na celou obrazovku, aby využil její plochu naplno. Toto větší zobrazení je užitečné, když se zobrazuje více sloupců.

## <a name="replication-status-dashboard"></a>Řídicí panel Stav replikace
Tento řídicí panel zobrazuje stav replikace a topologii replikace pro řadiče domén, které monitorujete. Stav posledního pokusu o replikaci je uvedený spolu s užitečnou dokumentací pro jakékoli nalezené chyby. Dvojitým kliknutím na řadič domény s chybou otevřete nové okno s informacemi, jako jsou podrobnosti o chybě, doporučené kroky řešení a odkazy na dokumentaci týkající se odstraňování potíží.

![Stav replikace](./media/how-to-connect-health-adds/aadconnect-health-adds-replication.png)

## <a name="monitoring"></a>Monitorování
Tato funkce zajišťuje grafické zobrazení trendů různých čítačů výkonu, které se průběžně shromažďují ze všech monitorovaných řadičů domény. Výkon řadiče domény můžete snadno porovnat se všemi ostatními monitorovanými řadiči domény v doménové struktuře. Kromě toho můžete různé čítače výkonu zobrazit vedle sebe, což je užitečné při řešení problémů ve vašem prostředí.

![Monitorování](./media/how-to-connect-health-adds/aadconnect-health-adds-monitoring.png)

Ve výchozím nastavení jsme předem vybrali čtyři čítače výkonu. Kliknutím na příkaz filtru a výběrem nebo zrušením výběru libovolných požadovaných čítačů, můžete přidat nebo odebrat další čítače. Můžete také dvakrát kliknout na graf čítače výkonu. Tím otevřete nové okno, které obsahuje datové body každého monitorovaného řadiče domény.

## <a name="related-links"></a>Související odkazy
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalace agenta služby Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operace služby Azure AD Connect Health](how-to-connect-health-operations.md)
* [Používání služby Azure AD Connect Health se službou AD FS](how-to-connect-health-adfs.md)
* [Používání služby Azure AD Connect Health pro synchronizaci](how-to-connect-health-sync.md)
* [Azure AD Connect Health – nejčastější dotazy](reference-connect-health-faq.md)
* [Historie verzí služby Azure AD Connect Health](reference-connect-health-version-history.md)

