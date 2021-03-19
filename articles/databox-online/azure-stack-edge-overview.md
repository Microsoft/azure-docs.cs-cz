---
title: Přehled Microsoft Azure Stack Edge pro | Microsoft Docs
description: Popisuje Azure Stack Edge pro, řešení úložiště, které používá fyzické zařízení pro přenos prostřednictvím sítě do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/15/2021
ms.author: alkohli
ms.openlocfilehash: e6cd1f8a1f7d1777e786ab91637b4065a2c5e850
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585940"
---
# <a name="what-is-azure-stack-edge-pro-with-fpga"></a>Co je Azure Stack Edge Pro s FPGA?

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

Azure Stack Edge pro s FPGA je hraniční výpočetní zařízení s podporou AI a možnosti přenosu dat v síti. Tento článek poskytuje přehled Azure Stack Edge pro s řešeními FPGA, výhodami, klíčovými funkcemi a scénáři nasazení.

Azure Stack Edge pro s FPGA je řešení typu hardware jako služba. Microsoft dodá zařízení spravované v cloudu s vestavěným polem programovatelné brány (FPGA), které umožňuje akcelerované AI-Inferencing a má všechny možnosti brány síťového úložiště.

Azure Data Box Edge se přejmenuje jako Azure Stack Edge.

## <a name="use-cases"></a>Případy použití

Tady jsou různé scénáře, kdy Azure Stack Edge pro je možné použít pro rychlé Machine Learning (ML) Inferencing na okrajích a předzpracování dat před jejich odesláním do Azure.

