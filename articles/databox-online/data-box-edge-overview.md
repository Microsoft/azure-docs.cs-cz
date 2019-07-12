---
title: Microsoft Azure Data Box Edge – přehled | Microsoft Docs
description: Tento článek popisuje Azure Data Box Edge, což je řešení úložiště, které používá fyzické zařízení pro síťový přenos do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 07/09/2019
ms.author: alkohli
ms.openlocfilehash: bc6b9662a5d67f6ed315c33444bcb061115fe6d4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67701478"
---
# <a name="what-is-azure-data-box-edge"></a>Co je Azure Data Box Edge? 

Azure Data Box Edge je řešení úložiště, které umožňuje zpracovat data a odeslat je přes síť do Azure. Tento článek poskytuje přehled řešení Data Box Edge, jeho výhody, klíčové funkce a scénáře, ve kterých můžete toto zařízení nasadit. 

K urychlení bezpečného přenosu dat používá Data Box Edge fyzické zařízení dodané Microsoftem. Fyzické zařízení se nachází ve vašich prostorách a data na něj zapisujete pomocí protokolů NFS a SMB. 

Data Box Edge má všechny funkce zařízení Data Box Gateway. Zařízení Data Box je navíc vybavené hraničními výpočetními prostředky s podporou AI, které pomáhají analyzovat, zpracovávat a filtrovat data při přesunu do objektů blob bloku nebo objektů blob stránky Azure, případně do souborů Azure.  

## <a name="use-cases"></a>Případy použití

Azure Data Box Edge je zařízení s hraničními výpočetními funkcemi s podporou AI, které má funkce pro síťový přenos dat. Zde je několik různých scénářů, ve kterých je vhodné k přenosu dat použít Data Box Edge.

- **Předzpracování dat** – umožňuje analyzovat data z místních zařízení nebo zařízení IoT, abyste rychle získali výsledek, i když jste v blízkosti místa, kde se data generují. Data Box Edge přenese úplnou sadu dat do cloudu, aby bylo možné provést rozšířené zpracování nebo podrobnější analýzy.  Předzpracování lze použít k těmto účelům: 

    - Agregace dat
    - Změna dat, například odebrání osobních údajů
    - Vytvoření podmnožin dat potřebných pro podrobnější analýzu v cloudu a jejich přenos
    - Analýza a reakce na události IoT 

- **Odvození Azure Machine Learning** – pomocí Data Box Edge můžete spouštět modely Machine Learning (ML), abyste rychle získali výsledky, u kterých můžete provést akce dříve, než se data odešlou do cloudu. Kompletní data lze přenést dál přeučování a vylepšit své modely ML. Další informace o tom, jak používat Azure ML hardware najdete v článku akcelerované modely v zařízení Data Box Edge [nasazení Azure ML hardware accelerated modely na okraji pole Data](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Přenos dat do Azure přes síť** – Data Box Edge umožňuje snadno a rychle přenášet data do Azure, aby bylo možné provádět další výpočetní operace a analýzy pro účely archivace. 

## <a name="benefits"></a>Výhody

Data Box Edge má následující výhody:

- **Snadný přenos dat** – přenos dat do a z úložiště Azure je stejně snadný jako práce s místní sdílenou síťovou složkou.  
- **Vysoký výkon** – umožňuje vysoce výkonné přenosy dat do a z Azure. 
- **Rychlý přístup** – poslední soubory ukládá do mezipaměti z důvodu rychlého přístupu k místním souborům.  
- **Využití omezené šířky pásma** – data lze zapisovat do Azure, i když je síť omezena, aby se snížilo využití během frekventované pracovní doby.  
- **Transformace dat** – umožňuje analýzu, zpracování a filtrování dat při přesunu do Azure.

## <a name="key-capabilities"></a>Klíčové funkce

Data Box Edge nabízí následující funkce:

|Funkce |Popis  |
|---------|---------|
|Vysoký výkon     | Plně automatizovaný a vysoce optimalizovaný přenos dat a šířka pásma.|
|Podporované protokoly     | Podpora standardních protokolů SMB a NFS pro příjem dat. <br> Další informace o podporovaných verzích najdete v [systémových požadavcích pro Data Box Edge](data-box-edge-system-requirements.md).|
|Výpočetní operace       |Umožňuje analýzu, zpracování a filtrování dat.|
|Přístup k datům     | Přímý přístup k datům z rozšíření Azure Storage Blob a ze souborů Azure pomocí cloudových rozhraní API za účelem dalšího zpracování dat v cloudu.|
|Rychlý přístup     | Místní mezipaměť na zařízení pro rychlý přístup k naposledy použitým souborům.|
|Nahrání offline     | Odpojený režim podporuje scénáře nahrání v režimu offline.|
|Aktualizace dat     | Možnost aktualizovat místní soubory pomocí nejnovějších souborů z cloudu.|
|Šifrování    | BitLocker podporuje místní šifrování dat a bezpečný přenos dat do cloudu přes protokol *https*.       |
|Odolnost     | Integrovaná odolnost sítě.        |


## <a name="components"></a>Komponenty

Řešení Data Box Edge sestává z prostředku Data Box Edge, fyzického zařízení Data Box Edge a místního webového uživatelského rozhraní.

* **Fyzické zařízení Data Box Edge** – server 1U namontovaný Microsoftem do racku, který lze nakonfigurovat na odeslání dat do Azure. 
    
* **Prostředek Data Box Edge** – prostředek na webu Azure Portal, který vám umožní spravovat zařízení Data Box Edge z webového rozhraní, ke kterému můžete získat přístup z různých geografických umístění. Prostředek Data Box Edge slouží k vytvoření a správě prostředků, zobrazení, správě zařízení a výstrah a ke správě sdílených složek.  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Další informace najdete v části [vytvoření objednávky zařízení Data Box Edge](data-box-edge-deploy-prep.md#create-a-new-resource).

* **Místní webové uživatelské rozhraní Data Box** – můžete ho použít ke spuštění diagnostiky, vypnutí nebo restartování zařízení Data Box Edge, zobrazení protokolů kopírování a kontaktování podpory Microsoftu, aby vytvořila žádost o služby.

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Informace o používání uživatelského rozhraní založeného na webu najdete v tématu [Použití uživatelského rozhraní založeného na webu ke správě vašeho zařízení Data Box](data-box-edge-manage-access-power-connectivity-mode.md).


## <a name="region-availability"></a>Dostupnost v oblastech

Fyzické zařízení Data Box Edge, prostředek Azure a účet cílového úložiště, do kterého přenášíte data, se nemusí nacházet ve stejné oblasti.

- **Dostupnost prostředků** – seznam všech oblastech, kde okraj pole Data prostředku je k dispozici, přejděte na [dostupné produkty Azure podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Data Box Edge je také nasadit v cloudu Azure Government. Další informace najdete v tématu [co je Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).
    
- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure. Oblasti, kam ukládat data hrany pole dat účtů úložiště musí být umístěné blízko kde je umístěné pro optimální výkon zařízení. Účet úložiště umístěný daleko od zařízení bude vykazovat dlouhé latence a nižší výkon. 


## <a name="next-steps"></a>Další kroky

- Přečtěte si [systémové požadavky služby Data Box Edge](data-box-edge-system-requirements.md).
- Seznamte se s [omezeními služby Data Box Edge](data-box-edge-limits.md).
- Rychle nasaďte [Azure Data Box Edge](data-box-edge-deploy-prep.md) na webu Azure Portal.




