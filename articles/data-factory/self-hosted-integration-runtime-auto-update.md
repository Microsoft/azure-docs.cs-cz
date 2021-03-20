---
title: Oznámení automatické aktualizace a vypršení platnosti místního prostředí Integration Runtime
description: Informace o automatické aktualizaci prostředí Integration runtime a oznámení o vypršení platnosti
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 972015f0f42a8a869de0edcc8f0e921ae7278cd1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376254"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>Oznámení automatické aktualizace a vypršení platnosti místního prostředí Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak umožnit automatickou aktualizaci místního prostředí Integration runtime na nejnovější verzi a způsob, jakým ADF spravuje verze prostředí Integration runtime v místním prostředí.

## <a name="self-hosted-integration-runtime-auto-update"></a>Automatické aktualizace pro samoobslužné hostování Integration Runtime
Obecně platí, že když nainstalujete místní prostředí Integration runtime do svého místního počítače nebo virtuálního počítače Azure, máte dvě možnosti, jak spravovat verzi místního prostředí Integration Runtime: Automatické aktualizace nebo údržba ručně. Standard ADF vydává každý měsíc dvě nové verze prostředí Integration runtime v místním prostředí, které zahrnuje novou verzi funkcí, opravu chyb nebo vylepšení. Proto doporučujeme, aby si uživatelé aktualizovali nejnovější verzi, aby mohli získat nejnovější funkce a vylepšení.

Nejpohodlnější způsob je povolit automatické aktualizace při vytváření nebo úpravách místního prostředí Integration runtime. Pak se automaticky aktualizuje na nejnovější verzi. Aktualizaci můžete naplánovat také v nejvhodnějším časovém intervalu, podle kterého chcete.

![Povolit automatické aktualizace](media/create-self-hosted-integration-runtime/shir-auto-update.png)

Datum a čas poslední aktualizace můžete ověřit v klientovi Integration runtime v místním prostředí.

![Snímek obrazovky s kontrolou času aktualizace](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> Aby se zajistila stabilita prostředí Integration runtime v místním prostředí, i když vydáváme dvě verze, automaticky je aktualizujeme každý měsíc. V některých případech se někdy zjistí, že automaticky aktualizovaná verze je předchozí verze aktuální nejnovější verze. Pokud chcete získat nejnovější verzi, můžete přejít na [webu Download Center](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="self-hosted-integration-runtime-expire-notification"></a>Oznámení o vypršení platnosti Integration Runtime v místním prostředí
Pokud chcete ručně řídit, která verze prostředí Integration runtime v místním prostředí je, můžete zakázat nastavení automatických aktualizací a nainstalovat je ručně. Každá verze místního prostředí Integration runtime v místním prostředí vyprší za jeden rok. Zpráva o vypršení platnosti se zobrazuje na portálu ADF a klientovi Integration runtime **90 dní** před vypršením platnosti.

## <a name="next-steps"></a>Další kroky

- Projděte si [koncept prostředí Integration runtime v Azure Data Factory](./concepts-integration-runtime.md).

- Naučte se [vytvořit místní prostředí Integration runtime v Azure Portal](./create-self-hosted-integration-runtime.md).
