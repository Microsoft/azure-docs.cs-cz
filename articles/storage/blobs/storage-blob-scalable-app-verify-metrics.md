---
title: Ověření metrik latence a propustnosti pro účet úložiště na webu Azure Portal | Microsoft Docs
description: Zjistěte, jak ověřit metriky latence a propustnosti pro účet úložiště na webu Azure Portal.
services: storage
author: roygara
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: e498e44fcda6877aa69ec763e46e7ae7879e5aa9
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
ms.locfileid: "30237497"
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>Ověření metrik latence a propustnosti pro účet úložiště

Tento kurz je čtvrtou a poslední částí série. V předchozích kurzech jste se naučili nahrávat a stahovat velké objemy náhodných dat do účtu úložiště Azure. V tomto kurzu se dozvíte, jak pomocí metrik zobrazit propustnost a latenci na webu Azure Portal.

Ve čtvrté části této série se naučíte:

> [!div class="checklist"]
> * Konfigurace grafů na webu Azure Portal
> * Ověření metrik latence a propustnosti

Azure Monitor s využitím [metrik úložiště Azure](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) poskytuje jednotný přehled o výkonu a dostupnosti vašeho účtu úložiště.

## <a name="configure-metrics"></a>Konfigurace metrik

V **NASTAVENÍ** svého účtu úložiště přejděte do části **Metriky (Preview)**.

Z rozevíracího seznamu **DÍLČÍ SLUŽBA** zvolte objekt blob.

V části **METRIKA** vyberte jednu z metrik uvedených v následující tabulce:

Pomocí následujících metrik získáte představu o latenci a propustnosti aplikace. Metriky, které nakonfigurujete na portálu, používají průměrné hodnoty za 1 minutu. Pokud se transakce dokončí v půlce minuty, pro průměr se použije polovina těchto minutových dat. V aplikaci se měřil čas operací nahrávání a stahování a na výstupu se zobrazila skutečná doba, jakou trvalo nahrání a stažení souborů. Tyto informace můžete použít společně s metrikami na portálu k úplnému porozumění propustnosti.

|Metrika|Definice|
|---|---|
|**Celková latence při úspěchu**|Průměrná celková latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|
|**Latence serveru při úspěchu**|Průměrná doba zpracování úspěšného požadavku službou Azure Storage. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu. |
|**Transakce**|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. V tomto příkladu byla velikost bloku nastavená na 100 MB. V tomto případě se každý 100MB blok považuje za transakci.|
|**Příchozí přenos dat**|Množství příchozích dat. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure. |
|**Výchozí přenos dat**|Množství výchozích dat. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat. |

Vedle možnosti **Čas** vyberte **Posledních 24 hodin (automaticky)**. Zvolte **Poslední hodina**, jako **Časové intervaly** zvolte **Minuta** a pak klikněte na **Použít**.

![Metriky účtu úložiště](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

Ke grafům může být přiřazených více metrik, ale přiřazením více než jedné metriky se vypne možnost seskupování podle dimenzí.

## <a name="dimensions"></a>Dimenze

[Dimenze](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions) slouží k podrobnějšímu zkoumání grafů a získání podrobnějších informací. Různé metriky mají různé dimenze. Jednou z dostupných dimenzí je dimenze **Název rozhraní API**. Tato dimenze dělí graf na jednotlivá volání rozhraní API. První obrázek níže ukazuje příklad grafu celkových transakcí účtu úložiště. Druhý obrázek ukazuje stejný graf, ale s vybranou dimenzí Název rozhraní API. Jak je vidět, u každé transakce jsou uvedené další podrobnosti o počtu provedených volání podle názvu rozhraní API.

![Metriky účtu úložiště – transakce bez dimenze](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![Metriky účtu úložiště – transakce](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, virtuální počítač a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků příslušného virtuálního počítače a kliknete na Odstranit.

## <a name="next-steps"></a>Další kroky

Ve čtvrté části série jste se seznámili se zobrazováním metrik ukázkového řešení a naučili jste se například:

> [!div class="checklist"]
> * Konfigurace grafů na webu Azure Portal
> * Ověření metrik latence a propustnosti

Pod tímto odkazem najdete ukázky předdefinovaných úložišť.

> [!div class="nextstepaction"]
> [Ukázky skriptů úložiště Azure](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md