---
title: Jak se na Azure App Service izolované razítka vztahují slevy na rezervace
description: Přečtěte si, jak se slevové slevy vztahují na Azure App Service izolovaná razítka.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: banders
ms.openlocfilehash: cb4b371deadb1ae9e6ae048c3157809aff857c9d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298253"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>Jak se na Azure App Service izolované razítka vztahují slevy na rezervace

Po zakoupení Služba App Service v izolovaném prostředí rezervované kapacity poplatků za razítko se sleva rezervace automaticky použije pro poplatek za razítko v oblasti. Sleva rezervace se vztahuje na využití vygenerované měřičem poplatku za izolované razítko. Pracovní procesy, další přední strany a jakékoli další prostředky, které jsou přidružené k razítku, se budou účtovat běžnými sazbami.

## <a name="reservation-discount-application"></a>Aplikace slev pro rezervaci

Pro běh izolovaných razítek na každou hodinu se použije Služba App Service v izolovaném prostředía sleva za razítko. Pokud nemáte razítko nasazené po určitou hodinu, Rezervovaná kapacita je za tuto hodinu nevyužitá. Neprovádí se.

Po nákupu se rezervace, kterou zakoupíte, shoduje s izolovaným razítkem běžícím v konkrétní oblasti. Pokud jste toto razítko vypnuli, pak se slevy za rezervované automaticky aplikují na jiná razítka běžící v této oblasti. Pokud žádná razítka neexistují, použije se rezervace na další razítko, které je v oblasti vytvořeno.

Když razítka neběží po celou hodinu, bude se tato rezervace automaticky vztahovat na další vyhovující razítka ve stejné oblasti během jedné hodiny.

## <a name="choose-a-stamp-type---windows-or-linux"></a>Zvolit typ razítka – Windows nebo Linux

Prázdné izolované razítko ve výchozím nastavení vygeneruje měřič razítka systému Windows. Například pokud nejsou nasazeny žádné pracovní procesy. I nadále vygeneruje měřič při nasazení pracovníků Windows. Když nasadíte pracovní proces Linux, měřič se změní na měřič razítka systému Linux. Razítko vydává měřič Windows, když jsou nasazeni pracovníci systému Linux i Windows.

V důsledku toho se měřič razítka může během životnosti razítka měnit mezi systémy Windows a Linux. V obou případech jsou rezervace specifické pro operační systém. Budete si muset koupit rezervaci, která podporuje pracovníky, které plánujete nasadit na razítko. Pouze razítka se systémem Windows a smíšená razítka používají rezervaci systému Windows. Pouze razítka se systémem Linux používají rezervaci Linux.

Rezervace Linux byste si měli koupit jenom v případě, že máte v razítku _jenom_ procesy Linux.

## <a name="discount-examples"></a>Příklady slev

Následující příklady ukazují, jak se používá zvýhodněná sleva vyhrazené instance pro poplatky za razítko v závislosti na nasazeních.

- **Příklad 1**: Jednu instanci izolované rezervované kapacity můžete koupit v oblasti bez Služba App Service v izolovaném prostředí razítek. Do této oblasti nasadíte nové razítko a platíte za ně sazby za toto razítko.
- **Příklad 2**: Zakoupíte jednu instanci izolované rezervované kapacity razítka v oblasti, ve které už je Služba App Service v izolovaném prostředíé razítko nasazené. Zahájíte příjem rezervované míry pro nasazené razítko.
- **Příklad 3**: V oblasti s již nasazeným Služba App Service v izolovaném prostředí razítkem si můžete koupit jednu instanci izolované rezervované kapacity. Zahájíte příjem rezervované míry na nasazeném razítku. Později razítko odstraníte a nasadíte nový. Obdržíte rezervovanou sazbu pro nové razítko. Slevy se neprovádějí po dobu trvání bez nasazených razítek.
- **Příklad 4**: V oblasti si koupíte jednu instanci rezervované kapacity rezervovaných prostředků Linux a potom do této oblasti nasadíte nové razítko. Když se razítko zpočátku nasadí bez pracovních procesů, vygeneruje měřič razítka Windows. Neobdržela se žádná sleva. Když první pracovník pro Linux nasadí razítko, vygeneruje měřič razítka pro Linux a použije se sleva rezervace. Pokud je pracovník Windows později nasazený na razítko, měřič razítka se vrátí do systému Windows. Pro rezervaci rezervovaného razítka izolovaného systému Linux už neobdržíte slevu.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak spravovat rezervaci, najdete v tématu [správa Azure reservations](billing-manage-reserved-vm-instance.md).
- Další informace o předběžném nákupu Služba App Service v izolovaném prostředí rezervované kapacity pro úspory peněz najdete v tématu [platba za Azure App Service rezervované poplatky za vyhrazené kapacity](billing-prepay-app-service-isolated-stamp.md).
- Další informace o Azure Reservations najdete v následujících článcích:
  - [Co jsou Azure Reservations?](billing-save-compute-costs-reservations.md)
  - [Správa rezervací v Azure](billing-manage-reserved-vm-instance.md)
  - [Vysvětlení využití rezervací pro předplatné s tarify průběžných plateb](billing-understand-reserved-instance-usage.md)
  - [Vysvětlení použití rezervací pro podnikovou registraci](billing-understand-reserved-instance-usage-ea.md)
