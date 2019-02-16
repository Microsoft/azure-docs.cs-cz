---
title: Konfigurace úložiště pro virtuální počítače s SQL serverem | Dokumentace Microsoftu
description: Toto téma popisuje, jak Azure nakonfiguruje úložiště pro virtuální počítače s SQL serverem během zřizování (model nasazení Resource Manager). Také vysvětluje, jak nakonfigurovat úložiště pro vaše existující virtuální počítače s SQL serverem.
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: craigg
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: ninarn
ms.openlocfilehash: da850b8ff9174fa310c5247cd7e99af69db28a8b
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328431"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Konfigurace úložiště pro virtuální počítače s SQL serverem

Když konfigurujete image virtuálního počítače systému SQL Server v Azure, pomáhá automatizovat konfiguraci úložiště na portálu. Jedná se o připojení k virtuálnímu počítači, zpřístupnění úložiště k systému SQL Server a jeho konfigurace pro optimalizaci pro vaše požadavky na konkrétní výkon úložiště.

Toto téma vysvětluje, jak Azure nakonfiguruje úložiště pro virtuální počítače s SQL serverem během zřizování i pro stávající virtuální počítače. Tato konfigurace je založená na [osvědčené postupy z hlediska výkonu](virtual-machines-windows-sql-performance.md) pro virtuální počítače Azure s SQL serverem.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Požadavky

Použití nastavení konfigurace automatického úložiště, virtuální počítač vyžaduje následující vlastnosti:

