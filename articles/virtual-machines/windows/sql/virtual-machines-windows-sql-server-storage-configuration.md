---
title: Konfigurace úložiště pro virtuální počítače s SQL Server | Microsoft Docs
description: Toto téma popisuje, jak Azure nakonfiguruje úložiště pro SQL Server virtuálních počítačů během zřizování (Správce prostředků modelu nasazení). Vysvětluje taky, jak můžete nakonfigurovat úložiště pro stávající virtuální počítače s SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: mathoma
ms.openlocfilehash: e28478d31a674d742870344b33eac6b84c3ae584
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123841"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Konfigurace úložiště pro virtuální počítače s SQL Server

Když nakonfigurujete image virtuálního počítače s SQL Server v Azure, portál vám pomůže automatizovat konfiguraci úložiště. To zahrnuje připojení úložiště k virtuálnímu počítači, zpřístupnění tohoto úložiště pro SQL Server a konfiguraci pro optimalizaci pro konkrétní požadavky na výkon.

V tomto tématu se dozvíte, jak Azure nakonfiguruje úložiště pro vaše SQL Server virtuální počítače jak během zřizování, tak i u stávajících virtuálních počítačů. Tato konfigurace je založená na [osvědčených postupech výkonu](virtual-machines-windows-sql-performance.md) pro virtuální počítače Azure s SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít nastavení konfigurace automatizovaného úložiště, váš virtuální počítač musí mít následující vlastnosti:

