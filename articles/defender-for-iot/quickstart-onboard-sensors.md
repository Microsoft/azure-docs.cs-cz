---
title: 'Rychlý Start: zprovoznění senzorů v Azure Defenderu pro IoT'
description: Naučte se, jak začlenit senzory pro nasazení Azure Defenderu pro IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2020
ms.author: rkarlin
ms.openlocfilehash: 28d64f5d9d845c2a14f0277799213501e1d68010
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421749"
---
# <a name="quickstart-deploy-and-onboard-a-sensor"></a>Rychlý Start: nasazení a zprovoznění snímače

Tento článek poskytuje přehled procesu nasazení senzoru. Snímače musí být připojené k portálu Azure Defender pro IoT Portal.

Tento proces vyžaduje získání předkonfigurovaného senzoru nebo získání zařízení s certifikovaným senzorem a instalaci softwaru snímače sami.

Pokud pracujete se zařízením s certifikovaným senzorem, doporučujeme před zahájením zkontrolovat [Průvodce specifikacemi hardwaru pro Azure Defender for IoT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) .


> [!NOTE]
> Místní konzolu pro správu Azure Defender pro IoT není nutné připojit.


Senzory připojování vám umožní:

|||
|------ | ----------- |
| **Definovat název snímače** | Pojmenujte senzor, který se chystáte připojit, a přidružte ho k IoT Hub nebo předplatnému.<br /><br />Další informace najdete v tématu **senzory zprovoznění** .|
|**Zvolit předplatné a počet potvrzených zařízení**|Vyberte předplatné a počet zařízení zahrnutých v rámci předplatného. Zadejte číslo v přírůstcích po 1000.|
| **Definování režimu správy senzorů** | Určete, kde se zobrazí Asset, výstraha a další informace zjištěné senzorem. To se určuje na základě vámi definovaného **režimu správy senzorů** .<br /><br />**Místně spravovaný režim** : informace zjištěné senzorem se zobrazí v konzole senzorů. Informace o detekci se také sdílí s místní konzolou pro správu, pokud je senzor připojený k němu.<br /><br />**Režim Cloud Managed** : informace zjištěné senzorem se zobrazí v konzole senzorů. Kromě toho jsou informace o výstrahách doručovány prostřednictvím IoT Hub a mohou být sdíleny s ostatními službami Azure, například pomocí Azure Sentinel.<br />Další informace najdete v tématu **senzor zprovoznění** . |
| **Získání aktivačního souboru senzoru** | Pro **místně spravované** senzory se ke správě autorizovaných dob aktivace senzoru používá aktivační soubor.<br /><br />Pro **cloudy spravované** senzory se jako propojení mezi senzorem a IoT Hub používá aktivační soubor. Další informace najdete v tématu **senzory zprovoznění**. |
| **Nahrání aktivačního souboru na senzor** | Aktivační soubory musí být nahrány do vašich podnikových senzorů. Monitorování sítě a přístup k funkcím konzoly snímače nebudou k dispozici, dokud nebude odeslán aktivační soubor. Další informace najdete v tématu **nahrání aktivačních souborů snímače**. |
| **Aktualizovat parametry sítě senzorů před aktivací** | Parametry aktualizace definované při instalaci senzoru Další informace najdete v tématu **Chyba. Zdroj odkazu nebyl nalezen**.|


**Postup nasazení snímače:**

1. Přejít na Azure Defender pro IoT z Azure Portal.

2. Vyberte Začínáme **.**

3. V části **zjišťování sítě** vyberte **nastavit**.

   ![Azure Defender pro IoT – zjištění zobrazení v síti](media/updates/image5.png)

