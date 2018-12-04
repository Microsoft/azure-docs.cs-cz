---
title: Migrace místních serverů s Windows Serverem 2008 do Azure pomocí Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje migraci místních počítačů s Windows Serverem 2008 do Azure pomocí Azure Site Recovery.
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 11/27/2018
ms.author: bsiva
ms.custom: MVC
ms.openlocfilehash: 32b8337121c2552a29f9af35e50f540972a1add7
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847639"
---
# <a name="migrate-servers-running-windows-server-2008-to-azure"></a>Migrace serverů s Windows Serverem 2008 do Azure

V tomto kurzu se dozvíte, jak migrovat místní servery s Windows Serverem 2008 nebo 2008 R2 do Azure pomocí Azure Site Recovery. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Příprava místního prostředí na migraci
> * Nastavení cílového prostředí
> * Nastavení zásady replikace
> * Povolení replikace
> * Spuštění testu migrace za účelem ověření, že vše funguje podle očekávání
> * Převzetí služeb při selhání do Azure s dokončení migrace

V části Omezení a známé problémy najdete několik omezení a alternativních řešení známých problémů, se kterými se můžete setkat při migraci počítačů s Windows Serverem 2008 do Azure. 


## <a name="supported-operating-systems-and-environments"></a>Podporované operační systémy a prostředí


|Operační systém  | Místní prostředí  |
|---------|---------|
|Windows Server 2008 SP2 – 32bitová a 64bitová verze(IA-32 a x86-64)</br>– Standard</br>– Enterprise</br>– Datacenter   |     Virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery    |
|Windows Server 2008 R2 SP1 – 64bitová verze</br>– Standard</br>– Enterprise</br>– Datacenter     |     Virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery|

> [!WARNING]
> - Migrace serverů se Server Core se nepodporuje.
> - Před migrací se ujistěte, že máte nainstalovanou nejnovější aktualizaci Service Pack a aktualizace Windows.


## <a name="prerequisites"></a>Požadavky

Než začnete, je vhodné se seznámit s architekturou Azure Site Recovery pro [migraci VMware a fyzických serverů](vmware-azure-architecture.md) nebo [migraci virtuálních počítačů Hyper-V](hyper-v-azure-architecture.md). 

Pokud chcete migrovat virtuální počítače Hyper-V s Windows Serverem 2008 nebo Windows Serverem 2008 R2, postupujte podle kroků v kurzu [migrace místních počítačů do Azure](migrate-tutorial-on-premises-azure.md).

Ve zbývající části tohoto kurzu se dozvíte, jak migrovat místní virtuální počítače VMware a fyzické servery s Windows Serverem 2008 nebo 2008 R2.


## <a name="limitations-and-known-issues"></a>Omezení a známé problémy

- Na konfiguračním serveru, dalších procesových serverech a ve službě Mobility, které použijete k migraci serverů s Windows Serverem 2008 R2, musí běžet software Azure Site Recovery verze 9.19.0.0 nebo novější.

- Body obnovení konzistentní vzhledem k aplikacím a funkce zajištění konzistence napříč několika virtuálními počítači se nepodporují u replikace serverů s Windows Serverem 2008 SP2. Servery s Windows Serverem 2008 SP2 se musí migrovat do bodu obnovení konzistentního vzhledem k selháním. Body obnovení konzistentní vzhledem k selháním se ve výchozím nastavení generují každých 5 minut. Použití zásady replikace s nakonfigurovanou frekvencí pořizování snímků konzistentních vzhledem k aplikacím způsobí kritický stav replikace kvůli chybějícím bodům obnovení konzistentním vzhledem k aplikacím. Pokud se chcete vyhnout falešně pozitivním výsledkům, nastavte frekvenci pořizování snímků konzistentních vzhledem k aplikacím v zásadě replikace na Vypnuto.

- Migrované servery musí obsahovat .NET Framework 3.5 s aktualizací Service Pack 1, aby služba Mobility fungovala.

