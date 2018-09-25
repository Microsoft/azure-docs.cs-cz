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
ms.openlocfilehash: b28830ac2634ad2238d834238de83c9184bcd6f0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47010387"
---
# <a name="enabling-azure-ultra-ssds"></a>Povolení Azure Ultra disky SSD

Azure Ultra SSD poskytuje vysokou propustnost, vysoké IOPS a stálá nízká latence diskové úložiště pro virtuální počítače Azure IaaS. Tato nová nabídka poskytuje horní řádek výkon ve stejné úrovně dostupnosti jako naše stávající nabídky disky. Další výhody Ultra SSD patří schopnost dynamicky měnit výkonu disku spolu s vašimi úlohami, aniž byste museli restartovat své virtuální počítače. Ultra SSD, je vhodné pro úlohy náročné na data, jako je SAP HANA, databáze na nejvyšší úrovni a transakce náročné úlohy.

V současné době ve verzi preview jsou Ultra disků SSD a vy musíte [zaregistrovat](https://aka.ms/UltraSSDPreviewSignUp) ve verzi preview, aby bylo možné přistupovat k nim.

Po schválení se spuštěním jednoho z následujících příkazů můžete určit, které zóna v oblasti východní USA 2 pro nasazení vaší Ultra SSD pro:

PowerShell: `Get-AzureRmComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

ROZHRANÍ PŘÍKAZOVÉHO ŘÁDKU: `az vm list-skus --resource-type disks --query “[?name==’UltraSSD_LRS’]”`

Odpověď bude podobná formuláři níže, kde X je pásmo má být použito pro nasazení v oblasti východní USA 2. X může být buď 1, 2 nebo 3.

|ResourceType  |Název  |Umístění  |Zóny  |Omezení  |Schopnost  |Hodnota  |
|---------|---------|---------|---------|---------|---------|---------|
|Disky     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

Pokud příkaz neodpověděl, znamená to registrace pro funkci je buď stále čekající na vyřízení nebo není schváleno ještě.

Teď, když znáte zóně, ve které chcete nasadit, postupujte podle kroků nasazení v tomto článku se získat první nasazená s Ultra SSD disky virtuálních počítačů.

## <a name="deploying-an-ultra-ssd"></a>Nasazení Ultra SSD

Nejprve určete velikost virtuálního počítače k nasazení. V rámci této verze Preview jsou podporovány pouze řady DsV3 a EsV3 virtuálního počítače. Naleznete v druhé tabulce v tomto [blogu](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) další podrobné informace o těchto velikostí virtuálních počítačů.
Také odkaz na ukázku [vytvoření Virtuálního počítače s více disky Ultra SSD](https://aka.ms/UltraSSDTemplate), který ukazuje, jak vytvořit virtuální počítač s více Ultra SSD disky.

Dále jsou změny šablony Resource Manageru nových/upravených: **apiVersion** pro `Microsoft.Compute/virtualMachines` a `Microsoft.Compute/Disks` musí být nastavena jako `2018-06-01` (nebo novější).

Zadejte Disk skladová položka UltraSSD_LRS, kapacity disku, IOPS a propustnost v MB/s, chcete-li vytvořit disk s Ultra SSD. Tady je příklad, který vytvoří disk s 1 024 GB (GiB = 2 ^ 30 bajtů), 80 000 vstupně-výstupních operací a 1 200 MB/s (MB/s = 10 ^ 6 bajtů za sekundu):

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

Přidat další možnosti ve vlastnostech virtuálního počítače označuje SSD jeho Ultra povolené (odkazovat [ukázka](https://aka.ms/UltraSSDTemplate) pro úplnou šablonu Resource Manageru):

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

## <a name="additional-ultra-ssd-scenarios"></a>Další scénáře Ultra SSD

- Během vytváření virtuálních počítačů Ultra SSD lze implicitně vytvořit také. Ale tyto disky se zobrazí výchozí hodnotu (500) IOPS a propustnost (8 MiB/s).
- Další jednotky SSD Ultra lze připojit k Ultra SSD kompatibilních virtuálních počítačů.
- Ultra SSD podporuje nastavení vlastnosti výkonu disku (IOPS a propustnost) za běhu bez odpojení disku od virtuálního počítače. Jakmile se operace změny velikosti disku výkonu se vystavil na disku, může trvat až hodinu, tato změna se skutečně projeví.
- Stále se rozšiřující kapacity disku vyžaduje virtuální počítač, aby se zrušit jejich přidělení.

# <a name="next-steps"></a>Další postup

Pokud jste chtěli vyzkoušet nový typ disku a ještě nezaregistrovali pro verzi preview, [žádat o přístup prostřednictvím tohoto průzkumu](https://aka.ms/UltraSSDPreviewSignUp).