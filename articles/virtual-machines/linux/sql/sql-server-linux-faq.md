---
title: Nejčastější dotazy k SQL Serveru v linuxových virtuálních počítačích Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se spuštění SQL Serveru na virtuálních počítačích Linux Azure.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1e729c608a2cad28c810f8d5236360c909a496b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70082032"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Nejčastější dotazy týkající se SQL Serveru na virtuálních počítačích Linux Azure

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Tento článek obsahuje odpovědi na některé z nejčastějších otázek týkajících se spuštění [SQL Serveru na virtuálních počítačích Linux Azure](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Tento článek se zaměřuje na problémy specifické pro SQL Server na virtuálních počítačích s Linuxem. Pokud používáte SQL Server na virtuálních počítačích se systémem Windows, přečtěte si [nejčastější dotazy k systému Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>Obrázky

1. **Jaké image galerie virtuálních počítačů serveru SQL Server jsou k dispozici?**

   Azure udržuje image virtuálních strojů pro všechny podporované hlavní verze SQL Serveru ve všech edicích pro Linux i Windows. Další podrobnosti najdete v kompletním seznamu [iimages virtuálních počítačových virtuálních aplikací v Linuxu](sql-server-linux-virtual-machines-overview.md#create) a [bitových kopií virtuálních počítačích s Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **Jsou aktualizovány existující bitové kopie galerie virtuálních počítačů serveru SQL Server?**

   Každé dva měsíce jsou bitové kopie serveru SQL Server v galerii virtuálních počítačů aktualizovány nejnovějšími aktualizacemi Linuxu a Windows. Pro linuxové obrázky to zahrnuje nejnovější aktualizace systému. Pro bitové kopie systému Windows to zahrnuje všechny aktualizace, které jsou ve službě Windows Update označeny jako důležité, včetně důležitých aktualizací zabezpečení serveru SQL Server a aktualizací Service Pack. Kumulativní aktualizace SQL Serveru jsou zpracovávány odlišně pro Linux a Windows. Pro Linux, SQL Server kumulativní aktualizace jsou také zahrnuty v aktualizaci. V současné době však virtuální servery Windows nejsou aktualizovány kumulativními aktualizacemi serveru SQL Server nebo Windows Server.

1. **Jaké související balíčky serveru SQL Server jsou také nainstalovány?**

   Balíčky serveru SQL Server, které jsou ve výchozím nastavení nainstalovány na virtuálních počítačích SQL Server Linux, naleznete [v tématu Nainstalované balíčky](sql-server-linux-virtual-machines-overview.md#packages).

1. **Mohou být image virtuálních počítačů serveru SQL Server odebrány z galerie?**

   Ano. Azure udržuje jenom jednu bitovou kopii na hlavní verzi a edici. Například při vydání nové aktualizace SERVICE Pack serveru SQL Server azure přidá novou bitovou kopii do galerie pro tuto aktualizaci Service Pack. Bitová kopie serveru SQL Server pro předchozí aktualizaci Service Pack se okamžitě odebere z portálu Azure. Je však stále k dispozici pro zřizování z prostředí PowerShell pro další tři měsíce. Po třech měsících již není k dispozici předchozí bitová kopie aktualizace Service Pack. Tato zásada odebrání by také použít, pokud sql server verze stane nepodporovaný, když dosáhne konce svého životního cyklu.

## <a name="creation"></a>Vytvoření

1. **Jak vytvořím virtuální počítač Linux Azure s SQL Serverem?**

   Nejjednodušším řešením je vytvořit virtuální počítač s Linuxem, který obsahuje SQL Server. Kurz o registraci pro Azure a vytvoření virtuálního počítače SQL z portálu najdete [v tématu Zřízení virtuálního počítače Linux SQL Server na webu Azure Portal](provision-sql-server-linux-virtual-machine.md). Máte také možnost ručně nainstalovat SQL Server na virtuální počítač s volně licencovanou edicí (Developer nebo Express) nebo opětovným použitím místní licence. Pokud si přivedete vlastní licenci, musíte mít [mobilitu licencí prostřednictvím programu Software Assurance v Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Proč nemůžu zřídit virtuální počítač RHEL nebo SLES SQL Server s předplatným Azure, které má limit útraty?**

   Virtuální počítače RHEL a SLES vyžadují předplatné bez limitu útraty a ověřenou platební metodu (obvykle platební kartu) přidruženou k předplatnému. Pokud zřídíte virtuální počítač RHEL nebo SLES bez odebrání limitu útraty, vaše předplatné se deaktivuje a všechny virtuální počítače/služby se zastaví. Pokud narazíte na tento stav, chcete-li znovu povolit [předplatné, odeberte limit útraty](https://account.windowsazure.com/subscriptions). Vaše zbývající kredity budou obnoveny pro aktuální fakturační cyklus, ale příplatek za obrázek rhel nebo SLES VM se bude přičítat vaší kreditní kartě, pokud se rozhodnete znovu spustit a pokračovat v jeho spuštění.

## <a name="licensing"></a>Licencování

1. **Jak můžu na virtuální počítač Azure nainstalovat licencovanou kopii SQL Serveru?**

   Nejprve vytvořte virtuální počítač pouze pro operační systém Linux. Potom spusťte [kroky instalace serveru SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) pro distribuci Linuxu. Pokud neinstalujete jednu z volně licencovaných edicí SERVERU SQL Server, musíte mít také licenci SQL Serveru a [mobilitu licencí prostřednictvím programu Software Assurance v Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Existují pro SQL Server image virtuálních strojů Linuxu (Bring-Your-Own-License) (BYOL) ?**

   V současné době neexistují žádné BYOL Linux image virtuálního počítače pro SQL Server. Sql Server však můžete ručně nainstalovat na virtuální počítač pouze pro Linux, jak je popsáno v předchozích otázkách.

1. **Můžu změnit virtuální počítač tak, aby používal vlastní licenci SQL Serveru, pokud byl vytvořený z některé z imagí z galerie s průběžnými platbami?**

   Ne. Z licencování s platbou za sekundu nelze přejít na používání vlastní licence. Musíte vytvořit nový virtuální počítač s Linuxem, nainstalovat SQL Server a migrovat data. Další podrobnosti o podání vlastní licence naleznete v předchozí otázce.

## <a name="administration"></a>Správa

1. **Mohu spravovat virtuální počítač Linux SQL Server pomocí sql serveru Management Studio (SSMS)?**

   Ano, ale SSMS je v současné době pouze nástroj pro Windows. Chcete-li používat virtuální počítače SSMS s linuxovým SQL Serverem, musíte se vzdáleně připojit z počítače se systémem Windows. Lokálně na Linuxu může nový nástroj [mssql-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) provádět mnoho administrativních úloh. Nástroj pro správu databází napříč platformami najdete v [tématu Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is).

1. **Můžu z virtuálního počítače SQL zcela odebrat SQL Server?**

   Ano, ale bude se vám nadále účtovat poplatky za virtuální počítač SQL, jak je popsáno v [pokynech pro ceny pro virtuální počítače SQL Server Azure](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Pokud už SQL Server nepotřebujete, můžete nasadit nový virtuální počítač a migrovat na něj data a aplikace. Pak můžete odebrat virtuální počítač s SQL Serverem.

## <a name="updating-and-patching"></a>Aktualizace a opravy

1. **Jak můžu upgradovat na novou verzi nebo edici SQL Serveru v virtuálním počítači Azure?**

   Aktuálně není dostupný místní upgrade pro SQL Server spuštěný ve virtuálním počítači Azure. Vytvořte nový virtuální počítač Azure s požadovanou verzí/edicí SQL Serveru a potom pomocí [standardních technik migrace dat](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview) proveďte migraci databází na tento nový server.

## <a name="general"></a>Obecné

1. **Jsou řešení SQL Server s vysokou dostupností podporovaná na virtuálních počítačích Azure?**

   V tuto chvíli to není možné. Vždy na dostupnost skupiny a clustering s podporou převzetí služeb při selhání vyžadují clustering řešení v Linuxu, jako je například kardiostimulátor. Podporované distribuce Linuxu pro SQL Server nepodporují jejich doplňky s vysokou dostupností v cloudu.

## <a name="resources"></a>Prostředky

**Virtuální počítače S Linuxem**:

* [Přehled SQL Serveru na virtuálním počítači s Linuxem](sql-server-linux-virtual-machines-overview.md)
* [Zřízení virtuálního počítače SQL Server Linux](provision-sql-server-linux-virtual-machine.md)
* [Sql Server v dokumentaci k Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Virtuální aplikace Windows**:

* [Přehled sql serveru na virtuálním počítači se systémem Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Zřízení virtuálního virtuálního modulu SQL Server pro Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Nejčastější dotazy (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)