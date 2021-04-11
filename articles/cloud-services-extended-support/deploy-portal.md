---
title: Nasazení cloudové služby Azure (Rozšířená podpora) – Azure Portal
description: Nasazení cloudové služby Azure (Rozšířená podpora) pomocí Azure Portal
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 306294cc654e46dbe8af80b25cb9c709071fad20
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166322"
---
# <a name="deploy-a-azure-cloud-services-extended-support-using-the-azure-portal"></a>Nasazení Azure Cloud Services (Rozšířená podpora) pomocí Azure Portal
Tento článek vysvětluje, jak pomocí Azure Portal vytvořit nasazení cloudové služby (rozšířené podpory). 

## <a name="before-you-begin"></a>Než začnete

Projděte si [požadavky nasazení](deploy-prerequisite.md) pro Cloud Services (Rozšířená podpora) a vytvořte přidružené prostředky. 

## <a name="deploy-a-cloud-services-extended-support"></a>Nasazení Cloud Services (Rozšířená podpora) 
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

2.  Pomocí panelu hledání umístěného v horní části Azure Portal vyhledejte a vyberte **Cloud Services (Rozšířená podpora)**.

    :::image type="content" source="media/deploy-portal-1.png" alt-text="Obrázek zobrazuje okno všechny prostředky v Azure Portal.":::
 
3.  V podokně Cloud Services (Rozšířená podpora) vyberte **vytvořit**. 

    :::image type="content" source="media/deploy-portal-2.png" alt-text="Image ukazuje, jak koupit cloudovou službu z webu Marketplace.":::

4. Na kartě **základy** se otevře okno pro vytvoření Cloud Services (Rozšířená podpora). 
    - Vyberte předplatné.
    - Vyberte skupinu prostředků nebo vytvořte novou.
    - Zadejte požadovaný název nasazení cloudové služby (rozšířené podpory).
        - Název DNS cloudové služby je oddělený a zadaný pomocí popisku názvu DNS veřejné IP adresy a dá se upravit v části veřejná IP adresa na kartě Konfigurace.
    -  Vyberte oblast, do které se má nasadit.

    :::image type="content" source="media/deploy-portal-3.png" alt-text="Obrázek ukazuje okno Cloud Services (Rozšířená podpora).":::

5. Přidejte konfiguraci cloudové služby, soubory balíčku a definice. Můžete přidat existující soubory z úložiště objektů BLOB nebo je nahrát z místního počítače. Při nahrávání z místního počítače budou tyto hodnoty uloženy v účtu úložiště. 

    :::image type="content" source="media/deploy-portal-4.png" alt-text="Při vytváření obrázku se na kartě základy zobrazuje část nahrávání.":::

6. Až budou všechna pole Dokončená, přejděte na kartu **Konfigurace** a dokončete ji. 
    - Vyberte virtuální síť, kterou chcete přidružit ke cloudové službě, nebo vytvořte novou. 
        - Nasazení cloudové služby (Rozšířená podpora) se **musí** nacházet ve virtuální síti. Na virtuální síť **musí** být také odkazováno v souboru konfigurace služby (. cscfg) v `NetworkConfiguration` části.
    - Vyberte existující veřejnou IP adresu, kterou chcete přidružit ke cloudové službě, nebo vytvořte novou.
        - Pokud v souboru definice služby (. csdef) máte definované **vstupní koncové body IP** adres, bude nutné pro cloudovou službu vytvořit veřejnou IP adresu. 
        - Cloud Services (Rozšířená podpora) podporuje jenom základní SKU IP adresy.
        - Pokud vaše konfigurace služby (. cscfg) obsahuje rezervovanou IP adresu, musí být typ přidělení veřejné IP adresy nastavený na hodnotu transakční program **static**. 
        - Volitelně můžete přiřadit název DNS pro koncový bod cloudové služby tím, že aktualizujete vlastnost Popisek DNS veřejné IP adresy, která je přidružená ke cloudové službě.  
    - Volitelné Spusťte cloudovou službu. Vyberte možnost spustit nebo nespustit službu hned po vytvoření.
    - Vyberte Key Vault 
        - Key Vault se vyžaduje v případě, že v souboru konfigurace služby (. cscfg) zadáte jeden nebo více certifikátů. Když vyberete Trezor klíčů, pokusíme se najít vybrané certifikáty ze souboru konfigurace služby (. cscfg) na základě jejich kryptografických otisků. Pokud v trezoru klíčů chybí nějaké certifikáty, můžete je teď nahrát a kliknout na **aktualizovat**.   

 :::image type="content" source="media/deploy-portal-5.png" alt-text="Obrázek zobrazuje okno Konfigurace v Azure Portal při vytváření Cloud Services (Rozšířená podpora).":::

7. Až budou všechna pole Dokončená, přejděte na kartu **zkontrolovat a vytvořit** , která ověří konfiguraci nasazení a vytvoří cloudovou službu (Rozšířená podpora).

## <a name="next-steps"></a>Další kroky 
- Přečtěte si [Nejčastější dotazy](faq.md) k Cloud Services (Rozšířená podpora).
- Nasaďte cloudovou službu (rozšířenou podporu) pomocí [Azure Portal](deploy-portal.md), [PowerShellu](deploy-powershell.md), [šablony](deploy-template.md) nebo sady [Visual Studio](deploy-visual-studio.md).
- Navštivte [úložiště ukázek Cloud Services (rozšířené podpory)](https://github.com/Azure-Samples/cloud-services-extended-support) .
