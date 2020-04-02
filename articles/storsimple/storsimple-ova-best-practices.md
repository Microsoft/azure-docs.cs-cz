---
title: Doporučené postupy pro virtuální pole StorSimple | Dokumenty společnosti Microsoft
description: Popisuje osvědčené postupy pro nasazení a správu virtuálního pole StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 82608c98fc8ea15167b690547906c2238b1b3c04
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80544329"
---
# <a name="storsimple-virtual-array-best-practices"></a>Osvědčené postupy pro StorSimple Virtual Array

## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Microsoft Azure StorSimple Virtual Array je integrované úložné řešení, které spravuje úlohy úložiště mezi místním virtuálním zařízením běžícím v hypervisoru a cloudovým úložištěm Microsoft Azure. StorSimple Virtual Array je efektivní a nákladově efektivní alternativa k fyzickému poli řady 8000. Virtuální pole lze spustit na vaší stávající infrastruktuře hypervisoru, podporuje protokoly iSCSI i SMB a je vhodné pro scénáře vzdálené kanceláře nebo pobočky. Další informace o řešeních StorSimple naleznete v [microsoft azure storsimple přehled](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Tento článek popisuje osvědčené postupy implementované během počátečního nastavení, nasazení a správy virtuálního pole StorSimple. Tyto osvědčené postupy poskytují ověřené pokyny pro nastavení a správu virtuálního pole. Tento článek je zaměřen na správce IT, kteří nasazují a spravují virtuální pole ve svých datových centrech.

Doporučujeme pravidelnou kontrolu osvědčených postupů, které vám pomohou zajistit, aby vaše zařízení stále dodržovalo, když jsou provedeny změny v nastavení nebo provozním toku. Pokud při implementaci těchto doporučených postupů ve virtuálním poli narazíte na nějaké problémy, požádejte o pomoc [podporu společnosti Microsoft.](storsimple-virtual-array-log-support-ticket.md)

## <a name="configuration-best-practices"></a>Doporučené postupy konfigurace
Tyto osvědčené postupy zahrnují pokyny, které je třeba dodržovat při počátečním nastavení a nasazení virtuálních polí. Mezi tyto osvědčené postupy patří postupy související se zřizováním virtuálního počítače, nastavením zásad skupiny, změnou velikosti, nastavením sítě, konfigurací účtů úložiště a vytvářením sdílených složek a svazků pro virtuální pole. 

### <a name="provisioning"></a>Zřizování
StorSimple Virtual Array je virtuální počítač (VM) zřízená na hypervisoru (Hyper-V nebo VMware) hostitelského serveru. Při zřizování virtuálního počítače se ujistěte, že váš hostitel je schopen vyhradit dostatečné prostředky. Další informace naleznete na [minimální požadavky na prostředky](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) zřídit pole.

Při zřizování virtuálního pole implementujte následující osvědčené postupy:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Typ virtuálního počítače** |**Generace 2** Virtuální ho virtuálního připojení pro použití se systémem Windows Server 2012 nebo novějším a bitovou *kopii .vhdx.* <br></br> **Generace 1** Virtuální ho virtuálního připojení pro použití se systémem Windows Server 2008 nebo novějším a s bitovou *kopií .vhd.* |Při použití bitové *kopie .vmdk* použijte virtuální počítač verze 8. |
| **Typ paměti** |Konfigurace jako **statická paměť**. <br></br> Nepoužívejte možnost **dynamické paměti.** | |
| **Typ datového disku** |Ustanovení jako **dynamicky se rozšiřující**.<br></br> **Pevná velikost** trvá dlouhou dobu. <br></br> Nepoužívejte možnost **differencing.** |Použijte možnost **tenkého zřizování.** |
| **Modifikace datového disku** |Rozšíření nebo zmenšení není povoleno. Pokus o to má za následek ztrátu všech místních dat v zařízení. |Rozšíření nebo zmenšení není povoleno. Pokus o to má za následek ztrátu všech místních dat v zařízení. |

### <a name="sizing"></a>Velikosti
Při dimenzování virtuálního pole StorSimple zvažte následující faktory:

* Místní rezervace svazků nebo sdílených složek. Přibližně 12 % místa je vyhrazeno na místní úrovni pro každý zřízený vrstvený svazek nebo sdílenou složku. Zhruba 10 % místa je také vyhrazeno pro místně vázaný svazek pro systém souborů.
* Režie snímku. Zhruba 15 % místa na místní úrovni je vyhrazeno pro snímky.
* Potřeba obnovení. Pokud provádíte obnovení jako novou operaci, velikost by měla odpovídat za prostor potřebný pro obnovení. Obnovení se provádí do sdílené složky nebo svazku stejné velikosti.
* Některé vyrovnávací paměť by měla být přidělena pro všechny neočekávané růstu.

Na základě předchozích faktorů mohou být požadavky na velikost reprezentovány následující rovnicí:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Následující příklady ilustrují, jak můžete velikost virtuální pole na základě vašich požadavků.

#### <a name="example-1"></a>Příklad 1:
Na virtuálním poli chcete mít možnost

* zřizování odstupňovaného svazku nebo sdílené položky o velikosti 2 TB.
* zřizování vrstveného svazku nebo sdílené položky o velikosti 1 TB.
* 300 GB místně vázanýsvazek nebo sdílené složky.

Pro předchozí svazky nebo sdílené složky, pojďme vypočítat požadavky na místo na místní úrovni.

Za prvé, pro každý vrstvený objem nebo sdílenou složku by se místní rezervace rovnala 12 % velikosti svazku/sdílené položky. Pro místně vázaný svazek/sdílené složky je místní rezervace 10 % místně vázaný objem/sdílené složky (kromě zřízeného velikosti). V tomto příkladu je třeba

* Místní rezervace 240 GB (pro vrstvený svazek/sdílenou složku o velikosti 2 TB)
* Místní rezervace 120 GB (pro vrstvený svazek/sdílenou složku o velikosti 1 TB)
* 330 GB pro místně vázaný svazek nebo sdílenou složku (přidání 10 % místní rezervace do 300 GB rezervované velikosti)

Celkové místo potřebné na místní úrovni je zatím: 240 GB + 120 GB + 330 GB = 690 GB.

Za druhé, potřebujeme alespoň tolik místa na místní úrovni jako největší jednotlivá rezervace. Tato další částka se používá v případě, že potřebujete obnovit z snímku cloudu. V tomto příkladu je největší místní rezervace 330 GB (včetně rezervace pro systém souborů), takže byste ji přidali k 690 GB: 690 GB + 330 GB = 1020 GB.
Pokud jsme provedli další další obnovení, můžeme vždy uvolnit místo z předchozí operace obnovení.

Za třetí, potřebujeme 15 % vašeho celkového místního prostoru, abychom mohli ukládat místní snímky, takže je k dispozici pouze 85 % z něj. V tomto příkladu by to bylo kolem 1020 GB = 0,85&ast;zřízeného datového disku TB. Takže zřízený datový disk by byl (1020&ast;(1/0.85))= 1200 GB = 1,20 TB ~ 1,25 TB (zaokrouhlování na nejbližší kvartil)

Faktoring v neočekávaném růstu a nových obnoveních byste měli zřídit místní disk kolem 1,25 - 1,5 TB.

> [!NOTE]
> Doporučujeme také, aby místní disk je tence zřízena. Toto doporučení je, protože místo obnovení je potřeba pouze v případě, že chcete obnovit data, která je starší než pět dní. Obnovení na úrovni položky umožňuje obnovit data za posledních pět dní bez nutnosti další prostor pro obnovení.


#### <a name="example-2"></a>Příklad 2:
Na virtuálním poli chcete mít možnost

* zřízení odstupňovaného svazku o velikosti 2 TB
* zřízení místně připnutého svazku o velikosti 300 GB

Na základě 12 % místní rezervace prostoru pro vrstvené objemy/akcie a 10 % pro místně vázanýobjemy/akcie potřebujeme

* Místní rezervace 240 GB (pro 2 TB vrstvený svazek/sdílená složky)
* 330 GB pro místně vázaný svazek nebo sdílenou složku (přidání 10 % místní rezervace do 300 GB zřízeného místa)

Celkové požadované místo na místní úrovni je: 240 GB + 330 GB = 570 GB

Minimální místní místo potřebné pro obnovení je 330 GB.

15 % z celkového počtu disků slouží k ukládání snímků tak, aby bylo k dispozici pouze 0,85. Takže velikost disku je (900&ast;(1/0.85)) = 1.06 TB ~ 1.25 TB (zaokrouhlení na nejbližší kvartil)

