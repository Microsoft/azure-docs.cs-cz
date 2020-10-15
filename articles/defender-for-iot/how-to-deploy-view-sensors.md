---
title: Zobrazení a Správa integrovaných senzorů v Azure Defenderu pro IoT
description: Tento článek popisuje, jak zobrazit a odstranit senzory připojení a také stáhnout nové aktivační soubory pro integrované senzory v Azure Defenderu pro IoT.
author: rkarlin
ms.author: rkarlin
ms.date: 10/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 6aec19d413e1730bc1599f6cbac1c62e9b304ecd
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094315"
---
# <a name="view-and-manage-onboarded-sensors"></a>Umožňuje zobrazit a spravovat senzory zprovoznění.

Tento článek popisuje, jak zobrazit a odstranit senzory připojení a také stáhnout nové aktivační soubory pro integrované senzory.

## <a name="update-sensor-management-mode"></a>Aktualizovat režim správy senzorů

Možná budete chtít aktualizovat režim, který váš senzor spravuje. Když to uděláte, budete muset odebrat aktuální senzor ze stránky správy senzorů a nahrát nový soubor aktivace.

- **Pracujte v cloudovém spravovaném režimu místo místně spravovaného režimu**: aktualizujte aktivační soubor pro místně spravovaný senzor pomocí aktivačního souboru pro cloudový spravovaný senzor. Po opětovné aktivaci se Detekce senzorů zobrazí jak na senzoru, tak v Azure Defenderu pro IoT Portal. Po úspěšném odeslání souboru opětovné aktivace se do Azure pošle nově zjištěné informace o výstrahách.

- **Pracujte v místně spravovaném režimu namísto cloudového spravovaného režimu**: aktualizujte aktivační soubor pro cloudový spravovaný senzor s aktivačním souborem pro místně spravovaný senzor. Po opětovné aktivaci se informace o detekci senzorů zobrazí jenom ve snímači.

- **Přidružit senzor k novému IoT Hub**: možná budete chtít svůj senzor přidružit k novému IoT Hub. Provedete to tak, že znovu zaregistrujete senzor a nahrajete nový aktivační soubor.

**Postup opětovné aktivace snímače:**

1. Přejděte na stránku Azure Defender pro IoT, **správu senzorů** .

2. Vyberte senzor, pro který chcete odeslat nový aktivační soubor.

3. Odstraňte ji.

4. Znovu zaveďte senzor ze stránky **zprovoznění** do nového režimu nebo pomocí nového IoT Hub.

5. Stáhněte si aktivační soubor ze stránky **soubor pro aktivaci stahování** .

6. Přihlaste se ke konzole Azure Defender pro IoT snímač.

7. V konzole senzorů vyberte **nastavení systému** a pak vyberte **Opětovná aktivace**.

   ![Snímek obrazovky s zobrazením opětovné aktivace](media/updates/image14.png)

8. Vyberte **nahrát** a vyberte soubor, který jste uložili.

9. Vyberte **aktivovat**.
 
## <a name="delete-sensors"></a>Odstranit senzory

Pokud odstraníte senzorem spravovaný Cloud, informace nebudou odesílány do IoT Hub.

Odstraňte místně spravované senzory, když už s nimi nepracujete.

**Pokud chcete odstranit senzory:**

1. Přejděte na stránku Azure Defender pro **správu senzorů** IoT.

2. Vyberte senzory, které chcete odstranit.

3. Vyberte **Odstranit senzor**.

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o možnostech konfigurace, přejděte k příručce Průvodce konfigurací modulu.
> [!div class="nextstepaction"]
> [Průvodce konfigurací modulů](./how-to-agent-configuration.md)