- Pokud váš server obsahuje dynamické disky, možná si všimnete, že v určitých konfiguracích se tyto disky na serveru, u kterého došlo k převzetí služeb při selhání, označí jako offline nebo se zobrazí jako cizí disky. Také si můžete všimnout, že se stav zrcadlené sady u zrcadlených svazků napříč dynamickými disky označí jako Selhání redundance. Tento problém můžete vyřešit v nástroji diskmgmt.msc tím, že tyto disky ručně importujete a znovu je aktivujete.

- Migrované servery musí obsahovat ovladač vmstorfl.sys. Pokud na migrovaném serveru tento ovladač není, převzetí služeb při selhání může selhat. 
  > [!TIP]
  >Zkontrolujte, že se tento ovladač nachází v umístění C:\Windows\system32\drivers\vmstorfl.sys. Pokud se ovladač nenajde, můžete tento problém obejít tím, že ve stejném umístění vytvoříte fiktivní soubor. 
  >
  > Otevřete příkazový řádek (Spustit > cmd) a spusťte následující příkaz: copy nul c:\Windows\system32\drivers\vmstorfl.sys

- K serverům s 32bitovým operačním systémem Windows Server 2008 R2 se možná nebudete moct připojit pomocí protokolu RDP ihned poté, co u nich dojde k převzetí služeb při selhání nebo testovacímu převzetí služeb při selhání do Azure. Na webu Azure Portal restartujte virtuální počítač, u kterého došlo k převzetí služeb při selhání, a zkuste se připojit znovu. Pokud se pořád nemůžete připojit, zkontrolujte, jestli je server nakonfigurovaný tak, aby povoloval připojení ke vzdálené ploše, a ujistěte se, že připojení neblokují žádná pravidla brány firewall ani skupiny zabezpečení sítě. 
  > [!TIP]
  > Před migrací serverů se důrazně doporučuje provést testovací převzetí služeb při selhání. Ujistěte se, že jste provedli aspoň jeden úspěšné testovací převzetí služeb při selhání na každém serveru, kterou migrujete. V rámci testovacího převzetí služeb při selhání se připojte k počítači, u kterého došlo k testovacímu převzetí služeb při selhání, a zkontrolujte, že vše funguje podle očekávání.
  >
  >Operace testovacího převzetí služeb při selhání je nenarušující a pomáhá testovat migrace tím, že vytváří virtuální počítače v izolované síti podle vašeho výběru. Na rozdíl od operace převzetí služeb při selhání během operace testovacího převzetí služeb při selhání dál pokračuje replikace dat. Než budete připraveni na migraci, můžete provést tolik testovacích převzetí služeb při selhání, kolik budete chtít. 
  >
  >


## <a name="getting-started"></a>Začínáme

Provedením následujících úloh připravte předplatné Azure a VMware v místním prostředí nebo fyzické prostředí:

1. [Příprava Azure](tutorial-prepare-azure.md)
2. Příprava [VMware](vmware-azure-tutorial-prepare-on-premises.md) v místním prostředí


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Klikněte na **Vytvořit prostředek** > **Monitorování a správa** > **Backup a Site Recovery**.
3. Do pole **Název** zadejte popisný název **W2K8-migration**. Pokud máte více předplatných, vyberte příslušné předplatné.
4. Vytvořte skupinu prostředků **w2k8migrate**.
5. Zadejte oblast Azure. Informace o tom, které oblasti jsou podporované, najdete v části s geografickou dostupností v tématu s [podrobnostmi o cenách Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Pokud chcete mít možnost rychle se dostat k trezoru z řídicího panelu, klikněte na **Připnout na řídicí panel** a potom klikněte na **Vytvořit**.

   ![Nový trezor](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

Nový trezor se přidá do oblasti **Řídicí panel** v části **Všechny prostředky** a na hlavní stránku **Trezory Recovery Services**.


## <a name="prepare-your-on-premises-environment-for-migration"></a>Příprava místního prostředí na migraci

- Pokud chcete migrovat virtuální počítače s Windows Serverem 2008 spuštěné ve VMware, [nastavte místní konfigurační server ve VMware](vmware-azure-tutorial.md#set-up-the-source-environment).
- Pokud konfigurační server není možné nastavit jako virtuální počítač VMware, [nastavte konfigurační server na místním fyzickém serveru nebo virtuálním počítači](physical-azure-disaster-recovery.md#set-up-the-source-environment).

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. Klikněte na **Připravit infrastrukturu** > **Cíl** a vyberte předplatné Azure, které chcete použít.
2. Zadejte model nasazení Resource Manager.
3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.


## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

1. Novou zásadu replikace vytvoříte tak, že kliknete na **Infrastruktura Site Recovery** > **Zásady replikace** > **+ Zásada replikace**.
2. V části **Vytvoření zásad replikace** zadejte název zásady.
3. V části **Prahová hodnota cíle bodu obnovení** (RPO) zadejte omezení cíle bodu obnovení. Když cíl bodu obnovení replikace překročí tento limit, vygeneruje se upozornění.
4. V části **Uchování bodu obnovení** zadejte (v hodinách), jak dlouhý je interval uchovávání dat pro jednotlivé body obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu. Pro počítače replikované do úložiště úrovně Premium se podporuje uchování po dobu až 24 hodin. V případě úložiště úrovně Standard je to 72 hodin.
5. V části **Frekvence pořizování snímků konzistentních vzhledem k aplikacím** zadejte **Vypnuto**. Kliknutím na tlačítko **OK** vytvořte zásadu.

Tato zásada se automaticky přidruží ke konfiguračnímu serveru.

> [!WARNING]
> Ujistěte se, že v nastavení Frekvence pořizování snímků konzistentních vzhledem k aplikacím v zásadě replikace zadáte **VYPNUTO**. Při replikaci serverů s Windows Serverem 2008 se podporují pouze body obnovení konzistentní vzhledem k selháním. Zadání jakékoli jiné hodnoty u nastavení Frekvence pořizování snímků konzistentních vzhledem k aplikacím bude mít za následek generování falešných upozornění, protože stav replikace serveru bude kritický kvůli chybějícím bodům obnovení konzistentních vzhledem k aplikacím.

   ![Vytvoření zásady replikace](media/migrate-tutorial-windows-server-2008/create-policy.png)

## <a name="enable-replication"></a>Povolení replikace

[Povolte replikaci](physical-azure-disaster-recovery.md#enable-replication) pro server s Windows Serverem 2008 SP2 nebo Windows Serverem 2008 R2 SP1, který se má migrovat.
   
   ![Přidání fyzického serveru](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![Povolení replikace](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

## <a name="run-a-test-migration"></a>Spuštění testu migrace

Po dokončení počáteční replikace a změně stavu serveru na **Chráněno** můžete provést testovací převzetí služeb při selhání replikovaných serverů.

Spusťte [testovací převzetí služeb při selhání](tutorial-dr-drill-azure.md) do Azure a ujistěte se, že vše funguje podle očekávání.

   ![Testovací převzetí služeb při selhání](media/migrate-tutorial-windows-server-2008/testfailover.png)


## <a name="migrate-to-azure"></a>Migrace do Azure

Spusťte převzetí služeb při selhání pro počítače, které chcete migrovat.

1. V části **Nastavení** > **Replikované položky** klikněte na počítač a pak na **Převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Vyberte nejnovější bod obnovení.
3. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Služba Site Recovery se před aktivací převzetí služeb při selhání pokusí server vypnout. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
4. Zkontrolujte, že se virtuální počítač Azure zobrazuje v Azure podle očekávání.
5. V části **Replikované položky** klikněte pravým tlačítkem na virtuální počítač a klikněte na **Dokončit migraci**. To provede následující akce:

    - Proce migrace, zastaví se replikace virtuálního počítače AWS a zastaví se fakturace služby Site Recovery pro virtuální počítač.
    - Tento krok vyčištění dat replikace. Nedojde k odstranění migrovaných virtuálních počítačů.

   ![Dokončení migrace](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **Nepřerušujte v průběhu proces převzetí služeb při selhání:** Replikace virtuálního počítače se před spuštěním převzetí služeb při selhání zastaví. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.