4. Vyberte možnost získání senzoru.

   ![Zobrazení síťového snímače Azure Defenderu pro IoT](media/updates/image6.png)

  - **Koupit předkonfigurovaný senzor** : Společnost Microsoft a šipka mají partnerství, aby poskytovaly předem nakonfigurované senzory. Chcete-li zakoupit předkonfigurovaný senzor, šipka kontaktu na adrese: <hardware.sales@arrow.com> . Senzor se doručí do vašeho zařízení. Je nainstalovaná nejnovější verze.

  - **Přineste si vlastní zařízení (instalace ISO)** : řešení běží na certifikovaných zařízeních. Při nákupu certifikovaného zařízení použijte [Průvodce specifikacemi hardwaru pro Azure Defender pro IoT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) .

    - V nabídce **vybrat verzi** vyberte verzi.

    - Vyberte **Stáhnout** a uložte soubor. Podrobnosti o stažení bitové kopie ISO a instalaci softwaru snímače najdete v **Průvodci instalací Azure Defenderu pro IoT** .

5. Po instalaci softwaru na senzor nebo obdržení předem nakonfigurovaného senzoru proveďte úlohy nastavení sítě. Podrobnosti najdete v [Průvodci nastavením sítě](https://aka.ms/AzureDefenderForIoTNetworkSetup) .

## <a name="onboard-a-sensor"></a>Připojení snímače

Snímače musí být připojené k portálu Azure Defender pro IoT Portal. Připojování se provádí ve dvou fázích:

1. Zaregistrujte senzor pomocí Azure Defenderu pro IoT Portal.

2. Stáhněte si aktivační soubor pro senzor. Soubor se později nahraje na senzor.

**Přístup ke stránce připojení:**

1. Přejděte na stránku Microsoft Azure Defender pro IoT **Začínáme** .

2. Vyberte **2)** z Azure Defenderu pro IoT.

   ![Snímek obrazovky se zobrazením stránky na panelu](media/updates/image7.png)

3. Otevře se stránka **snímače** . Poskytuje možnosti registrace snímače a stažení aktivačního souboru.

   ![Snímek obrazovky zobrazení stránky snímače na desce](media/quickstart/onboard-sensors.png)

**Postup registrace snímače:**

1. Vyberte název snímače. Pro cloudy spravované senzory se v názvu, který je tady definován, používá název, který se zobrazí v konzole senzorů: Tento název nejde změnit z konzoly. U místně spravovaných senzorů se název, který se použije tady, uloží do Azure, ale dá se aktualizovat v konzole senzorů. Doporučuje se zahrnout IP adresu snímače, který jste nainstalovali jako součást názvu, nebo použít snadno identifikovatelný název. Tím se zajistí snazší sledování a konzistentní pojmenování mezi názvem registrace v Azure Defenderu pro službu IoT Portal a IP adresou nasazeného senzoru zobrazeného v konzole senzorů.

2. V rozevíracím seznamu předplatné vyberte předplatné.
3. Do pole **potvrzená zařízení** zadejte počet zařízení zahrnutých v rámci předplatného. U každého snímače, který jste připojili ke stejnému předplatnému, byste měli toto číslo znovu zadat.  Pokud je například předplatné "A" přidruženo k zařízením 6000, zadejte 6000 pro každý senzor přidružený k předplatnému "A".   
4. Vyberte režim správy senzorů pomocí přepínače **Cloud Connect** . Pokud je přepínač zapnutý, senzor je **spravovaný v cloudu**. Pokud je přepínač vypnutý, je senzor **místně spravován**.


| Režim správy senzorů | Popis                                                |
| ---------------------- | ---------------------------------------------------------  |
| **Spravované v cloudu**          | Informace zjištěné senzorem se zobrazí v konzole senzorů. Kromě toho jsou informace o výstrahách doručovány prostřednictvím IoT Hub a mohou být sdíleny s ostatními službami Azure, například pomocí Azure Sentinel.<br /><br />Vyberte IoT Hub pro přidružení k tomuto senzoru.<br /><br />Do cloudu spravovaných senzory budete potřebovat nahrát soubor spravovaného cloudu.<br /><br />Podrobnosti najdete v tématu **nahrání aktivačních souborů snímače** . |
| **Místně spravovaná**        | Informace zjištěné senzory, které jsou místně spravovány, se zobrazí v konzole senzorů. Pokud pracujete v letecké gapped síti a chcete mít jednotný přehled o všech informacích zjištěných více místně spravovanými senzory, pracujte s místní konzolou pro správu.<br /><br />Senzory, které jsou *lokálně spravované* , jsou přidružené k předplatnému Azure a obsahují pokyny týkající se senzorů doby vypršení platnosti aktivace.<br /><br />Vyberte předplatné, které se má přidružit k tomuto senzoru.<br /><br />Do každého snímače budete potřebovat nahrát místně spravovaný aktivační soubor. Podrobnosti najdete v tématu **nahrání aktivačních souborů snímače** . |

