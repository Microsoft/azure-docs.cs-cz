---
title: Řešení potíží s převzetí služeb při selhání do Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak řešit běžné problémy během převzetí služeb při selhání do Azure pomocí Azure Site Recovery.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 09/11/2018
ms.author: ponatara
ms.openlocfilehash: 420d061b34734c7b5997f5cdd58fe7faaee9cb82
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51236752"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Řešení chyb při selhání virtuálního počítače do Azure

Může zobrazit jedna z následující chyby při provádění převzetí služeb při selhání virtuálního počítače do Azure. Řešení potíží s, popsané kroky použijte pro všechny chybové stavy.

## <a name="failover-failed-with-error-id-28031"></a>Převzetí služeb při selhání s ID chyby 28031

Site Recovery se nepodařilo vytvořit nezdařené přes virtuální počítač v Azure. K tomu mohlo dojít vlivem jednoho z následujících důvodů:

* Není k dispozici dostatečnou kvótu pro vytvoření virtuálního počítače k dispozici: dostupnou kvótu můžete zkontrolovat tak, že přejdete k předplatnému -> využití a kvóty. Můžete otevřít [novou žádost o podporu](https://aka.ms/getazuresupport) o navýšení kvóty.

* Pokoušíte se převzetí služeb při selhání virtuálních počítačů z různých velikostní řady ve stejné sadě dostupnosti. Ujistěte se, že zvolíte stejnou řadu velikostí pro všechny virtuální počítače ve stejné sadě dostupnosti. Změnit velikost tak, že přejdete do nastavení výpočty a síť virtuálního počítače a pak zkuste převzetí služeb při selhání.

* Na předplatné, které brání vytvoření virtuálního počítače není zásadu. Změna zásad pro povolení vytvoření virtuálního počítače a potom opakujte převzetí služeb při selhání.

## <a name="failover-failed-with-error-id-28092"></a>Převzetí služeb při selhání s ID chyby 28092

Site Recovery se nepodařilo vytvořit síťové rozhraní, pro které bylo provedeno přes virtuální počítač. Ujistěte se, že máte dostatečnou kvótu pro vytvoření síťových rozhraní v rámci předplatného k dispozici. Dostupnou kvótu můžete zkontrolovat tak, že přejdete k předplatnému -> využití a kvóty. Můžete otevřít [novou žádost o podporu](https://aka.ms/getazuresupport) o navýšení kvóty. Pokud máte dostatečnou kvótu, pak může se jednat přerušovaný vydávání, zkuste operaci zopakovat. Pokud se problém nevyřeší ani po opakovaných pokusů, pak napište komentář na konci tohoto dokumentu.  

## <a name="failover-failed-with-error-id-70038"></a>Převzetí služeb při selhání s ID chyby 70038

Site Recovery se nepodařilo vytvořit nezdařené přes klasický virtuální počítač v Azure. K tomu mohlo dojít, protože:

* Jeden z prostředků, jako je například virtuální síť, která se vyžaduje pro virtuální počítač, který se má vytvořit neexistuje. Vytvoření virtuální sítě, jak je uvedeno v části Nastavení výpočty a síť virtuálního počítače nebo upravit nastavení pro virtuální síť, která již existuje a zkuste převzetí služeb při selhání.

## <a name="unable-to-connectrdpssh---vm-connect-button-grayed-out"></a>Nelze se připojit nebo RDP/SSH - připojení virtuálního počítače nejde aktivovat tlačítko

Pokud **připojit** tlačítko na převzetí virtuálního počítače v Azure je zobrazena šedě a nejste připojení k Azure přes Expressroute nebo VPN typu Site-to-Site připojení, potom

1. Přejděte na **virtuálního počítače** > **sítě**, klikněte na název požadované síťové rozhraní.  ![síťové rozhraní](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Přejděte do **konfigurací protokolu Ip**, potom klikněte na název pole požadované konfigurace protokolu IP. ![Konfigurací IP adresy](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Pokud chcete povolit veřejné IP adresy, klikněte na **povolit**. ![Povolit IP adresu](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Klikněte na **konfigurovat požadované nastavení** > **vytvořit nový**. ![Vytvořit nový](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Zadejte název veřejné adresy, vyberte výchozí možnosti pro **SKU** a **přiřazení**, pak klikněte na tlačítko **OK**.
6. Nyní uložit změny, klikněte na tlačítko **Uložit**.
7. Zavřete panely a přejděte do **přehled** části virtuální počítač pro připojení nebo protokol RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Nelze se připojit nebo RDP/SSH - připojení virtuálního počítače k dispozici tlačítko

Pokud **připojit** tlačítko na převzetí virtuálního počítače v Azure je k dispozici (není zobrazena šedě), a pak ověřte **Diagnostika spouštění** na virtuálním počítači a zkontrolujte chyby, jak je uvedeno v [v tomto článku](../virtual-machines/windows/boot-diagnostics.md).

1. Pokud se virtuální počítač nespustil, zkuste převzetí služeb při selhání do staršího bodu obnovení.
2. Pokud aplikace ve virtuálním počítači není zapnutý, zkuste převzetí služeb při selhání do bodu obnovení s konzistentní aplikací.
3. Pokud je virtuální počítač k doméně, pak zajistěte, aby že řadiče domény funguje správně. To lze provést pomocí následujících níže uvedené kroky:

    a. Vytvořte nový virtuální počítač ve stejné síti.

    b.  Ujistěte se, že se může připojit ke stejné doméně, ve kterém se přes virtuální počítač se očekává.

    c. Pokud je řadič domény **není** funguje správně, zkuste protokolování, u kterého proběhlo přes virtuální počítač pomocí místního účtu správce.
4. Pokud používáte vlastní server DNS, ujistěte se, že je dostupný. To lze provést pomocí následujících níže uvedené kroky:

    a. Vytvoření nového virtuálního počítače ve stejné síti a

    b. Zkontrolujte, jestli je virtuální počítač moct dokáže překládat pomocí vlastního serveru DNS.

>[!Note]
>Povolení kteréhokoli jiného nastavení než Diagnostika spouštění by vyžadovalo Azure VM Agent nainstalovaný na virtuálním počítači před převzetí služeb

## <a name="unexpected-shutdown-message-event-id-6008"></a>Neočekávané vypnutí zprávy (6008 ID události)

Při spouštění virtuálním počítači Windows příspěvek převzetí služeb při selhání, pokud se zobrazí zpráva neočekávané vypnutí na obnoveného virtuálního počítače, znamená to, že stav vypnutí virtuálního počítače nebyly zaznamenány v bodu obnovení pro převzetí služeb při selhání. To se stane, když obnovíte do bodu, když virtuální počítač kdyby byl vypnutý plně.

To není obvykle příčinou znepokojení a obvykle se dá ignorovat pro neplánované převzetí služeb při selhání. V případě plánované převzetí služeb při selhání Ujistěte se, že virtuální počítač je správně vypnutý před převzetí služeb při selhání a poskytnout dostatek času na probíhající replikaci dat v místním k odeslání do Azure. Potom použijte **nejnovější** možnost [převzetí služeb při selhání obrazovky](site-recovery-failover.md#run-a-failover) tak, aby všechny čekající data v Azure je zpracován do bodu obnovení, který potom slouží pro převzetí služeb při selhání virtuálního počítače.

## <a name="retaining-drive-letter-after-failover"></a>Zachování písmeno jednotky po převzetí služeb při selhání
Pokud chcete zachovat písmeno jednotky na virtuální počítače po převzetí služeb při selhání, můžete nastavit **zásada SAN** pro virtuální počítač místní k **OnlineAll**. [Další informace](https://support.microsoft.com/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).

## <a name="next-steps"></a>Další postup
- Řešení potíží s [připojení RDP k virtuálnímu počítači Windows](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- Řešení potíží s [připojení SSH k virtuálnímu počítači s Linuxem](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Pokud potřebujete další pomoc, pak zveřejněte svůj dotaz na [fórum Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) nebo komentář na konci tohoto dokumentu. Aktivní komunitě, který by měl být schopni pomoct, máme.
