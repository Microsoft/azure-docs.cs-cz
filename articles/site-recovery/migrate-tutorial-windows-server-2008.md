---
title: Migrace na místních serverech systému Windows Server 2008 do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak migrovat místní Windows Server 2008 počítače do Azure pomocí Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/23/2018
ms.author: bsiva
ms.openlocfilehash: 552a0d131f630db7b3a73293d330377ee350d2a9
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214614"
---
# <a name="migrate-servers-running-windows-server-2008-2008-r2-to-azure"></a>Migrace serverů s Windows serverem 2008, 2008 R2 do Azure

V tomto kurzu se dozvíte jak provést migraci místních serverů se systémem Windows Server 2008 nebo 2008 R2 do Azure pomocí Azure Site Recovery. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Příprava místní prostředí pro migraci
> * Nastavení cílového prostředí
> * Nastavení zásady replikace
> * Povolení replikace
> * Spuštění testu migrace za účelem ověření, že vše funguje podle očekávání
> * Převzetí služeb při selhání do Azure a dokončit migraci

Omezení a známé problémy části seznamů, omezení a alternativní řešení známých problémů, které se může dojít při migraci Windows serveru 2008 počítačů do Azure. 


## <a name="supported-operating-systems-and-environments"></a>Podporované operační systémy a prostředí


|Operační systém  | V místním prostředí  |
|---------|---------|
|Windows Server 2008 SP2 - 32 a 64-bit (IA-32 a x86 – x 64)</br>-Standard</br>-Enterprise</br>-Datacenter   |     Virtuální počítače VMware, virtuálních počítačů Hyper-V a fyzické servery    |
|Windows Server 2008 R2 SP1 – 64 bitů</br>-Standard</br>-Enterprise</br>-Datacenter     |     Virtuální počítače VMware, virtuálních počítačů Hyper-V a fyzické servery|

> [!WARNING]
> - Migrace serverů se systémem Server Core není podporována.
> - Ujistěte se, že máte nejnovější aktualizaci service pack a aktualizace Windows nainstalované před migrací.


## <a name="prerequisites"></a>Požadavky

Než začnete, je vhodné zkontrolovat architekturu Azure Site Recovery pro [VMware a fyzické migrace serveru](vmware-azure-architecture.md) nebo [migrace virtuálních počítačů Hyper-V](hyper-v-azure-architecture.md) 

Pokud chcete migrovat virtuální počítače Hyper-V systémem Windows Server 2008 nebo Windows Server 2008 R2, postupujte podle kroků v [migrace místních počítačů do Azure](migrate-tutorial-on-premises-azure.md) kurzu.

Zbývající části tohoto kurzu se dozvíte, jak můžete migrovat virtuální počítače v místním VMware a fyzických serverů s Windows serverem 2008 nebo 2008 R2.


## <a name="limitations-and-known-issues"></a>Omezení a známé problémy

