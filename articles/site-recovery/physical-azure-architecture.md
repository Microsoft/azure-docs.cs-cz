---
title: Architektura zotavení po havárii fyzického serveru v Azure Site Recovery
description: Tento článek poskytuje přehled komponent a architektury používaných při zotavení po havárii místních fyzických serverů do Azure pomocí služby Azure Site Recovery.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 966636e269043d81912b552711635f34b412f22f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654723"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Architektura zotavení po havárii fyzického serveru do Azure

Tento článek popisuje architekturu a procesy používané při replikaci, převzetí služeb při selhání a obnovování fyzických serverů s Windows a Linux mezi místními lokalitami a Azure pomocí služby [Azure Site Recovery](site-recovery-overview.md) .

## <a name="architectural-components"></a>Komponenty architektury

Následující tabulka a grafika obsahují podrobný pohled na součásti používané pro replikaci fyzického serveru do Azure.

| **Komponenta** | **Požadavek** | **Podrobnosti** |
| --- | --- | --- |
| **Azure** | Předplatné Azure a síť Azure. | Replikovaná data z místních fyzických počítačů se ukládají ve službě Azure Managed disks. Virtuální počítače Azure se při spuštění převzetí služeb při selhání z místního prostředí do Azure vytvoří s replikovanými daty. Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření. |
| **Procesový Server** | Instaluje se ve výchozím nastavení společně s konfiguračním serverem. | Funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure.<br/><br/> Procesový Server také nainstaluje službu mobility na servery, které chcete replikovat.<br/><br/> Jak vaše nasazení poroste, můžete přidat další samostatné procesové servery, které budou zpracovávat větší objemy replikačních přenosů. |
| **Hlavní cílový server** | Instaluje se ve výchozím nastavení společně s konfiguračním serverem. | Zpracovává replikační data během navrácení služeb po obnovení z Azure.<br/><br/> U rozsáhlých nasazení můžete přidat další samostatný hlavní cílový server pro navrácení služeb po obnovení. |
| **Replikované servery** | Služba mobility je nainstalovaná na každém serveru, který budete replikovat. | Doporučujeme, abyste povolili automatickou instalaci z procesového serveru. Nebo můžete službu nainstalovat ručně nebo použít metodu automatizovaného nasazení, například Configuration Manager. |

**Architektura replikace z fyzických prostředků do Azure**

