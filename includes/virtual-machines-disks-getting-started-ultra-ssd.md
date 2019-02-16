---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0369a7792f0d9c97aa3d943708dfcc07228effa2
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330928"
---
# <a name="enabling-azure-ultra-disks"></a>Povolení ultra disků v Azure

Disky Azure ultra poskytovat vysokou propustnost, vysoké IOPS a stálá nízká latence diskové úložiště pro virtuální počítače Azure IaaS. Tato nová nabídka poskytuje horní řádek výkon ve stejné úrovně dostupnosti jako naše stávající nabídky disky. Další výhody ultra disků patří schopnost dynamicky měnit výkonu disku spolu s vašimi úlohami, aniž byste museli restartovat své virtuální počítače. Ultra disky jsou vhodné pro úlohy náročné na data, jako je SAP HANA, databáze na nejvyšší úrovni a transakce náročné úlohy.

V současné době ultra disky jsou ve verzi preview a je nutné [zaregistrovat](https://aka.ms/UltraSSDPreviewSignUp) ve verzi preview, aby bylo možné přistupovat k nim.

Po schválení se spuštěním jednoho z následujících příkazů můžete určit, které zóna v oblasti východní USA 2, ultra disku pro nasazení:

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI: `az vm list-skus --resource-type disks --query “[?name==’UltraSSD_LRS’]”`

Odpověď bude podobná formuláři níže, kde X je pásmo má být použito pro nasazení v oblasti východní USA 2. X může být buď 1, 2 nebo 3.

|ResourceType  |Název  |Umístění  |Zóny  |Omezení  |Schopnost  |Hodnota  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

Pokud příkaz neodpověděl, znamená to registrace pro funkci je buď stále čekající na vyřízení nebo není schváleno ještě.

Teď, když znáte zóně, ve které chcete nasadit, postupujte podle kroků nasazení v tomto článku se získat první nasazená s ultra disky virtuálních počítačů.

## <a name="deploying-an-ultra-disk"></a>Nasazení ultra disku

Nejprve určete velikost virtuálního počítače k nasazení. V rámci této verze Preview jsou podporovány pouze řady DsV3 a EsV3 virtuálního počítače. Naleznete v druhé tabulce v tomto [blogu](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) další podrobné informace o těchto velikostí virtuálních počítačů.
Také odkaz na ukázku [vytvoření virtuálního počítače s více disky ultra](https://aka.ms/UltraSSDTemplate), který ukazuje, jak vytvořit virtuální počítač s více disky ultra.

Dále jsou změny šablony Resource Manageru nových/upravených: **apiVersion** pro `Microsoft.Compute/virtualMachines` a `Microsoft.Compute/Disks` musí být nastavena jako `2018-06-01` (nebo novější).

Zadejte Disk skladová položka UltraSSD_LRS, kapacity disku, IOPS a propustnost v MB/s pro vytvoření ultra disku. Tady je příklad, který vytvoří disk s 1 024 GB (GiB = 2 ^ 30 bajtů), 80 000 vstupně-výstupních operací a 1 200 MB/s (MB/s = 10 ^ 6 bajtů za sekundu):

```json
"properties": {  
    "creationData": {  
    "createOption": "Empty"  
},  
"diskSizeGB": 1024,  
"diskIOPSReadWrite": 80000,  
"diskMBpsReadWrite": 1200,  
}
```

Přidat další možnosti ve vlastnostech virtuálního počítače k označení jeho ultra povolené (odkazovat [ukázka](https://aka.ms/UltraSSDTemplate) pro úplnou šablonu Resource Manageru):

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "properties": {
                    "hardwareProfile": {},
                    "additionalCapabilities" : {
                                    "ultraSSDEnabled" : "true"
                    },
                    "osProfile": {},
                    "storageProfile": {},
                    "networkProfile": {}
    }
}
```

Po zřízení virtuálního počítače můžete oddílu a formátování datových disků a nakonfigurovat je pro vaše úlohy.

## <a name="additional-ultra-disk-scenarios"></a>Další ultra disk scénáře

- Během vytváření virtuálních počítačů, ultra disky je možné implicitně vytvořit také. Ale tyto disky se zobrazí výchozí hodnotu (500) IOPS a propustnost (8 MiB/s).
- Další ultra disky můžete připojit k kompatibilních virtuálních počítačů.
- Ultra disky podporují nastavení vlastnosti výkonu disku (IOPS a propustnost) za běhu bez odpojení disku od virtuálního počítače. Jakmile se operace změny velikosti disku výkonu se vystavil na disku, může trvat až hodinu, tato změna se skutečně projeví.
- Stále se rozšiřující kapacity disku vyžaduje virtuální počítač, aby se zrušit jejich přidělení.

## <a name="next-steps"></a>Další postup

Pokud jste chtěli vyzkoušet nový typ disku a ještě nezaregistrovali pro verzi preview, [žádat o přístup prostřednictvím tohoto průzkumu](https://aka.ms/UltraSSDPreviewSignUp).
