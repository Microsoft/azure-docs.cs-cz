---
title: Nastavení plánu zotavení po havárii pro virtuální počítače s Windows – Azure
description: Jak nastavit provozní kontinuitu a plán zotavení po havárii pro nasazení virtuálního počítače s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 18089bc00e9d02087acb149511fbc2c55077c153
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446923"
---
# <a name="set-up-a-business-continuity-and-disaster-recovery-plan"></a>Nastavte provozní kontinuitu a plán zotavení po havárii.

Aby se zajistila bezpečnost dat vaší organizace, možná budete muset přijmout strategii pro provozní kontinuitu a zotavení po havárii (BCDR). Strategie zvukové BCDR zajišťuje, aby vaše aplikace a úlohy běžely a běžely během plánovaných i neplánovaných služeb nebo výpadků Azure.

Virtuální počítač s Windows nabízí BCDR, aby služba Virtual Desktop systému Windows zachovala metadata zákazníků během výpadků. Pokud v oblasti dojde k výpadku, součásti infrastruktury služby převezmou sekundární umístění a budou fungovat normálně. Pořád máte přístup k metadatům souvisejícím s službami a uživatelé se pořád můžou připojit k dostupným hostitelům. Připojení koncových uživatelů zůstanou online, dokud nebude klientské prostředí nebo hostitelé dostupné.

Aby se uživatelé mohli i nadále připojovat během výpadku oblasti, je potřeba replikovat své virtuální počítače v jiném umístění. Během výpadků dojde při převzetí služeb při selhání primární lokality na replikované virtuální počítače v sekundárním umístění. Uživatelé můžou dál přistupovat k aplikacím ze sekundárního umístění bez přerušení. V případě replikace virtuálních počítačů budete muset zajistit, aby byly identity uživatelů přístupné v sekundárním umístění. Pokud používáte kontejnery profilů, budete je muset taky replikovat. Nakonec se ujistěte, že vaše obchodní aplikace, které spoléhají na data v primárním umístění, můžou převzít služby při selhání se zbytkem dat.

Aby bylo možné vytvářet souhrny uživatelů v průběhu výpadku, je třeba provést následující akce v tomto pořadí:

- Replikujte virtuální počítače do sekundárního umístění.
- Pokud používáte kontejnery profilů, nastavte pro replikaci dat v sekundárním umístění.
- Ujistěte se, že identity uživatelů, které jste nastavili v primárním umístění, jsou k dispozici v sekundárním umístění.
- Ujistěte se, že se při převzetí služeb při selhání do sekundárního umístění převezmou obchodní aplikace spoléhající na data v primárním umístění.

## <a name="vm-replication"></a>Replikace virtuálního počítače

Nejdřív budete muset virtuální počítače replikovat do sekundárního umístění. Vaše možnosti jsou závislé na tom, jak jsou vaše virtuální počítače nakonfigurované:

- Všechny virtuální počítače můžete nakonfigurovat pro fondy fondů i osobní hostitele pomocí Azure Site Recovery. V této metodě budete muset nastavit jenom jeden fond hostitelů a související skupiny a pracovní prostory aplikací.
- V oblasti převzetí služeb při selhání můžete vytvořit nový fond hostitelů a přitom ponechat všechny prostředky v umístění převzetí služeb při selhání vypnuté. V této metodě byste v oblasti převzetí služeb při selhání měli nastavit nové skupiny a pracovní prostory aplikací. Pak můžete použít plán Azure Site Recovery pro zapnutí fondů hostitelů.
- Můžete vytvořit fond hostitelů, který je vyplněný virtuálními počítači vytvořenými v primárních oblastech i v oblasti převzetí služeb při selhání, a přitom zachovat virtuální počítače v oblasti převzetí služeb při selhání. V takovém případě stačí nastavit jenom jeden fond hostitelů a jeho související skupiny a pracovní prostory aplikací. Pomocí této metody můžete použít plán Azure Site Recovery k napájení fondů hostitelů.