![Komponenty](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>Nastavení odchozího připojení k síti

Aby mohla Site Recovery fungovat podle očekávání, musíte upravit odchozí síťové připojení, aby bylo možné prostředí replikovat.

> [!NOTE]
> Site Recovery nepodporuje připojení k síti pomocí ověřovacího proxy serveru.

### <a name="outbound-connectivity-for-urls"></a>Odchozí připojení pro adresy URL

Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, povolte přístup k těmto adresám URL:

| **Název**                  | **Komerční**                               | **Státní správa**                                 | **Popis** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Umožňuje zápis dat z virtuálního počítače do účtu úložiště mezipaměti ve zdrojové oblasti. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Zajišťuje autorizaci a ověřování pro adresy URL služby Site Recovery. |
| Replikace               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Umožňuje komunikaci virtuálního počítače se službou Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Umožňuje virtuálnímu počítači zapisovat data monitorování a diagnostiky Site Recovery. |


## <a name="replication-process"></a>Proces replikace

1. Nastavili jste nasazení, včetně místních a Azure komponent. V Recovery Services trezoru zadáte zdroj a cíl replikace, nastavíte konfigurační server, vytvoříte zásadu replikace a povolíte replikaci.
1. Počítače se replikují pomocí zásad replikace a počáteční kopie dat serveru se replikují do služby Azure Storage.
1. Po dokončení počáteční replikace se spustí replikace rozdílových změn do Azure. Sledované změny pro počítač jsou uloženy v souboru s příponou _. hrl_ .
   - Počítače komunikují s konfiguračním serverem na portu HTTPS 443 příchozí, pro správu replikací.
   - Počítače odesílají data replikace na procesový Server na portu HTTPS 9443 příchozí (dá se upravit).
   - Konfigurační server orchestruje správu replikace s Azure přes odchozí port HTTPS 443.
   - Procesový server přijímá data ze zdrojových počítačů, optimalizuje je a šifruje a odesílá je do služby Azure Storage přes odchozí port HTTPS 443.
   - Pokud povolíte konzistenci napříč několika virtuálními počítači, budou spolu počítače v replikační skupině komunikovat přes port 20004. Konzistence více virtuálních počítačů znamená, že seskupíte víc virtuálních počítačů do replikační skupiny, v rámci které se sdílí body obnovení konzistentní vzhledem k selháním a konzistentní vzhledem k aplikacím, když dojde k převzetí služeb při selhání. Tyto skupiny jsou užitečné v případě, že počítače používají stejnou úlohu a potřebují být konzistentní.
1. Provoz se přes internet replikuje do veřejných koncových bodů úložiště Azure. Alternativně můžete použít [veřejný partnerský vztah](../expressroute/about-public-peering.md) Azure ExpressRoute.

   > [!NOTE]
   > Replikace se nepodporuje přes síť VPN typu Site-to-Site z místní lokality nebo [privátního partnerského vztahu](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute)Azure ExpressRoute.

**Z fyzického procesu replikace do Azure**

![Proces replikace](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

Po nastavení replikace můžete spustit procházení pro zotavení po havárii (testovací převzetí služeb při selhání) a zkontrolovat, jestli všechno funguje podle očekávání. Pak můžete převzít služby při selhání a navrácení služeb po obnovení podle potřeby. Vezměte v úvahu následující položky:

- Plánované převzetí služeb není podporované.
- Navrácení služeb po obnovení do místního virtuálního počítače VMware je nezbytné. Budete potřebovat místní infrastrukturu VMware, a to i při replikaci místních fyzických serverů do Azure.
- Dojde k převzetí služeb při selhání jednoho počítače nebo k převzetí služeb při selhání více počítačů dohromady.
- Když spustíte převzetí služeb při selhání, vytvoří se virtuální počítače Azure z replikovaných dat ve službě Azure Storage.
- Po aktivaci počátečního převzetí služeb při selhání se potvrdíte, že zahájíte přístup ke úlohám z virtuálního počítače Azure.
- Až bude vaše místní lokalita opět dostupná, můžete službu navrátit.
- Nastavte infrastrukturu pro navrácení služeb po obnovení, která zahrnuje:
  - **Dočasný procesový Server v Azure**: Pokud chcete navrátit služby po obnovení z Azure, nastavte virtuální počítač Azure, který bude fungovat jako procesový Server, a zpracujte replikaci z Azure. Tento virtuální počítač můžete po dokončení obnovení po chybě odstranit.
  - **Připojení VPN**: pro navrácení služeb po obnovení potřebujete připojení k síti VPN (nebo Azure ExpressRoute) ze sítě Azure do místní lokality.
  - **Samostatný hlavní cílový server**: ve výchozím nastavení se navrácení služeb po obnovení zpracovává hlavní cílový server, který byl nainstalovaný s konfiguračním serverem na místním virtuálním počítači VMware. Pokud potřebujete navrátit navrácení velkých objemů dat, měli byste nastavit samostatný místní hlavní cílový server.
  - **Zásady navrácení služeb po obnovení:** Pro zpětnou replikaci do vaší místní lokality budete potřebovat zásady navrácení služeb. Zásady se automaticky vytvořily při vytváření zásad replikace z místního prostředí do Azure.
  - **Infrastruktura VMware**: pro navrácení služeb po obnovení potřebujete infrastrukturu VMware. Nelze navrátit služby po obnovení v případě fyzického serveru.
- Po dokončení komponent dojde k selhání ve třech fázích:
  - **Fáze 1**: znovu nastavte ochranu virtuálních počítačů Azure tak, aby se replikoval z Azure zpátky na místní virtuální počítače VMware.
  - **Fáze 2**: spuštění převzetí služeb při selhání v místní lokalitě.
  - **Fáze 3**: po navrácení služby po selhání se znovu povolí replikace.

**VMware navrácení služeb po obnovení z Azure**

![Navrácení služeb po obnovení](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>Další kroky

Informace o nastavení zotavení po havárii pro fyzické servery do Azure najdete v [Průvodci postupy](physical-azure-disaster-recovery.md).
