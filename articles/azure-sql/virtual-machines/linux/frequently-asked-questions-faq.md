---
title: Nejčastější dotazy k SQL Server on Linux virtuálních počítačů | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se spouštění SQL Server on Linux virtuálních počítačů.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ad991974df30060e552d21a44d5796cd2ba165e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792544"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-virtual-machines"></a>Nejčastější dotazy k SQL Server on Linux virtuálním počítačům
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/frequently-asked-questions-faq.md)
> * [Linux](frequently-asked-questions-faq.md)

Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se spouštění [SQL Server on Linux virtuálních počítačů](sql-server-on-linux-vm-what-is-iaas-overview.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Fotografií

1. **Jaké jsou k dispozici Image Galerie virtuálních počítačů SQL Server?**

   Azure udržuje image virtuálních počítačů (VM) pro všechny podporované hlavní verze SQL Server ve všech edicích pro Linux i Windows. Další podrobnosti najdete v tématu úplný seznam [imagí virtuálních počítačů Linux](sql-server-on-linux-vm-what-is-iaas-overview.md#create) a [imagí virtuálních počítačů s Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).

1. **Jsou existující obrázky galerie virtuálních počítačů aktualizované SQL Server?**

   Každé dva měsíce SQL Server Image v galerii virtuálních počítačů se aktualizují s nejnovějšími aktualizacemi pro Linux a Windows. Pro Image Linux to zahrnuje nejnovější aktualizace systému. Pro image Windows To zahrnuje všechny aktualizace, které jsou v web Windows Update označené jako důležité, včetně důležitých SQL Server aktualizací zabezpečení a aktualizací Service Pack. SQL Server kumulativní aktualizace se pro systémy Linux a Windows liší. V případě systému SQL Server Linux jsou do aktualizace zahrnuty také kumulativní aktualizace. Ale v tuto chvíli nejsou virtuální počítače s Windows aktualizované s SQL Server nebo kumulativními aktualizacemi Windows serveru.

1. **Jaké související balíčky SQL Server jsou také nainstalovány?**

   Pokud chcete zobrazit SQL Server balíčky, které jsou ve výchozím nastavení nainstalovány na SQL Server on Linux virtuálních počítačů, přečtěte si téma [nainstalované balíčky](sql-server-on-linux-vm-what-is-iaas-overview.md#packages).

1. **SQL Server můžou se z Galerie odebrat image virtuálních počítačů?**

   Ano. Azure udržuje jenom jednu Image na hlavní verzi a edici. Například když je vydána nová aktualizace Service Pack SQL Server, Azure přidá novou bitovou kopii do galerie pro danou aktualizaci Service Pack. Obrázek SQL Server pro předchozí aktualizaci Service Pack je okamžitě odebrán z Azure Portal. Je ale stále k dispozici pro zřizování z PowerShellu po dobu příštích tří měsíců. Po třech měsících již není k dispozici předchozí obrázek aktualizace Service Pack. Tato zásada odebrání by se taky použila v případě, že se SQL Serverá verze Nepodporovaná, když dosáhne konce svého životního cyklu.

## <a name="creation"></a>Vytvoření

1. **Návody vytvořit virtuální počítač se systémem Linux pomocí SQL Server?**

   Nejjednodušším řešením je vytvoření virtuálního počítače se systémem Linux, který obsahuje SQL Server. Kurz týkající se registrace do Azure a vytvoření virtuálního počítače s SQL Server z portálu najdete v tématu [zřízení virtuálního počítače se systémem Linux běžícího SQL Server v Azure Portal](sql-vm-create-portal-quickstart.md). Máte také možnost ručně nainstalovat SQL Server na virtuálním počítači s jednou z licencovaných edicí (Developer nebo Express) nebo pomocí místní licence. Pokud přenesete vlastní licenci, musíte mít [License mobility prostřednictvím Software Assurance v Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Proč nemůžu zřídit virtuální počítač RHEL nebo SQL Server SLES s předplatným Azure, které má limit útraty?**

   Virtuální počítače s RHEL a SLES vyžadují předplatné bez limitu útraty a ověřený způsob platby (obvykle platební karta) přidruženou k předplatnému. Pokud zřídíte virtuální počítač RHEL nebo SLES, aniž byste odebrali limit útraty, vaše předplatné se deaktivuje a všechny virtuální počítače/služby se zastavily. Pokud provedete spuštění do tohoto stavu, pro opětovné povolení odběru [odeberte limit útraty](https://account.windowsazure.com/subscriptions). Zbývající kredity se obnoví pro aktuální fakturační cyklus, ale příplatek za image RHEL nebo SLES se bude účtovat na platební kartu, pokud se rozhodnete ji znovu spustit a pokračovat v jejím spuštění.

## <a name="licensing"></a>Licencování

1. **Jak můžu na virtuální počítač Azure nainstalovat licencovanou kopii SQL Serveru?**

   Nejdřív vytvořte virtuální počítač jenom s operačním systémem Linux. Pak spusťte [instalační kroky SQL Server](/sql/linux/sql-server-linux-setup#platforms) pro distribuci systému Linux. Pokud neinstalujete některou z volně licencovaných edicí SQL Server, musíte mít také licenci SQL Server a [License mobility prostřednictvím Software Assurance v Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Jsou pro SQL Server k dispozici image virtuálních počítačů Linux pro vlastní licenci (BYOL)?**

   V tuto chvíli nejsou k dispozici žádné image virtuálních počítačů s BYOL Linux pro SQL Server. SQL Server ale můžete na virtuální počítač jenom se systémem Linux nainstalovat ručně, jak je popsáno v předchozích dotazech.

1. **Můžu změnit virtuální počítač tak, aby používal vlastní licenci SQL Serveru, pokud byl vytvořený z některé z imagí z galerie s průběžnými platbami?**

   No. Nemůžete přepnout z licencování platby za sekundu na používání vlastní licence. Musíte vytvořit nový virtuální počítač Linux, nainstalovat SQL Server a migrovat data. Další podrobnosti o tom, jak vlastnit vlastní licenci, najdete na předchozí otázce.

## <a name="administration"></a>Správa

1. **Můžu spravovat virtuální počítač se systémem Linux běžící SQL Server s využitím SQL Server Management Studio (SSMS)?**

   Ano, ale SSMS je aktuálně nástroj jenom pro Windows. Abyste mohli používat SSMS s virtuálními počítači Linux se systémem SQL Server, musíte se vzdáleně připojit z počítače s Windows. V místním počítači se systémem Linux může nový nástroj [MSSQL-conf](/sql/linux/sql-server-linux-configure-mssql-conf) provádět mnoho úloh správy. Nástroj pro správu databází pro různé platformy najdete v tématu [Azure Data Studio](/sql/azure-data-studio/what-is).

1. **Můžu z virtuálního počítače s SQL Serverem zcela odebrat SQL Server?**

   Ano, ale budete se nadále účtovat za váš SQL Server virtuální počítač, jak je popsáno v [doprovodnéch materiálech pro SQL Server virtuálních počítačů Azure](../windows/pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Pokud už SQL Server nepotřebujete, můžete nasadit nový virtuální počítač a migrovat na něj data a aplikace. Pak můžete odebrat virtuální počítač s SQL Serverem.

## <a name="updating-and-patching"></a>Aktualizace a opravy

1. **Návody upgradovat na novou verzi nebo edici SQL Server na virtuálním počítači Azure?**

   Aktuálně není dostupný místní upgrade pro SQL Server spuštěný ve virtuálním počítači Azure. Vytvořte nový virtuální počítač Azure s požadovanou verzí/edicí SQL Serveru a potom pomocí [standardních technik migrace dat](/sql/linux/sql-server-linux-migrate-overview) proveďte migraci databází na tento nový server.

## <a name="general"></a>Obecné

1. **Jsou SQL Server řešení s vysokou dostupností podporovaná na virtuálních počítačích Azure?**

   V tuto chvíli to není možné. Skupiny dostupnosti Always On a clusteringu s podporou převzetí služeb při selhání vyžadují řešení clusteringu v systému Linux, například Pacemaker. Podporovaná distribuce systému Linux pro SQL Server v cloudu nepodporuje jejich doplňky pro vysokou dostupnost.

## <a name="resources"></a>Zdroje informací

**Virtuální počítače se systémem Linux**:

* [Přehled SQL Server na virtuálním počítači se systémem Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Zřízení SQL Server na virtuálním počítači se systémem Linux](sql-vm-create-portal-quickstart.md)
* [Dokumentace k SQL Server on Linux](/sql/linux/sql-server-linux-overview)

**Virtuální počítače s Windows**:

* [Přehled SQL Server na virtuálním počítači s Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Zřízení SQL Server na virtuálním počítači s Windows](../windows/sql-vm-create-portal-quickstart.md)
* [Nejčastější dotazy (Windows)](../windows/frequently-asked-questions-faq.md)