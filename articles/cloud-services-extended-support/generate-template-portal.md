---
title: Vygenerovat šablonu ARM pro Cloud Services (Rozšířená podpora) pomocí Azure Portal
description: Vygenerujte a stáhněte šablonu a soubor parametrů ARM pro Cloud Services (Rozšířená podpora) pomocí Azure Portal
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 03/07/2021
ms.custom: ''
ms.openlocfilehash: a4f206d68df3cd8dd4dd5b1b411d316e7aacde92
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077093"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>Vygenerovat šablonu ARM pro Cloud Services (Rozšířená podpora) pomocí Azure Portal

Tento článek vysvětluje, jak stáhnout šablonu a soubor parametrů ARM z [Azure Portal](https://portal.azure.com) pro cloudovou službu. Šablonu a soubor parametrů ARM se dají použít v nasazeních přes PowerShell k vytvoření nebo aktualizaci cloudové služby.

## <a name="get-arm-template-via-portal"></a>Získat šablonu ARM prostřednictvím portálu

  1. Přejít na Azure Portal a [vytvořit novou cloudovou službu](deploy-portal.md). Přidejte konfiguraci cloudové služby, soubory balíčku a definice. 
    :::image type="content" source="media/deploy-portal-4.png" alt-text="Při vytváření obrázku se na kartě základy zobrazuje část nahrávání.":::
  
  2. Až budou všechna pole Dokončená, přejděte na kartu Kontrola a vytvoření a ověřte konfiguraci nasazení a klikněte na **Stáhnout šablonu pro automatizaci** cloudové služby (Rozšířená podpora).
    :::image type="content" source="media/download-template-portal-1.png" alt-text="Obrázek ukazuje stažení šablony v rámci cloudové služby (Rozšířená podpora) na Azure Portal.":::
  
  3. Stáhněte si šablonu a soubory parametrů. 
    :::image type="content" source="media/generate-template-portal-3.png" alt-text="Obrázek ukazuje stažení souboru šablony na Azure Portal.":::
  
  4. Zkopírujte identifikátor URI SAS balíčku a identifikátor URI konfigurace SAS z karty revize a vytvořit a přidejte je do parameter.jsv souboru. Tyto soubory se teď dají použít k vytvoření nové cloudové služby prostřednictvím PowerShellu.
    :::image type="content" source="media/download-template-portal-2.png" alt-text="Image zobrazuje parametry identifikátoru URI SAS balíčku a konfigurace identifikátoru URI SAS na Azure Portal.":::
  
## <a name="next-steps"></a>Další kroky 
- Přečtěte si [Nejčastější dotazy](faq.md) k Cloud Services (Rozšířená podpora).
- Nasazení cloudové služby (Rozšířená podpora) pomocí [Azure Portal](deploy-portal.md)
  
