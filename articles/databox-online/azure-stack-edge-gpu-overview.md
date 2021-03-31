---
title: Microsoft Azure Stack Edge pro s přehledem GPU | Microsoft Docs
description: Popisuje Azure Stack Edge pro s grafickým procesorem, řešení úložiště, které používá fyzické zařízení pro přenos prostřednictvím sítě do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 01/18/2021
ms.author: alkohli
ms.openlocfilehash: 8e829d149e96cf658c7a5aaf37291e84def2061e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602651"
---
# <a name="what-is-azure-stack-edge-pro-with-gpu"></a>Co je Azure Stack Edge Pro s GPU?

Azure Stack Edge pro s GPU je hraniční výpočetní zařízení s podporou AI a možnosti přenosu dat v síti. Tento článek vám poskytne přehled o řešení Azure Stack Edge pro, výhodách, klíčových funkcích a scénářích, kde můžete nasadit toto zařízení.

Azure Stack Edge pro s grafickým procesorem je řešení typu hardware jako služba. Společnost Microsoft si dodává cloudové zařízení, které funguje jako brána síťového úložiště, a má vestavěnou funkci grafického procesoru (GPU), která umožňuje akcelerovanou AI-Inferencing. 

## <a name="use-cases"></a>Případy použití

Tady jsou různé scénáře, kdy Azure Stack Edge pro je možné použít pro rychlé Machine Learning (ML) Inferencing na okrajích a předzpracování dat před jejich odesláním do Azure.