* Zřízeny [image SQL serveru z Galerie](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Používá [modelu nasazení Resource Manager](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Používá [SSD disků premium](../disks-types.md).

## <a name="new-vms"></a>Nové virtuální počítače

Následující části popisují postup konfigurace úložiště pro nové virtuální počítače systému SQL Server.

### <a name="azure-portal"></a>Azure Portal

Při zřizování virtuálního počítače Azure pomocí image z galerie systému SQL Server, můžete pro automatickou konfiguraci úložiště pro nový virtuální počítač. Určete velikost úložiště, limity pro výkon a typ úlohy. Na následujícím snímku obrazovky se zobrazí okno Konfigurace úložiště využitých virtuálních počítačů SQL zřizování.

![Konfigurace úložiště virtuálního počítače SQL serveru během zřizování](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Na základě vašich voleb, provede Azure těchto konfiguračních úloh úložiště po vytvoření virtuálního počítače:

* Vytvoří a připojí disky premium SSD pro virtuální počítač.
* Nakonfiguruje datové disky dostupná pro SQL Server.
* Nakonfiguruje datových disků do fondu úložiště na základě zadané velikosti a výkonu (IOPS a propustnost) požadavků.
* Fond úložiště přidruží nové jednotky na virtuálním počítači.
* Optimalizuje tuto novou jednotku podle typu zadaného pracovního vytížení (skladování dat, zpracování transakcí nebo obecná).

Další podrobnosti o tom, jak Azure nakonfiguruje nastavení úložiště, najdete v článku [úložiště konfigurační oddíl](#storage-configuration). Úplný návod, jak vytvořit virtuální počítač s SQL serverem na webu Azure Portal najdete v tématu [kurzu zřizování](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Správa šablon prostředků

Pokud použijete následující šablony Resource Manageru, jsou ve výchozím nastavení se žádné konfigurace fondu úložiště připojené dva datové disky premium. Můžete však přizpůsobit tyto šablony, chcete-li změnit počet datových disků premium, které jsou připojené k virtuálnímu počítači.

* [Vytvoření virtuálního počítače pomocí provádí automatické zálohování](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Vytvoření virtuálního počítače pomocí automatizované opravy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Vytvoření virtuálního počítače pomocí integrace se službou AZURE](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Stávající virtuální počítače

Pro existující virtuální počítače SQL serveru můžete upravit některá nastavení úložiště na webu Azure Portal. Vyberte svůj virtuální počítač, přejděte do oblasti nastavení a pak vyberte konfiguraci systému SQL Server. Konfigurace systému SQL Server okno aktuální využití úložiště virtuálního počítače. V tomto grafu se zobrazují všechny jednotky, které existují na vašem virtuálním počítači. Pro každou jednotku v prostoru úložiště zobrazí v čtyři části:

* SQL data
* Protokol SQL
* Ostatní (úložiště bez SQL)
* K dispozici.

![Konfigurace úložiště pro existující systém SQL Server VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Konfigurace úložiště, které chcete přidat novou jednotku nebo rozšířit existující jednotce, klikněte na odkaz upravit nad grafem.

Možnosti konfigurace, které se zobrazí, se liší v závislosti na tom, jestli se mají použít tuto funkci před. Pokud používáte poprvé, můžete určit požadavky na úložiště pro nový disk. Pokud jste dříve používali tuto funkci Pokud chcete vytvořit jednotku, můžete rozšířit úložiště tuto jednotku.

### <a name="use-for-the-first-time"></a>Pro první použití

Pokud je vaše první přihlášení pomocí této funkce, můžete zadat omezení velikosti a výkonu úložiště pro novou jednotku. Toto prostředí je podobný co se zobrazí na čas zřízení. Hlavní rozdíl je, že nemáte oprávnění k určení typu úlohy. Smyslem tohoto omezení přerušení všechny existující konfigurace systému SQL Server na virtuálním počítači.

![Konfigurace SQL serveru úložiště posuvníky](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

Azure vytvoří novou jednotku podle vašich požadavků. V tomto scénáři Azure provádí následující úlohy konfigurace úložiště:

* Vytvoří a připojí data disky storage úrovně premium pro virtuální počítač.
* Nakonfiguruje datové disky dostupná pro SQL Server.
* Nakonfiguruje datových disků do fondu úložiště na základě zadané velikosti a výkonu (IOPS a propustnost) požadavků.
* Fond úložiště přidruží nové jednotky na virtuálním počítači.

Další podrobnosti o tom, jak Azure nakonfiguruje nastavení úložiště, najdete v článku [úložiště konfigurační oddíl](#storage-configuration).

### <a name="add-a-new-drive"></a>Přidat novou jednotku

Pokud jste už nakonfigurovali úložiště na virtuální počítač s SQL serverem, rozbalení úložiště zobrazí dvě nové možnosti. První možností je přidat novou jednotku, což může zvýšit úroveň výkonu virtuálního počítače.

![Přidat novou jednotku k virtuálnímu počítači SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

Ale po přidání jednotky, je nutné provést některé další ruční konfigurace dosáhnout zvýšení výkonu.

### <a name="extend-the-drive"></a>Rozšířit jednotku

Další možností pro rozšíření úložiště je rozšířit existující jednotce. Tato možnost zvětší úložiště k dispozici pro váš disk, ale nezvyšuje výkon. Díky fondům úložiště není možné pozměnit počet sloupců po vytvoření fondu úložiště. Počet sloupců určuje počet paralelních zápisy, které může být rozdělená mezi datovými disky. Proto všechny přidané datové disky nelze zvýšit výkon. Další úložiště, může poskytnout pouze pro zapisovaných dat. Toto omezení také znamená, že při rozšiřování na jednotce, počet sloupců Určuje minimální počet datových disků, které můžete přidat. Proto pokud vytvoříte fond úložiště se čtyřmi datovými disky, počet sloupců, jsou také čtyři. Pokaždé, když rozšíření úložiště, je nutné přidat alespoň čtyři datové disky.

![Rozšířit jednotku pro virtuální počítač s SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Konfigurace úložiště

Tato část obsahuje odkaz změny v konfiguraci úložiště, které Azure automaticky provede během zřizování virtuálního počítače SQL nebo konfigurace na webu Azure Portal.

* Pokud vyberete méně než dvě TB úložiště pro virtuální počítač Azure není vytvoření fondu úložiště.
* Pokud jste vybrali minimálně dva TB úložiště pro virtuální počítač, Azure nakonfiguruje fond úložiště. Další část tohoto tématu obsahuje podrobnosti o konfiguraci fondu úložiště.
* Konfigurace automatického úložiště vždy používá [SSD disků premium](../disks-types.md) datové disky P30. V důsledku toho existuje mapování 1:1 mezi váš vybraný počet terabajty a počet datových disků připojených k virtuálnímu počítači.

Informace o cenách najdete v článku [ceny za službu Storage](https://azure.microsoft.com/pricing/details/storage) na stránce **diskové úložiště** kartu.

### <a name="creation-of-the-storage-pool"></a>Vytvoření fondu úložiště

Azure používá následující nastavení k vytvoření fondu úložiště na virtuální počítače s SQL serverem.

| Nastavení | Hodnota |
| --- | --- |
| Velikost platformy stripe |256 KB (skladování dat); 64 KB (transakční) |
| Velikost disků |1 TB |
| Mezipaměť |Čtení |
| Velikost alokační |Velikost alokační jednotky 64 KB systému souborů NTFS |
| Inicializace rychlé souboru |Povoleno |
| Uzamknout stránky v paměti |Povoleno |
| Obnovení |Jednoduché obnovení (bez odolnosti) |
| Počet sloupců |Počet datových disků<sup>1</sup> |
| Umístění databáze TempDB |Uložené na datové disky<sup>2</sup> |

<sup>1</sup> po vytvoření fondu úložiště není možné pozměnit počet sloupců ve fondu úložiště.

<sup>2</sup> tato nastavení platí jenom pro první disk vytvoříte pomocí funkce úložiště konfigurace.

## <a name="workload-optimization-settings"></a>Nastavení optimalizace pracovního vytížení

Následující tabulka popisuje dostupné možnosti Typ tři úlohy a jejich odpovídající optimalizace:

| Typ úlohy | Popis | Optimalizace |
| --- | --- | --- |
| **Obecné** |Výchozí nastavení, která podporuje většinu úloh |Žádný |
| **Zpracování transakcí** |Optimalizuje úložiště pro standardní úlohy databází OLTP |Příznak trasování. 1117<br/>Příznak trasování 1118 |
| **Datového skladu** |Optimalizuje úložiště pro úlohy analýz a generování sestav |Příznak trasování 610<br/>Příznak trasování. 1117 |

> [!NOTE]
> Typ úlohy můžete zadat jenom při zřizování virtuálního počítače s SQL tak, že ji vyberete v kroku konfigurace úložiště.

## <a name="next-steps"></a>Další postup

Další témata související s SQL serverem na virtuálních počítačích Azure, najdete v části [systému SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-server-iaas-overview.md).
