---
title: Konfiguraci úložiště pro virtuální počítače SQL serveru | Microsoft Docs
description: Toto téma popisuje, jak Azure konfiguruje úložiště pro virtuální počítače SQL serveru při zřizování (modelu nasazení Resource Manager). Také vysvětluje, jak konfigurace úložiště pro existující virtuální počítače SQL serveru.
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
ms.openlocfilehash: 21c8b955d48da03559097db93b2cb66029a203ec
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399079"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Konfiguraci úložiště pro virtuální počítače serveru SQL
Když konfigurujete image virtuálního počítače systému SQL Server v Azure, pomáhá automatizovat konfiguraci úložiště na portálu. Jedná se o připojení k virtuálnímu počítači, zpřístupnění této úložiště pro SQL Server a nakonfigurovat jej pro optimalizaci pro vašim požadavkům na výkon konkrétní úložiště.

Toto téma vysvětluje, jak nakonfiguruje Azure úložiště pro virtuální počítače SQL serveru při zřizování i pro existující virtuální počítače. Tato konfigurace je založená na [osvědčené postupy z hlediska výkonu](virtual-machines-windows-sql-performance.md) pro virtuální počítače Azure se systémem SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Požadavky
Pokud chcete použít nastavení konfigurace automatizované úložiště, virtuální počítač vyžaduje následující vlastnosti:

* Zřízená [image SQL serveru Galerie](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Používá [modelu nasazení Resource Manager](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Používá [Storage úrovně Premium](../premium-storage.md).

## <a name="new-vms"></a>Nové virtuální počítače
Následující části popisují postup konfigurace úložiště pro nový virtuální počítače systému SQL Server.

### <a name="azure-portal"></a>Azure Portal
Při zřizování virtuálního počítače Azure pomocí Galerie bitovou kopii systému SQL Server, můžete pro automatickou konfiguraci úložiště pro nový virtuální počítač. Určete velikost úložiště, výkon a typ úlohy. Následující snímek obrazovky ukazuje okno Konfigurace úložiště používá během virtuální počítač SQL zřizování.

![Konfigurace úložiště virtuálního počítače SQL serveru při zřizování](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Podle vaší volby, Azure provádí následující úkoly konfigurace úložiště po vytvoření virtuálního počítače:

* Vytvoří a připojí premium storage datových disků k virtuálnímu počítači.
* Nakonfiguruje datových disků byla přístupná pro SQL Server.
* Nakonfiguruje datových disků do fondu úložiště na základě zadané velikosti a výkonu (IOPS a propustnost) požadavků.
* Přidruží fondu úložiště na nový disk ve virtuálním počítači.
* Optimalizuje tento nový disk, na základě vašeho typu zadané úlohy (datové sklady, zpracování transakcí nebo Obecné).

Další informace o tom, jak Azure nakonfiguruje nastavení úložiště, najdete v článku [úložiště konfigurační oddíl](#storage-configuration). Úplné návod, jak vytvořit virtuální počítač SQL Server na portálu Azure, najdete v části [kurzu zřizování](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Spravovat šablony prostředků
Pokud chcete použít následující šablony Resource Manageru, jsou připojené dva premium datových disků ve výchozím nastavení se žádné konfigurace fondu úložiště. Tyto šablony, chcete-li změnit počet datových disků premium, které jsou připojené k virtuálnímu počítači však můžete přizpůsobit.

* [Vytvoření virtuálního počítače s automatizované zálohování](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Vytvoření virtuálního počítače s automatizované opravy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Vytvoření virtuálního počítače s integrace se službou AZURE](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Existující virtuální počítače
Pro existující virtuální počítače SQL serveru můžete upravit některé nastavení úložiště na portálu Azure. Vyberte virtuální počítač, přejděte do oblasti nastavení a pak vyberte konfiguraci systému SQL Server. V okně Konfigurace serveru SQL Server je zobrazeno aktuální využití úložiště virtuálního počítače. V tomto grafu se zobrazují všechny jednotky, které existují na vašem virtuálním počítači. Pro každou jednotku v prostoru úložiště zobrazí v čtyři části:

* SQL data
* Protokolu databáze SQL
* Jiné (mimo SQL úložiště)
* Dostupné

![Konfigurace úložiště pro existující systém SQL Server virtuálního počítače](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Konfigurace úložiště, které chcete přidat nový disk nebo rozšířit existující jednotce, klikněte na odkaz upravit výše grafu.

Možnosti konfigurace, které se zobrazí, se liší v závislosti na tom, jestli se mají použít tuto funkci před. Pokud používáte poprvé, můžete určit požadavky na úložiště pro nový disk. Pokud chcete vytvořit jednotku jste dříve používali tuto funkci, můžete rozšířit úložiště tuto jednotku.

### <a name="use-for-the-first-time"></a>Použití první
Pokud je poprvé pomocí této funkce, můžete zadat omezení velikosti a výkonu úložiště pro nový disk. Toto prostředí je podobná by najdete na zřizování čas. Hlavní rozdíl je, že nemáte oprávnění k určení typu úlohy. Toto omezení zabrání přerušení všechny existující konfigurace systému SQL Server na virtuálním počítači.

![Konfigurace SQL serveru úložiště posuvníky](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

Azure vytvoří na nový disk podle vašich požadavků. V tomto scénáři Azure provádí následující úkoly konfigurace úložiště:

* Vytvoří a připojí premium storage datových disků k virtuálnímu počítači.
* Nakonfiguruje datových disků byla přístupná pro SQL Server.
* Nakonfiguruje datových disků do fondu úložiště na základě zadané velikosti a výkonu (IOPS a propustnost) požadavků.
* Přidruží fondu úložiště na nový disk ve virtuálním počítači.

Další informace o tom, jak Azure nakonfiguruje nastavení úložiště, najdete v článku [úložiště konfigurační oddíl](#storage-configuration).

### <a name="add-a-new-drive"></a>Přidat nový disk
Pokud jste již nakonfigurovali úložiště na virtuální počítač s SQL serverem, rozšíření úložiště zobrazí dvě nové možnosti. První možností je přidat nový disk, což může zvýšit úroveň výkonu virtuálního počítače.

![Přidat nový disk do virtuálního počítače s SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

Po přidání jednotky, ale musí provést některé velmi ruční konfigurace dosáhnout zvýšení výkonu.

### <a name="extend-the-drive"></a>Rozšíření jednotku
Další možností pro rozšíření úložiště je rozšířit existující jednotce. Tato možnost zvyšuje úložiště k dispozici pro vašeho disku, ale nezvyšuje výkon. Díky fondům úložiště nelze změnit počet sloupců, po vytvoření fondu úložiště. Počet sloupců určuje počet paralelní zápisy, které může být rozdělená mezi datových disků. Proto všechny disky přidané dat nelze zvýšit výkon. Můžete pouze poskytují další úložiště pro zapisovaných. Toto omezení také znamená, že když rozšíření jednotku, počet sloupců Určuje minimální počet datových disků, které můžete přidat. Proto pokud vytvoříte fond úložiště s čtyři datových disků, počet sloupců je také čtyři. Kdykoli můžete rozšířit úložiště, je nutné přidat alespoň čtyři datových disků.

![Rozšíření pro virtuální počítač SQL na jednotku](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Konfigurace úložiště
Tato část obsahuje odkaz pro změny konfigurace úložiště, které Azure automaticky provede při zřizování virtuálních počítačů SQL nebo konfigurace na portálu Azure.

* Pokud vyberete méně než dvě TBs úložiště pro virtuální počítač Azure nevytvoří fondu úložiště.
* Pokud vyberete aspoň dva TBs úložiště pro virtuální počítač Azure nakonfiguruje fond úložiště. V další části tohoto tématu poskytuje podrobnosti o konfiguraci fondu úložiště.
* Konfigurace automatického úložiště vždy používá [storage úrovně premium](../premium-storage.md) P30 datových disků. Proto je mapování 1:1 mezi vaší vybraný počet terabajtů a počet datových disků připojených k virtuálnímu počítači.

Informace o cenách najdete v článku [Storage – ceny](https://azure.microsoft.com/pricing/details/storage) stránky na **diskového úložiště** kartě.

### <a name="creation-of-the-storage-pool"></a>Vytvoření fondu úložiště
Azure používá následující nastavení k vytvoření fondu úložiště na virtuálních počítačích SQL Server.

| Nastavení | Hodnota |
| --- | --- |
| Velikost stripe |256 KB (datové sklady); 64 KB (transakcí) |
| Velikost disků |1 TB |
| Mezipaměť |Čtení |
| Velikost alokační |Velikost alokační jednotky 64 KB systému souborů NTFS |
| Inicializace rychlých souboru |Povoleno |
| Zamknout stránky paměti |Povoleno |
| Obnovení |Obnovení Simple (bez odolnosti) |
| Počet sloupců |Počet datových disků<sup>1</sup> |
| Umístění databáze TempDB |Uložené na datové disky<sup>2</sup> |

<sup>1</sup> po vytvoření fondu úložiště nelze změnit počet sloupců ve fondu úložiště.

<sup>2</sup> toto nastavení platí jenom pro první disk můžete vytvořit pomocí funkce Konfigurace úložiště.

## <a name="workload-optimization-settings"></a>Nastavení úloh optimalizace
Následující tabulka popisuje možnosti Typ tři úlohy dostupné a jejich odpovídající optimalizace:

| Typ úlohy | Popis | Optimalizace |
| --- | --- | --- |
| **Obecné** |Výchozí nastavení, která podporuje většinu úloh |Žádné |
| **Zpracování transakcí** |Optimalizuje úložiště pro standardní úlohy databází OLTP |Příznak trasování. 1117<br/>Příznak trasování 1118 |
| **Datového skladu** |Optimalizuje úložiště pro úlohy analýz a generování sestav |Příznak trasování 610<br/>Příznak trasování. 1117 |

> [!NOTE]
> Typ úlohy lze zadat pouze při zřizování virtuálního počítače systému SQL tak, že ji vyberete v kroku konfigurace úložiště.
>
>

## <a name="next-steps"></a>Další postup
Další témata související se systémem SQL Server ve virtuálních počítačích Azure, najdete v části [systému SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-server-iaas-overview.md).
