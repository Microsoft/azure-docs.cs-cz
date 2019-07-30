---
title: Microsoft Azure Data Box Edge – přehled | Microsoft Docs
description: Popisuje Azure Data Box Edge řešení úložiště, které používá fyzické zařízení pro přenos prostřednictvím sítě do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 07/17/2019
ms.author: alkohli
ms.openlocfilehash: 69580f956b603423ef302353953a45ad5d00391e
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305425"
---
# <a name="what-is-azure-data-box-edge"></a>Co je Azure Data Box Edge? 

Azure Data Box Edge je zařízení s hraničními výpočetními funkcemi s podporou AI, které má funkce pro síťový přenos dat. Tento článek poskytuje přehled řešení Data Box Edge, jeho výhody, klíčové funkce a scénáře, ve kterých můžete toto zařízení nasadit. 

Data Box Edge je řešení typu hardware jako služba. Microsoft dodá zařízení spravované v cloudu s integrovaným polem programovatelné brány (FPGA), které umožňuje akcelerované AI-Inferencing a má všechny možnosti brány úložiště. 

## <a name="use-cases"></a>Případy použití

Tady jsou různé scénáře, kdy je možné Data Box Edge použít pro rychlé Machine Learning (ML) Inferencing na hranici a předzpracování dat před jejich odesláním do Azure.

- Při **odvozování pomocí Azure Machine Learning** – s data box Edge můžete spouštět modely ml a získat tak rychlé výsledky, na jejichž základě se dá data do cloudu odeslat. Úplnou datovou sadu lze volitelně přenést, aby bylo možné pokračovat v přeučení a vylepšení modelů ML. Další informace o tom, jak používat hardwarové akcelerované modely Azure ML na Data Box Edge zařízení, najdete v tématu [nasazení hardwarových akcelerovaných modelů Azure ml na data box Edge](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- Před odesláním do Azure můžete předzpracovat data – Transformujte data, abyste mohli vytvořit další napadnutelnou datovou sadu. Předzpracování lze použít k těmto účelům: 

    - Agregace dat
    - Úpravou dat můžete například odebrat osobní údaje.
    - Podmnožiny dat pro optimalizaci úložiště a šířky pásma nebo pro další analýzu.
    - Analýza a reakce na události IoT 

- **Přenos dat do Azure přes síť** – Data Box Edge umožňuje snadno a rychle přenášet data do Azure, aby bylo možné provádět další výpočetní operace a analýzy pro účely archivace. 


## <a name="key-capabilities"></a>Klíčové funkce

Data Box Edge nabízí následující funkce:

|Funkce |Popis  |
|---------|---------|
|Akcelerované AI Inferencing| Povoluje vestavěnou FPGA.|
|Výpočetní operace       |Umožňuje analýzu, zpracování a filtrování dat.|
|Vysoký výkon | Vysoce výkonné výpočetní a datové přenosy.|
|Přístup k datům     | Přímý přístup k datům z rozšíření Azure Storage Blob a ze souborů Azure pomocí cloudových rozhraní API za účelem dalšího zpracování dat v cloudu. Místní mezipaměť v zařízení se používá pro rychlý přístup k naposledy použitým souborům.|
|Spravované v cloudu     |Zařízení a služba se spravují prostřednictvím Azure Portal.  |
|Nahrání offline     | Odpojený režim podporuje scénáře nahrání v režimu offline.|
|Podporované protokoly     | Podpora standardních protokolů SMB a NFS pro příjem dat. <br> Další informace o podporovaných verzích najdete v [systémových požadavcích pro Data Box Edge](data-box-edge-system-requirements.md).|
|Aktualizace dat     | Možnost aktualizovat místní soubory pomocí nejnovějších souborů z cloudu.|
|Šifrování    | BitLocker podporuje místní šifrování dat a bezpečný přenos dat do cloudu přes protokol *https*.|
|Omezení šířky pásma| Omezí omezení využití šířky pásma během špičky.|


## <a name="components"></a>Komponenty

Řešení Data Box Edge sestává z prostředku Data Box Edge, fyzického zařízení Data Box Edge a místního webového uživatelského rozhraní.

* **Fyzické zařízení Data Box Edge** – server 1U namontovaný Microsoftem do racku, který lze nakonfigurovat na odeslání dat do Azure. 
    
* **Prostředek Data Box Edge** – prostředek na webu Azure Portal, který vám umožní spravovat zařízení Data Box Edge z webového rozhraní, ke kterému můžete získat přístup z různých geografických umístění. Prostředek Data Box Edge slouží k vytvoření a správě prostředků, zobrazení, správě zařízení a výstrah a ke správě sdílených složek.  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Další informace najdete v poznámkách k [vytvoření objednávky pro zařízení data box Edge](data-box-edge-deploy-prep.md#create-a-new-resource).

* **Místní webové uživatelské rozhraní Data Box** – můžete ho použít ke spuštění diagnostiky, vypnutí nebo restartování zařízení Data Box Edge, zobrazení protokolů kopírování a kontaktování podpory Microsoftu, aby vytvořila žádost o služby.

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Informace o používání uživatelského rozhraní založeného na webu najdete v tématu [Použití uživatelského rozhraní založeného na webu ke správě vašeho zařízení Data Box](data-box-edge-manage-access-power-connectivity-mode.md).


## <a name="region-availability"></a>Dostupnost v oblastech

Fyzické zařízení Data Box Edge, prostředek Azure a účet cílového úložiště, do kterého přenášíte data, se nemusí nacházet ve stejné oblasti.

- **Dostupnost prostředků** – seznam všech oblastí, ve kterých je prostředek data box Edge k dispozici, najdete v části [produkty Azure dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Data Box Edge taky můžete nasadit v cloudu Azure Government. Další informace najdete v tématu [co je Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).
    
- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure. Oblasti, ve kterých se ukládají účty úložiště Data Box Edge data by se měly umístit blízko místa, kde se zařízení nachází pro optimální výkon. Účet úložiště umístěný daleko od zařízení bude vykazovat dlouhé latence a nižší výkon. 


## <a name="next-steps"></a>Další postup

- Přečtěte si [systémové požadavky služby Data Box Edge](data-box-edge-system-requirements.md).
- Seznamte se s [omezeními služby Data Box Edge](data-box-edge-limits.md).
- Rychle nasaďte [Azure Data Box Edge](data-box-edge-deploy-prep.md) na webu Azure Portal.




