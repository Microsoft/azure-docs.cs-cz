---
title: Zprovoznění a správa senzorů v programu Defender pro IoT Portal
description: Naučte se, jak pořídit, zobrazovat a spravovat senzory v programu Defender pro IoT Portal.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/27/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: a763d8b65049cd9f301379c2c038a1d799114653
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838708"
---
# <a name="onboard-and-manage-sensors-in-the-defender-for-iot-portal"></a>Zprovoznění a správa senzorů v programu Defender pro IoT Portal

Tento článek popisuje, jak připojit, zobrazit a spravovat senzory v programu Defender pro IoT Portal.

## <a name="onboard-sensors"></a>Nasazení senzorů

Senzor zaregistrujete pomocí Azure Defenderu pro IoT a stáhnete soubor aktivace senzoru.

### <a name="register-the-sensor"></a>Zaregistrovat senzor

Postup registrace:

1. Přejít na **uvítací** stránku v programu Defender pro IoT Portal.
1. Vyberte **senzor zprovoznění**.
1. Vytvořte název snímače. Doporučujeme, abyste zahrnuli IP adresu snímače, který jste nainstalovali jako součást názvu, nebo můžete použít snadno identifikovatelný název. Tím se zajistí snazší sledování a konzistentní pojmenování mezi názvem registrace v Azure Defenderu pro IoT Portal a IP adresou nasazeného senzoru zobrazeného v konzole senzorů.
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

Na portálu Defender pro IoT můžete zobrazit základní informace o integrovaných senzorech. 

1. Vyberte **weby a senzory**.
1. Na stránce **lokality a senzory** použijte nástroje pro filtrování a vyhledávání k nalezení informací o senzorech, které potřebujete.

Dostupné informace zahrnují:

- Kolik senzorů se připojilo
- Počet senzorů, které jsou připojené ke cloudu a jsou místně spravované
- Centrum přidružené k integrovanému senzoru
- Podrobnosti o snímači, jako je název přiřazený senzoru během připojování, zóna přidružená ke senzoru nebo jiné popisné informace přidané pomocí značek

## <a name="manage-onboarded-sensors"></a>Správa integrovaných senzorů

Pro úlohy správy související se senzory se používá Defender pro IoT Portal.

### <a name="export"></a>Export

Pokud chcete exportovat informace o senzoru, vyberte v horní části stránky **a senzorů** ikonu **exportovat** .

### <a name="edit"></a>Upravit

Pomocí nástrojů pro úpravy **webů a senzorů** přidejte a upravte název, zónu a značky lokality.

### <a name="delete"></a>Odstranit

Pokud odstraníte senzor připojený ke cloudu, informace se nebudou odesílat do centra IoT. Odstraňte místně připojené senzory, když už s nimi nepracujete.

Odstranění snímače:

1. Vyberte tři tečky (**...**) pro senzor, který chcete odstranit. 
1. Potvrďte odstranění.

### <a name="reactivate"></a>Znovu aktivovat

Můžete chtít aktualizovat režim, ve kterém je senzor spravovaný. Například:

- **Pracovat v režimu připojeném ke cloudu místo v místním spravovaném režimu**: Pokud to chcete udělat, aktualizujte aktivační soubor pro místně připojený senzor s aktivačním souborem pro senzor připojený ke cloudu. Po opětovné aktivaci se Detekce senzorů zobrazí jak na senzoru, tak i v programu Defender pro IoT Portal. Po úspěšném odeslání souboru opětovné aktivace se do Azure pošle nově zjištěné informace o výstrahách.

- **Pracovat v místně připojeném režimu místo v režimu připojeném k cloudu**: provedete to tak, že aktualizujete aktivační soubor pro senzor připojený k cloudu s aktivačním souborem pro místně spravovaný senzor. Po opětovné aktivaci se informace o detekci senzorů zobrazí jenom ve snímači.

- **Přidružte senzor k nové službě IoT Hub**: Pokud to chcete provést, znovu zaregistrujte senzor a nahrajte nový aktivační soubor.

Postup opětovné aktivace snímače:

1. Přejít na stránku **weby a senzory** na portálu Defender pro IoT Portal.

2. Vyberte senzor, pro který chcete odeslat nový aktivační soubor.

3. Odstraňte senzor.

4. Znovu zaveďte senzor ze stránky **připojování** v novém nebo novém centru IoT.

5. Stáhněte si aktivační soubor ze stránky **soubor pro aktivaci stahování** .

6. Přihlaste se ke konzole služby Defender pro IoT snímač.

7. V konzole senzorů vyberte **nastavení systému** a pak vyberte **Opětovná aktivace**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Nahrajte aktivační soubor a znovu aktivujte senzor.":::

8. Vyberte **nahrát** a vyberte soubor, který jste uložili.

9. Vyberte **aktivovat**. 

## <a name="see-also"></a>Viz také

[Aktivace a nastavení senzoru](how-to-activate-and-set-up-your-sensor.md)
