---
title: Přehled Microsoft Azure Stack Edge pro | Microsoft Docs
description: Popisuje Azure Stack Edge pro, řešení úložiště, které používá fyzické zařízení pro přenos prostřednictvím sítě do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/09/2020
ms.author: alkohli
ms.openlocfilehash: 51469c23574f55c102f0c6fb98e5aa5178b59a4a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455736"
---
# <a name="what-is-azure-stack-edge-pro-with-fpga"></a>Co je Azure Stack Edge Pro s FPGA?

[!INCLUDE [data-box-edge-gateway-rename-note](../../includes/data-box-edge-gateway-rename-note.md)]

Azure Stack Edge pro s FPGA je hraniční výpočetní zařízení s podporou AI a možnosti přenosu dat v síti. Tento článek poskytuje přehled Azure Stack Edge pro s řešeními FPGA, výhodami, klíčovými možnostmi a scénáři, kde můžete nasadit toto zařízení.

Azure Stack Edge pro s FPGA je řešení typu hardware jako služba. Microsoft dodá zařízení spravované v cloudu s vestavěným polem programovatelné brány (FPGA), které umožňuje akcelerované AI-Inferencing a má všechny možnosti brány síťového úložiště. 

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

* **Azure Stack Edge pro fyzické zařízení** – 1U server připojený k racku dodávaný Microsoftem, který se dá nakonfigurovat tak, aby odesílal data do Azure.
    
* **Prostředek Azure Stack Edge** – prostředek v Azure Portal, který umožňuje spravovat Azure Stack Edge pro zařízení z webového rozhraní, ke kterému můžete přistupovat z různých geografických umístění. Pomocí Azure Stackho hraničního prostředku můžete vytvářet a spravovat prostředky, zobrazovat a spravovat zařízení a výstrahy a spravovat sdílené složky.  

    <!--![The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Další informace získáte, když přejdete na [vytvořit objednávku pro zařízení Azure Stack Edge pro](azure-stack-edge-deploy-prep.md#create-a-new-resource).

* **Azure Stack lokální webové uživatelské rozhraní pro Edge** – pomocí místního webového uživatelského rozhraní můžete spustit diagnostiku, vypnout a znovu spustit Azure Stack Edge pro zařízení, zobrazit kopírování protokolů a kontaktovat podpora Microsoftu k zaznamenání žádosti o službu.

    <!--![The Azure Stack Edge Pro local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Informace o používání webového uživatelského rozhraní najdete v [části použití webového uživatelského rozhraní pro správu Azure Stack Edge pro](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Dostupnost v oblastech

Fyzické zařízení Azure Stack Edge pro, prostředek Azure a cílový účet úložiště, do kterého přenášíte data, nemusí být ve stejné oblasti.

- **Dostupnost prostředků** – seznam všech oblastí, ve kterých je dostupný Azure Stack hraniční prostředek, najdete v tématu [Dostupné produkty Azure v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Stack Edge pro je možné nasadit také v cloudu Azure Government. Další informace najdete v tématu [co je Azure Government?](../azure-government/documentation-government-welcome.md).
    
- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure. Oblasti, ve kterých se ukládají účty úložiště Azure Stack hraniční data pro, jsou umístěny blízko místa, kde se zařízení nachází pro optimální výkon. Účet úložiště umístěný daleko od zařízení bude vykazovat dlouhé latence a nižší výkon.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [požadavky na systém Azure Stack Edge pro](azure-stack-edge-system-requirements.md).
- Pochopte [omezení Azure Stack Edge pro](azure-stack-edge-limits.md).
- Nasaďte [Azure Stack Edge pro](azure-stack-edge-deploy-prep.md) v Azure Portal.