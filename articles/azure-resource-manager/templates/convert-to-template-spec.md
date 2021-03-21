---
title: Převést šablonu portálu na specifikaci šablony
description: Popisuje, jak převést existující šablonu z Galerie Azure Portal na specifikace šablony.
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: c59275fca1eb3037b48b7293fc9e507df46b7fcb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99555937"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>Převést galerii šablon na portálu na specifikace šablon

Azure Portal poskytuje způsob, jak ve svém účtu ukládat šablony Azure Resource Manager (šablony ARM). Nicméně [specifikace šablon](template-specs.md) nabízí snazší způsob, jak sdílet šablony s uživateli ve vaší organizaci, a propojit s ostatními šablonami. Tento článek ukazuje, jak převést existující šablony v galerii šablon na specifikace šablon.

Pokud chcete zjistit, jestli máte nějaké šablony k převedení, zobrazte si [galerii šablon na portálu](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems). Tyto šablony mají typ prostředku `Microsoft.Gallery/myareas/galleryitems` .

## <a name="convert-with-powershell-script"></a>Převod pomocí skriptu PowerShellu

Pokud chcete zjednodušit převod šablon v galerii šablon, použijte skript PowerShellu z úložiště šablon Azure pro rychlý Start. Při spuštění skriptu můžete buď vytvořit novou specifikaci šablony pro každou šablonu, nebo stáhnout šablonu, která vytvoří specifikaci šablony. Skript neodstraní šablonu z galerie šablon.

1. Zkopírujte [skript migrace](https://github.com/Azure/azure-quickstart-templates/blob/master/201-templatespec-migrate-create/Migrate-GalleryItems.ps1). Uložte místní kopii s názvem *Migrate-GalleryItems.ps1*.
1. Chcete-li vytvořit nové specifikace šablony, zadejte hodnoty `-ResourceGroupName` parametrů a `-Location` . 

   Nastavte `ItemsToExport` na `MyGalleryItems` Export šablon. Nastavte na tuto hodnotu, chcete-li `AllGalleryItems` exportovat všechny šablony, ke kterým máte přístup.

   Následující příklad vytvoří nové specifikace šablony pro každou šablonu ve skupině prostředků s názvem **migratedRG**. Skript vytvoří skupinu prostředků, pokud neexistuje.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ResourceGroupName migratedRG -Location westus2 -ItemsToExport MyGalleryItems
   ```

1. Chcete-li stáhnout šablony, které lze použít k vytvoření specifikace šablony, nezadávejte hodnoty pro skupinu prostředků nebo umístění. Místo toho zadejte `-ExportToFile` . Šablona není shodná s vaší šablonou v galerii. Místo toho obsahuje prostředek specifikace šablony, který vytváří specifikace šablony pro šablonu.

   Následující příklad stáhne šablony bez vytváření specifikací šablony.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ItemsToExport MyGalleryItems -ExportToFile
   ```

   Informace o tom, jak nasadit šablonu, která vytvoří specifikace šablony, najdete v tématu [rychlý Start: vytvoření a nasazení specifikace šablony (Preview)](quickstart-create-template-specs.md).

Další informace o skriptu a jeho parametrech najdete v tématu [Vytvoření TemplateSpecs z šablon galerie šablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-templatespec-migrate-create).

## <a name="manually-convert-through-portal"></a>Ručně převést přes portál

Šablony můžete z Galerie ručně kopírovat do nových specifikací šablon.

1. Otevřete [šablony (Preview)](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems) na portálu.
1. Vyberte šablonu, kterou chcete migrovat.
1. Vyberte možnost **Zobrazit šablonu**.
1. Zkopírujte obsah šablony.
1. Na panelu hledání na portálu vyhledejte **specifikace šablony**. Vyberte tuto možnost.
1. Vyberte **vytvořit šablonu – specifikace**.
1. Zadejte hodnoty pro název, předplatné, skupinu prostředků, umístění a verzi.
1. Vyberte **Další: upravit šablonu**.
1. Pro obsah šablony vložte šablonu zkopírovanou z galerie šablon.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Po úspěšném dokončení ověření vyberte **vytvořit**.

Pokud potřebujete sdílet specifikaci šablony s ostatními uživateli ve vaší organizaci, [nastavte řízení přístupu na základě role](../../role-based-access-control/tutorial-role-assignments-group-powershell.md) na skupinu nebo uživatele, kteří potřebují přístup.

## <a name="next-steps"></a>Další kroky

Další informace o specifikacích šablon najdete v tématu [Vytvoření a nasazení specifikací šablony](template-specs.md).
