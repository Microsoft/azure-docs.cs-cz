---
title: Architektura zotavení po havárii fyzického serveru v Azure Site Recovery
description: Tento článek obsahuje přehled součástí a architektury používané při zotavení po havárii místních fyzických serverů do Azure se službou Azure Site Recovery.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 089d981284986a2b6eb0ee7f1dbd401fc7ce4fcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162833"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Architektura zotavení po havárii fyzického serveru do Azure

Tento článek popisuje architekturu a procesy používané při replikaci, převzetí služeb při selhání a obnovení fyzických serverů Windows a Linux mezi místním webem a Azure pomocí služby [Azure Site Recovery.](site-recovery-overview.md)

## <a name="architectural-components"></a>Komponenty architektury

Následující tabulka a grafika poskytuje zobrazení na vysoké úrovni komponent používaných pro replikaci fyzického serveru do Azure.

| **Komponenta** | **Požadavek** | **Podrobnosti** |
| --- | --- | --- |
| **Azure** | Předplatné Azure a síť Azure. | Replikovaná data z místních fyzických počítačů se ukládají na spravovaných discích Azure. Virtuální počítače Azure se vytvářejí s replikovanámi daty, když spustíte převzetí služeb při selhání z místního do Azure. Virtuální počítače Azure se připojí k virtuální síti Azure po svém vytvoření. |
| **Procesní server** | Ve výchozím nastavení nainstalovánspolu s konfiguračním serverem. | Funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure.<br/><br/> Procesní server také nainstaluje službu Mobility na servery, které chcete replikovat.<br/><br/> Jak vaše nasazení roste, můžete přidat další samostatné procesní servery pro zpracování větších svazků replikačního provozu. |
| **Hlavní cílový server** | Ve výchozím nastavení nainstalovánspolu s konfiguračním serverem. | Zpracovává data replikace během navrácení služeb po selhání z Azure.<br/><br/> U velkých nasazení můžete přidat další samostatný hlavní cílový server pro navrácení služeb po selhání. |
| **Replikované servery** | Služba Mobilita je nainstalována na každém serveru, který replikujete. | Doporučujeme povolit automatickou instalaci z procesního serveru. Nebo můžete nainstalovat službu ručně nebo použít metodu automatického nasazení, jako je například Configuration Manager. |

**Architektura replikace z fyzických prostředků do Azure**

![Komponenty](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Proces replikace

1. Nastavíte nasazení, včetně místních a součásti Azure. V trezoru služby Recovery Services určíte zdroj replikace a cíl, nastavíte konfigurační server, vytvoříte zásadu replikace a povolíte replikaci.
1. Počítače se replikují pomocí zásad replikace a počáteční kopie dat serveru se replikuje do úložiště Azure.
1. Po dokončení počáteční replikace začne replikace rozdílových změn do Azure. Sledované změny pro stroj jsou uloženy v souboru s příponou _HRL._
   - Počítače komunikují s konfiguračním serverem na příchozím portu HTTPS 443 pro správu replikace.
   - Počítače odesílají data replikace na procesní server na příchozím portu HTTPS 9443 (lze upravit).
   - Konfigurační server orchestruje správu replikace pomocí odchozího portu Azure přes HTTPS 443.
   - Procesní server přijímá data ze zdrojových počítačů, optimalizuje je a šifruje a odesílá je do úložiště Azure přes výstupní port HTTPS 443.
   - Pokud povolíte konzistenci napříč několika virtuálními počítači, budou spolu počítače v replikační skupině komunikovat přes port 20004. Konzistence více virtuálních počítačů znamená, že seskupíte víc virtuálních počítačů do replikační skupiny, v rámci které se sdílí body obnovení konzistentní vzhledem k selháním a konzistentní vzhledem k aplikacím, když dojde k převzetí služeb při selhání. Tyto skupiny jsou užitečné, pokud počítače jsou spuštěny stejné úlohy a musí být konzistentní.
1. Provoz se přes internet replikuje do veřejných koncových bodů úložiště Azure. Alternativně můžete použít [veřejný partnerský vztah](../expressroute/about-public-peering.md) Azure ExpressRoute.

   > [!NOTE]
   > Replikace není podporovaná přes síť VPN mezi lokalitami z místního webu nebo [privátního partnerského vztahu](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute)Azure ExpressRoute .

**Proces replikace fyzické ho azure**

![Proces replikace](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

Po nastavení replikace můžete spustit cvičení zotavení po havárii (test převzetí služeb při selhání) a zkontrolovat, zda vše funguje podle očekávání. Potom můžete převzetí služeb při selhání a převzetí služeb při selhání podle potřeby. Zvažte následující položky:

- Plánované převzetí služeb není podporované.
- Je nutné vrátit zpět do místního virtuálního počítači VMware. Potřebujete místní infrastrukturu VMware, i když replikujete místní fyzické servery do Azure.
- Převzetí služeb při selhání jednoho počítače nebo vytvořit plány obnovení, chcete-li převzetí služeb při selhání více počítačů společně.
- Když spustíte převzetí služeb při selhání, virtuální počítače Azure se vytvoří z replikovaných dat v úložišti Azure.
- Po aktivaci počáteční převzetí služeb při selhání, potvrdíte jej ke spuštění přístupu k zatížení z virtuálního počítače Azure.
- Až bude vaše místní lokalita opět dostupná, můžete službu navrátit.
- Nastavte infrastrukturu navrácení služeb po selhání, která zahrnuje:
  - **Dočasný procesní server v Azure:** Chcete-li z Azure navrácení služeb po službě zabezpečení, nastavíte virtuální počítač Azure tak, aby fungoval jako procesní server, který bude zpracovávat replikaci z Azure. Tento virtuální virtuální ms můžete odstranit po dokončení navrácení služeb po selhání.
  - **Připojení VPN**: Chcete-li navrácení služeb po obnovení, potřebujete připojení VPN (nebo Azure ExpressRoute) ze sítě Azure do místního webu.
  - **Samostatný hlavní cílový server**: Ve výchozím nastavení je vrácení služeb při selhání zpracováno hlavním cílovým serverem, který byl nainstalován s konfiguračním serverem v místním virtuálním počítači VMware. Pokud potřebujete navrácení velkého množství provozu, měli byste nastavit samostatný místní hlavní cílový server.
  - **Zásady navrácení služeb po obnovení:** Pro zpětnou replikaci do vaší místní lokality budete potřebovat zásady navrácení služeb. Zásady byly vytvořeny automaticky při vytvoření zásad replikace z místního do Azure.
  - **Infrastruktura VMware**: Chcete-li získat zpět své selhání, potřebujete infrastrukturu VMware. Nelze navrátit služby po obnovení v případě fyzického serveru.
- Po komponenty jsou na místě, navrácení služeb po selhání dochází ve třech fázích:
  - **Fáze 1**: Znovu připevněte virtuální počítače Azure tak, aby se replikovaly z Azure zpět do místních virtuálních počítačů VMware.
  - **Fáze 2**: Spusťte převzetí služeb při selhání do místního webu.
  - **Fáze 3**: Po selhání úloh zpět, opětovné povolení replikace.

**Navrácení služeb po selhání v Mware z Azure**

![Navrácení služeb po obnovení](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>Další kroky

Pokud chcete nastavit zotavení po havárii pro fyzické servery do Azure, [přečtěte si návod](physical-azure-disaster-recovery.md).
