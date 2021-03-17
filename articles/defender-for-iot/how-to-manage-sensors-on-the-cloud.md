---
title: Zprovoznění a správa senzorů a předplatných v programu Defender pro IoT Portal
description: Naučte se, jak pořídit, zobrazovat a spravovat senzory v programu Defender pro IoT Portal.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 2/18/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 63b3b450e289b40aa9acbfb0d5170e8eb57f9e58
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733257"
---
# <a name="onboard-and-manage-sensors-and-subscriptions-in-the-defender-for-iot-portal"></a>Zprovoznění a správa senzorů a předplatných v programu Defender pro IoT Portal

Tento článek popisuje, jak připojit, zobrazit a spravovat senzory v programu [Defender pro IoT Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

## <a name="onboard-sensors"></a>Nasazení senzorů

Senzor zaregistrujete pomocí Azure Defenderu pro IoT a stáhnete soubor aktivace senzoru.

### <a name="register-the-sensor"></a>Zaregistrovat senzor

Postup registrace:

1. Přejít na **uvítací** stránku v programu [Defender pro IoT Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Vyberte **senzor zprovoznění**.
1. Vytvořte název snímače. Doporučujeme, abyste zahrnuli IP adresu snímače, který jste nainstalovali jako součást názvu, nebo můžete použít snadno identifikovatelný název. Tím se zajistí snazší sledování a konzistentní pojmenování mezi názvem registrace v Azure [Defenderu pro IoT Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) a IP adresou nasazeného senzoru zobrazeného v konzole senzorů.
1. Přidružte senzor k předplatnému Azure.
1. Vyberte režim správy senzorů pomocí přepínače **připojeného ke cloudu** . Pokud je přepínač zapnutý, je senzor připojený ke cloudu. Pokud je přepínač vypnutý, je senzor místně spravován.

   - **Senzory připojené ke cloudu**: informace, které senzor detekuje, se zobrazí v konzole senzorů. Informace o výstrahách se doručují prostřednictvím služby IoT Hub a můžou se sdílet s ostatními službami Azure, jako je Azure Sentinel.

   - **Místně spravované senzory**: informace, které detekuje senzory, se zobrazí v konzole senzorů. Pokud pracujete v letecké gapped síti a chcete mít jednotný přehled o všech informacích zjištěných více místně spravovanými senzory, pracujte s místní konzolou pro správu.

   Pro senzory připojené k cloudu je název definovaný během připojování název, který se zobrazí v konzole senzorů. Tento název nemůžete změnit přímo z konzoly. U místně spravovaných senzorů se název používaný během připojování uloží do Azure a bude možné ho aktualizovat v konzole senzorů.

1. Vyberte IoT Hub, který bude sloužit jako brána mezi tímto senzorem a Azure Defenderem pro IoT.
1. Pokud je senzor připojený ke cloudu, přidružte ho ke službě IoT Hub a pak definujte název a zónu lokality. Můžete také přidat popisné značky. Název lokality, zóna a značky jsou popisné záznamy na [stránce weby a senzory](#view-onboarded-sensors).

### <a name="download-the-sensor-activation-file"></a>Stáhnout aktivační soubor senzoru

Aktivační soubor snímače obsahuje pokyny týkající se režimu správy snímače. Pro každý senzor, který nasazujete, si stáhnete jedinečný aktivační soubor. Uživatel, který se přihlásí do konzoly senzorů pro první nahrání aktivačního souboru do snímače.

Stažení aktivačního souboru:

1. Na stránce **senzory** připojení vyberte **Stáhnout aktivační soubor**.
1. Zpřístupněte přístup k souboru uživateli, který se přihlašuje k konzole senzoru poprvé.

## <a name="view-onboarded-sensors"></a>Zobrazení integrovaných senzorů

Na [portálu Defender pro IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)můžete zobrazit základní informace o integrovaných senzorech.

1. Vyberte **weby a senzory**.
1. Pomocí nástrojů pro filtrování a vyhledávání Najděte informace o senzorech a analýzách hrozeb, které potřebujete.

- Kolik senzorů se připojilo
- Počet senzorů, které jsou připojené ke cloudu a jsou místně spravované
- Centrum přidružené k integrovanému senzoru
- Podrobnosti o snímači, jako je název přiřazený senzoru během připojování, zóna přidružená ke senzoru nebo jiné popisné informace přidané pomocí značek

## <a name="manage-onboarded-sensors"></a>Správa integrovaných senzorů

Použijte [Defender pro IoT Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) pro úlohy správy související se senzory.

Integrované senzory je možné zobrazit na stránce **weby a senzory** . Z této stránky můžete také upravovat informace o senzoru.

### <a name="export-sensor-details"></a>Podrobnosti o exportním senzoru

Pokud chcete exportovat informace o senzoru, vyberte v horní části stránky **a senzorů** ikonu **exportovat** .

### <a name="edit-sensor-zone-details"></a>Upravit podrobnosti zóny senzorů

Pomocí možností úprav **webů a senzorů** upravte název a zónu senzorů.

Postup úpravy:

1. Vyberte **tři tečky** (**...**) pro senzor, který chcete upravit.
1. Vyberte **Upravit**.
1. Aktualizujte zónu senzorů nebo vytvořte novou zónu.

### <a name="delete-a-sensor"></a>Odstranění snímače

Pokud odstraníte senzor připojený ke cloudu, informace se nebudou odesílat do centra IoT. Odstraňte místně připojené senzory, když už s nimi nepracujete.

Odstranění snímače:

1. Vyberte tři tečky (**...**) pro senzor, který chcete odstranit.
1. Potvrďte odstranění.

### <a name="reactivate-a-sensor"></a>Opětovná aktivace snímače 

Možná budete muset senzor znovu aktivovat, protože chcete:

- **Práce v režimu připojeném ke cloudu namísto místně spravovaného režimu**: po opětovné aktivaci se senzory zobrazí ve snímači a nově zjištěné informace o výstrahách se doručí prostřednictvím centra IoT. Tyto informace se dají sdílet s ostatními službami Azure, jako je třeba Sentinel Azure.

- **Funguje v lokálně spravovaném režimu místo v režimu připojeném do cloudu**: po opětovné aktivaci se informace o detekci senzorů zobrazí jenom ve snímači.

- **Přidružte senzor k nové službě IoT Hub**: Pokud to chcete udělat, zaregistrujte senzor znovu pomocí nového centra a pak stáhněte nový aktivační soubor.

Postup opětovné aktivace snímače:

1. Přejít na stránku **weby a senzory** na [portálu Defender pro IoT Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

2. Vyberte senzor, pro který chcete odeslat nový aktivační soubor.

3. Odstraňte senzor.

4. Zaregistrujte senzor znovu v novém režimu nebo pomocí nového centra IoT, a to výběrem možnosti připojit **senzor** ze stránky Začínáme.

5. Stáhněte si aktivační soubor.

1. Přihlaste se ke konzole služby Defender pro IoT snímač.

7. V konzole senzorů vyberte **nastavení systému** a pak vyberte **Opětovná aktivace**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Nahrajte aktivační soubor a znovu aktivujte senzor.":::

8. Vyberte **nahrát** a vyberte soubor, který jste uložili na stránce snímače desky.

9. Vyberte **aktivovat**.

## <a name="offboard-a-subscription"></a>Odpojení předplatného

Předplatná se spravují měsíčně. Když odpojení předplatné, bude se vám účtovat toto předplatné až do konce měsíce. 

Před zrušením předplatného odinstalujte všechny senzory, které jsou přidružené k předplatnému. Další informace o tom, jak odstranit senzor, najdete v tématu [odstranění snímače](#delete-a-sensor). 

Odpojení předplatného:

1. Přejděte na stránku s **cenami** .
1. Vyberte předplatné a pak vyberte ikonu **Odstranit** :::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false"::: .
1. V místní nabídce potvrzení zaškrtněte políčko a potvrďte tak, že jste odstranili všechny senzory přidružené k předplatnému.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="Zaškrtněte políčko a vyberte odpojení, aby se odpojení senzor.":::

1. Vyberte tlačítko **odpojení** . 

Místní prostředí to neovlivní, ale měli byste senzor odinstalovat z místního prostředí nebo ho znovu přiřadit k jinému předplatnému, aby se zabránilo jakémukoli souvisejícím datům v toku do místní konzoly pro správu. 

## <a name="see-also"></a>Viz také

[Aktivace a nastavení senzoru](how-to-activate-and-set-up-your-sensor.md)