- Při **odvozování pomocí Azure Machine Learning** – s Azure Stack Edge pro je možné spouštět modely ml a získat tak rychlé výsledky, na jejichž základě se můžete v případě, že se data odešlou do cloudu, zpracovávat. Úplnou datovou sadu lze volitelně přenést, aby bylo možné pokračovat v přeučení a vylepšení modelů ML. Další informace o tom, jak používat hardwarové akcelerované modely Azure ML na zařízení Azure Stack Edge pro, najdete v tématu [nasazení hardwarových akcelerovaných modelů Azure ml na Azure Stack Edge pro](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- Před odesláním do Azure můžete **předzpracovat** data – Transformujte data, abyste mohli vytvořit další napadnutelnou datovou sadu. Předzpracování lze použít k těmto účelům: 

    - Agregace dat
    - Úpravou dat můžete například odebrat osobní údaje.
    - Podmnožiny dat pro optimalizaci úložiště a šířky pásma nebo pro další analýzu.
    - Analýza a reakce na události IoT 

- **Přenos dat přes síť do Azure** – použijte Azure Stack Edge pro k snadnému a rychlému přenosu dat do Azure, abyste umožnili další výpočty a analýzy nebo pro účely archivace. 

## <a name="key-capabilities"></a>Klíčové funkce

Azure Stack Edge pro nabízí následující možnosti:

|Schopnost |Description  |
|---------|---------|
|Akcelerované AI Inferencing| Povoluje vestavěnou FPGA.|
|Výpočetní       |Umožňuje analýzu, zpracování a filtrování dat.|
|Vysoký výkon | Vysoce výkonné výpočetní a datové přenosy.|
|Přístup k datům     | Přímý přístup k datům z rozšíření Azure Storage Blob a ze souborů Azure pomocí cloudových rozhraní API za účelem dalšího zpracování dat v cloudu. Místní mezipaměť v zařízení se používá pro rychlý přístup k naposledy použitým souborům.|
|Spravované v cloudu     |Zařízení a služba se spravují prostřednictvím Azure Portal.  |
|Nahrání offline     | Odpojený režim podporuje scénáře nahrání v režimu offline.|
|Podporované protokoly     | Podpora standardních protokolů SMB a NFS pro příjem dat. <br> Další informace o podporovaných verzích najdete v tématu [požadavky na systém Azure Stack Edge pro](azure-stack-edge-system-requirements.md).|
|Aktualizace dat     | Možnost aktualizovat místní soubory pomocí nejnovějších souborů z cloudu.|
|Šifrování    | BitLocker podporuje místní šifrování dat a bezpečný přenos dat do cloudu přes protokol *https*.|
|Omezení šířky pásma| Omezí omezení využití šířky pásma během špičky.|
|ExpressRoute | Bylo přidáno zabezpečení prostřednictvím ExpressRoute. Použijte konfiguraci partnerského vztahu, při které se provoz z místních zařízení do koncových bodů cloudového úložiště přenáší přes ExpressRoute. Další informace najdete v článku [Přehled ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="components"></a>Komponenty

Řešení Azure Stack Edge pro se skládá z prostředků Azure Stack Edge, fyzického zařízení Azure Stack Edge pro a místního webového uživatelského rozhraní.

* **Fyzické zařízení Azure Stack Edge pro**: server připojený k racku dodávaný Microsoftem, který je možné nakonfigurovat tak, aby odesílal data do Azure.
    
* **Azure Stack hraniční prostředek**: prostředek v Azure Portal, který umožňuje spravovat Azure Stack Edge pro zařízení z webového rozhraní, ke kterému můžete přistupovat z různých zeměpisných míst. Pomocí Azure Stackho hraničního prostředku můžete vytvářet a spravovat prostředky, spravovat sdílené složky a zobrazovat a spravovat zařízení a výstrahy.
  
   <!--[The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

   Jak Azure Stack Edge pro blíží ke konci životního cyklu, vyplňují se žádné objednávky nových zařízení Azure Stack Edge pro. Pokud jste nový zákazník, doporučujeme, abyste se seznámili s používáním zařízení Azure Stack Edge pro-GPU pro vaše úlohy. Další informace najdete na webu [co je Azure Stack Edge pro s grafickým procesorem](azure-stack-edge-gpu-overview.md). Informace o řazení Azure Stack Edge pro s grafickým procesorem získáte, když přejdete na [vytvořit nový prostředek pro procesor Azure Stack Edge pro-GPU](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

   Pokud jste stávající zákazník, můžete přesto vytvořit nový prostředek Azure Stack Edge pro, pokud potřebujete nahradit nebo resetovat stávající zařízení Azure Stack Edge pro. Pokyny najdete v tématu [vytvoření objednávky pro zařízení Azure Stack Edge](azure-stack-edge-deploy-prep.md#create-new-resource-for-existing-device)pro.

* **Azure Stack lokální webové uživatelské rozhraní pro Edge** – pomocí místního webového uživatelského rozhraní můžete spustit diagnostiku, vypnout a znovu spustit Azure Stack Edge pro zařízení, zobrazit kopírování protokolů a kontaktovat podpora Microsoftu k zaznamenání žádosti o službu.

    <!--![The Azure Stack Edge Pro local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Informace o používání webového uživatelského rozhraní najdete v [části použití webového uživatelského rozhraní pro správu Azure Stack Edge pro](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Dostupnost v oblastech

Fyzické zařízení Azure Stack Edge pro, prostředek Azure a cílový účet úložiště, do kterého přenášíte data, nemusí být ve stejné oblasti.

- **Dostupnost prostředků** – seznam všech oblastí, ve kterých je dostupný Azure Stack hraniční prostředek, najdete v tématu [Dostupné produkty Azure v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Stack Edge pro je možné nasadit také v cloudu Azure Government. Další informace najdete v tématu [co je Azure Government?](../azure-government/documentation-government-welcome.md).
    
- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure. Oblasti, ve kterých se ukládají účty úložiště Azure Stack hraniční data pro, jsou umístěny blízko místa, kde se zařízení nachází pro optimální výkon. Účet úložiště umístěný daleko od zařízení bude vykazovat dlouhé latence a nižší výkon.

Služba Azure Stack Edge je neoblastní služba. Další informace najdete v tématu [oblasti a zóny dostupnosti v Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). Služba Azure Stack Edge nemá závislost na konkrétní oblasti Azure, takže je odolná vůči výpadkům v rámci zóny a výpadkům v rámci oblastí.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [požadavky na systém Azure Stack Edge pro](azure-stack-edge-system-requirements.md).
- Pochopte [omezení Azure Stack Edge pro](azure-stack-edge-limits.md).
- Nasaďte [Azure Stack Edge pro](azure-stack-edge-deploy-prep.md) v Azure Portal.