- Konfigurace serveru, dalších procesových serverů a služba mobility se používá k migraci serverů Windows Server 2008 SP2 by měl běžet 9.18.0.1 verzi softwaru Azure Site Recovery. Sjednocené instalace pro verzi 9.18.0.1 konfigurační Server a procesový server si můžete stáhnout z [ https://aka.ms/asr-w2k8-migration-setup ](https://aka.ms/asr-w2k8-migration-setup).

- K existující konfigurační Server nebo procesový server nelze použít k migraci serverů se systémem Windows Server 2008 SP2. Nová konfigurační Server by mělo proběhnout zřízení 9.18.0.1 verze softwaru Azure Site Recovery. Tato konfigurační Server by měla sloužit pouze k migraci serverů Windows do Azure.

- Body obnovení konzistentní vzhledem k aplikaci a funkci konzistence více virtuálních počítačů nejsou podporovány pro replikaci serverů se systémem Windows Server 2008 SP2. Servery Windows Server 2008 SP2, by se měly migrovat do bodu obnovení s konzistentní při selhání. Body obnovení konzistentní vzhledem k selhání jsou ve výchozím nastavení generovány každých 5 minut. Použití zásady replikace s frekvencí snímek konzistentní vzhledem k nakonfigurované aplikaci způsobí, že stav replikace, chcete-li důležité z důvodu nedostatku body obnovení konzistentní vzhledem k aplikaci. Abyste zabránili falešně pozitivních výsledků, nastavte frekvenci snímků konzistentních s aplikací v zásadách replikace na "Off".

- Migrované servery by měl mít rozhraní .NET Framework 3.5 Service Pack 1 u služby mobility pro práci.


- Pokud váš server má dynamické disky, může dojít při některých konfiguracích, které tyto disky na se selhání serveru jsou označeny v režimu offline nebo zobrazena jako cizí disky. Můžete také všimnout, zrcadlené nastavit stav pro zrcadlených svazků dynamických disků je označen "Se nezdařilo redundance". Tento problém z diskmgmt.msc můžete vyřešit ručním importu těchto disků a opětovná aktivace je.

- Servery migruje by měly mít vmstorfl.sys ovladače. Převzetí služeb při selhání může selhat, pokud ovladač není k dispozici na serveru migraci. 
  > [!TIP]
  >Zkontrolujte, jestli je ovladač k dispozici v "C:\Windows\system32\drivers\vmstorfl.sys". Pokud ovladač není nalezen, můžete tento problém obejdete tak, že vytvoříte fiktivní soubor na místě. 
  >
  > Otevřít příkazový řádek (Spustit > cmd) a spusťte následující příkaz: "zkopírujte nul c:\Windows\system32\drivers\vmstorfl.sys"

- Jste možná nebudete moct RDP na Windows Server 2008 SP2 servery se systémem 32bitová verze operačního systému hned při selhání nebo testovací převzetí služeb při selhání do Azure. Restartování se přes virtuální počítač z portálu Azure portal a zkuste se znovu připojit. Pokud se pořád nemůžete připojit, zkontrolujte, pokud je server nakonfigurovaný pro povolení připojení ke vzdálené ploše a ujistěte se, že neexistují žádná pravidla brány firewall nebo skupiny zabezpečení sítě blokuje připojení. 
  > [!TIP]
  > Testovací převzetí služeb se důrazně doporučuje před migrací servery. Ujistěte se, že jste provedli aspoň jednu úspěšné testovací převzetí služeb při selhání na každém serveru, kterou migrujete. Jako součást testovacího převzetí služeb při připojení k testovací převzetí služeb počítače při selhání a zajistit co fungují podle očekávání.
  >
  >Operace převzetí služeb při selhání testu je nenarušující a pomůže vám testovací migrace tím, že vytvoříte virtuální počítače v izolované síti podle vašeho výběru. Na rozdíl od operaci převzetí služeb při selhání, během operace převzetí služeb při selhání testu replikace dat nadále progres. Můžete provádět tolik testovacích převzetí služeb při selhání, jak vám vyhovuje, než se budete připravení migrovat. 
  >
  >


## <a name="getting-started"></a>Začínáme

Proveďte následující úkoly pro přípravu Azure předplatného a v místním prostředí VMware/fyzických prostředků:

1. [Příprava Azure](tutorial-prepare-azure.md)
2. Příprava místních [VMware](vmware-azure-tutorial-prepare-on-premises.md)


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Klikněte na **Vytvořit prostředek** > **Monitorování a správa** > **Backup a Site Recovery**.
3. V **název**, zadejte popisný název **W2K8 migrace**. Pokud máte více předplatných, vyberte příslušné předplatné.
4. Vytvořte skupinu prostředků **w2k8migrate**.
5. Zadejte oblast Azure. Informace o tom, které oblasti jsou podporované, najdete v části s geografickou dostupností v tématu s [podrobnostmi o cenách Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Pokud chcete mít možnost rychle se dostat k trezoru z řídicího panelu, klikněte na **Připnout na řídicí panel** a potom klikněte na **Vytvořit**.

   ![Nový trezor](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

Nový trezor se přidá do oblasti **Řídicí panel** v části **Všechny prostředky** a na hlavní stránku **Trezory Recovery Services**.


## <a name="prepare-your-on-premises-environment-for-migration"></a>Příprava místní prostředí pro migraci

- Stažení instalačního programu konfiguračního serveru (sjednocené instalace) z [https://aka.ms/asr-w2k8-migration-setup](https://aka.ms/asr-w2k8-migration-setup)
- Postupujte podle kroků uvedených níže pro nastavení zdrojového prostředí pomocí instalačního souboru si stáhli v předchozím kroku.

> [!IMPORTANT]
> - Ujistěte se, že pomocí souboru instalačního programu si stáhli v prvním kroku požadavků instalace a registrace konfiguračního serveru. Nestahovat instalační soubor na webu Azure Portal. K dispozici na instalační soubor [ https://aka.ms/asr-w2k8-migration-setup ](https://aka.ms/asr-w2k8-migration-setup) je jedinou verzí, který podporuje migraci Windows serveru 2008.
>
> - Existující konfigurační Server nemůžete použít k migraci počítačů s Windows serverem 2008. Budete muset nastavit nové konfigurační Server pomocí výše uvedeného odkazu.
>
> - Postupujte podle kroků níže uvedené instalaci konfiguračního serveru. Nepokoušejte se použít postup instalace grafického uživatelského rozhraní na základě jednotný instalační program aplikace přímo. To způsobí pokus o instalaci, selháním kvůli nesprávné chybová zpráva, že neexistuje žádné připojení k Internetu.

 
1) Stažení souboru s přihlašovacími údaji z portálu: na webu Azure portal, vyberte trezor služby Recovery Services vytvořené v předchozím kroku. V nabídce na stránce úložiště vyberte **infrastruktura Site Recovery** > **konfigurační servery**. Pak klikněte na tlačítko **+ Server**. Vyberte *konfigurační Server pro fyzický počítač* z rozevírací nabídky formuláře na stránce, které se otevře. Klikněte na tlačítko Stáhnout v kroku 4 ke stažení souboru s přihlašovacími údaji.

 ![Stáhněte si registrační klíč trezoru](media/migrate-tutorial-windows-server-2008/download-vault-credentials.png) 

2) Kopírování souboru s přihlašovacími údaji si stáhli v předchozím kroku a soubor sjednocené instalace předtím stáhli na ploše počítače konfiguračního serveru (Windows Server 2012 R2 nebo Windows Server 2016 počítače, na kterém chcete nainstalovat Konfigurace serverového softwaru.)

3) Ujistěte se, že konfigurační Server má připojení k Internetu a zda jsou správně nakonfigurovány systémových hodin a nastavení časového pásma v počítači. Stáhněte si [MySQL 5.7](https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi) instalačního programu a umístěte ho na *C:\Temp\ASRSetup* (pokud neexistuje, vytvořte adresář.) 

4) Vytvořte soubor s přihlašovacími údaji MySQL s následujícími řádky a umístěte ho na ploše na **C:\Users\Administrator\MySQLCreds.txt** . Nahraďte "heslo ~ 1" pod vhodné a silné heslo:

```
[MySQLCredentials]
MySQLRootPassword = "Password~1"
MySQLUserPassword = "Password~1"
```

5) Extrahujte obsah stažený jednotný instalační soubor na plochu spuštěním následujícího příkazu:

