---
title: Microsoft Azure Stack Edge – přehled | Dokumenty společnosti Microsoft
description: Popisuje Azure Stack Edge, řešení úložiště, které používá fyzické zařízení pro síťový přenos do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 11/04/2019
ms.author: alkohli
ms.openlocfilehash: f463e8883efd5e2dfc4d7fff80912c193665b850
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78399802"
---
# <a name="what-is-azure-stack-edge"></a>Co je Azure Stack Edge?

[!INCLUDE [data-box-edge-gateway-rename-note](../../includes/data-box-edge-gateway-rename-note.md)]

Azure Stack Edge je hraniční výpočetní zařízení s podporou AI s možnostmi přenosu síťových dat. Tento článek poskytuje přehled řešení Azure Stack Edge, výhody, klíčové funkce a scénáře, kde můžete nasadit toto zařízení.

Azure Stack Edge je řešení hardwaru jako služby. Společnost Microsoft dodává zařízení spravované cloudem s vestavěným polem programovatelným gate polem (FPGA), které umožňuje zrychlené odvodování umělou inteligence a má všechny možnosti brány síťového úložiště.

## <a name="use-cases"></a>Případy použití

Tady jsou různé scénáře, kde azure stack edge lze použít pro rychlé odvozování strojového učení (ML) na hraniční chod a předzpracování dat před odesláním do Azure.

- **Odvození s Azure Machine Learning** – s Azure Stack Edge, můžete spustit modely ML získat rychlé výsledky, které lze zareagovat před odesláním dat do cloudu. Úplnou datovou sadu lze volitelně přenést, aby bylo možné pokračovat v rekvalifikaci a vylepšování modelů ML. Další informace o tom, jak používat hardwarově akcelerované modely Azure ML na zařízení Azure Stack Edge, najdete [v tématu Nasazení hardwarově akcelerovaných modelů Azure ML na Azure Stack Edge](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Předzpracování dat** – transformace dat před odesláním do Azure vytvořit více použitelné datové sady. Předzpracování lze použít k těmto účelům: 

    - Agregace dat
    - Upravte data, například za účelem odebrání osobních údajů.
    - Podmnožina dat pro optimalizaci úložiště a šířku pásma nebo pro další analýzu.
    - Analýza a reakce na události IoT 

- **Přenos dat přes síť do Azure** – pomocí Azure Stack Edge můžete snadno a rychle přenášet data do Azure, abyste povolili další výpočetní prostředky a analýzy nebo pro účely archivace. 

## <a name="key-capabilities"></a>Klíčové funkce

Azure Stack Edge má následující možnosti:

|Schopnost |Popis  |
|---------|---------|
|Zrychlené odvození umělou a id| Povoleno vestavěným FPGA.|
|Výpočetní operace       |Umožňuje analýzu, zpracování a filtrování dat.|
|Vysoký výkon | Vysoce výkonné výpočetní a datové přenosy.|
|Přístup k datům     | Přímý přístup k datům z rozšíření Azure Storage Blob a ze souborů Azure pomocí cloudových rozhraní API za účelem dalšího zpracování dat v cloudu. Místní mezipaměť v zařízení se používá pro rychlý přístup k naposledy použitým souborům.|
|Spravovaná pomocí cloudu     |Zařízení a služby se spravují přes portál Azure.  |
|Nahrání offline     | Odpojený režim podporuje scénáře nahrání v režimu offline.|
|Podporované protokoly     | Podpora standardních protokolů SMB a NFS pro příjem dat. <br> Další informace o podporovaných verzích najdete v [tématu Požadavky na systém Azure Stack Edge](data-box-edge-system-requirements.md).|
|Aktualizace dat     | Možnost aktualizovat místní soubory pomocí nejnovějších souborů z cloudu.|
|Šifrování    | BitLocker podporuje místní šifrování dat a bezpečný přenos dat do cloudu přes protokol *https*.|
|Omezení šířky pásma| Omezení pro omezení využití šířky pásma ve špičce.|
|ExpressRoute | Přidáno zabezpečení prostřednictvím ExpressRoute. Použijte konfiguraci partnerského vztahu, kde se provoz z místních zařízení do koncových bodů cloudového úložiště pohybuje přes ExpressRoute. Další informace najdete v článku [Přehled ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="components"></a>Komponenty

Řešení Azure Stack Edge se skládá z prostředku Azure Stack Edge, fyzického zařízení Azure Stack Edge a místního webového uživatelského rozhraní.

* **Fyzické zařízení Azure Stack Edge** – 1U server připojený k racku dodávaný společností Microsoft, který lze nakonfigurovat tak, aby odesílal data do Azure.
    
* **Prostředek Azure Stack Edge** – prostředek na webu Azure Portal, který umožňuje spravovat zařízení Azure Stack Edge z webového rozhraní, ke kterému máte přístup z různých geografických umístění. Pomocí prostředku Azure Stack Edge můžete vytvářet a spravovat prostředky, zobrazovat a spravovat zařízení a výstrahy a spravovat sdílené složky.  

    <!--![The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Další informace najdete v části [Vytvoření objednávky pro zařízení Azure Stack Edge](data-box-edge-deploy-prep.md#create-a-new-resource).

* **Azure Stack Edge místní webové uživatelské rozhraní** – pomocí místního webového uživatelského rozhraní spusťte diagnostiku, vypněte a restartujte zařízení Azure Stack Edge, zobrazte protokoly kopírování a obraťte se na podporu Microsoftu a požádejte o žádost o službu.

    <!--![The Azure Stack Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Informace o používání webového uživatelského uživatelského nastavení najdete v části Správa azure [stack edge pomocí webového uživatelského uživatelského nastavení](data-box-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Dostupnost v oblastech

Fyzické zařízení Azure Stack Edge, prostředek Azure a cílový účet úložiště, do kterého přenášíte data, nemusí být všechny ve stejné oblasti.

- **Dostupnost prostředků** – seznam všech oblastí, kde je k dispozici prostředek Azure Stack Edge, najdete v [tématu Produkty Azure dostupné podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Stack Edge se dá nasadit taky v Azure Government Cloud. Další informace najdete v tématu [Co je Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).
    
- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure. Oblasti, kde účty úložiště ukládají data Azure Stack Edge by měly být umístěny v blízkosti místa, kde se nachází zařízení pro optimální výkon. Účet úložiště umístěný daleko od zařízení bude vykazovat dlouhé latence a nižší výkon.

## <a name="next-steps"></a>Další kroky

- Zkontrolujte [požadavky na systém Azure Stack Edge](data-box-edge-system-requirements.md).
- Seznamte se s [limity Azure Stack Edge](data-box-edge-limits.md).
- Nasazení [Azure Stack Edge](data-box-edge-deploy-prep.md) na webu Azure Portal.