5. Vyberte **Zaregistrovat**.

6. Na stránce soubor aktivace pro stahování vyberte **Stáhnout aktivační** soubor.

   ![Snímek obrazovky s integrovaným zobrazením senzorů](media/updates/image9.png)

7. Soubor uložte. Formát názvu souboru je: `<hub_name>_<sensor_name>` . Název snímače odkazuje na název, který jste definovali výše.

8. Vyberte **Dokončit**.

9. Pokud chcete dokončit proces zprovoznění senzoru, nahrajte soubor na senzor z konzoly Azure Defender pro IoT.
 
## <a name="upload-a-sensor-activation-file"></a>Nahrání souboru aktivace snímače

Tento článek popisuje, jak nahrát aktivační soubor do snímače. Měli byste soubor přijmout při připojování snímače.

**Místně spravované aktivační soubory**

Místně spravované senzory jsou přidružené k předplatnému Azure.  Aktivační soubor pro místně spravované senzory obsahuje datum vypršení platnosti. Jeden měsíc před tímto datem se v horní části konzole senzorů zobrazí varovná zpráva. Upozornění zůstane až po aktualizaci aktivačního souboru.

Po vypršení platnosti aktivačního souboru můžete pokračovat v práci s funkcí Azure Defender for IoT.

**Soubory s aktivací spravované v cloudu**

Senzory, které jsou spravovány v cloudu, jsou přidruženy k Azure IoT Hub. Tyto senzory nejsou omezeny časovými intervaly pro aktivaci souborů. Aktivační soubor pro cloudové spravované senzory slouží k zajištění připojení k IoT Hub.

Podrobnosti o službě IoT Hub najdete v tématu [o IoT Hub](../iot-hub/about-iot-hub.md).

**Postup nahrání aktivačního souboru:**

1. Ověřte, že máte:

   - IP adresa pro senzor definovaný během instalace

   - Přihlašovací údaje uživatele potřebné pro senzor

2. Přístup ke konzole Azure Defender pro IoT z prohlížeče pomocí IP adresy pro tento senzor.

3. Přihlaste se ke konzole Azure Defender pro IoT snímač.

   ![Azure Defender pro zobrazení protokolů konzoly IoT](media/updates/image11.png)

4. Po úspěšném přihlášení se otevře obrazovka aktivace. Vyberte nahrát a zvolte aktivační soubor, který jste uložili.

   ![Zobrazení aktivace v Azure Defenderu IoT](media/updates/image12.png)

5. Schvalte podmínky a ujednání.

6. Vyberte **aktivovat**. Parametry konfigurace sítě snímače byly definovány během instalace softwaru nebo při nákupu předem nakonfigurovaného senzoru. Byly definovány následující parametry:
   - IP adresa
   - DNS  
   - Výchozí brána
   - Maska podsítě
   - Název hostitele
 
   Tyto informace možná budete chtít před aktivací senzoru aktualizovat z těchto důvodů:
   - Je potřeba změnit předem nakonfigurované předdefinované parametry.  
   - Chcete znovu nakonfigurovat parametry sítě po instalaci. před aktivací senzoru můžete také definovat nastavení proxy serveru.

7. Vyberte odkaz **Konfigurace sítě senzoru** v dialogovém okně Aktivace.  

   ![Snímek obrazovky s úpravami zobrazení konfigurace sítě](media/updates/image13.png)

8. Zobrazí se parametry definované během instalace. Možnost je k dispozici také pro definování proxy serveru. Podle potřeby aktualizujte a vyberte **Uložit**.
 
 
## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak nasazovat senzory. Další informace o tom, jak začít, najdete v následujícím článku:

- [Začínáme](getting-started.md)
