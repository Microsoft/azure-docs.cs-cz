---
title: Přehled Microsoft Azure Stack Edge | Microsoft Docs
description: Popisuje Azure Stack Edge, což je řešení úložiště, které používá fyzické zařízení pro přenos prostřednictvím sítě do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 11/04/2019
ms.author: alkohli
ms.openlocfilehash: 654ca34ae4c0e393920965083567e3d9c71148d0
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863627"
---
# <a name="what-is-azure-stack-edge"></a>Co je Azure Stack Edge? 

[!INCLUDE [data-box-edge-gateway-rename-note](../../includes/data-box-edge-gateway-rename-note.md)]

Azure Stack Edge je hraniční výpočetní zařízení s podporou AI a možnosti přenosu dat v síti. Tento článek vám poskytne přehled o řešení Azure Stack Edge, výhodách, klíčových funkcích a scénářích, kde můžete nasadit toto zařízení. 

Azure Stack Edge je řešení typu hardware jako služba. Microsoft dodá zařízení spravované v cloudu s vestavěným polem programovatelné brány (FPGA), které umožňuje akcelerované AI-Inferencing a má všechny možnosti brány síťového úložiště. 

## <a name="use-cases"></a>Případy použití

Tady jsou různé scénáře, kdy je možné Azure Stack Edge použít pro rychlé Machine Learning (ML) Inferencing na hranici a předzpracování dat před jejich odesláním do Azure.

- Při **odvozování pomocí Azure Machine Learning** – s Azure Stack Edge můžete spouštět modely ml, abyste získali rychlé výsledky, na jejichž základě se dá data odeslat do cloudu. Úplnou datovou sadu lze volitelně přenést, aby bylo možné pokračovat v přeučení a vylepšení modelů ML. Další informace o tom, jak používat hardwarové akcelerované modely Azure ML na Azure Stack hraničním zařízení, najdete v tématu [nasazení hardwarových akcelerovaných modelů Azure ml na Azure Stack Edge](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- Před odesláním do Azure můžete **předzpracovat** data – Transformujte data, abyste mohli vytvořit další napadnutelnou datovou sadu. Předzpracování lze použít k těmto účelům: 

    - Agregace dat
    - Úpravou dat můžete například odebrat osobní údaje.
    - Podmnožiny dat pro optimalizaci úložiště a šířky pásma nebo pro další analýzu.
    - Analýza a reakce na události IoT 

- **Přenos dat přes síť do Azure** – pomocí Azure Stack Edge můžete snadno a rychle přenášet data do Azure a umožnit tak další výpočty a analýzu nebo pro účely archivace. 


## <a name="key-capabilities"></a>Klíčové funkce

Azure Stack Edge má následující možnosti:

|Schopnost |Popis  |
|---------|---------|
|Akcelerované AI Inferencing| Povoluje vestavěnou FPGA.|
|Výpočetní operace       |Umožňuje analýzu, zpracování a filtrování dat.|
|Vysoký výkon | Vysoce výkonné výpočetní a datové přenosy.|
|Přístup k datům     | Přímý přístup k datům z rozšíření Azure Storage Blob a ze souborů Azure pomocí cloudových rozhraní API za účelem dalšího zpracování dat v cloudu. Místní mezipaměť v zařízení se používá pro rychlý přístup k naposledy použitým souborům.|
|Spravované v cloudu     |Zařízení a služba se spravují prostřednictvím Azure Portal.  |
|Nahrání offline     | Odpojený režim podporuje scénáře nahrání v režimu offline.|
|Podporované protokoly     | Podpora standardních protokolů SMB a NFS pro příjem dat. <br> Další informace o podporovaných verzích najdete v [Azure Stack požadavcích na systém Edge](data-box-edge-system-requirements.md).|
|Aktualizace dat     | Možnost aktualizovat místní soubory pomocí nejnovějších souborů z cloudu.|
|Šifrování    | BitLocker podporuje místní šifrování dat a bezpečný přenos dat do cloudu přes protokol *https*.|
|Omezení šířky pásma| Omezí omezení využití šířky pásma během špičky.|


## <a name="components"></a>Komponenty

Azure Stack hraniční řešení se skládá z prostředků Azure Stack Edge, Azure Stack hraniční fyzické zařízení a místního webového uživatelského rozhraní.

* **Fyzické zařízení Azure Stack Edge** – 1U server připojený k racku dodávaný Microsoftem, který se dá nakonfigurovat tak, aby odesílal data do Azure. 
    
* **Prostředek Azure Stack Edge** – prostředek v Azure Portal, který umožňuje spravovat Azure Stack hraniční zařízení z webového rozhraní, ke kterému můžete přistupovat z různých geografických umístění. Pomocí Azure Stackho hraničního prostředku můžete vytvářet a spravovat prostředky, zobrazovat a spravovat zařízení a výstrahy a spravovat sdílené složky.  

    <!--![The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Další informace najdete v [poznámkách k vytvoření objednávky pro zařízení Azure Stack Edge](data-box-edge-deploy-prep.md#create-a-new-resource).

* **Azure Stack lokální webové uživatelské rozhraní** – pomocí místního webového uživatelského rozhraní můžete spustit diagnostiku, vypnout a znovu spustit Azure Stack hraniční zařízení, zobrazit Kopírovat protokoly a kontaktovat podpora Microsoftu k zaznamenání žádosti o službu.

    <!--![The Azure Stack Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Informace o používání webového uživatelského rozhraní najdete v [části použití webového uživatelského rozhraní pro správu Azure Stackho okraje](data-box-edge-manage-access-power-connectivity-mode.md).


## <a name="region-availability"></a>Regionální dostupnost

Fyzické zařízení Azure Stack Edge, prostředek Azure a cílový účet úložiště, do kterého přenášíte data, nemusí být ve stejné oblasti.

- **Dostupnost prostředků** – seznam všech oblastí, ve kterých je prostředek Azure Stack Edge dostupný, najdete v části [produkty Azure dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Stack Edge je taky možné nasadit v cloudu Azure Government. Další informace najdete v tématu [co je Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).
    
- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure. Oblasti, ve kterých jsou uloženy účty úložiště Azure Stack hraniční data, by měly být umístěny blízko místa, kde se zařízení nachází pro optimální výkon. Účet úložiště umístěný daleko od zařízení bude vykazovat dlouhé latence a nižší výkon. 


## <a name="next-steps"></a>Další kroky

- Projděte si [požadavky na systém Azure Stack Edge](data-box-edge-system-requirements.md).
- Seznamte se s [limity Azure Stack hran](data-box-edge-limits.md).
- Nasaďte [Azure Azure Stack Edge](data-box-edge-deploy-prep.md) do Azure Portal.