* Zřízeno s [imagí galerie SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Používá [model nasazení Správce prostředků](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Používá [prémiové SSD](../disks-types.md).

## <a name="new-vms"></a>Nové virtuální počítače

Následující části popisují, jak nakonfigurovat úložiště pro nové virtuální počítače SQL Server.

### <a name="azure-portal"></a>portál Azure

Při zřizování virtuálního počítače Azure pomocí SQL Server Image Galerie můžete zvolit automatickou konfiguraci úložiště pro nový virtuální počítač. Zadejte velikost úložiště, omezení výkonu a typ úlohy. Následující snímek obrazovky ukazuje okno Konfigurace úložiště použité během zřizování virtuálního počítače SQL.

![SQL Server konfigurace úložiště virtuálních počítačů během zřizování](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Na základě vašich voleb provede Azure po vytvoření virtuálního počítače tyto úlohy konfigurace úložiště:

* Vytvoří a připojí SSD úrovně Premium k virtuálnímu počítači.
* Nakonfiguruje datové disky k dispozici pro SQL Server.
* Nakonfiguruje datové disky do fondu úložiště na základě zadaného počtu požadavků na velikost a výkon (IOPS a propustnost).
* Přidružuje fond úložiště k nové jednotce na virtuálním počítači.
* Optimalizuje tuto novou jednotku na základě zadaného typu úlohy (datové sklady, zpracování transakcí nebo obecné).

Další podrobnosti o tom, jak Azure nakonfiguruje nastavení úložiště, najdete v [části věnované konfiguraci úložiště](#storage-configuration). Úplný návod, jak vytvořit SQL Server virtuální počítač v Azure Portal, najdete v [kurzu zřizování](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Zdroje – Správa šablon

Pokud použijete následující šablony Správce prostředků, standardně se připojí dva datové disky Premium bez konfigurace fondu úložiště. Tyto šablony však můžete přizpůsobit a změnit počet disků prémiových dat, které jsou připojeny k virtuálnímu počítači.

* [Vytvoření virtuálního počítače pomocí automatizovaného zálohování](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Vytvoření virtuálního počítače pomocí automatizovaných oprav](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Vytvoření virtuálního počítače s integrací integrace](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Existující virtuální počítače

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

U stávajících SQL Server virtuálních počítačů můžete upravit některá nastavení úložiště v Azure Portal. Otevřete [prostředek virtuálních počítačů SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)a vyberte **Přehled**. Na stránce Přehled SQL Server se zobrazuje aktuální využití úložiště virtuálního počítače. V tomto grafu se zobrazí všechny jednotky, které se nacházejí na vašem VIRTUÁLNÍm počítači. Pro každou jednotku se prostor úložiště zobrazuje ve čtyřech částech:

* Data SQL
* Protokol SQL
* Jiné (jiné úložiště než SQL)
* K dispozici

Pokud chcete upravit nastavení úložiště, vyberte **Konfigurovat** v části **Nastavení**. 

![Konfigurace úložiště pro existující virtuální počítač SQL Server](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Možnosti konfigurace, které vidíte, se liší v závislosti na tom, zda jste tuto funkci použili dříve. Při prvním použití můžete pro novou jednotku zadat požadavky na úložiště. Pokud jste tuto funkci dříve použili k vytvoření jednotky, můžete si vybrat, že chcete tuto jednotku úložiště zvětšit.

### <a name="use-for-the-first-time"></a>První použití

Pokud tuto funkci používáte poprvé, můžete zadat velikost úložiště a omezení výkonu pro novou jednotku. Toto prostředí se podobá tomu, co se vám zobrazuje při zřizování. Hlavním rozdílem je, že není povoleno zadat typ úlohy. Toto omezení zabrání přerušení stávajících konfigurací SQL Server na virtuálním počítači.

Azure vytvoří novou jednotku na základě vašich specifikací. V tomto scénáři Azure provádí následující úlohy konfigurace úložiště:

* Vytvoří a připojí disky dat služby Premium Storage k virtuálnímu počítači.
* Nakonfiguruje datové disky k dispozici pro SQL Server.
* Nakonfiguruje datové disky do fondu úložiště na základě zadaného počtu požadavků na velikost a výkon (IOPS a propustnost).
* Přidružuje fond úložiště k nové jednotce na virtuálním počítači.

Další podrobnosti o tom, jak Azure nakonfiguruje nastavení úložiště, najdete v [části věnované konfiguraci úložiště](#storage-configuration).

### <a name="add-a-new-drive"></a>Přidat novou jednotku

Pokud jste na svém SQL Serverovém VIRTUÁLNÍm počítači už nakonfigurovali úložiště, rozšíření úložiště přináší dvě nové možnosti. První možností je přidat novou jednotku, která může zvýšit úroveň výkonu virtuálního počítače.

Po přidání jednotky je však nutné provést několik dalších ručních konfigurací, aby bylo možné zvýšit výkon.

### <a name="extend-the-drive"></a>Rozšiřování jednotky

Další možností rozšíření úložiště je rozšíření stávající jednotky. Tato možnost zvyšuje dostupné úložiště pro vaši jednotku, ale nezvyšuje výkon. Pomocí fondů úložiště nemůžete po vytvoření fondu úložiště změnit počet sloupců. Počet sloupců určuje počet paralelních zápisů, které mohou být rozloženy mezi datové disky. Proto by všechny přidané datové disky nemohly zvýšit výkon. Můžou poskytovat jenom úložiště dat, která se zapisují. Toto omezení také znamená, že při rozšíření jednotky určí počet sloupců minimální počet datových disků, které můžete přidat. Takže pokud vytvoříte fond úložiště se čtyřmi datovými disky, je počet sloupců také čtyři. Pokaždé, když úložiště rozšíříte, musíte přidat aspoň čtyři datové disky.

![Rozšiřování jednotky na virtuálním počítači s SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Konfigurace úložiště

V této části najdete referenční informace ke změnám konfigurace úložiště, které Azure během zřizování a konfigurace virtuálního počítače SQL automaticky provede během Azure Portal.

* Pokud jste pro svůj virtuální počítač vybrali méně než dvě služby TBs úložiště, Azure nevytvoří fond úložiště.
* Pokud jste pro virtuální počítač vybrali aspoň dvě služby TBs úložiště, Azure nakonfiguruje fond úložiště. V další části tohoto tématu najdete podrobné informace o konfiguraci fondu úložiště.
* Automatická konfigurace úložiště vždycky používá datové disky [Premium SSD](../disks-types.md) P30. V důsledku toho je k dispozici mapování 1:1 mezi vybraným počtem terabajtů a počtem datových disků připojených k VIRTUÁLNÍmu počítači.

Informace o cenách najdete na stránce s [cenami za úložiště](https://azure.microsoft.com/pricing/details/storage) na kartě **Disk Storage** .

### <a name="creation-of-the-storage-pool"></a>Vytvoření fondu úložiště

Azure na SQL Server virtuálních počítačích vytvoří fond úložiště pomocí následujících nastavení.

| Nastavení | Value |
| --- | --- |
| Velikost pruhu |256 KB (datové sklady); 64 KB (transakční) |
| Velikost disků |1 TB |
| Mezipaměť |Čtení |
| Velikost přidělení |velikost alokační jednotky systému souborů NTFS v 64 KB |
| Okamžitá inicializace souboru |Enabled |
| Zamknout stránky v paměti |Enabled |
| Obnovení |Jednoduché obnovení (bez odolnosti) |
| Počet sloupců |Počet datových disků<sup>1</sup> |
| Umístění v databázi TempDB |Uloženo na datových discích<sup>2</sup> |

<sup>1</sup> po vytvoření fondu úložiště nemůžete změnit počet sloupců ve fondu úložiště.

<sup>2</sup> toto nastavení se vztahuje jenom na první vytvořenou jednotku pomocí funkce konfigurace úložiště.

## <a name="workload-optimization-settings"></a>Nastavení optimalizace úloh

Následující tabulka popisuje tři dostupné možnosti typu úlohy a jejich odpovídající optimalizace:

| Typ úlohy | Popis | Optimalizace |
| --- | --- | --- |
| **Obecné** |Výchozí nastavení, které podporuje většinu úloh |Žádné |
| **Zpracování transakcí** |Optimalizuje úložiště pro tradiční databázové OLTP úlohy. |Příznak trasování 1117<br/>Příznak trasování 1118 |
| **Datového skladu** |Optimalizuje úložiště pro analytické a generování sestav úloh. |Příznak trasování 610<br/>Příznak trasování 1117 |

> [!NOTE]
> Typ úlohy můžete zadat jenom při zřizování virtuálního počítače SQL, a to tak, že ho vyberete v kroku konfigurace úložiště.

## <a name="next-steps"></a>Další kroky

Další témata související se spouštěním SQL Server ve virtuálních počítačích Azure najdete v tématu [SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).
