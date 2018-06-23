---
title: Řešení potíží s převzetí služeb při selhání do Azure selhání | Microsoft Docs
description: Tento článek popisuje způsoby řešení běžných chyb v převzetí služeb při selhání do Azure
services: site-recovery
documentationcenter: ''
author: ponatara
manager: abhemraj
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/09/2018
ms.author: ponatara
ms.openlocfilehash: 838eac510fc17d56f808f541f4e205a279f63c56
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318887"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Řešení chyb při přebírání služeb při selhání virtuálního počítače do Azure

Zobrazí jednu z těchto chyb při provádění převzetí služeb při selhání virtuálního počítače do Azure. K odstraňování, použijte popisuje kroky pro všechny chybové stavy.

## <a name="failover-failed-with-error-id-28031"></a>Převzetí služeb při selhání s ID chyby 28031

Site Recovery se nepodařilo vytvořit nezdařené přes virtuální počítač v Azure. K tomu mohlo dojít vlivem jednoho z následujících důvodů:

* Není k dispozici dostatečnou kvótu k dispozici pro vytvoření virtuálního počítače: dostupná kvóta můžete zkontrolovat tak, že přejdete do předplatného -> využití + kvóty. Můžete otevřít [novou žádost o podporu](http://aka.ms/getazuresupport) chcete kvótu zvýšit.

* Pokoušíte se k virtuálním počítačům převzetí služeb při selhání jinou velikost rodin ve stejné skupině dostupnosti. Ujistěte se, že zvolíte stejná rodina velikost pro všechny virtuální počítače ve stejné sadě dostupnosti. Změnit velikost tak, že přejdete do nastavení výpočty a síť virtuálního počítače a potom zkuste zopakovat převzetí služeb při selhání.

* Není zásadu na předplatné, které brání vytvoření virtuálního počítače. Změňte nastavení zásad pro povolení vytvoření virtuálního počítače a potom zkuste zopakovat převzetí služeb při selhání.

## <a name="failover-failed-with-error-id-28092"></a>Převzetí služeb při selhání s ID chyby 28092

Site Recovery nemohl vytvořit síťové rozhraní pro neúspěšný přes virtuální počítač. Ujistěte se, že máte dostatečnou kvótu k dispozici pro vytvoření síťových rozhraní v rámci předplatného. Dostupná kvóta můžete zkontrolovat tak, že přejdete do předplatného -> využití + kvóty. Můžete otevřít [novou žádost o podporu](http://aka.ms/getazuresupport) chcete kvótu zvýšit. Pokud máte dostatečnou kvótu, pak může se jednat přerušované vystavování, operaci opakujte. Pokud problém přetrvá i po opakování, pak nechte komentář na konci tohoto dokumentu.  

## <a name="failover-failed-with-error-id-70038"></a>Převzetí služeb při selhání s ID chyby 70038

Site Recovery se nepodařilo vytvořit nezdařené přes klasické virtuální počítač v Azure. K tomu mohlo dojít, protože:

* Jeden z prostředků, jako jsou například virtuální sítě, která je požadována pro virtuální počítač, který se má vytvořit neexistuje. Vytvoření virtuální sítě, jak je uvedeno v nastavení výpočtů a sítě virtuálního počítače nebo změnit nastavení pro virtuální síť, která již existuje a potom zkuste zopakovat převzetí služeb při selhání.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Nepodařilo se připojit nebo připojení RDP/SSH k neúspěšný přes virtuální počítač z důvodu šedě tlačítko Připojit na virtuálním počítači

Pokud není k dispozici tlačítko Připojit a nejste připojení k Azure prostřednictvím Expressroute nebo VPN typu Site-to-Site připojení, potom

1. Přejděte na **virtuálního počítače** > **sítě**, klikněte na název požadované síťové rozhraní.  ![síťové rozhraní](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Přejděte na **konfigurace protokolu Ip**, potom klikněte na pole název požadované konfigurace protokolu IP. ![Konfigurace IP adresy](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Chcete-li povolit veřejnou IP adresu, klikněte na **povolit**. ![Povolit IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Klikněte na **konfigurovat požadované nastavení** > **vytvořit nový**. ![Vytvořit nový](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Zadejte název veřejnou adresu, vyberte výchozí možnosti pro **SKU** a **přiřazení**, pak klikněte na tlačítko **OK**.
6. Nyní uložit změny provedené, klikněte na **Uložit**.
7. Zavřete panely a přejděte do **přehled** část virtuálního počítače pro připojení nebo připojení RDP.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-even-though-connect-button-is-available-not-grayed-out-on-the-virtual-machine"></a>Nepodařilo se připojit nebo připojení RDP/SSH k neúspěšný přes virtuální počítače i když Connect tlačítko je k dispozici (není k dispozici) na virtuálním počítači

Zkontrolujte **spouštění diagnostiky** na virtuálním počítači a zkontrolujte chyby uvedené v tomto článku.

1. Pokud virtuální počítač nebyla spuštěna, zkuste přebírání služeb při selhání starší bod obnovení.
2. Pokud aplikace ve virtuálním počítači není nahoru, zkuste převzetí služeb při selhání pro bod obnovení konzistentních s aplikací.
3. Pokud je virtuální počítač připojený k doméně, pak zajistěte, aby že přesně funguje tohoto řadiče domény. To lze provést pomocí následujících níže uvedené kroky.
    a. Vytvoření nového virtuálního počítače ve stejné síti

    b.  Ujistěte se, že je schopný připojit ke stejné doméně, na kterém neúspěšný přes virtuální počítač je očekáván přijít.

    c. Pokud je řadič domény **není** funguje správně, zkuste protokolování do neúspěšný přes virtuální počítač pomocí místního účtu správce
4. Pokud používáte vlastního serveru DNS, pak se ujistěte, zda je dostupná. To lze provést pomocí následujících níže uvedené kroky.
    a. Vytvoření nového virtuálního počítače ve stejné síti a b. Zkontrolujte, jestli virtuální počítač je moct překlad pomocí vlastního serveru DNS

>[!Note]
>Povolení jakékoli nastavení jiné než Diagnostika spouštění by vyžadovaly instalaci ve virtuálním počítači před převzetí agenta virtuálního počítače Azure

## <a name="next-steps"></a>Další postup

Pokud potřebujete další pomoc, následně je publikovat svůj dotaz na [Site Recovery fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) nebo komentář na konci tohoto dokumentu. Máme aktivní komunitě, které byste měli mít vám pomůže.