Pro správu replikačních virtuálních počítačů v jiných umístěních Azure doporučujeme použít [Azure Site Recovery](../site-recovery/site-recovery-overview.md) , jak je popsáno v tématu [Architektura zotavení po havárii z Azure do Azure](../site-recovery/azure-to-azure-architecture.md). Doporučujeme použít Azure Site Recovery pro fondy osobních hostitelů, protože Azure Site Recovery podporuje i [serverové SKU založené na serverech i na klientech](../site-recovery/azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

Pokud používáte Azure Site Recovery, nebudete muset tyto virtuální počítače registrovat ručně. Agent virtuálního počítače s Windows v sekundárním virtuálním počítači automaticky použije nejnovější token zabezpečení pro připojení k instanci služby, která je k ní nejbližší. Virtuální počítač (hostitel relace) v sekundárním umístění se automaticky stane součástí fondu hostitelů. Koncový uživatel bude muset během procesu znovu připojit, ale kromě toho neexistují žádné další ruční operace.

Pokud během výpadku existují připojení uživatele, před tím, než správce může spustit převzetí služeb při selhání sekundární oblastí, je nutné ukončit připojení uživatelů v aktuální oblasti.

Pokud chcete odpojit uživatele na virtuálním počítači s Windows (Classic), spusťte tuto rutinu:

```powershell
Invoke-RdsUserSessionLogoff
```

Pokud chcete odpojit uživatele v rámci verze virtuálního klienta Windows integrované s Azure, spusťte tuto rutinu:

```powershell
Remove-AzWvdUserSession
```

Po odhlášení všech uživatelů v primární oblasti můžete převzít služby virtuálních počítačů v primární oblasti a umožnit uživatelům, aby se připojili k virtuálním počítačům v sekundární oblasti. Další informace o tom, jak tento proces funguje, najdete v tématu [replikace virtuálních počítačů Azure do jiné oblasti Azure](../site-recovery/azure-to-azure-how-to-enable-replication.md).

## <a name="virtual-network"></a>Virtuální síť

V dalším kroku zvažte připojení k síti během výpadku. Musíte se ujistit, že jste v sekundární oblasti nastavili virtuální síť (VNET). Pokud uživatelé potřebují přístup k místním prostředkům, musíte tuto virtuální síť nakonfigurovat, aby k nim měli přístup. Můžete vytvářet místní připojení k síti VPN, ExpressRoute nebo virtuální síti WAN.

Doporučujeme použít Azure Site Recovery k nastavení virtuální sítě v oblasti převzetí služeb při selhání, protože zachovává nastavení primární sítě a nepotřebuje partnerské vztahy.

## <a name="user-identities"></a>Identity uživatelů

Dále zkontrolujte, zda je řadič domény k dispozici v sekundárním umístění. 

Existují tři způsoby, jak řadič domény udržovat k dispozici:

   - Mít kontrolér Doména služby Active Directory v sekundárním umístění
   - Použití místního řadiče Doména služby Active Directory
   - Replikace Doména služby Active Directory kontroleru pomocí [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md)

## <a name="user-and-app-data"></a>Data uživatelů a aplikací

Pokud používáte kontejnery profilu, je dalším krokem nastavení replikace dat v sekundárním umístění. Máte pět možností pro uložení profilů FSLogix:

   - Prostory úložiště s přímým přístupem (S2D)
   - Síťové jednotky (virtuální počítač s dalšími jednotkami)
   - Azure Files
   - Azure NetApp Files
   - Mezipaměť cloudu pro replikaci

Další informace najdete [v možnostech úložiště pro kontejnery profilů FSLogix ve virtuálním počítači s Windows](store-fslogix-profile.md).

Pokud nastavujete zotavení po havárii pro profily, máte tyto možnosti:

   - Nastavte nativní replikaci Azure (například replikace účtu úložiště Azure Storage úrovně Standard, replikace Azure NetApp Files, nebo Azure Files Sync pro souborové servery).
    
     >[!NOTE]
     >Replikace NetApp se po prvním nastavení automaticky nastaví. Pomocí plánů Azure Site Recovery můžete přidat předzálohovací skripty a následné skripty pro převzetí služeb při selhání prostředky nevirtuálními počítači, které replikují Azure Storage prostředky.

   - Nastavte cloudovou mezipaměť FSLogix pro data aplikace i uživatele.
   - Nastavte zotavení po havárii jenom pro data aplikací, abyste zajistili nepřetržitý přístup k důležitým podnikovým datům. Pomocí této metody můžete načíst data uživatelů po výpadku.

Pojďme se podívat, jak nakonfigurovat FSLogix pro nastavení zotavení po havárii pro jednotlivé možnosti.

### <a name="fslogix-configuration"></a>Konfigurace FSLogix

Agent FSLogix může podporovat více umístění profilů, pokud nakonfigurujete položky registru pro FSLogix.

Konfigurace položek registru:

1. Otevřete **Editor registru**.
2. Přejít na **profily počítače**  >  **HKEY_LOCAL_MACHINE**  >  **software**  >  **FSLogix**  >  .
   
     > [!div class="mx-imgBorder"]
     > ![Snímek obrazovky okna profily v editoru registru. Je vybraná možnost VHDLocation.](media/regedit-profiles.png)

3. Klikněte pravým tlačítkem na **VHDLocations** a vyberte **Upravit více řetězců**.

     > [!div class="mx-imgBorder"]
     > ![Snímek obrazovky s oknem úprav víceřádkového řetězce V údajích hodnoty jsou uvedena umístění Centrual USA a Východní USA.](media/multi-string-edit.png)

4. Do pole **data hodnoty** zadejte umístění, která chcete použít.
5. Až to bude hotové, vyberte **OK**.

Pokud není první umístění k dispozici, agent FSLogix se automaticky převezme k druhému a tak dále.

Doporučujeme nakonfigurovat agenta FSLogix s cestou k sekundárnímu umístění v hlavní oblasti. Po vypnutí primárního umístění se agent FLogix replikuje jako součást replikace virtuálního počítače Azure Site Recovery. Jakmile budou replikované virtuální počítače připravené, Agent se automaticky pokusí o cestu k sekundární oblasti.

Řekněme například, že virtuální počítače hostitele vaší primární relace jsou v oblasti Střed USA, ale váš kontejner profilu je v oblasti Střed USA z hlediska výkonu.

V takovém případě byste měli agenta FSLogix nakonfigurovat s cestou k úložišti v Střed USA. Nakonfigurujete virtuální počítače hostitele relace tak, aby se replikoval v Západní USA. Jakmile cesta k Střed USA selže, pokusí se agent místo toho vytvořit novou cestu k úložišti v Západní USA.

### <a name="s2d"></a>S2D

Vzhledem k tomu, že S2D zpracovává replikaci mezi oblastmi interně, nemusíte ručně nastavit sekundární cestu.

### <a name="network-drives-vm-with-extra-drives"></a>Síťové jednotky (virtuální počítač s dalšími jednotkami)

Pokud replikují virtuální počítače síťového úložiště pomocí Azure Site Recovery, jako jsou třeba virtuální počítače hostitele relace, pak obnovení zachová stejnou cestu, což znamená, že nemusíte překonfigurovat FSlogix.

### <a name="azure-files"></a>Azure Files

Služba soubory Azure podporuje asynchronní replikaci mezi jednotlivými oblastmi, kterou můžete zadat při vytváření účtu úložiště. Pokud již asynchronní povaha souborů Azure pokrývá vaše cíle zotavení po havárii, nemusíte provádět žádnou další konfiguraci.

Pokud potřebujete synchronní replikaci pro minimalizaci ztráty dat, doporučujeme místo toho použít cloudovou mezipaměť FSLogix.

>[!NOTE]
>Tato část nezahrnuje mechanismus ověřování převzetí služeb při selhání pro soubory Azure.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Přečtěte si další informace o Azure NetApp Files v [vytváření partnerských vztahů replikace pro Azure NetApp Files](../azure-netapp-files/cross-region-replication-create-peering.md).

## <a name="app-dependencies"></a>Závislosti aplikací

Nakonec se ujistěte, že všechny obchodní aplikace, které spoléhají na data umístěná v primární oblasti, můžou převzít služby při selhání do sekundárního umístění. Nezapomeňte také nakonfigurovat nastavení, které aplikace potřebují pro práci v novém umístění. Například pokud jedna z aplikací závisí na back-endu SQL, nezapomeňte replikovat SQL do sekundárního umístění. Aplikaci byste měli nakonfigurovat tak, aby používala sekundární umístění jako součást procesu převzetí služeb při selhání nebo jako výchozí konfiguraci. Můžete modelovat závislosti aplikací v Azure Site Recoverych plánech. Další informace najdete v tématu [o plánech obnovení](../site-recovery/recovery-plan-overview.md).

## <a name="disaster-recovery-testing"></a>Testování zotavení po havárii

Po dokončení nastavení zotavení po havárii budete chtít svůj plán otestovat, abyste se ujistili, že funguje.

Tady jsou některé návrhy, jak testovat váš plán:

- Pokud testovací virtuální počítače mají přístup k Internetu, převezmou všechny existující hostitele relace pro nová připojení, ale všechna existující připojení k původnímu hostiteli relace zůstanou aktivní. Ujistěte se, že správce, který spouští test, před testováním plánu odhlásí všechny aktivní uživatele. 
- Během časového období údržby byste měli provádět pouze úplné testy zotavení po havárii, aby nedošlo k přerušení vašich uživatelů. Pro tento test můžete použít také fond hostitelů v ověřovacím prostředí. 
- Ujistěte se, že se váš test zabývá všemi důležitými firemními aplikacemi.
- Pro převzetí služeb při selhání vám doporučujeme jenom 100 virtuálních počítačů. Pokud máte více virtuálních počítačů, doporučujeme, abyste je nastavili v dávkách po dobu 10 minut od jejich selhání.

## <a name="next-steps"></a>Další kroky

Pokud máte dotazy týkající se zabezpečení dat kromě plánování výpadků, Projděte si naši [příručku zabezpečení](security-guide.md).