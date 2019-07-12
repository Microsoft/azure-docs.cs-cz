---
title: Příprava virtuálních počítačů VMware s Linuxem pro migraci do Azure pomocí služby Azure Migrate serveru migrace | Dokumentace Microsoftu
description: Popisuje postup přípravy virtuálního počítače s Linuxem pro migraci do Azure pomocí služby Azure Migrate migrace serveru
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/14/2018
ms.author: raynew
ms.openlocfilehash: efc410699ef6f4722857c812b38473c8f54b911b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811788"
---
# <a name="prepare-linux-vmware-vms-for-migration-to-azure"></a>Příprava virtuálních počítačů VMware s Linuxem pro migraci do Azure 

Tento článek popisuje, jak připravit virtuální počítače VMware s Linuxem, když chcete migrovat do Azure s využitím [Azure Migrate](migrate-overview.md). 

> [!NOTE]
> Migrace serveru do Azure Migrate je aktuálně ve verzi public preview. Existující verze GA Azure Migrate můžete použít ke zjištění a posouzení virtuálních počítačů pro migraci, ale skutečné migrace se nepodporuje v existující všeobecně dostupné verze.

Příprava počítače s Linuxem následujícím způsobem:

1. Instalace technologie Hyper-V Linux Integration Services. Novější verze Linuxových distribucí, pravděpodobně to ve výchozím nastavení nainstalovaná).
2. Opětovné sestavení image Linuxu init, tak, aby obsahoval potřebné ovladače Hyper-V, a tak, aby virtuální počítač se spustí v Azure (vyžadováno pro některé distribuce).
3. Povolení protokolování konzoly sériového portu pro řešení potíží. [Další informace](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console).
4. Aktualizujte soubor mapování zařízení s názvem zařízení na přidružení svazku, používat identifikátory trvalé zařízení.
5. Aktualizace položky fstab identifikátory trvalý svazek.
6. Odeberte všechna udev pravidla, která rezervovat názvy rozhraní na základě adresy MAC atd.
7. Síťová rozhraní aktualizace pro příjem IP adresy služby DHCP.
8. Zajištění ssh je povolená. Zkontrolujte, jestli je služba sshd nastavena na automatické spuštění při restartování.
9. Ujistěte se, že příchozí ssh připojení požadavky nejsou blokovány bránou firewall operačního systému nebo pravidla tabulky protokolu IP.

[Další informace](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) o provedení těchto změn na většinou oblíbených Linuxových distribucí.

## <a name="sample-script"></a>Ukázkový skript

Tento skript (připravit pro azure.sh) poskytuje ukázku pro přípravu počítače s Linuxem. Skript nemusí fungovat pro všechna prostředí a distribucí, ale je vytvoření užitečného počátečního bodu.

Skript ukazuje postup: 

- Znovu vygenerovat init image Linuxu s potřebné ovladače v případě potřeby.
- Aktualizace položky fstab identifikátory trvalý svazek.
- Přesměrujte protokoly konzoly sériového portu.
- Povolit přístup ke službě Azure sériové konzoly
- Odeberte pravidla net procesu udev
- Vložit spustit na spouštěcí skript, který se spustí, jakmile virtuální počítač se spustí. Zkontroluje, zda počítač běží v Azure. Pokud se jedná, aktualizace konfigurace sítě na virtuálním počítači a nastaví první rozhraní sítě ethernet pro používání protokolu DHCP k získání IP adresy.

### <a name="before-you-start"></a>Než začnete

- Ukázkový skript obsahuje ukázkový postup. Nespouštějte na provozní systémy. To může poškodit nebo poškození virtuálního počítače, na kterém běží.
- Doporučujeme, abyste že ho spustíte na testovacím virtuálním počítači. Než začnete, provést zálohování virtuálního počítače nebo snímku tak, aby v případě potřeby můžete obnovit virtuální počítač. 
- Skript funguje, když virtuální počítač s některým z těchto distribucí systému Linux:
    - Red Hat Enterprise Linux verze 6.5 + 7.1 +
    - Operační systém cent 6.5 + 7.1 +
    - SUSE Linux Enterprise Server 12 SP1, SP2 SP3
    - Ubuntu 14.04, 16.04, 18.04
    - Debian 7, 8

### <a name="run-the-script"></a>Spuštění skriptu

1. Kopírovat skript, který chcete systému Linux testovací virtuální počítač pomocí protokolu sftp nebo klientem spojovací bod služby, například Filezilly nebo WinScp.
2. Připojte se přes SSH počítač s Linuxem pomocí účtu správce.
3. Přejděte do adresáře, skript.
4. Chcete-li vytvořit skript jako spustitelný soubor, spusťte **sudo chmod 777 Příprava pro azure.sh**.
5. Spusťte skript s **./prepare-for-azure.sh**.

Tady je způsob spuštění skriptu:

![Skript Linux](./media/how-to-prepare-linux-for-migration/script1.png)
![skriptu Linux](./media/how-to-prepare-linux-for-migration/script2.png)
![skriptu Linux](./media/how-to-prepare-linux-for-migration/script3.png)
![skriptu Linux](./media/how-to-prepare-linux-for-migration/script4.png)
![skriptu Linux](./media/how-to-prepare-linux-for-migration/script5.png)
![skriptu Linux ](./media/how-to-prepare-linux-for-migration/script6.png)
 ![Skriptu Linux](./media/how-to-prepare-linux-for-migration/script7.png)
![skriptu Linux](./media/how-to-prepare-linux-for-migration/script8.png)
![skriptu Linux](./media/how-to-prepare-linux-for-migration/script9.png)
![skriptu Linux](./media/how-to-prepare-linux-for-migration/script10.png)
![skriptu Linux ](./media/how-to-prepare-linux-for-migration/script11.png)
 ![Skriptu Linux](./media/how-to-prepare-linux-for-migration/script12.png)
![skriptu Linux](./media/how-to-prepare-linux-for-migration/script13.png)
![skriptu Linux](./media/how-to-prepare-linux-for-migration/script14.png)
![skriptu Linux](./media/how-to-prepare-linux-for-migration/script15.png)
![skriptu Linux ](./media/how-to-prepare-linux-for-migration/script16.png)
 ![Skriptu Linux](./media/how-to-prepare-linux-for-migration/script17.png)
![skriptu Linux](./media/how-to-prepare-linux-for-migration/script18.png)
![skriptu Linux](./media/how-to-prepare-linux-for-migration/script19.png)
![skriptu Linux](./media/how-to-prepare-linux-for-migration/script20.png)
![skriptu Linux ](./media/how-to-prepare-linux-for-migration/script21.png)
 ![Skriptu Linux](./media/how-to-prepare-linux-for-migration/script22.png)
![skriptu Linux](./media/how-to-prepare-linux-for-migration/script23.png)
![skriptu Linux](./media/how-to-prepare-linux-for-migration/script24.png)
![skriptu Linux](./media/how-to-prepare-linux-for-migration/script25.png)



## <a name="next-steps"></a>Další kroky

- Další informace o použití [mapování závislosti počítačů](how-to-create-group-machine-dependencies.md) k vytvoření skupiny s vysokou spolehlivostí.
- [Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