- Při **odvozování pomocí Azure Machine Learning** – s Azure Stack Edge pro je možné spouštět modely ml a získat tak rychlé výsledky, na jejichž základě se můžete v případě, že se data odešlou do cloudu, zpracovávat. Úplnou datovou sadu lze volitelně přenést, aby bylo možné pokračovat v přeučení a vylepšení modelů ML. Další informace o tom, jak používat hardwarové akcelerované modely Azure ML na zařízení Azure Stack Edge pro, najdete v tématu [nasazení hardwarových akcelerovaných modelů Azure ml na Azure Stack Edge pro](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- Před odesláním do Azure pomocí možností výpočetních prostředků, jako jsou například kontejnerové úlohy a Virtual Machines k vytvoření užitečnější datové sady, **předzpracování** dat Transformujte data. Předzpracování lze použít k těmto účelům: 

    - Agregace dat
    - Úpravou dat můžete například odebrat osobní údaje.
    - Podmnožiny dat pro optimalizaci úložiště a šířky pásma nebo pro další analýzu.
    - Analýza a reakce na události IoT 

- **Přenos dat přes síť do Azure** – použijte Azure Stack Edge pro k snadnému a rychlému přenosu dat do Azure, abyste umožnili další výpočty a analýzy nebo pro účely archivace. 

## <a name="key-capabilities"></a>Klíčové funkce

Azure Stack Edge pro nabízí následující možnosti:

|Schopnost |Description  |
|---------|---------|
|Akcelerované AI Inferencing| Povoleno integrovaným grafickým procesorem (jeden nebo dva v závislosti na modelu).|
|Hraniční Computing      |Podporuje úlohy virtuálních počítačů a kontejnerů, aby bylo možné analyzovat, zpracovávat a filtrovat data. |
|Přístup k datům     | Přímý přístup k datům z rozšíření Azure Storage Blob a ze souborů Azure pomocí cloudových rozhraní API za účelem dalšího zpracování dat v cloudu. Místní mezipaměť v zařízení se používá pro rychlý přístup k naposledy použitým souborům.|
|Spravované v cloudu     |Zařízení a služba se spravují prostřednictvím Azure Portal.  |
|Nahrání offline     | Odpojený režim podporuje scénáře nahrání v režimu offline.|
|Podporované protokoly pro přenos souborů      | Podpora pro protokoly Standard SMB, NFS a REST pro příjem dat. <br> Další informace o podporovaných verzích najdete v tématu [požadavky na systém Azure Stack Edge pro](azure-stack-edge-system-requirements.md).|
|Aktualizace dat     | Možnost aktualizovat místní soubory pomocí nejnovějších souborů z cloudu.|
|Šifrování    | BitLocker podporuje místní šifrování dat a bezpečný přenos dat do cloudu přes protokol *https*.|
|Omezení šířky pásma| Omezí omezení využití šířky pásma během špičky.|
<!--|ExpressRoute | Bylo přidáno zabezpečení prostřednictvím ExpressRoute. Použijte konfiguraci partnerského vztahu, při které se provoz z místních zařízení do koncových bodů cloudového úložiště přenáší přes ExpressRoute. Další informace najdete v článku [Přehled ExpressRoute](../expressroute/expressroute-introduction.md).|-->

## <a name="components"></a>Komponenty

Řešení Azure Stack Edge pro se skládá z prostředků Azure Stack Edge, fyzického zařízení Azure Stack Edge pro a místního webového uživatelského rozhraní.

* **Azure Stack Edge pro fyzické zařízení** – 1U server připojený k racku dodávaný Microsoftem, který se dá nakonfigurovat tak, aby odesílal data do Azure.
    
* **Prostředek Azure Stack Edge** – prostředek v Azure Portal, který umožňuje spravovat Azure Stack Edge pro zařízení z webového rozhraní, ke kterému můžete přistupovat z různých geografických umístění. Pomocí Azure Stackho hraničního prostředku můžete vytvářet a spravovat prostředky, zobrazovat a spravovat zařízení a výstrahy a spravovat sdílené složky.  

    Další informace získáte, když přejdete na [vytvořit objednávku pro zařízení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).

* **Místní webové uživatelské rozhraní Azure Stack Edge pro** – místní uživatelské rozhraní založené na prohlížeči na vašem zařízení Azure Stack Edge pro je primárně určené pro počáteční konfiguraci zařízení. Pomocí místního webového uživatelského rozhraní můžete spustit diagnostiku, vypnout a restartovat zařízení Azure Stack Edge pro, zobrazit kopírování protokolů a kontaktovat podpora Microsoftu a požádat o soubor žádosti o službu.

    Informace o používání webového uživatelského rozhraní najdete v [části použití webového uživatelského rozhraní pro správu Azure Stack Edge pro](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Dostupnost v oblastech

Fyzické zařízení Azure Stack Edge pro, prostředek Azure a cílový účet úložiště, do kterého přenášíte data, nemusí být ve stejné oblasti.

- **Dostupnost prostředků** – pro tuto verzi je prostředek k dispozici v oblastech východní USA, západní EU a Jižní východní Asie.

- **Dostupnost zařízení** – seznam všech zemí nebo oblastí, ve kterých je zařízení Azure Stack Edge pro dostupné, najdete v části **dostupnost** na kartě **Azure Stack Edge** pro [Azure Stack ceny pro Edge pro](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro).
    
- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure. Oblasti, ve kterých se ukládají účty úložiště Azure Stack hraniční data pro, jsou umístěny blízko místa, kde se zařízení nachází pro optimální výkon. Účet úložiště umístěný daleko od zařízení bude vykazovat dlouhé latence a nižší výkon.

Služba Azure Stack Edge je neoblastní služba. Další informace najdete v tématu [oblasti a zóny dostupnosti v Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). Služba Azure Stack Edge nemá závislost na konkrétní oblasti Azure, takže je odolná vůči výpadkům v rámci zóny a výpadkům v rámci oblastí.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [požadavky na systém Azure Stack Edge pro](azure-stack-edge-gpu-system-requirements.md).

- Pochopte [omezení Azure Stack Edge pro](azure-stack-edge-limits.md).
- Nasaďte [Azure Stack Edge pro](azure-stack-edge-gpu-deploy-prep.md) v Azure Portal.
