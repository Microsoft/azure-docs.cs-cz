---
title: Azure Stack – přehled zkrácení R | Microsoft Docs
description: Popisuje Azure Stack hraniční Mini R, řešení úložiště pro vojenské aplikace, které používá přenosné fyzické zařízení s baterií pro přenos přes Wi-Fi do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 20efae411ae4d2fae9bf3b5e69dbfdd98da1603a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985642"
---
# <a name="what-is-the-azure-stack-edge-mini-r"></a>Co je Azure Stack hraniční Mini R?

Azure Stack hraniční Mini R je řešení typu hardware jako služba. Společnost Microsoft dodává robustní, cloudově spravované a vysoce přenosné zařízení s vestavěnou výpočetní jednotkou (VPU), která umožňuje akcelerované AI-Inferencing a má všechny možnosti brány síťového úložiště. Tato zařízení jsou vhodná pro použití v prostředích harshest a jsou optimalizovaná pro AI, analýzu a výpočetní výkon serveru.

Tento článek vám poskytne přehled o Azure Stackovém minimu řešení R, klíčovým funkcím a scénářích, kde můžete nasadit toto zařízení.


## <a name="key-capabilities"></a>Klíčové funkce

Azure Stack hraniční Mini R má následující možnosti:

|Schopnost |Description  |
|---------|---------|
|Robustní hardware| Robustní hardware určený pro harshest prostředí.|
|Ultra Portable| Ultra Portable, formulář založený na baterii.|
|Spravované v cloudu|Zařízení a služba se spravují prostřednictvím Azure Portal.|
|Výpočetní úlohy hraničních procesů|Umožňuje analýzu, zpracování a filtrování dat.<br>Podporuje virtuální počítače a kontejnerové úlohy. |
|Akcelerované AI Inferencing| Povoleno technologií Intel Movidius nesčetných X VPU.|
|Drátové a bezdrátové sítě | Povoluje přenos drátových a bezdrátových dat.|
|Přístup k datům     | Přímý přístup k datům z rozšíření Azure Storage Blob a ze souborů Azure pomocí cloudových rozhraní API za účelem dalšího zpracování dat v cloudu. Místní mezipaměť v zařízení se používá pro rychlý přístup k naposledy použitým souborům.|
|Odpojený režim|  Zařízení a službu je možné volitelně spravovat prostřednictvím centra Azure Stack. Nasaďte, spouštějte a spravujte aplikace v offline režimu. <br> Odpojený režim podporuje scénáře nahrání v režimu offline.|
|Podporované protokoly     |Podporuje standardní protokoly SMB, NFS a REST pro přijímání dat. <br> Další informace o podporovaných verzích najdete v [Azure Stackch Mini R požadavky na systém](azure-stack-edge-gpu-system-requirements.md).|
|Aktualizace dat     | Možnost aktualizovat místní soubory pomocí nejnovějších souborů z cloudu.|
|Dvojité šifrování    | Použití samočinně zašifrovaného disku poskytuje první vrstvu šifrování. SÍŤ VPN poskytuje druhou vrstvu šifrování. Podpora BitLockeru pro místně zašifrování dat a zabezpečení přenosu dat do cloudu přes *https* .|
|Omezení šířky pásma| Omezí omezení využití šířky pásma během špičky.|

## <a name="use-cases"></a>Případy použití

Tady jsou různé scénáře, kdy Azure Stack hraniční Mini R je možné použít pro rychlé Machine Learning (ML) Inferencing na okrajích a předzpracování dat před jejich odesláním do Azure.

- Při **odvozování pomocí Azure Machine Learning** – s Azure Stackou hraniční zkratkou R můžete spouštět modely ml a získat tak rychlé výsledky, na jejichž základě se můžete před odesláním dat do cloudu začít zpracovávat. Úplnou datovou sadu lze volitelně přenést, aby bylo možné pokračovat v přeučení a vylepšení modelů ML. Další informace o tom, jak používat hardwarové akcelerované modely Azure ML na Azure Stack hraničních zařízeních v R, najdete v tématu [nasazení hardwarových akcelerovaných modelů Azure ml na Azure Stack Edge Mini r](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- Před odesláním do Azure můžete data transformovat **data pomocí** možností výpočtů, jako jsou kontejnery nebo virtuální počítače, a vytvořit tak další napadnutelnou datovou sadu. Předzpracování lze použít k těmto účelům:

    - Agregace dat
    - Úpravou dat můžete například odebrat osobní údaje.
    - Podmnožiny dat pro optimalizaci úložiště a šířky pásma nebo pro další analýzu.
    - Analýza a reakce na události IoT

- **Přenos dat přes síť do Azure** – pomocí Azure Stack Edge Mini R můžete snadno a rychle přenášet data do Azure a umožnit tak další výpočty a analýzu nebo pro účely archivace.

## <a name="components"></a>Komponenty

Řešení Azure Stack Edge Mini R se skládá z Azure Stack hraničního prostředku, Azure Stack hraničního a nerobustního, Ultra přenosného fyzického zařízení a místního webového uživatelského rozhraní.

* **Azure Stack hraniční Mini R fyzické zařízení** – ultra přenosné zařízení s integrovaným baterií a robustním encasing poskytnutým společností Microsoft, které se dá nakonfigurovat tak, aby odesílalo data do Azure. Zařízení s baterií se vyváží méně než 7 kg.

    ![Azure Stack hraničních zařízení v R](media/azure-stack-edge-k-series-overview/perspective-view-1.png)

* **Prostředek Azure Stack Edge** – prostředek v Azure Portal, který vám umožní spravovat robustní, Azure Stack hraniční Mini zařízení R z webového rozhraní, ke kterému můžete přistupovat z různých geografických umístění. Pomocí Azure Stackho hraničního prostředku můžete vytvářet a spravovat prostředky, zobrazovat a spravovat zařízení a výstrahy a spravovat sdílené složky.  

* **Azure Stack hraniční Mini R lokální webové uživatelské rozhraní** – pomocí místního webového uživatelského rozhraní pro počáteční konfiguraci zařízení spusťte diagnostiku, vypněte a znovu spusťte Azure Stack hraničního zařízení R, zobrazte kopírování protokolů a kontaktujte podpora Microsoftu a zaznamenejte žádost o službu.


## <a name="region-availability"></a>Dostupnost v oblastech

Azure Stack fyzické zařízení, které je na hraničních zařízeních, a cílový účet úložiště, do kterého přenášíte data, nemusí být ve stejné oblasti.

- **Dostupnost prostředků** – seznam všech oblastí, ve kterých je prostředek Azure Stack Edge dostupný, najdete v části [produkty Azure dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). 

- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure. Oblasti, ve kterých se ukládají účty úložiště Azure Stack data o minim R, se nacházejí blízko místa, kde se zařízení nachází pro optimální výkon. Účet úložiště umístěný daleko od zařízení bude vykazovat dlouhé latence a nižší výkon.


## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [požadavky na systém pro Azure Stack na Mini R](azure-stack-edge-gpu-system-requirements.md).


