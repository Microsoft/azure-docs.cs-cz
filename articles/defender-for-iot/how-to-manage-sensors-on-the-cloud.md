---
title: Správa senzorů v programu Defender pro IoT Portal
description: Naučte se, jak pořídit, zobrazovat a spravovat senzory v programu Defender pro IoT Portal.
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: f407a65f60a1b969f17ebe00be39a888a09ec83d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752709"
---
# <a name="manage-sensors-in-the-defender-for-iot-portal"></a>Správa senzorů v programu Defender pro IoT Portal

Tento článek popisuje, jak připojit, zobrazit a spravovat senzory v programu [Defender pro IoT Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

## <a name="onboard-sensors"></a>Nasazení senzorů

Senzor zaregistrujete pomocí Azure Defenderu pro IoT a stáhnete soubor aktivace senzoru.

### <a name="register-the-sensor"></a>Zaregistrovat senzor

Postup registrace:

1. Přejít na **uvítací** stránku v programu [Defender pro IoT Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Vyberte **senzor zprovoznění**.
1. Vytvořte název snímače. Doporučujeme, abyste zahrnuli IP adresu snímače, který jste nainstalovali jako součást názvu, nebo můžete použít snadno identifikovatelný název. Tím se zajistí snazší sledování a konzistentní pojmenování mezi názvem registrace v Azure [Defenderu pro IoT Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) a IP adresou nasazeného senzoru zobrazeného v konzole senzorů.
1. Přidružte senzor k předplatnému Azure.
1. Vyberte režim připojení senzoru pomocí přepínače **připojeného ke cloudu** . Pokud je přepínač zapnutý, je senzor připojený ke cloudu. Pokud je přepínač vypnutý, je senzor místně spravován.

   - **Senzory připojené ke cloudu**: informace, které senzor detekuje, se zobrazí v konzole senzorů. Informace o výstrahách se doručují prostřednictvím služby IoT Hub a můžou se sdílet s ostatními službami Azure, jako je Azure Sentinel. Balíčky analýzy hrozeb se navíc dají z Azure Defenderu pro IoT Portal přidat do senzorů. Naopak když není senzor připojený ke cloudu, musíte stáhnout balíčky pro řešení hrozeb a pak je nahrát na své podnikové senzory. Pokud chcete programu Defender pro IoT povolit vkládání balíčků do senzorů, povolte přepínač **aktualizace automatických analýz pro analýzu hrozeb** . Další informace najdete v tématu [výzkum a balíčky pro analýzu hrozeb](how-to-work-with-threat-intelligence-packages.md).
   Vyberte službu IoT Hub, která bude sloužit jako brána mezi tímto senzorem a portálem Azure Defender pro IoT. Definujte název a zónu lokality. Můžete také přidat popisné značky. Název lokality, zóna a značky jsou popisné záznamy na [stránce weby a senzory](#view-onboarded-sensors).

   - **Místně spravované senzory**: informace, které detekuje senzory, se zobrazí v konzole senzorů. Pokud pracujete v letecké gapped síti a chcete mít jednotný přehled o všech informacích zjištěných více místně spravovanými senzory, pracujte s místní konzolou pro správu.

   Pro senzory připojené k cloudu je název definovaný během připojování název, který se zobrazí v konzole senzorů. Tento název nemůžete změnit přímo z konzoly. U místně spravovaných senzorů se název používaný během připojování uloží do Azure, ale dá se aktualizovat v konzole senzorů.

### <a name="download-the-sensor-activation-file"></a>Stáhnout aktivační soubor senzoru

Aktivační soubor snímače obsahuje pokyny týkající se režimu správy snímače. Pro každý senzor, který nasazujete, si stáhnete jedinečný aktivační soubor. Uživatel, který se přihlásí do konzoly senzorů pro první nahrání aktivačního souboru do snímače.

Stažení aktivačního souboru:

1. Na stránce **senzory** připojení vyberte **Stáhnout aktivační soubor**.
1. Zpřístupněte přístup k souboru uživateli, který se přihlašuje k konzole senzoru poprvé.

## <a name="view-onboarded-sensors"></a>Zobrazení integrovaných senzorů

Na [portálu Defender pro IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)můžete zobrazit důležité provozní informace o integrovaných senzorech.

1. Vyberte **weby a senzory**. Na stránce se zobrazuje počet senzorů, které jsou připojené ke cloudu a jsou místně spravované, a také tyto senzory:

- název snímače přiřazený během připojování
- Typ připojení (připojený ke cloudu nebo místně spravovaný)
- zóna přidružená ke senzoru
- Nainstalovaná verze senzoru
- Stav připojení senzoru ke cloudu.
- Čas posledního zjištění senzoru při připojování ke cloudu.

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

## <a name="next-steps"></a>Další kroky

[Aktivace a nastavení senzoru](how-to-activate-and-set-up-your-sensor.md)