Faktoring v každém neočekávaném růstu, můžete zřídit 1,25 - 1,5 TB místní disk.

### <a name="group-policy"></a>Zásady skupiny
Zásady skupiny je infrastruktura, která umožňuje implementovat konkrétní konfigurace pro uživatele a počítače. Nastavení zásad skupiny jsou obsažena v objektech zásad skupiny (GPO), které jsou propojeny s následujícími kontejnery služby AD DS (Active Directory Domain Services): sítě, domény nebo organizační jednotky (OU). 

Pokud je vaše virtuální pole připojeno k doméně, lze na něj použít objekty zásad skupiny. Tyto objekty Zásad skupiny mohou instalovat aplikace, jako je například antivirový software, který může nepříznivě ovlivnit provoz virtuálního pole StorSimple.

Proto doporučujeme:

* Ujistěte se, že vaše virtuální pole je ve vlastní organizační jednotce (OU) pro službu Active Directory.
* Ujistěte se, že žádné objekty zásad skupiny (GPO) jsou použity na virtuální pole. Můžete blokovat dědičnost a zajistit tak, aby virtuální pole (podřízený uzel) automaticky nedědí žádné objekty zásad skupiny z nadřazeného objektu. Další informace naleznete v [blokování dědičnosti](https://technet.microsoft.com/library/cc731076.aspx).

### <a name="networking"></a>Sítě
Konfigurace sítě pro virtuální pole se provádí prostřednictvím místního webového uživatelského prostředí. Rozhraní virtuální sítě je povoleno prostřednictvím hypervisoru, ve kterém je zřízeno virtuální pole. Stránka [Nastavení sítě](storsimple-virtual-array-deploy3-fs-setup.md) slouží ke konfiguraci adresy IP, podsítě a brány virtuálního síťového rozhraní.  Můžete také nakonfigurovat primární a sekundární server DNS, nastavení času a volitelná nastavení proxy serveru pro vaše zařízení. Většina konfigurace sítě je jednorázové nastavení. Před nasazením virtuálního pole zkontrolujte [požadavky na síť StorSimple.](storsimple-ova-system-requirements.md#networking-requirements)

Při nasazování virtuálního pole doporučujeme dodržovat tyto doporučené postupy:

* Ujistěte se, že síť, ve které je virtuální pole nasazeno, má vždy kapacitu pro vyhradit šířku pásma internetu 5 Mb/s (nebo více).
  
  * Potřeba šířky pásma internetu se liší v závislosti na charakteristikách pracovního vytížení a rychlosti změny dat.
  * Změna dat, která může být zpracována, je přímo úměrná šířce pásma Internetu. Jako příklad při zálohování může šířka pásma 5 Mb/s pojmout změnu dat přibližně o 18 GB za 8 hodin. Díky čtyřnásobné větší šířce pásma (20 Mb/s) zvládnete čtyřikrát více změn dat (72 GB).
* Ujistěte se, že připojení k Internetu je vždy k dispozici. Sporadické nebo nespolehlivé připojení k Internetu k zařízením může mít za následek ztrátu přístupu k datům v cloudu a může mít za následek nepodporovanou konfiguraci.
* Pokud plánujete nasadit zařízení jako server iSCSI:
  
  * Doporučujeme zakázat možnost **Získat IP adresu automaticky** (DHCP).
  * Konfigurace statických adres IP. Je nutné nakonfigurovat primární a sekundární server DNS.
  * Pokud definujete více síťových rozhraní ve virtuálním poli, může se do cloudu dostat pouze první síťové rozhraní (ve výchozím nastavení je toto rozhraní **Ethernet).** Chcete-li řídit typ provozu, můžete vytvořit více virtuálních síťových rozhraní ve virtuálním poli (nakonfigurovaném jako server iSCSI) a připojit tato rozhraní k různým podsítím.
* Chcete-li omezit šířku pásma cloudu pouze (používá virtuální pole), nakonfigurujte omezení na směrovači nebo bráně firewall. Pokud definujete omezení v hypervisoru, omezí všechny protokoly včetně iSCSI a SMB namísto pouze šířky pásma cloudu.
* Ujistěte se, že je povolena časová synchronizace hypervisorů. Pokud používáte technologii Hyper-V, vyberte virtuální pole ve Správci technologie Hyper-V, přejděte na **službu Integrace nastavení &gt; **a zkontrolujte, zda je kontrolována **synchronizace času.**

### <a name="storage-accounts"></a>Účty úložiště
StorSimple Virtual Array může být přidruženka k jednomu účtu úložiště. Tento účet úložiště může být automaticky generovaný účet úložiště, účet ve stejném předplatném jako služba nebo účet úložiště související s jiným předplatným. Další informace naleznete v tématu [správa účtů úložiště pro virtuální pole](storsimple-virtual-array-manage-storage-accounts.md).

Použijte následující doporučení pro účty úložiště přidružené k virtuálnímu poli.

* Při propojování více virtuálních polí s jedním účtem úložiště faktor v maximální kapacitu (64 TB) pro virtuální pole a maximální velikost (500 TB) pro účet úložiště. To omezuje počet virtuálních polí plné velikosti, které mohou být přidruženy k tomuto účtu úložiště asi 7.
* Při vytváření nového účtu úložiště
  
  * Doporučujeme jej vytvořit v oblasti, která je nejblíže vzdálené pobočce, kde je vaše virtuální pole StorSimple nasazeno, aby se minimalizovala latence.
  * Mějte na paměti, že účet úložiště nelze přesunout v různých oblastech. Také nelze přesunout službu mezi předplatnými.
  * Použijte účet úložiště, který implementuje redundanci mezi datovými centry. Geograficky redundantní úložiště (GRS), zónově redundantní úložiště (ZRS) a místně redundantní úložiště (LRS) jsou podporovány pro použití s virtuálním polem. Další informace o různých typech účtů úložiště najdete v [části Replikace úložiště Azure](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Podíly a objemy
Pro vaše virtuální pole StorSimple můžete zřídit sdílené složky, pokud jsou nakonfigurovány jako souborový server a svazky, pokud jsou nakonfigurovány jako server iSCSI. Doporučené postupy pro vytváření sdílených složek a svazků se vztahují k velikosti a nakonfigurovanému typu.

#### <a name="volumeshare-size"></a>Velikost svazku/sdílené hod
Ve virtuálním poli můžete zřídit sdílené složky, pokud jsou nakonfigurovány jako souborový server a svazky, pokud jsou nakonfigurovány jako server iSCSI. Doporučené postupy pro vytváření sdílených složek a svazků se vztahují k velikosti a nakonfigurovanému typu. 

Mějte na paměti následující osvědčené postupy při zřizování sdílených složek nebo svazků na virtuálním zařízení.

* Velikostsouboru vzhledem k zřízené velikosti vrstvené sdílené složky může mít vliv na výkon vrstvení. Práce s velkými soubory může mít za následek pomalé vrstvy ven. Při práci s velkými soubory doporučujeme, aby největší soubor byl menší než 3 % velikosti sdílené složky.
* Ve virtuálním poli lze vytvořit maximálně 16 svazků nebo sdílených složek. Omezení velikosti místně vázaných a vrstvených svazků/sdílených složek vždy nastupte v [limitech virtuálního pole StorSimple](storsimple-ova-limits.md).
* Při vytváření objemu faktor v očekávané spotřebě dat, jakož i budoucí růst. Svazek nebo sdílenou složku nelze později rozbalit.
* Po vytvoření svazku nebo sdílené složky nelze zmenšit velikost svazku nebo sdílené složky na StorSimple.
* Při zápisu na vrstvený svazek na StorSimple, když data svazku dosáhne určité prahové hodnoty (vzhledem k místnímu prostoru vyhrazenému pro svazek), je vstupně-tank omezen. Pokračování zápisu do tohoto svazku výrazně zpomaluje iO. I když můžete zapisovat vrstvený svazek nad rámec jeho zřízená kapacita (nemáme aktivně zastavit uživatele z psaní nad rámec zřízená kapacita), zobrazí upozornění v tom smyslu, že jste oversubscribed. Jakmile se výstraha zobrazí, je nezbytné provést nápravná opatření, jako je odstranění dat svazku (rozšíření svazku není aktuálně podporováno).
* V případech použití zotavení po havárii, protože počet povolených sdílených složek/svazků je 16 a maximální počet sdílených složek/svazků, které mohou být zpracovány paralelně, je také 16, počet sdílených složek/svazků nemá vliv na vaše RPO a RTO.

#### <a name="volumeshare-type"></a>Typ svazku/sdílené složky
StorSimple podporuje dva typy svazků nebo sdílení na základě použití: místně připnuté a vrstvené. Místně vázaných svazků nebo sdílených složek jsou hustě zřízené vzhledem k tomu, že vrstvené svazky nebo sdílené složky jsou řídce zřízené. Místně vázaný svazek nebo sdílenou složku nelze po vytvoření převést na vrstvený nebo *naopak.*

Při konfiguraci svazků/sdílených složek StorSimple doporučujeme implementovat následující doporučené postupy:

* Určete typ svazku na základě úloh, které chcete nasadit před vytvořením svazku. Používejte místně vázaných svazků pro úlohy, které vyžadují místní záruky dat (i během výpadku cloudu) a které vyžadují nízké latence cloudu. Po vytvoření svazku ve virtuálním poli nelze změnit typ svazku z místně připnutého na vrstvený nebo *naopak*. Jako příklad můžete při nasazování úloh SQL nebo úloh hostování virtuálních počítačů (Virtuální počítače) vytvářet místně připojené svazky; používejte vrstvené svazky pro úlohy sdílené složky.


#### <a name="volume-format"></a>Formát svazku
Po vytvoření svazků StorSimple na serveru iSCSI je třeba svazky inicializovat, připojit a naformátovat. Tato operace se provádí na hostiteli připojeném k zařízení StorSimple. Následující doporučené postupy se doporučuje při montáži a formátování svazků na hostiteli StorSimple.

* Proveďte rychlý formát na všech svazcích StorSimple.
* Při formátování svazku StorSimple použijte velikost alokační jednotky (AUS) 64 KB (výchozí hodnota je 4 KB). 64 KB AUS je založen na testování provádí in-house pro běžné úlohy StorSimple a další úlohy.
* Při použití virtuálního pole StorSimple nakonfigurovaného jako server iSCSI nepoužívejte rozložené svazky nebo dynamické disky, protože tyto svazky nebo disky nejsou podporovány službou StorSimple.

#### <a name="share-access"></a>Přístup ke sdílení
Při vytváření sdílených složek na souborovém serveru virtuálního pole postupujte podle následujících pokynů:

* Při vytváření sdílené položky přiřaďte skupinu uživatelů jako správce sdílené složky namísto jednoho uživatele.
* Oprávnění systému souborů NTFS můžete spravovat po vytvoření sdílené složky úpravou sdílených složek prostřednictvím Průzkumníka Windows.

#### <a name="volume-access"></a>Přístup ke svazku
Při konfiguraci svazků iSCSI ve virtuálním poli StorSimple je důležité řídit přístup tam, kde je to nutné. Chcete-li zjistit, které hostitelské servery mohou přistupovat ke svazkům, vytvářet a přidružovat záznamy řízení přístupu (AVR) ke svazkům StorSimple.

Při konfiguraci akreditivů pro svazky StorSimple použijte následující doporučené postupy:

* Vždy přidružte ke svazku alespoň jeden acr.

* Při přiřazování více než jednoho acr ke svazku, ujistěte se, že svazek není vystaven způsobem, kde je možné současně přistupovat více než jeden hostitel bez clusteru. Pokud jste ke svazku přiřadili více akreditivů, zobrazí se varovná zpráva, která zkontroluje vaši konfiguraci.

### <a name="data-security-and-encryption"></a>Zabezpečení a šifrování dat
Vaše virtuální pole StorSimple má funkce zabezpečení dat a šifrování, které zajišťují důvěrnost a integritu vašich dat. Při použití těchto funkcí doporučujeme dodržovat tyto doporučené postupy: 

* Před odesláním dat z virtuálního pole do cloudu definujte šifrovací klíč cloudového úložiště pro generování šifrování AES-256. Tento klíč není vyžadován, pokud jsou data zašifrována. Klíč lze generovat a uchovávat v bezpečí pomocí systému správy klíčů, jako je [například trezor klíčů Azure](../key-vault/key-vault-overview.md).
* Při konfiguraci účtu úložiště prostřednictvím služby StorSimple Manager se ujistěte, že povolíte režim TLS a vytvoříte zabezpečený kanál pro síťovou komunikaci mezi zařízením StorSimple a cloudem.
* Pravidelně obnovujte klíče pro vaše účty úložiště (přístupem ke službě Azure Storage) tak, aby odpovídaly všem změnám v přístupu na základě změněného seznamu správců.
* Data ve virtuálním poli se před odesláním do Azure komprimují a deduplikují. Nedoporučujeme používat službu role Odstranění duplicitních dat na hostiteli systému Windows Server.

## <a name="operational-best-practices"></a>Osvědčené provozní postupy
Osvědčené provozní postupy jsou pokyny, které je třeba dodržovat během každodenní správy nebo provozu virtuálního pole. Tyto postupy zahrnují konkrétní úlohy správy, jako je zálohování, obnovení ze zálohovací sady, provedení převzetí služeb při selhání, deaktivace a odstranění pole, sledování využití systému a stavu a spuštění virových kontrol ve virtuálním poli.

### <a name="backups"></a>Zálohování
Data ve vašem virtuálním poli jsou zálohována do cloudu dvěma způsoby, výchozí automatizovanou denní zálohou celého zařízení od 22:30 nebo pomocí ručního zálohování na vyžádání. Ve výchozím nastavení zařízení automaticky vytvoří denní cloudové snímky všech dat, která na něm jsou seminat. Další informace naleznete v [části Zálohování virtuálního pole StorSimple](storsimple-virtual-array-backup.md).

Četnost a uchovávání informací spojené s výchozími zálohami nelze změnit, ale můžete nakonfigurovat čas, kdy jsou denní zálohy inicializovány každý den. Při konfiguraci času spuštění pro automatické zálohování doporučujeme:

* Naplánujte si zálohování na pracovní dobu mimo špičku. Čas spuštění zálohování by se neměl shodovat s mnoha vi.
* Při plánování převzetí služeb při selhání zařízení nebo před oknem údržby můžete zahájit ruční zálohování na vyžádání, abyste ochránili data ve virtuálním poli.

### <a name="restore"></a>Obnovení
Ze zálohovací sady můžete obnovit dvěma způsoby: obnovit na jiný svazek nebo sdílet nebo provést obnovení na úrovni položky (k dispozici pouze na virtuálním poli nakonfigurovaném jako souborový server). Obnovení na úrovni položky umožňuje provést podrobné obnovení souborů a složek z cloudové zálohy všech sdílených složek na zařízení StorSimple. Další informace naleznete v [obnovení ze zálohy](storsimple-virtual-array-clone.md).

Při provádění obnovení mějte na paměti následující pokyny:

* Virtuální pole StorSimple nepodporuje obnovení na místě. Toho však lze snadno dosáhnout dvoustupňovým procesem: vytvořte místo ve virtuálním poli a potom obnovte jiný svazek nebo sdílenou složku.
* Při obnovení z místního svazku mějte na paměti, že obnovení bude dlouho běžící operace. Ačkoli objem může rychle přijít online, data jsou i nadále hydratovaná na pozadí.
* Typ svazku zůstává stejný během procesu obnovení. Vrstvený svazek se obnoví na jiný vrstvený svazek a místně vázaný svazek na jiný místně vázaný svazek.
* Při pokusu o obnovení svazku nebo sdílené složky ze zálohovací sady může být v případě selhání úlohy obnovení na portálu stále vytvořen cílový svazek nebo sdílená sada. Je důležité odstranit tento nepoužívaný cílový svazek nebo sdílet na portálu, abyste minimalizovali budoucí problémy vyplývající z tohoto prvku.

### <a name="failover-and-disaster-recovery"></a>Převzetí služeb při selhání a zotavení po havárii
Převzetí služeb při selhání zařízení umožňuje migrovat data ze *zdrojového* zařízení v datovém centru do jiného *cílového* zařízení umístěného ve stejném nebo jiném zeměpisném umístění. Převzetí služeb při selhání zařízení je pro celé zařízení. Během převzetí služeb při selhání změní cloudová data pro zdrojové zařízení vlastnictví na vlastnictví cílového zařízení.

Pro vaše StorSimple Virtual Array, můžete pouze převzetí služeb při selhání do jiného virtuálního pole spravovaného stejnou službou StorSimple Manager. Převzetí služeb při selhání zařízení řady 8000 nebo pole spravované jinou službou StorSimple Manager (než služba pro zdrojové zařízení) není povoleno. Další informace o aspektech převzetí služeb při selhání naleznete [v požadavcích na převzetí služeb při selhání .](storsimple-virtual-array-failover-dr.md)

Při provádění převzetí služeb při selhání pro vaše virtuální pole mějte na paměti následující:

* Pro plánované převzetí služeb při selhání je doporučeno osvědčeným postupem převést všechny svazky nebo sdílené složky do offline před zahájením převzetí služeb při selhání. Postupujte podle pokynů pro konkrétní operační systém převést svazky nebo sdílené složky offline na hostiteli první a pak je převést do režimu offline na virtuálním zařízení.
* Pro zotavení po havárii souborového serveru (DR) doporučujeme připojit cílové zařízení ke stejné doméně jako zdroj, aby byla automaticky vyřešena oprávnění ke sdílení. V této verzi je podporováno pouze převzetí služeb při selhání cílovému zařízení ve stejné doméně.
* Po úspěšném dokončení zotavení po havárii se zdrojové zařízení automaticky odstraní. I když zařízení již není k dispozici, virtuální počítač, který jste zřídit v hostitelském systému stále spotřebovává prostředky. Doporučujeme odstranit tento virtuální počítač z hostitelského systému, abyste zabránili vzniku jakýchkoli poplatků.
* Všimněte si, že i v případě, že převzetí služeb při selhání je neúspěšné, **data jsou vždy bezpečné v cloudu**. Zvažte následující tři scénáře, ve kterých převzetí služeb při selhání nebylo úspěšně dokončeno:
  
  * K chybě došlo v počátečních fázích převzetí služeb při selhání, například při provádění předběžných kontrol zotavení po havárii. V takovém případě je cílové zařízení stále použitelné. Můžete opakovat převzetí služeb při selhání na stejném cílovém zařízení.
  * Během skutečného procesu převzetí služeb při selhání došlo k chybě. V tomto případě je cílové zařízení označeno jako nepoužitelné. Je nutné zřídit a nakonfigurovat jiné cílové virtuální pole a použít pro převzetí služeb při selhání.
  * Převzetí služeb při selhání bylo dokončeno, po kterém bylo zdrojové zařízení odstraněno, ale cílové zařízení má problémy a nemáte přístup k žádným datům. Data jsou stále v bezpečí v cloudu a lze je snadno načíst vytvořením jiného virtuálního pole a jeho následným použitím jako cílového zařízení pro zotavení po havárii.

### <a name="deactivate"></a>Deaktivovat
Když deaktivujete virtuální pole StorSimple, přerušíte spojení mezi zařízením a odpovídající službou StorSimple Manager. Deaktivace je **trvalá** operace a nelze ji vrátit zpět. Deaktivované zařízení nelze znovu zaregistrovat ve službě StorSimple Manager. Další informace naleznete v [deaktivaci a odstranění virtuálního pole StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

Při deaktivaci virtuálního pole mějte na paměti následující doporučené postupy:

* Před deaktivací virtuálního zařízení pořizovat snímek cloudu se všemi daty. Při deaktivaci virtuálního pole dojde ke ztrátě všech dat místního zařízení. Pořízení snímku cloudu vám umožní obnovit data v pozdější fázi.
* Před deaktivací virtuálního pole StorSimple nezapomeňte zastavit nebo odstranit klienty a hostitele, kteří jsou na tomto zařízení závislí.
* Pokud již nepoužíváte, odstraňte deaktivované zařízení, aby nenabíjelo poplatky.

### <a name="monitoring"></a>Monitorování
Chcete-li zajistit, aby vaše Virtuální pole StorSimple bylo v nepřetržitém stavu, je třeba sledovat pole a zajistit, že obdržíte informace ze systému včetně výstrah. Chcete-li sledovat celkový stav virtuálního pole, implementujte následující osvědčené postupy:

* Nakonfigurujte monitorování tak, aby sledovalo využití disku datového disku virtuálního pole i disku operačního systému. Pokud používáte technologii Hyper-V, můžete ke sledování hostitelů virtualizace použít kombinaci správce virtuálních strojů System Center (SCVMM) a Nástroje pro provoz operations manageru system center.
* Nakonfigurujte e-mailová oznámení ve virtuálním poli tak, aby odesílala upozornění na určité úrovni využití.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Index vyhledávání a antivirové aplikace
Virtuální pole StorSimple může automaticky vrstvit data z místní vrstvy do cloudu Microsoft Azure. Při použití aplikace, jako je například vyhledávání indexu nebo vyhledávání virů ke skenování dat uložených na StorSimple, je třeba dbát na to, aby se k datům v cloudu nezískal přístup a stáhl i zpět na místní úroveň.

Při konfiguraci vyhledávání indexu nebo vyhledávání virů ve virtuálním poli doporučujeme implementovat následující doporučené postupy:

* Zakažte všechny automaticky nakonfigurované operace úplného skenování.
* U vrstvených svazků nakonfigurujte aplikaci pro vyhledávání v indexu nebo pro vyhledávání virů tak, aby prováděla přírůstkovou prohledávačku. To by prohledávají pouze nová data pravděpodobně s bydlištěm na místní úrovni. Data, která je vrstvené do cloudu není přístupná během přírůstkové operace.
* Ujistěte se, že jsou nakonfigurovány správné vyhledávací filtry a nastavení tak, aby byly prohledány pouze zamýšlené typy souborů. Například obrazové soubory (JPEG, GIF a TIFF) a inženýrské výkresy by neměly být kontrolovány během přírůstkového nebo úplného obnovení indexu.

Pokud používáte proces indexování systému Windows, postupujte podle následujících pokynů:

* Nepoužívejte Windows Indexer pro vrstvené svazky, protože připomíná velké množství dat (TBs) z cloudu, pokud index je třeba často znovu sestavit. Opětovné sestavení indexu by načetlo všechny typy souborů k indexování jejich obsahu.
* Proces indexování systému Windows pro místně vázaných svazků, protože by to přístup k datům na místních vrstvách k vytvoření indexu (cloudová data nebudou přístupná).

### <a name="byte-range-locking"></a>Zamykání rozsahu bajtů
Aplikace mohou uzamknout zadaný rozsah bajtů v rámci souborů. Pokud je povoleno zamykání rozsah bajtů v aplikacích, které zapisují do vašeho StorSimple, pak vrstvení nefunguje na virtuální pole. Aby vrstvení fungovalo, měly by být odemčeny všechny oblasti přístupných souborů. Uzamčení rozsahu bajtů není podporováno vrstvenými svazky ve virtuálním poli.

Mezi doporučená opatření ke zmírnění uzamčení rozsahu bajtů patří:

* Vypněte uzamčení rozsahu bajtů v logice aplikace.
* Pro data přidružená k této aplikaci použijte místně vázaných svazků (namísto vrstvených). Místně vázaných svazků nejsou vrstvy do cloudu.
* Při použití místně vázaných svazků s povoleným zamykáním rozsahu bajtů může být svazek před dokončením obnovení online. V těchto případech musíte počkat na dokončení obnovení.

## <a name="multiple-arrays"></a>Více polí
Několik virtuálních polí může být nutné nasadit, aby se zohlednila rostoucí pracovní sada dat, která by mohla přelít do cloudu, což by ovlivnilo výkon zařízení. V těchto případech je nejlepší škálovat zařízení jako pracovní sada roste. To vyžaduje přidání jednoho nebo více zařízení v místním datovém centru. Při přidávání zařízení můžete:

* Rozdělení aktuální sady dat.
* Nasazujte nové úlohy do nových zařízení.
* Pokud nasazujete více virtuálních polí, doporučujeme, aby z hlediska vyrovnávání zatížení distribuovat pole mezi různými hostiteli hypervisoru.
* Více virtuálních polí (při konfiguraci jako souborový server nebo server iSCSI) lze nasadit v oboru názvů distribuovaného systému souborů. Podrobné kroky naleznete v příručce [Distributed File System Namespace Solution with Hybrid Cloud Storage Deployment Guide](https://www.microsoft.com/download/details.aspx?id=45507). Replikace distribuovaného systému souborů se aktuálně nedoporučuje používat s virtuálním polem. 

## <a name="see-also"></a>Viz také
Přečtěte si, jak [spravovat virtuální pole StorSimple](storsimple-virtual-array-manager-service-administration.md) prostřednictvím služby StorSimple Manager.