```
cd C:\Users\Administrator\Desktop

MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Users\Administrator\Desktop\9.18
```
  
6) Instalace serverového softwaru konfigurace pomocí extrahované obsah spuštěním následujících příkazů:

```
cd C:\Users\Administrator\Desktop\9.18.1

UnifiedSetup.exe /AcceptThirdpartyEULA /ServerMode CS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /MySQLCredsFilePath "C:\Users\Administrator\Desktop\MySQLCreds.txt" /VaultCredsFilePath <vault credentials file path> /EnvType VMWare /SkipSpaceCheck
```

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. Klikněte na **Připravit infrastrukturu** > **Cíl** a vyberte předplatné Azure, které chcete použít.
2. Zadejte model nasazení Resource Manager.
3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.


## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

1. Chcete-li vytvořit novou zásadu replikace, klikněte na tlačítko **infrastruktura Site Recovery** > **zásady replikace** > **+ zásada replikace**.
2. V **vytvoření zásady replikace**, zadejte název zásady.
3. V **prahová hodnota cíle bodu obnovení**, zadejte cíle (RPO) limit bodů obnovení. Pokud cíl bodu obnovení replikace překročí tento limit, je vygenerována výstraha.
4. V **uchování bodu obnovení**, určete, jak dlouho (v hodinách) je interval uchovávání dat pro jednotlivé body obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu. Je pro počítače replikované do služby premium storage a 72 hodin pro úložiště úrovně standard podporuje až uchování 24 hodin.
5. V **frekvence snímků konzistentní vzhledem k**, zadejte **vypnout**. Kliknutím na tlačítko **OK** vytvořte zásadu.

Tato zásada se automaticky přidruží ke konfiguračnímu serveru.

> [!WARNING]
> Ujistěte se, že jste určili **OFF** v aplikaci konzistentních snímku nastavení četnosti replikace zásad. Při replikaci serverů se systémem Windows Server 2008 jsou podporovány pouze body obnovení konzistentní při selhání. Providers, budou jakákoli jiná hodnota frekvence snímků konzistentní vzhledem k zapnutí stav replikace serveru kritické z důvodu nedostatku bodů obnovení konzistentní vzhledem k vést falešným výstrahám.

   ![Vytvoření zásady replikace](media/migrate-tutorial-windows-server-2008/create-policy.png)

## <a name="enable-replication"></a>Povolení replikace

[Povolení replikace](physical-azure-disaster-recovery.md#enable-replication) systému Windows Server 2008 SP2 nebo serveru systému Windows Server 2008 R2 SP1 k migraci.
   
   ![Přidat fyzický server](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![Povolení replikace](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

## <a name="run-a-test-migration"></a>Spuštění testu migrace

Můžete provádět převzetí služeb při selhání testu po dokončení počáteční replikace a stav serveru změní replikaci serverů **chráněné**.

Spusťte [testovací převzetí služeb při selhání](tutorial-dr-drill-azure.md) do Azure a ujistěte se, že vše funguje podle očekávání.

   ![Testovací převzetí služeb při selhání](media/migrate-tutorial-windows-server-2008/testfailover.png)


## <a name="migrate-to-azure"></a>Migrace do Azure

Spusťte převzetí služeb při selhání pro počítače, které chcete migrovat.

1. V části **Nastavení** > **Replikované položky** klikněte na počítač a pak na **Převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Vyberte nejnovější bod obnovení.
3. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Site Recovery se pokusí o vypněte server. před aktivací převzetí služeb. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
4. Zkontrolujte, že se virtuální počítač Azure zobrazuje v Azure podle očekávání.
5. V části **Replikované položky** klikněte pravým tlačítkem na virtuální počítač a klikněte na **Dokončit migraci**. Tím se dokončí proce migrace, zastaví se replikace virtuálního počítače a zastaví se fakturace služby Site Recovery pro daný virtuální počítač.

   ![Dokončení migrace](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **Nepřerušujte v průběhu proces převzetí služeb při selhání:** Replikace virtuálního počítače se před spuštěním převzetí služeb při selhání zastaví. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.
