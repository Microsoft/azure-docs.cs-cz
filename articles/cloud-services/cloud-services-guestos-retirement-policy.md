---
title: Příručka k podpoře a vyřazení zásad pro hostovaný operační systém Azure | Microsoft Docs
description: Poskytuje informace o tom, co Microsoft bude podporovat s operačním systémem hosta Azure, který používá Cloud Services.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: fbe9b3379799fe3cf0a56d921ab257bc87606ca9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "68945457"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Zásady podpory a vyřazení hostovaného operačního systému Azure
Informace na této stránce se vztahují k hostovanému operačnímu systému Azure ([hostovaný](cloud-services-guestos-update-matrix.md)operační systém) pro Cloud Services pracovní procesy a webové role (PaaS). Nevztahuje se na Virtual Machines (IaaS).

Společnost Microsoft zveřejnila [zásady podpory pro hostovaný operační systém](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Stránka, kterou právě čtete, popisuje, jak je zásada implementovaná.

Zásada je

1. Microsoft bude podporovat **aspoň nejnovější dvě rodiny hostovaných operačních systémů**. Po vyřazení rodiny mají zákazníci 12 měsíců od oficiálního data vyřazení za účelem aktualizace na novější podporovanou rodinu hostovaných operačních systémů.
2. Microsoft bude podporovat **aspoň nejnovější dvě verze podporovaných rodin operačních systémů**.
3. Microsoft bude podporovat **aspoň nejnovější dvě verze sady Azure SDK**. Po vyřazení verze sady SDK budou mít zákazníci 12 měsíců od oficiálního data vyřazení za účelem aktualizace na novější verzi.

V některých případech může být podporováno více než dvě rodiny nebo vydání. Oficiální informace o podpoře hostovaného operačního systému se zobrazí ve [verzích hostovaného operačního systému Azure a v matici kompatibility SDK](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>Když je vyřazená verze operačního systému hosta
Nové **verze** operačního systému hosta se zavádějí každý měsíc, aby zahrnovaly nejnovější aktualizace MSRC. Kvůli pravidelným měsíčním aktualizacím se verze operačního systému hosta obvykle zakázala asi 60 dní po jejím vydání. Tato aktivita udržuje alespoň dvě verze hostovaného operačního systému pro každou rodinu, která je k dispozici pro použití.

### <a name="process-during-a-guest-os-family-retirement"></a>Zpracovat během vyřazení řady hostovaného operačního systému
Po ohlášení vyřazení je zákazníkem po dobu 12 měsíců "přechod", než se starší rodina ze služby oficiálně odebere. Tato doba přechodu se dá prodloužit na uvážení Microsoftu. Aktualizace se budou publikovat v rámci vydaných [verzí operačního systému Azure hosta a kompatibility SDK](cloud-services-guestos-update-matrix.md).

Proces postupného vyřazení bude začínat šest (6) měsíců do přechodného období. Během této doby:

1. Společnost Microsoft bude informovat zákazníky o vyřazení.
2. Novější verze sady Azure SDK nebude podporovat vyřazení hostované řady operačních systémů.
3. V vyřazené rodině se nepovolují nová nasazení a nová nasazení Cloud Services.

Společnost Microsoft bude i nadále zavádět novou verzi operačního systému hosta, která zahrnuje nejnovější aktualizace služby MSRC, a to až do posledního dne přechodného období, které se označuje jako "datum vypršení platnosti". V době vypršení platnosti nebude Cloud Services stále běžet v rámci smlouvy SLA pro Azure. Společnost Microsoft má oprávnění k vynucení upgradu, odstranění nebo zastavení těchto služeb po tomto datu.

### <a name="process-during-a-guest-os-version-retirement"></a>Zpracovat během vyřazení verze operačního systému hosta
Pokud si zákazníci nastavují hostovaný operační systém tak, aby se automaticky aktualizovali, nikdy si nemusí dělat starosti se zabývat s verzemi operačního systému hosta. Vždycky budou používat nejnovější verzi hostovaného operačního systému.

Verze operačního systému hosta jsou vydávány každý měsíc. Z důvodu sazby za běžné verze má každá verze pevnou životnost.

Ve 60 dnech do životnosti je verze "*disabled*". "Zakázáno" znamená, že je verze z portálu odebrána. Verzi již nelze nastavovat z konfiguračního souboru CSCFG. Existující nasazení zůstanou spuštěná. Ale nová nasazení a aktualizace kódu a konfigurace pro existující nasazení nebudou povoleny.

Po deaktivaci se u hostovaného operačního systému verze vyprší a při všech instalacích, jejichž verze s vypršenou platností, se zveřejňují problémy zabezpečení a ohrožení zabezpečení. Obvykle se vypršení platnosti provádí v dávkách, takže období od zakázání do vypršení platnosti se může lišit.

Zákazníci, kteří konfigurují své služby pro ruční aktualizaci hostovaného operačního systému, by měli zajistit, aby jejich služby běžely v podporovaném operačním systému. Pokud je služba nakonfigurovaná tak, aby automaticky aktualizovala hostovaný operační systém, zaručí základní platforma dodržování předpisů a provede upgrade na nejnovější hostovaný operační systém.

Tato období se můžou prodloužit na základě rozhodnutí Microsoftu, aby se usnadnily přechody zákazníků. Veškeré změny budou sděleny v rámci [Azure hosta Release OS a kompatibility SDK](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Oznámení během vyřazení
* **Vyřazení rodiny** <br>Microsoft bude používat blogové příspěvky a oznámení na portálu. Zákazníci, kteří stále používají vyřazenou rodinu hostovaného operačního systému, budou upozorněni prostřednictvím přímé komunikace (e-mail, zprávy portálu, telefonní hovor) k přiřazeným správcům služby. Všechny změny se budou publikovat do vydaných [verzí operačního systému hostovaného v Azure a v tabulce kompatibility SDK](cloud-services-guestos-update-matrix.md).
* **Vyřazení verze** <br>Všechny změny a data, která se objeví, se budou publikovat do [vydaných verzí operačního systému Azure hosta a sady SDK kompatibility](cloud-services-guestos-update-matrix.md), včetně vydání, zakázání a vypršení platnosti. Správci služeb obdrží e-maily, pokud mají nasazení spuštěná na zakázané verzi operačního systému hosta nebo rodina. Časování těchto e-mailů se může lišit. Obvykle jsou alespoň v měsíci před zakazováním, i když toto časování není oficiální smlouvou SLA.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
**Jak můžu zmírnit dopady migrace?**

Pro návrh Cloud Services doporučujeme použít nejnovější rodinu hostovaného operačního systému.

1. Začněte plánovat migraci do novější rodiny.
2. Nastavte dočasná testovací nasazení, abyste otestovali cloudovou službu běžící na nové rodině.
3. Nastavte svou verzi hostovaného operačního systému na **automatickou** (osVersion = * v souboru [. cscfg](cloud-services-model-and-package.md#cscfg) ), aby migrace na nové verze operačního systému hosta automaticky probíhat.

**Co když moje webová aplikace vyžaduje hlubší integraci s operačním systémem?**

Pokud vaše architektura webové aplikace závisí na základních funkcích operačního systému, použijte funkce podporované platformou, jako jsou [úlohy po spuštění](cloud-services-startup-tasks.md) nebo jiné mechanismy rozšíření. Alternativně můžete také použít [Azure Virtual Machines](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – infrastruktura jako služba), kde zodpovídáte za údržbu základního operačního systému.

## <a name="next-steps"></a>Další kroky
Přečtěte si nejnovější [verze hostovaného operačního systému](cloud-services-guestos-update-matrix.md).
